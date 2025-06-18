---
id: performance
title: Performance Overview
---

相較於基於WebView的工具，使用React Native的一個重要原因是為了實現每秒60幀的流暢度，並賦予應用程式原生的外觀與操作體驗。在可能的情況下，我們希望React Native能自動處理好效能優化，讓您專注於應用開發。然而在某些領域，我們尚未完全實現這個目標；還有一些情況（如同直接編寫原生代碼時），React Native無法自動判斷最佳優化方式，此時需要手動介入調整。我們盡力預設提供絲般順滑的UI效能，但有時仍難以達成。

本指南旨在傳授基礎知識，幫助您[排查效能問題](profiling.md)，並探討[常見問題根源及其解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀率的必備知識

老一輩人將電影稱為「活動影像」有其道理：影片中流暢的動態效果，實際上是透過快速連續播放靜態畫面產生的視覺幻覺。我們稱這些靜態畫面為「幀」。每秒顯示的幀數（FPS）直接影響影片（或使用者介面）的流暢度與真實感。iOS裝置以每秒60幀運作，這意味著您與UI系統只有約16.67毫秒的時間來完成生成該幀所需的所有工作。若無法在此時間內完成，就會發生「掉幀」，導致介面出現卡頓。

補充說明：當您在應用中開啟開發者選單並啟用`顯示效能監測器`時，會注意到系統顯示兩種不同的幀率。

![](/docs/assets/PerfUtil.png)

### JS幀率（JavaScript執行緒）

多數React Native應用的業務邏輯運行於JavaScript執行緒，這裡處理React組件生命週期、API呼叫、觸控事件等。對原生視圖的更新會被批量處理，並在事件循環每次迭代結束時（理想情況下在幀截止時間前）發送至原生端。若JavaScript執行緒在某一幀期間無響應，就會被視為掉幀。例如，當您在複雜應用的根組件呼叫`this.setState`，導致需要重新渲染計算密集的子組件樹時，可能耗費200毫秒並造成12幀丟失——此時所有由JavaScript控制的動畫都會出現凍結。任何超過100毫秒的延遲都會被使用者明顯感知。

這種情況常見於`Navigator`轉場時：當推送新路由時，JavaScript執行緒需渲染場景所需的所有組件，才能向原生端發送正確指令來創建底層視圖。此過程通常會耗費數幀時間，造成[卡頓](http://jankfree.org/)，因為轉場動畫由JavaScript執行緒控制。有時組件在`componentDidMount`中執行額外工作，可能導致轉場過程中出現二次卡頓。

另一個例子是觸控回應：若JavaScript執行緒正在處理跨越多幀的任務，您可能會注意到`TouchableOpacity`等組件的響應延遲。這是因為JavaScript執行緒忙碌而無法處理從主執行緒傳來的原始觸控事件，導致`TouchableOpacity`無法即時調整原生視圖的透明度。

### UI幀率（主執行緒）

許多人注意到`NavigatorIOS`的預設效能優於`Navigator`，關鍵原因在於其轉場動畫完全在主執行緒執行，因此不會受JavaScript執行緒掉幀的影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍可以順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運行在主執行緒上。滾動事件會被分發到 JS 執行緒，但滾動行為的發生並不依賴於這些事件的接收。

## 常見效能問題來源

### 處於開發模式 (`dev=true`)

在開發模式下，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供良好的警告和錯誤訊息（例如驗證 propTypes 和其他各種斷言），運行時需要執行更多工作。請務必在[發行版本](running-on-device.md#building-your-app-for-production)中測試效能。

### 使用 `console.log` 語句

在運行打包後的應用程式時，這些語句可能會導致 JavaScript 執行緒出現嚴重瓶頸。這包括來自除錯庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，因此請確保在打包前移除它們。您也可以使用這個 [Babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。首先需要通過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 文件如下：

```json
{
  "env": {
    "production": {
      "plugins": ["transform-remove-console"]
    }
  }
}
```

這將自動移除專案發行（生產）版本中的所有 `console.*` 呼叫。

即使專案中沒有 `console.*` 呼叫，也建議使用此插件。第三方庫也可能會呼叫它們。

### `ListView` 初始渲染過慢或大型列表滾動效能不佳

改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新的列表元件還具有顯著的效能提升，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

如果您的 [`FlatList`](flatlist.md) 渲染速度慢，請確保實現了 [`getItemLayout`](flatlist.md#getitemlayout)，通過跳過渲染項目的測量來優化渲染速度。

### 重新渲染幾乎不變的視圖時 JS FPS 驟降

如果使用 ListView，您必須提供 `rowHasChanged` 函數，該函數可以通過快速判斷行是否需要重新渲染來減少大量工作。如果使用不可變數據結構，這可能只需要進行引用相等性檢查。

類似地，您可以實現 `shouldComponentUpdate` 並指定希望元件重新渲染的具體條件。如果編寫純元件（渲染函數的返回值完全依賴於 props 和 state），可以利用 PureComponent 來實現這一點。再次強調，不可變數據結構有助於保持高效——如果需要對大型對象列表進行深度比較，重新渲染整個元件可能更快，且代碼量更少。

### 由於同時在 JavaScript 執行緒上執行大量工作導致 JS 執行緒 FPS 下降

「導航器過渡緩慢」是這種情況最常見的表現，但也可能在其他時候發生。使用 InteractionManager 是一個不錯的方法，但如果延遲動畫期間的工作對用戶體驗影響太大，則可以考慮使用 LayoutAnimation。

目前，Animated API 會在 JavaScript 執行緒上按需計算每個關鍵幀，除非您[設置 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)，而 LayoutAnimation 利用 Core Animation，不受 JS 執行緒和主執行緒掉幀的影響。

我曾遇到的一個使用場景是：在初始化並可能接收多個網絡請求的回應、渲染模態框內容以及更新打開模態框的視圖時，同時動畫顯示模態框（從頂部滑下並淡入半透明遮罩）。有關如何使用 LayoutAnimation 的更多信息，請參閱動畫指南。

注意事項：

- LayoutAnimation 僅適用於「一次性觸發即忘」的動畫（「靜態」動畫）——若需中斷動畫，則必須使用 `Animated`。

### 移動畫面中的視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

此情況尤其容易發生於文字帶有透明背景並疊加於圖片上方時，或任何需要透過 alpha 合成逐幀重繪視圖的場景。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可顯著改善此問題。

請謹慎使用這些屬性，避免導致記憶體用量暴增。使用時務必分析效能與記憶體用量。若視圖不再需要移動，請關閉此屬性。

### 調整圖片尺寸動畫導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬高時，系統會從原始圖片重新裁切並縮放。此操作對大圖尤其耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如點擊圖片放大至全螢幕的場景即適用此技巧。

### TouchableX 元件觸控反應遲鈍

有時若在調整觸控回應元件的不透明度或高亮效果的同一幀中執行操作，可能需等到 `onPress` 函式執行完畢才會看到視覺變化。若 `onPress` 觸發的 `setState` 導致大量運算而掉幀，便會發生此狀況。解決方法是將 `onPress` 處理程序內的操作包裹於 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導覽器轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「從右側推入」場景轉場為例：每一幀中，新場景需從起始的螢幕外位置（假設 x 偏移值為 320）向左移動至 x 偏移值為 0 的定位點。若 JavaScript 執行緒被鎖定而無法傳送新偏移值，該幀的動畫更新就會停頓。

解決方案之一是將基於 JavaScript 的動畫卸載至主執行緒執行。以前述場景為例，我們可在轉場開始時預先計算新場景的所有 x 偏移值序列，並交由主執行緒以優化方式執行。釋放 JavaScript 執行緒的負擔後，即使渲染場景時掉幾幀也幾乎無感——流暢的轉場動畫會轉移你的注意力。

[React Navigation](navigation.md) 新函式庫的主要目標正是解決此問題。其元件採用原生元件與 [`Animated`](animated.md) 函式庫，透過原生執行緒實現 60 FPS 的流暢動畫。