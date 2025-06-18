---
id: performance
title: Performance Overview
---

相較於基於WebView的工具，使用React Native的一個重要原因是為了實現每秒60幀的流暢度，並為應用提供原生外觀與體驗。在可行情況下，我們希望React Native能自動處理優化，讓開發者能專注於應用本身而無需擔心效能問題。然而，目前仍有某些領域尚未達到此目標，還有一些情況React Native（如同直接編寫原生代碼）無法自動判斷最佳優化方式。此時就需要手動介入調整。我們預設會提供如絲般順滑的UI效能，但某些情況下可能無法實現。

本指南旨在傳授基礎知識，幫助您[排查效能問題](profiling.md)，並探討[常見問題根源及其解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀率的必備知識

老一輩人將電影稱為「活動影像」有其道理：影片中流暢的動態效果其實是通過快速切換靜態畫面產生的視覺幻覺。我們稱這些靜態畫面為「幀」。每秒顯示的幀數直接影響影片（或使用者介面）的流暢度與真實感。iOS設備以每秒60幀的速率刷新，這意味著您與UI系統只有約16.67毫秒來完成生成單一靜態畫面（幀）所需的所有工作。若無法在規定時間內完成幀生成，就會發生「掉幀」，導致介面出現卡頓。

需要特別說明的是，當您在應用中開啟[開發者選單](debugging.md#accessing-the-dev-menu)並啟用「顯示效能監測器」時，會注意到系統顯示兩種不同的幀率。

![](/docs/assets/PerfUtil.png)

### JS幀率（JavaScript執行緒）

多數React Native應用的業務邏輯都運行在JavaScript執行緒上。這裡是React應用的核心運作區域，負責API調用、觸控事件處理等工作。對原生視圖的更新會被批量處理，並在事件循環每次迭代結束時（理想情況下在幀截止時間前）發送至原生端。若JavaScript執行緒在某幀期間無響應，就會被視為掉幀。例如，當您在複雜應用的根組件調用`this.setState`導致需要重新渲染計算密集的子組件樹時，可能耗時200毫秒並造成12幀丟失，此時所有由JavaScript控制的動畫都會出現凍結現象。任何超過100毫秒的延遲都會被使用者明顯感知。

這種情況常見於`Navigator`轉場時：當推送新路由時，JavaScript執行緒需要渲染場景所需的所有組件，才能向原生端發送正確指令來創建底層視圖。此過程通常會耗費數幀時間，導致[畫面卡頓](https://jankfree.org/)，因為轉場動畫是由JavaScript執行緒控制的。有時組件在`componentDidMount`中執行額外工作，可能造成轉場過程中的二次卡頓。

另一個典型例子是觸控響應：若JavaScript執行緒正在處理跨越多幀的任務，您可能會注意到`TouchableOpacity`等組件的響應延遲。這是因為JavaScript執行緒處於忙碌狀態，無法處理從主執行緒發送的原始觸控事件，導致`TouchableOpacity`無法根據觸控事件通知原生視圖調整透明度。

### UI幀率（主執行緒）

許多開發者注意到`NavigatorIOS`的預設性能優於`Navigator`，其根本原因在於前者的轉場動畫完全在主執行緒上執行，因此不會受JavaScript執行緒掉幀的影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運作於主執行緒上。滾動事件會被派發至 JS 執行緒，但滾動行為的發生並不依賴這些事件的接收。

## 常見效能問題來源

### 處於開發模式 (`dev=true`)

在開發模式下執行時，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供完善的警告和錯誤訊息，運行時需執行更多工作。請務必在[正式版建置](running-on-device.md#building-your-app-for-production)中測試效能。

### 使用 `console.log` 語句

在執行打包後的應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包括來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，因此請確保在打包前移除它們。您也可以使用這個 [babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。需先透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

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

即使專案中未使用 `console.*` 呼叫，仍建議使用此插件。第三方函式庫也可能呼叫這些方法。

### `ListView` 初始渲染過慢或大型列表滾動效能不佳

請改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新列表元件還具有顯著的效能優化，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

If your [`FlatList`](flatlist.md) is rendering slow, be sure that you've implemented [`getItemLayout`](flatlist.md#getitemlayout) to optimize rendering speed by skipping measurement of the rendered items.

### 重新渲染幾乎未變化的視圖導致 JS FPS 驟降

若使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來大幅減少工作量。若使用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，可實作 `shouldComponentUpdate` 並明確指定元件重新渲染的條件。若編寫純元件（渲染函式的返回值完全依賴於 props 和 state），可透過 PureComponent 自動處理。再次強調，不可變資料結構有助於保持高效——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼量更少。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場過慢」是最常見的表現形式，但其他情況也可能發生。使用 InteractionManager 是良好的解決方案，但若延遲動畫期間的工作對使用者體驗影響過大，則可考慮改用 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格，除非您[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)，而 LayoutAnimation 則利用 Core Animation，不受 JS 執行緒和主執行緒掉幀影響。

我曾將此技術用於模態視窗的動畫（從頂部滑下並淡入半透明遮罩），同時初始化並可能接收多個網路請求的回應、渲染模態視窗內容，以及更新開啟模態視窗的原始視圖。詳見動畫指南以獲取更多關於 LayoutAnimation 的使用資訊。

注意事項：

- LayoutAnimation 僅適用於「一發不可動」的動畫（靜態動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況尤其發生於文字帶有透明背景並疊加在圖片上方時，或任何需要每幀重新繪製視圖的 alpha 合成場景。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可顯著改善此問題。

需謹慎使用以避免記憶體暴增。啟用這些屬性時請務必分析效能與記憶體用量。若視圖不再需要移動，請關閉此屬性。

### 調整圖片尺寸動畫導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬高時，系統會從原始圖片重新裁剪和縮放。對於大圖而言效能消耗極大。建議改用 `transform: [{scale}]` 樣式屬性實現尺寸動畫。典型應用場景是點擊圖片放大至全螢幕。

### TouchableX 元件響應遲鈍

若在觸控響應的同一幀中同時執行透明度/高亮調整與其他操作，可能導致 `onPress` 函數返回後才看到視覺效果。當 `onPress` 觸發的 `setState` 引發大量運算導致掉幀時會發生此現象。解決方案是將 `onPress` 處理程序內的操作包裹在 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導航器轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「右側推入」場景轉場為例：每一幀都需要將新場景從右側（假設初始 x 偏移 320）移動至左側（最終 x 偏移 0）。若 JavaScript 執行緒被阻塞，就無法發送新的 x 偏移值給主線程，導致動畫掉幀。

解決方案之一是將基於 JavaScript 的動畫卸載到主線程執行。例如預先計算新場景所有 x 偏移值並交由主線程優化執行。如此釋放 JavaScript 執行緒後，即使渲染場景時掉幀也不易察覺，因為流暢的轉場動畫會吸引用戶注意力。

[React Navigation](navigation.md) 新庫的主要目標正是解決此問題。其元件採用原生組件與 [`Animated`](animated.md) 庫，通過原生線程實現 60 FPS 動畫。