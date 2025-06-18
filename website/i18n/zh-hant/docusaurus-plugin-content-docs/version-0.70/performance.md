---
id: performance
title: Performance Overview
---

相較於基於WebView的工具，使用React Native的一個重要原因是為了實現每秒60幀的流暢度以及應用程式的原生外觀與操作體驗。在可能的情況下，我們希望React Native能夠自動處理好這些細節，讓您專注於應用程式開發而非效能優化。然而，在某些領域我們尚未完全實現這一目標，還有一些情況（類似直接編寫原生代碼）React Native無法自動為您決定最佳優化方式，這時就需要手動介入。我們盡力預設提供如絲般順滑的UI效能，但有時這並不可行。

本指南旨在教您一些基礎知識，幫助您[排查效能問題](profiling.md)，並討論[常見問題來源及其建議解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀的基礎知識

您的祖父母輩將電影稱為「活動影像」是有原因的：影片中逼真的動態效果其實是一種錯覺，由靜態畫面以固定速度快速切換所創造。我們將這些靜態畫面稱為「幀」。每秒顯示的幀數直接影響影片（或使用者介面）的流暢度與逼真程度。iOS裝置每秒顯示60幀，這意味著您和UI系統大約有16.67毫秒的時間來完成生成該幀所需的所有工作。如果您未能在分配的16.67毫秒內完成生成該幀的工作，就會「掉幀」，UI將顯得反應遲鈍。

現在稍微複雜化這個問題：在您的應用程式中打開開發者選單並切換「顯示效能監控器」。您會注意到有兩個不同的幀率。

![](/docs/assets/PerfUtil.png)

### JS幀率（JavaScript執行緒）

對於大多數React Native應用程式，您的業務邏輯會在JavaScript執行緒上運行。這裡是您的React應用程式運作的地方，包括API呼叫、觸控事件處理等...對原生視圖的更新會在事件循環的每次迭代結束時（如果一切順利）批量發送到原生端。如果JavaScript執行緒在某一幀期間無響應，就會被視為掉幀。例如，如果您在一個複雜應用程式的根元件上呼叫`this.setState`，導致需要重新渲染計算密集的子元件樹，這可能會耗時200毫秒，導致掉12幀。這段時間內任何由JavaScript控制的動畫都會看起來凍結。任何超過100毫秒的操作，使用者都會明顯感覺到延遲。

這種情況經常發生在`Navigator`轉場期間：當您推送新路由時，JavaScript執行緒需要渲染場景所需的所有元件，以便向原生端發送正確的命令來創建底層視圖。這裡的工作通常會耗費幾幀的時間，導致[卡頓](http://jankfree.org/)，因為轉場是由JavaScript執行緒控制的。有時元件會在`componentDidMount`中執行額外工作，這可能導致轉場過程中出現第二次卡頓。

另一個例子是觸控回應：如果您在JavaScript執行緒上跨多幀執行工作，您可能會注意到例如`TouchableOpacity`的回應延遲。這是因為JavaScript執行緒繁忙，無法處理從主執行緒發送的原始觸控事件。因此，`TouchableOpacity`無法對觸控事件做出反應並命令原生視圖調整其透明度。

### UI幀率（主執行緒）

許多人注意到`NavigatorIOS`的預設效能優於`Navigator`。這是因為轉場動畫完全在主執行緒上執行，因此不會受到JavaScript執行緒掉幀的影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，這是因為 `ScrollView` 運行在主執行緒上。滾動事件會被分派到 JS 執行緒，但滾動行為的發生並不依賴於這些事件的接收。

## 常見效能問題來源

### 處於開發模式 (`dev=true`)

在開發模式下，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供良好的警告和錯誤訊息（例如驗證 propTypes 和其他斷言），運行時需要執行更多工作。請務必在[正式版建置](running-on-device.md#building-your-app-for-production)中測試效能。

### 使用 `console.log` 語句

在打包應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包括來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，因此在打包前請確保移除它們。您也可以使用這個 [Babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。首先需透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

```json
{
  "env": {
    "production": {
      "plugins": ["transform-remove-console"]
    }
  }
}
```

這將自動移除專案正式版（生產環境）中的所有 `console.*` 呼叫。

即使專案中沒有使用 `console.*` 呼叫，仍建議使用此插件，因為第三方函式庫也可能呼叫它們。

### `ListView` 初始渲染過慢或大型列表滾動效能不佳

請改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新列表元件還具有顯著的效能提升，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

如果您的 [`FlatList`](flatlist.md) 渲染速度慢，請確保實作了 [`getItemLayout`](flatlist.md#getitemlayout)，透過跳過渲染項目的測量來優化渲染速度。

### 重新渲染幾乎未變化的視圖時 JS FPS 驟降

如果使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來減少大量工作。若使用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，您可以實作 `shouldComponentUpdate` 並明確指定元件需要重新渲染的條件。如果是純元件（渲染函式的返回值完全依賴於 props 和 state），可以利用 PureComponent 自動處理。不可變資料結構再次發揮作用——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼更簡潔。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場過慢」是最常見的表現，但其他情況也可能發生。使用 InteractionManager 是個好方法，但若延遲動畫期間的工作對使用者體驗影響過大，則可考慮 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格，除非您[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)，而 LayoutAnimation 則利用 Core Animation，不受 JS 執行緒和主執行緒掉幀影響。

我曾用此技術處理以下情境：在初始化多個網路請求、渲染模態框內容、更新模態框來源視圖的同時，以動畫顯示模態框（從頂部滑下並淡入半透明遮罩）。詳見動畫指南以瞭解如何使用 LayoutAnimation。

注意事項：

- LayoutAnimation 僅適用於一次性動畫（「靜態」動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況尤其發生在文字帶有透明背景並疊加在圖片上方時，或任何需要在每一幀重新繪製視圖的 alpha 合成場景。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可以顯著改善此問題。

請注意不要過度使用這些屬性，否則記憶體使用量可能會暴增。在使用這些屬性時，請務必分析效能和記憶體使用情況。如果不再需要移動視圖，請關閉這些屬性。

### 調整圖片大小動畫導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬度或高度時，系統會從原始圖片重新裁剪和縮放。這對大型圖片來說非常耗資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如，點擊圖片後將其放大至全螢幕的場景就適合使用此方法。

### 我的 TouchableX 視圖反應遲鈍

有時，如果我們在調整觸控回應元件的透明度或高亮效果的同一幀中執行動作，這些視覺效果會等到 `onPress` 函數執行完畢後才會顯示。如果 `onPress` 中的 `setState` 觸發了大量工作並導致掉幀，就可能發生這種情況。解決方法是將 `onPress` 處理程序內的動作包裹在 `requestAnimationFrame` 中：

```jsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導覽器轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「從右側推入」的場景轉場為例：每一幀中，新場景從右側（假設初始 x 偏移為 320）移動到左側，最終停在 x 偏移為 0 的位置。在此過程中，JavaScript 執行緒需要將每一幀的新 x 偏移發送給主執行緒。如果 JavaScript 執行緒被鎖定，就無法完成此操作，導致該幀沒有更新，動畫就會卡頓。

解決方案之一是將基於 JavaScript 的動畫卸載到主執行緒處理。以前述例子為例，我們可以在開始轉場時預先計算新場景的所有 x 偏移值，並將其發送給主執行緒以優化方式執行。這樣 JavaScript 執行緒就無需處理此任務，即使在渲染場景時掉幾幀也無妨——因為流暢的轉場動畫會讓你忽略這些微小卡頓。

新推出的 [React Navigation](navigation.md) 庫主要目標之一就是解決此問題。React Navigation 中的視圖使用原生元件和 [`Animated`](animated.md) 庫，透過原生執行緒運行的方式實現 60 FPS 流暢動畫。