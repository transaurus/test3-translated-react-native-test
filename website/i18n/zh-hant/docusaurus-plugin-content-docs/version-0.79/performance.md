---
id: performance
title: Performance Overview
---

相較於基於WebView的工具，使用React Native的一個重要原因是能達到至少60幀/秒的流暢度，並為應用提供原生外觀與體驗。我們盡可能讓React Native自動處理優化，使開發者能專注於應用邏輯而無需擔心效能問題。但某些情況下，框架尚未達到完全自動優化的程度，或是類似直接編寫原生代碼時無法自動判斷最佳優化路徑，此時便需要手動介入。我們默認追求如絲般順滑的UI表現，但仍有無法實現的例外情況。

本指南旨在傳授基礎知識，幫助您[排查效能問題](profiling.md)，並探討[常見問題根源及對應解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀的必備知識

老一輩將電影稱為「活動影像」有其道理：流暢的動畫效果實質是靜態畫面以固定速度快速切換形成的視覺幻象。每一幅靜態畫面稱為「幀」。每秒顯示的幀數直接決定了視頻（或用戶界面）的流暢度與真實感。iOS設備的刷新率為60幀/秒，這意味著您與UI系統最多只有16.67毫秒來完成生成單幀畫面所需的所有工作。若無法在該時間窗口內完成幀渲染，就會發生「掉幀」，導致UI出現卡頓。

需特別說明的是：打開應用中的[開發者菜單](debugging.md#opening-the-dev-menu)並啟用「顯示性能監測器」後，您會注意到系統會顯示兩種不同的幀率指標。

![](/docs/assets/PerfUtil.png)

### JS幀率（JavaScript線程）

大多數React Native應用的業務邏輯運行在JavaScript線程上。這裡是React組件樹的生命週期所在，也是API調用、觸控事件處理等操作的執行環境。對原生視圖的更新會在事件循環的每次迭代結束時批量發送到原生端（理想情況下會在幀截止時間前完成）。若JavaScript線程在單幀周期內無響應，就會被判定為掉幀。例如：當您在複雜應用的根組件上調用`this.setState`，導致需要重新渲染計算密集型的組件子樹時，可能耗時200毫秒並造成12幀丟失——此時所有由JavaScript控制的動畫都會出現凍結現象。任何超過100毫秒的延遲都會被用戶明顯感知。

這種情況常見於`Navigator`頁面跳轉：當推送新路由時，JavaScript線程需要完整渲染新場景的所有組件，才能向原生端發送正確的視圖創建指令。這個過程通常會耗費數幀時間，由於轉場動畫由JavaScript線程控制，就會導致[界面卡頓](https://jankfree.org/)。有時組件在`componentDidMount`中執行額外工作，可能造成轉場過程出現二次卡頓。

另一個典型場景是觸控響應：若JavaScript線程持續多幀處於繁忙狀態，您可能會注意到`TouchableOpacity`等組件的觸控反饋出現延遲。這是因為JavaScript線程無法及時處理從主線程傳遞過來的原始觸控事件，導致`TouchableOpacity`無法響應觸控並通知原生視圖調整透明度。

### UI幀率（主線程）

許多開發者注意到`NavigatorIOS`的默認性能優於`Navigator`，其根本原因在於前者的轉場動畫完全在主線程執行，因此不會受JavaScript線程掉幀的影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運作於主執行緒上。滾動事件會被派發至 JS 執行緒，但滾動行為的執行並不依賴這些事件是否被接收。

## 常見效能問題來源

### 處於開發模式 (`dev=true`)

在開發模式下，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供完善的警告與錯誤訊息，運行時需執行更多工作。請務必在[正式版建置](running-on-device.md#building-your-app-for-production)中測試效能表現。

### 使用 `console.log` 語句

在執行打包後的應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包含來自偵錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，請務必在打包前移除它們。您也可以使用這個 [babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。需先透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

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

請改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新列表元件還具備顯著的效能優化，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

若您的 [`FlatList`](flatlist.md) 渲染緩慢，請確認是否實作了 [`getItemLayout`](flatlist.md#getitemlayout)，透過跳過渲染項目的測量來優化渲染速度。

### 重新渲染幾乎未變化的視圖時 JS FPS 驟降

若使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來大幅減少工作量。若使用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，可實作 `shouldComponentUpdate` 並明確指定觸發元件重新渲染的條件。若編寫純元件（渲染函式的返回值完全依賴 props 和 state），可透過 PureComponent 自動處理。再次強調，不可變資料結構能保持高效——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼量更少。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場動畫卡頓」是最常見的表現形式，但其他情況也可能發生。使用 InteractionManager 是較好的解決方案，但若延遲動畫期間的工作會嚴重影響使用者體驗，則可考慮改用 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格（除非[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)），而 LayoutAnimation 則利用 Core Animation，不受 JS 執行緒與主執行緒掉幀影響。

我曾將此技術用於以下情境：在初始化模態框時（從頂部滑下並淡入半透明遮罩），同時處理多個網路請求的回應、渲染模態框內容，並更新開啟模態框的原始視圖。詳見動畫指南以瞭解如何使用 LayoutAnimation。

注意事項：

- LayoutAnimation 僅適用於一次性動畫（「靜態」動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況尤其發生在文字帶有透明背景並疊加在圖片上方時，或任何需要每幀重新繪製視圖的 alpha 合成場景。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可顯著改善此問題。

注意不要過度使用這些屬性，否則記憶體用量可能暴增。使用這些屬性時請分析效能和記憶體用量。如果不再需要移動視圖，請關閉此屬性。

### 動畫調整圖片大小導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬度或高度時，系統會從原始圖片重新裁剪和縮放。這對大圖尤其耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如點擊圖片放大至全螢幕的場景。

### 我的 TouchableX 元件反應遲鈍

有時，如果在同一幀中執行動作並調整回應觸控的元件透明度或高亮效果，這些效果會等到 `onPress` 函數返回後才顯示。如果 `onPress` 觸發的 `setState` 導致大量工作並丟失幾幀，就可能發生這種情況。解決方法是將 `onPress` 處理程序內的動作包裹在 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導覽器轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「從右側推入」場景轉場為例：每一幀中，新場景從右側（假設初始 x 偏移為 320）移動到左側，最終停在 x 偏移 0 的位置。在此過程中，JavaScript 執行緒需每幀發送新的 x 偏移值給主執行緒。若 JavaScript 執行緒被鎖定，動畫就會卡頓。

解決方案之一是將基於 JavaScript 的動畫卸載到主執行緒。採用此方法時，我們可能在轉場開始時預先計算所有 x 偏移值並發送給主執行緒優化執行。釋放 JavaScript 執行緒後，即使渲染場景時丟失幾幀也無妨——流暢的轉場動畫會讓用戶忽略這些微小卡頓。

[React Navigation](navigation.md) 新庫的主要目標之一就是解決此問題。它使用原生元件和 [`Animated`](animated.md) 庫實現由原生執行緒執行的 60 FPS 動畫。