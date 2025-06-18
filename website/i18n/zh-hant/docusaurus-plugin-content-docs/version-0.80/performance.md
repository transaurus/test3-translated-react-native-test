---
id: performance
title: Performance Overview
---

相較於基於WebView的工具，使用React Native的一個重要原因是能達到至少每秒60幀的流暢度，並為應用程式提供原生外觀與操作體驗。在可行情況下，我們希望React Native能自動處理優化，讓開發者能專注於應用功能而無需擔心效能問題。然而，在某些領域我們尚未完全實現這個目標，也有些情況（如同直接編寫原生代碼時）React Native無法自動判斷最佳優化方案，此時就需要手動介入調整。我們預設會提供如絲般順滑的UI效能，但偶爾仍可能出現無法達標的狀況。

本指南旨在傳授基礎知識，協助您[排查效能問題](profiling.md)，並探討[常見問題根源與建議解決方案](performance.md#common-sources-of-performance-problems)。

## 關於影格的基本知識

老一輩人將電影稱為「活動影像」有其道理：影片中流暢的動態效果其實是透過快速切換靜態畫面產生的視覺幻象。我們稱這些靜態畫面為「影格」。每秒顯示的影格數（FPS）直接影響影片（或使用者介面）的流暢度與真實感。iOS和Android設備的顯示刷新率至少為60FPS，這意味著您與UI系統最多只有16.67毫秒的時間來完成生成單一影格所需的所有工作。若無法在此時間內完成渲染，就會發生「掉幀」，導致介面出現卡頓。

補充說明：開啟應用程式的[開發者選單](debugging.md#opening-the-dev-menu)並切換「顯示效能監測器」時，您會注意到系統顯示兩種不同的幀率。

![效能監測器截圖](/docs/assets/PerfUtil.png)

### JS幀率（JavaScript執行緒）

多數React Native應用的業務邏輯都在JavaScript執行緒上運行——這裡是React組件生命週期、API調用、觸控事件處理等核心邏輯的執行環境。對原生視圖的更新會在事件循環的每次迭代結束時批量傳送至原生端（理想情況下會在影格截止期限前完成）。若JavaScript執行緒在某個影格周期內無響應，就會被視為掉幀。例如當您在複雜應用的根組件設置新狀態，導致需要重新渲染計算密集的子組件樹時，可能耗時200毫秒並造成12幀丟失，此時所有由JavaScript控制的動畫都會出現凍結現象。若掉幀過多，使用者將明顯感受到卡頓。

以觸控響應為例：若JavaScript執行緒持續多幀處於忙碌狀態，您可能會注意到`TouchableOpacity`等組件對觸控的反應延遲。這是因為JavaScript執行緒無法及時處理從主執行緒傳送的原始觸控事件，導致`TouchableOpacity`無法即時通知原生視圖調整透明度。

### UI幀率（主執行緒）

您可能已觀察到，原生堆疊導航器（如React Navigation提供的[@react-navigation/native-stack](https://reactnavigation.org/docs/native-stack-navigator)）的預設效能優於JavaScript實現的導航方案。這是因為轉場動畫直接在原生主UI執行緒執行，不會受JavaScript執行緒掉幀影響。

同理，當JavaScript執行緒阻塞時，您仍能流暢滾動`ScrollView`——因為滾動容器本身運行在主執行緒。雖然滾動事件會被派發至JS執行緒處理，但滾動操作本身並不依賴這些事件的接收。

## 常見效能問題根源

### 處於開發模式（dev=true）

在開發模式下執行時，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供良好的警告和錯誤訊息，運行時需要進行更多工作。請務必在[正式版本](running-on-device.md#building-your-app-for-production)中測試效能。

### 使用 `console.log` 語句

在打包應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包括來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，因此請確保在打包前移除它們。你也可以使用這個 [Babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。首先需要安裝它：`npm i babel-plugin-transform-remove-console --save-dev`，然後編輯專案目錄下的 `.babelrc` 檔案如下：

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

即使專案中沒有使用 `console.*` 呼叫，也建議使用此插件。第三方函式庫也可能會呼叫它們。

### `FlatList` 渲染速度過慢或大型列表的滾動效能不佳

如果 [`FlatList`](flatlist.md) 渲染速度過慢，請確保已實作 [`getItemLayout`](flatlist.md#getitemlayout) 以跳過渲染項目的測量步驟來優化渲染速度。

還有其他針對效能優化的第三方列表函式庫，包括 [FlashList](https://github.com/shopify/flash-list) 和 [Legend List](https://github.com/legendapp/legend-list)。

### Dropping JS thread FPS because of doing a lot of work on the JavaScript thread at the same time

「導航轉場緩慢」是最常見的表現形式，但也可能在其他情況下發生。使用 [`InteractionManager`](interactionmanager.md) 是一個不錯的解決方案，但如果延遲動畫期間的工作對使用者體驗影響太大，則可以考慮使用 [`LayoutAnimation`](layoutanimation.md)。

目前 [`Animated API`](animated.md) 會在 JavaScript 執行緒上按需計算每個關鍵影格，除非你[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)，而 [`LayoutAnimation`](layoutanimation.md) 則利用 Core Animation，不受 JS 執行緒和主執行緒掉幀的影響。

一個使用場景是在顯示模態視窗（從頂部滑下並淡入半透明遮罩）的同時，初始化並可能接收多個網路請求的回應、渲染模態視窗的內容，以及更新開啟模態視窗的原始視圖。有關如何使用 `LayoutAnimation` 的更多資訊，請參閱[動畫指南](animations.md)。

**注意事項：**

- `LayoutAnimation` 僅適用於一次性動畫（「靜態」動畫）——如果需要中斷動畫，則需要使用 [`Animated`](animated.md)。

### 移動畫面中的視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況在 Android 上尤其明顯，當文字具有透明背景並位於圖片上方，或任何需要在每一幀重新繪製視圖的 alpha 合成情況下。你會發現啟用 `renderToHardwareTextureAndroid` 可以顯著改善此問題。在 iOS 上，`shouldRasterizeIOS` 預設已啟用。

請注意不要過度使用此功能，否則記憶體使用量可能會暴增。在使用這些屬性時，請分析效能和記憶體使用情況。如果不再移動視圖，請關閉此屬性。

### 動畫調整圖片大小導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 [`Image` 元件](image.md) 的寬度或高度時，都會從原始圖片重新裁剪和縮放。這對於大型圖片來說可能非常耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。一個典型的使用場景是點擊圖片後將其放大至全螢幕。

### 我的 TouchableX 元件響應遲鈍

有時，如果我們在調整觸控回應元件的透明度或高亮效果的同一幀中執行操作，這些視覺效果會等到 `onPress` 函數執行完畢後才會顯現。這種情況可能發生在 `onPress` 觸發了導致嚴重重新渲染的狀態更新，進而導致掉幀時。解決方法是將 `onPress` 處理程序內的所有操作包裹在 `requestAnimationFrame` 中：

```tsx
function handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```