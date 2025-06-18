---
id: performance
title: Performance Overview
---

相較於基於WebView的工具，使用React Native的一個強力理由在於能實現至少60幀/秒的流暢度，並為應用程式提供原生外觀與操作體驗。我們盡可能讓React Native自動處理優化，使開發者能專注於應用邏輯而無需擔心效能問題。但某些領域我們尚未完全實現此目標，另一些情況則類似直接編寫原生代碼——React Native無法自動判斷最佳優化方式，此時需手動介入。我們默認追求絲滑般的UI性能，但偶爾仍可能無法達成。

本指南旨在傳授基礎知識，協助您[診斷效能問題](profiling.md)，並探討[常見問題根源與對應解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀率的必備知識

老一輩將電影稱為「活動影像」有其道理：影片中流暢的動態效果，實質是靜態畫面以固定速率切換造成的視覺幻覺。每一幅靜態畫面我們稱為「幀」。每秒顯示的幀數直接影響影片（或使用者介面）的流暢度與真實感。iOS裝置的畫面更新率至少為60幀/秒，這意味著您與UI系統最多只有16.67毫秒來完成生成該幀所需的所有工作。若無法在此時間內完成，就會「掉幀」，導致介面出現卡頓。

需特別說明的是：當您在應用中開啟[開發者選單](debugging.md#opening-the-dev-menu)並切換「顯示效能監測器」時，會注意到系統實際顯示兩種不同的幀率。

![](/docs/assets/PerfUtil.png)

### JavaScript幀率（JavaScript執行緒）

多數React Native應用的業務邏輯運行於JavaScript執行緒。這裡是React組件生命週期、API調用、觸控事件處理等邏輯的執行環境。對原生視圖的更新會批次處理，並在事件循環每次迭代結束時（理想情況下於幀截止時間前）發送至原生端。若JavaScript執行緒在某幀期間無響應，該幀即視為掉幀。例如：當您在複雜應用的根組件調用`this.setState`，導致需要重新渲染計算密集的子組件樹時，可能耗費200毫秒並造成12幀丟失——此時所有由JavaScript控制的動畫都將出現凍結現象。任何超過100毫秒的延遲都會被使用者明顯感知。

此現象常見於`Navigator`轉場過程：當推送新路由時，JavaScript執行緒需渲染場景所需的所有組件，才能向原生端發送正確指令來創建底層視圖。此過程常耗費數幀時間，由於轉場動畫由JavaScript執行緒控制，會導致[界面卡頓](https://jankfree.org/)。有時組件在`componentDidMount`中執行額外工作，可能造成轉場過程出現二次停頓。

另一典型案例是觸控響應：若JavaScript執行緒持續多幀處於忙碌狀態，您可能會注意到`TouchableOpacity`等組件的響應延遲。這是因為JavaScript執行緒無法及時處理從主執行緒傳遞的原始觸控事件，導致`TouchableOpacity`無法根據觸控事件通知原生視圖調整透明度。

### UI幀率（主執行緒）

許多開發者注意到`NavigatorIOS`的默認性能優於`Navigator`，關鍵原因在於其轉場動畫完全在主執行緒執行，因此不會受JavaScript執行緒掉幀影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運作於主執行緒上。滾動事件會被派發至 JS 執行緒，但滾動行為的執行並不依賴這些事件的接收。

## 常見效能問題來源

### 處於開發模式 (`dev=true`)

在開發模式下執行時，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供完善的警告和錯誤訊息，運行時需執行更多工作。請務必在[正式版建置](running-on-device.md#building-your-app-for-production)中測試效能表現。

### 使用 `console.log` 語句

在執行打包後的應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包含來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，請務必在打包前移除它們。您也可以使用這個 [babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。需先透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

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

即使專案中未使用 `console.*` 呼叫，仍建議使用此插件。第三方函式庫也可能會呼叫這些方法。

### `ListView` 初始渲染過慢或大型列表滾動效能不佳

請改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新列表元件還具備顯著的效能優化，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

若您的 [`FlatList`](flatlist.md) 渲染速度緩慢，請確認已實作 [`getItemLayout`](flatlist.md#getitemlayout)，透過跳過渲染項目的測量來優化渲染速度。

### 重新渲染幾乎無變化的視圖時 JS FPS 驟降

若使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來大幅減少工作量。若使用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，您可以實作 `shouldComponentUpdate` 並明確指定觸發元件重新渲染的條件。若編寫純元件（渲染函式的返回值完全依賴於 props 和 state），可利用 PureComponent 自動處理。再次強調，不可變資料結構有助於保持高效——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼量更少。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場動畫卡頓」是最常見的表現形式，但其他情況也可能發生。使用 InteractionManager 是可行的解決方案，但若延遲動畫期間的工作會嚴重影響使用者體驗，則可考慮改用 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格，除非您[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)，而 LayoutAnimation 則利用 Core Animation 實現，不受 JS 執行緒和主執行緒掉幀影響。

我曾將此技術應用於模態視窗動畫（從頂部滑下並淡入半透明遮罩），同時初始化並可能接收多個網路請求的回應、渲染模態內容，以及更新開啟模態的原始視圖。詳見動畫指南以獲取更多關於 LayoutAnimation 的使用資訊。

注意事項：

- LayoutAnimation 僅適用於「一發不可收拾」的動畫（「靜態」動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況尤其發生在文字帶有透明背景並疊加在圖片上方時，或任何需要每幀重新繪製視圖的 alpha 合成場景。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可顯著改善此問題。

但需謹慎使用，否則記憶體用量可能暴增。使用這些屬性時，請務必分析效能和記憶體用量。若視圖不再需要移動，請關閉此屬性。

### 調整圖片大小動畫導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬度或高度時，系統會從原始圖片重新裁剪和縮放。這對大圖尤其耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如點擊圖片放大至全螢幕的場景。

### TouchableX 元件響應遲鈍

有時若在調整觸控元件透明度或高亮效果的同一幀中執行動作，這些視覺變化會等到 `onPress` 函數執行完畢後才顯現。如果 `onPress` 觸發的 `setState` 導致大量運算和掉幀，就會發生此狀況。解決方法是將 `onPress` 處理程序內的動作包裹在 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導航器轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「從右側推入」場景轉場為例：每一幀中，新場景從右側（假設初始 x 偏移為 320）移動至左側（最終 x 偏移為 0）。若 JavaScript 執行緒被阻塞，就無法發送新的 x 偏移值給主執行緒，導致動畫掉幀。

解決方案之一是將 JavaScript 動畫卸載到主執行緒。例如在轉場開始時預先計算所有 x 偏移值並交由主執行緒優化執行。如此 JavaScript 執行緒即使掉幀也不影響動畫流暢度，用戶甚至不會察覺渲染延遲。

新推出的 [React Navigation](navigation.md) 庫正是為解決此問題而設計。它採用原生元件和 [`Animated`](animated.md) 庫實現至少 60 FPS 的原生執行緒動畫。