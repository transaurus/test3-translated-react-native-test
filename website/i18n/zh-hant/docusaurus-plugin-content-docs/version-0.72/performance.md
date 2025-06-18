---
id: performance
title: Performance Overview
---

相較於基於 WebView 的工具，使用 React Native 的一個重要原因是為了實現每秒 60 幀的流暢度以及應用程式的原生外觀和感覺。在可能的情況下，我們希望 React Native 能夠自動處理正確的事情，讓您專注於應用程式本身而非效能優化。然而，目前仍有一些領域我們尚未完全實現這一目標，還有一些情況下 React Native（類似於直接編寫原生代碼）無法自動為您確定最佳優化方式，因此需要手動介入。我們盡最大努力預設提供如絲般順滑的 UI 效能，但有時這並不可行。

本指南旨在教您一些基礎知識，幫助您[排查效能問題](profiling.md)，並討論[常見問題來源及其建議解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀的基礎知識

您的祖父母輩將電影稱為「活動影像」是有原因的：影片中逼真的動畫效果其實是一種錯覺，通過以固定速度快速切換靜態圖像來實現。我們將這些圖像中的每一張稱為「幀」。每秒顯示的幀數直接影響影片（或用戶界面）的流暢度和最終的逼真程度。iOS 設備每秒顯示 60 幀，這意味著您和 UI 系統大約有 16.67 毫秒的時間來完成生成該時間段內用戶將在螢幕上看到的靜態圖像（幀）所需的所有工作。如果您無法在分配的 16.67 毫秒內完成生成該幀所需的工作，則會「掉幀」，UI 將顯得無響應。

現在稍微複雜一點的是，在您的應用程式中打開[開發者菜單](debugging.md#accessing-the-dev-menu)並切換「顯示效能監視器」。您會注意到有兩個不同的幀率。

![](/docs/assets/PerfUtil.png)

### JS 幀率（JavaScript 線程）

對於大多數 React Native 應用程式，您的業務邏輯將在 JavaScript 線程上運行。這是您的 React 應用程式所在的地方，API 調用、觸摸事件處理等都在這裡進行... 對原生視圖的更新會在事件循環的每次迭代結束時批量發送到原生端，在幀截止時間之前（如果一切順利）。如果 JavaScript 線程在某一幀期間無響應，則會被視為掉幀。例如，如果您在一個複雜應用程式的根組件上調用 `this.setState`，並導致重新渲染計算量大的組件子樹，這可能會花費 200 毫秒並導致 12 幀丟失。在此期間，任何由 JavaScript 控制的動畫都會看起來像是凍結了。如果任何操作花費超過 100 毫秒，用戶就會感覺到卡頓。

這種情況經常發生在 `Navigator` 過渡期間：當您推送一個新路由時，JavaScript 線程需要渲染場景所需的所有組件，以便向原生端發送正確的命令來創建底層視圖。這裡的工作通常會花費幾幀的時間，並導致[卡頓](http://jankfree.org/)，因為過渡是由 JavaScript 線程控制的。有時組件會在 `componentDidMount` 中執行額外的工作，這可能導致過渡過程中出現第二次卡頓。

另一個例子是觸摸響應：如果您在 JavaScript 線程上跨多幀執行工作，您可能會注意到 `TouchableOpacity` 等組件的響應延遲。這是因為 JavaScript 線程繁忙，無法處理從主線程發送的原始觸摸事件。因此，`TouchableOpacity` 無法對觸摸事件做出反應並命令原生視圖調整其不透明度。

### UI 幀率（主線程）

許多人注意到 `NavigatorIOS` 的開箱即用性能優於 `Navigator`。原因是過渡的動畫完全在主線程上完成，因此不會受到 JavaScript 線程掉幀的影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運作於主執行緒上。滾動事件會被派發至 JS 執行緒，但滾動行為的執行無需等待這些事件被接收處理。

## 常見效能問題來源

### 處於開發模式（`dev=true`）

在開發模式下，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供完善的警告和錯誤訊息（例如驗證 propTypes 和其他斷言），運行時需執行更多工作。請務必在[正式版建置](running-on-device.md#building-your-app-for-production)中測試效能表現。

### 使用 `console.log` 語句

在執行打包後的應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包含來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，請務必在打包前移除它們。您也可以使用這個 [babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)自動移除所有 `console.*` 呼叫。需先透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

```json
{
  "env": {
    "production": {
      "plugins": ["transform-remove-console"]
    }
  }
}
```

這將在專案的正式（生產）版本中自動移除所有 `console.*` 呼叫。

即使專案中沒有直接使用 `console.*` 呼叫，仍建議使用此插件。第三方函式庫也可能會呼叫這些方法。

### `ListView` 初始渲染過慢或大型列表滾動效能不佳

請改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新列表元件還具備顯著的效能優化，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

若您的 [`FlatList`](flatlist.md) 渲染速度緩慢，請確認已實作 [`getItemLayout`](flatlist.md#getitemlayout) 以跳過渲染項目的測量步驟，從而優化渲染速度。

### 重新渲染幾乎未變化的視圖導致 JS FPS 驟降

若使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來大幅減少工作量。若採用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，可實作 `shouldComponentUpdate` 並明確指定觸發元件重新渲染的條件。若編寫純粹元件（渲染函式的返回值完全依賴於 props 和 state），可透過 PureComponent 自動實現此功能。再次強調，不可變資料結構能保持高效——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼量更少。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場動畫卡頓」是最常見的表徵，但其他情況也可能發生。使用 InteractionManager 是良好的解決方案，但若延遲動畫期間的工作會嚴重影響使用者體驗，則可考慮改用 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格（除非[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)），而 LayoutAnimation 基於 Core Animation 實現，不受 JS 執行緒和主執行緒掉幀影響。

我曾將此技術應用於模態視窗動畫（從頂部滑下並淡入半透明遮罩），同時初始化多個網路請求、渲染模態內容並更新開啟模態的原始視圖。詳見動畫指南以獲取更多 LayoutAnimation 的使用方法。

注意事項：

- LayoutAnimation 僅適用於「一發不可收」的動畫（「靜態」動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

當文字帶有透明背景並疊加在圖片上，或任何需要每幀重新繪製視圖的 alpha 合成情境時，此問題尤其明顯。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可大幅改善此情況。

注意避免過度使用這些屬性，否則記憶體用量可能暴增。使用時請務必分析效能和記憶體用量。若視圖不再需要移動，請關閉此屬性。

### 調整圖片尺寸動畫導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬高時，系統會從原始圖片重新裁剪和縮放。這對大圖尤其耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如點擊圖片放大至全螢幕的情境。

### 我的 TouchableX 元件反應遲鈍

若在調整觸控回應元件的透明度或高亮效果的同一幀中執行操作，可能直到 `onPress` 函數返回後才會看到效果。當 `onPress` 觸發的 `setState` 導致大量運算和掉幀時，此現象更明顯。解決方案是將 `onPress` 處理程序內的操作包裹在 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導航轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「從右側推入」場景轉場為例：每幀需將新場景從 x 偏移 320（螢幕外）移動至 x 偏移 0。若 JavaScript 執行緒被鎖定，無法發送新偏移值，就會導致動畫掉幀。

解決方案之一是將基於 JavaScript 的動畫卸載到主執行緒執行。例如預先計算轉場過程的所有 x 偏移值並提交給主執行緒優化執行。如此 JavaScript 執行緒即使掉幀也不影響動畫流暢度。

新推出的 [React Navigation](navigation.md) 庫正是為解決此問題而設計。它採用原生元件和 [`Animated`](animated.md) 庫，在主執行緒上實現 60 FPS 的流暢動畫。