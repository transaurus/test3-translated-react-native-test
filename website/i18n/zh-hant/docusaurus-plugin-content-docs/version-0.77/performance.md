---
id: performance
title: Performance Overview
---

選擇使用 React Native 而非基於 WebView 的工具，一個關鍵原因在於能實現至少每秒 60 幀的流暢度，並為應用提供原生外觀與操作體驗。我們原則上會讓 React Native 自動處理效能優化，讓開發者能專注於應用邏輯。但在某些領域，我們尚未完全實現這個目標，也有些情況（如同直接編寫原生代碼時）React Native 無法自動判斷最佳優化方式，此時便需要手動介入。雖然我們預設會提供絲滑般的 UI 效能，但仍有無法達成的例外情況。

本指南旨在傳授基礎知識，協助您[排查效能問題](profiling.md)，並探討[常見問題根源與對應解決方案](performance.md#common-sources-of-performance-problems)。

## 關於影格的基本認知

老一輩將電影稱為「活動影像」有其道理：影片中流暢的動態效果，其實是透過快速切換靜態畫面產生的視覺幻覺。每個靜態畫面我們稱為「影格」。每秒顯示的影格數（FPS）直接影響影片（或使用者介面）的流暢度與真實感。iOS 裝置的畫面更新率至少為 60 FPS，這意味著您與 UI 系統最多只有 16.67 毫秒的時間來完成生成單一影格所需的所有運算。若無法在時限內完成，就會發生「掉幀」，導致介面出現卡頓。

補充說明：當您在應用中開啟[開發者選單](debugging.md#opening-the-dev-menu)並啟用「顯示效能監測器」時，會注意到畫面上其實顯示兩種不同的幀率。

![](/docs/assets/PerfUtil.png)

### JavaScript 執行緒幀率

多數 React Native 應用的商業邏輯都在 JavaScript 執行緒上運行——這裡是 React 組件生命週期、API 呼叫、觸控事件處理等邏輯的執行環境。對原生視圖的更新會批次處理，並在事件循環的每個迭代週期結束時（理想情況下趕在影格截止期限前）發送到原生端。若 JavaScript 執行緒在某個影格週期內無回應，就會被視為掉幀。例如當您在複雜應用的根組件呼叫 `this.setState`，觸發計算密集型子樹重新渲染時，可能導致 200 毫秒的阻塞與 12 次掉幀，此時所有由 JavaScript 控制的動畫都會出現凍結。任何超過 100 毫秒的延遲都會被使用者明顯感知。

這種情況常見於 `Navigator` 轉場動畫：當推送新路由時，JavaScript 執行緒需渲染場景所需的所有組件，才能向原生端發送建立對應視圖的指令。這個過程常會耗費數個影格時間，導致[卡頓現象](https://jankfree.org/)，因為轉場動畫是由 JavaScript 執行緒控制的。有時組件在 `componentDidMount` 中執行額外工作，可能造成轉場過程中再次卡頓。

觸控響應是另一個例子：若 JavaScript 執行緒持續進行跨越多個影格的運算，您可能會注意到 `TouchableOpacity` 等元件出現響應延遲。這是因為 JavaScript 執行緒忙碌無法處理從主執行緒傳來的原始觸控事件，導致 `TouchableOpacity` 無法即時調控原生視圖的透明度變化。

### 主執行緒幀率（UI 執行緒）

許多人注意到 `NavigatorIOS` 的預設效能優於 `Navigator`，關鍵原因在於其轉場動畫完全在主執行緒執行，因此不會受 JavaScript 執行緒掉幀影響。

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運作於主執行緒上。滾動事件會被派發至 JS 執行緒，但滾動行為的發生並不依賴這些事件的接收。

## 常見效能問題來源

### 處於開發模式 (`dev=true`)

在開發模式下，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供完善的警告與錯誤訊息，運行時需執行更多工作。請務必在[正式版建置](running-on-device.md#building-your-app-for-production)中測試效能表現。

### 使用 `console.log` 語句

在執行打包後的應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包含來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，因此請確保在打包前移除它們。您也可以使用這個 [babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。需先透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

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

若您的 [`FlatList`](flatlist.md) 渲染緩慢，請確認已實作 [`getItemLayout`](flatlist.md#getitemlayout)，透過跳過渲染項目的測量來優化渲染速度。

### 重新渲染幾乎未變化的視圖時 JS FPS 驟降

若使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來大幅減少工作量。若使用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，您可以實作 `shouldComponentUpdate` 並明確指定觸發元件重新渲染的條件。若編寫純元件（渲染函式的返回值完全依賴於 props 和 state），可透過 PureComponent 自動實現此功能。再次強調，不可變資料結構有助於保持高效——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼量更少。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場動畫卡頓」是最常見的表現形式，但其他情況也可能發生。使用 InteractionManager 是可行的解決方案，但若延遲動畫期間的工作會嚴重影響使用者體驗，則可考慮改用 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格（除非您[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)），而 LayoutAnimation 則利用 Core Animation 實現，不受 JS 執行緒和主執行緒掉幀影響。

我曾將此技術應用於模態視窗的動畫（從頂部滑下並淡入半透明遮罩），同時初始化多個網路請求、渲染模態內容並更新開啟模態的原始視圖。詳見動畫指南以獲取更多關於 LayoutAnimation 的使用方法。

注意事項：

- LayoutAnimation 僅適用於「一發不可收拾」的動畫（「靜態」動畫）——若需中斷動畫，則必須使用 `Animated`。

### 移動畫面中的視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

當文字具有透明背景且疊加在圖片上方時尤為明顯，或任何需要每幀重新繪製視圖的 alpha 合成情境。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可大幅改善此問題。

需謹慎使用以避免記憶體暴增。啟用這些屬性時，請務必分析效能與記憶體使用狀況。若視圖不再移動，應關閉此屬性。

### 調整圖片尺寸動畫導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬高時，系統會從原始圖片重新裁剪縮放。對於大圖而言效能消耗極大。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如點擊圖片放大至全螢幕的情境。

### TouchableX 元件觸控反應遲鈍

若在觸發 `onPress` 的同一幀中調整元件透明度或高亮效果，這些視覺變化會等到 `onPress` 函數執行完畢後才顯現。當 `onPress` 內的 `setState` 觸發大量運算導致掉幀時，可將動作包裹在 `requestAnimationFrame` 中解決：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導航轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「右側推入」場景轉場為例：每一幀需將新場景從 x 軸偏移 320（螢幕外）移動至 0 的位置。若 JavaScript 執行緒阻塞無法傳送新偏移值，就會導致動畫斷續。

解決方案之一是將 JavaScript 動畫卸載至主執行緒。例如預先計算所有轉場階段的 x 軸偏移序列，交由主執行緒優化執行。如此 JavaScript 執行緒即使掉幀也不影響動畫流暢度。

[React Navigation](navigation.md) 新庫的核心目標正是解決此問題。其採用原生元件與 [`Animated`](animated.md) 庫實現 60 FPS 的原生執行緒動畫。