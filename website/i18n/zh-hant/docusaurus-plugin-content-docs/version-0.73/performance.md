---
id: performance
title: Performance Overview
---

相較於基於WebView的工具，使用React Native的一個重要原因是能實現每秒60幀的流暢度，並為應用提供原生外觀與操作體驗。在可行情況下，我們希望React Native能自動處理優化，讓開發者能專注於應用功能而無需擔心效能問題。然而，某些領域我們尚未完全實現這個目標，還有一些情況（如同直接編寫原生代碼時）React Native無法自動判斷最佳優化方案。此時就需要手動介入調整。我們默認會提供絲滑般的UI性能，但偶爾仍可能出現無法達標的狀況。

本指南旨在傳授基礎知識，協助您[排查效能問題](profiling.md)，同時探討[常見問題根源及其解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀率的必備知識

老一輩將電影稱為「活動影像」有其道理：影片中流暢的動態效果其實是靜態畫面以固定速度快速切換造成的視覺幻象。我們稱這些靜態畫面為「幀」。每秒顯示的幀數直接影響影片（或使用者介面）的流暢度與真實感。iOS設備以每秒60幀運行，這意味著您與UI系統只有約16.67毫秒來完成生成該幀所需的所有工作。若無法在此時間內完成幀渲染，就會發生「掉幀」，導致介面出現卡頓。

需要特別說明的是，當您在應用中開啟[開發者選單](debugging.md#accessing-the-dev-menu)並啟用「顯示性能監測器」時，會注意到實際上存在兩種不同的幀率指標。

![](/docs/assets/PerfUtil.png)

### JavaScript幀率（JS線程）

在大多數React Native應用中，業務邏輯都運行於JavaScript線程。這裡是React應用的核心運作區——處理API調用、觸控事件等。對原生視圖的更新會被批量處理，並在事件循環每次迭代結束時（若一切順利）趕在幀截止期限前發送至原生端。若JavaScript線程在某幀期間無響應，就會被視為掉幀。例如，當您在複雜應用的根組件調用`this.setState`，導致需要重新渲染計算密集的子組件樹時，可能耗時200毫秒從而造成12幀丟失。此時所有由JavaScript控制的動畫都會出現凍結現象。任何超過100毫秒的延遲都會被使用者明顯感知。

這種情況常見於`Navigator`轉場動畫：當推送新路由時，JavaScript線程需要渲染場景所需的所有組件，才能向原生端發送正確指令來創建底層視圖。此過程常會耗費數幀時間，由於轉場由JavaScript線程控制，就會導致[卡頓](https://jankfree.org/)。有時組件在`componentDidMount`中執行額外工作，可能造成轉場過程中出現二次停頓。

另一個典型例子是觸控響應：若JavaScript線程持續多幀處於忙碌狀態，您可能會注意到`TouchableOpacity`等組件的響應延遲。這是因為JavaScript線程無法及時處理從主線程傳來的原始觸控事件，導致`TouchableOpacity`不能立即響應觸控並通知原生視圖調整透明度。

### UI幀率（主線程）

許多開發者注意到`NavigatorIOS`的默認性能優於`Navigator`，其關鍵原因在於前者的轉場動畫完全在主線程執行，因此不會受JavaScript線程掉幀的影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運作於主執行緒上。滾動事件會被派發至 JS 執行緒，但滾動行為的發生並不依賴這些事件的接收。

## 常見效能問題來源

### 處於開發模式運行 (`dev=true`)

在開發模式下運行時，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供完善的警告和錯誤訊息（例如驗證 propTypes 和各種斷言），運行時需執行更多工作。請務必在[正式版本](running-on-device.md#building-your-app-for-production)中測試效能。

### 使用 `console.log` 語句

在運行打包後的應用程式時，這些語句會導致 JavaScript 執行緒出現嚴重瓶頸。這包括來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，因此請務必在打包前移除它們。您也可以使用這個 [babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。首先需透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

```json
{
  "env": {
    "production": {
      "plugins": ["transform-remove-console"]
    }
  }
}
```

這將自動移除專案正式（生產）版本中的所有 `console.*` 呼叫。

即使專案中沒有使用 `console.*` 呼叫，仍建議使用此插件，因為第三方函式庫也可能呼叫它們。

### `ListView` 初始渲染過慢或大型列表滾動效能不佳

請改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新列表元件還具有顯著的效能提升，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

若您的 [`FlatList`](flatlist.md) 渲染速度慢，請確保實作了 [`getItemLayout`](flatlist.md#getitemlayout)，透過跳過渲染項目的測量來優化渲染速度。

### 重新渲染幾乎未變化的視圖時 JS FPS 驟降

若使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來大幅減少工作量。若使用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，您可以實作 `shouldComponentUpdate` 並明確指定元件需要重新渲染的條件。若編寫純元件（渲染函式的返回值完全依賴於 props 和 state），可透過 PureComponent 自動處理。再次強調，不可變資料結構有助於保持高效——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼量更少。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場過慢」是最常見的表現形式，但其他情況也可能發生。使用 InteractionManager 是較好的解決方案，但若延遲動畫期間的工作對使用者體驗影響過大，則可考慮改用 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格（除非[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)），而 LayoutAnimation 則利用 Core Animation，不受 JS 執行緒和主執行緒掉幀影響。

我曾將此技術用於模態視窗的動畫（從頂部滑下並淡入半透明遮罩），同時初始化並可能接收多個網路請求的回應、渲染模態內容，以及更新開啟模態的原始視圖。詳見動畫指南以獲取更多關於 LayoutAnimation 的使用資訊。

注意事項：

- LayoutAnimation 僅適用於一次性動畫（「靜態」動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況尤其發生在文字帶有透明背景並疊加在圖片上，或任何需要在每一幀重新繪製視圖的 alpha 合成場景。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可以顯著改善此問題。

請謹慎使用這些屬性，否則記憶體使用量可能會暴增。在使用這些屬性時，請務必分析效能和記憶體使用情況。如果不再需要移動視圖，請關閉這些屬性。

### 動畫調整圖片大小導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬度或高度時，都會從原始圖片重新裁剪和縮放。這對於大圖來說非常耗資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如，點擊圖片後將其放大至全螢幕的場景。

### 我的 TouchableX 視圖反應遲鈍

有時，如果我們在調整回應觸控的元件透明度或高亮效果的同一幀中執行動作，則在 `onPress` 函數返回之前不會看到效果。如果 `onPress` 中的 `setState` 導致大量工作並丟失幾幀，就可能發生這種情況。解決方法是將 `onPress` 處理程序中的任何動作包裹在 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導航器過場動畫緩慢

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「從右側推入」的場景過場為例：每一幀中，新場景從右側（假設初始 x 偏移為 320）移動到左側，最終停在 x 偏移為 0 的位置。在此過場的每一幀，JavaScript 執行緒需要向主執行緒發送新的 x 偏移值。如果 JavaScript 執行緒被鎖定，則無法完成此操作，導致該幀沒有更新，動畫就會卡頓。

解決方法之一是將基於 JavaScript 的動畫卸載到主執行緒。如果採用這種方式處理上述範例，我們可能會在開始過場時計算新場景的所有 x 偏移值，並將其發送到主執行緒以優化執行。這樣 JavaScript 執行緒就無需負責此任務，即使在渲染場景時丟失幾幀也無關緊要——你可能根本不會注意到，因為會被流暢的過場動畫吸引。

解決此問題是新版 [React Navigation](navigation.md) 庫的主要目標之一。React Navigation 中的視圖使用原生元件和 [`Animated`](animated.md) 庫，以在主執行緒上實現 60 FPS 的流暢動畫。