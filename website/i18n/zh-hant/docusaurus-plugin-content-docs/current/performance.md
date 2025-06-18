---
id: performance
title: Performance Overview
---

選擇使用 React Native 而非基於 WebView 的工具，其關鍵原因在於能實現至少 60 FPS 的流暢度，並為應用程式提供原生般的操作體驗。我們盡可能讓 React Native 自動處理優化工作，使開發者能專注於應用邏輯而無需擔心效能問題。但某些領域我們尚未完全實現此目標，也有些情況 React Native（如同直接編寫原生代碼）無法自動為您選擇最佳優化方案。此時便需要手動介入調整。雖然我們預設追求絲滑般的 UI 效能，但偶爾仍會出現無法達標的狀況。

本指南旨在傳授基礎知識，協助您[排查效能問題](profiling.md)，並探討[常見問題根源與對應解決方案](performance.md#common-sources-of-performance-problems)。

## 關於影格的基本認知

老一輩人將電影稱為「活動影像」有其道理：影片中流暢的動態效果，實質上是透過快速切換靜態畫面所營造的視覺幻象。每一幅靜態畫面我們稱之為「影格」。每秒顯示的影格數（FPS）直接決定了影片（或使用者介面）的流暢度與真實感。iOS 和 Android 設備的顯示刷新率至少為 60 FPS，這意味著您與 UI 系統最多只有 16.67 毫秒的時間來完成生成單一影格所需的所有運算工作。若無法在該時間區間內完成影格渲染，就會發生「掉幀」現象，導致介面出現卡頓感。

補充說明：當您開啟應用程式的[開發者選單](debugging.md#opening-the-dev-menu)並啟用「顯示效能監測器」時，會注意到系統實際顯示兩種不同的幀率指標。

![效能監測器截圖](/docs/assets/PerfUtil.png)

### JS 影格率（JavaScript 執行緒）

在多數 React Native 應用中，業務邏輯主要運行於 JavaScript 執行緒。這裡是 React 應用核心所在——API 呼叫、觸控事件處理等操作均在此執行。對原生視圖的更新會批次處理，並在事件循環每次迭代結束時（若一切順利）趕在影格截止期限前發送至原生端。若 JavaScript 執行緒在某個影格週期內無響應，就會被視為掉幀。例如，當您對複雜應用的根組件設置新狀態，導致需要重新渲染計算密集的子組件樹時，可能耗費 200 毫秒並造成 12 影格丟失。此時所有由 JavaScript 控制的動畫都會出現凍結現象。若掉幀過於頻繁，使用者將明顯感受到卡頓。

以觸控響應為例：若 JavaScript 執行緒持續處於忙碌狀態，您可能會注意到 `TouchableOpacity` 等組件對觸控的反應出現延遲。這是因為 JavaScript 執行緒無法及時處理從主執行緒傳遞過來的原始觸控事件，導致 `TouchableOpacity` 無法即時調用原生視圖來調整透明度。

### UI 影格率（主執行緒）

You may have noticed that performance of native stack navigators (such as the [@react-navigation/native-stack](https://reactnavigation.org/docs/native-stack-navigator) provided by React Navigation) is better out of the box than JavaScript-based stack navigators. This is because the transition animations are executed on the native main UI thread, so they are not interrupted by frame drops on the JavaScript thread.

同理，當 JavaScript 執行緒阻塞時，您仍能流暢滾動 `ScrollView`——因為滾動操作實際發生在主執行緒。雖然滾動事件會傳遞至 JS 執行緒處理，但滾動行為本身並不依賴這些事件的接收。

## 常見效能問題根源

### 處於開發模式運行（dev=true）

在開發模式下運行時，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供良好的警告和錯誤訊息，運行時需要執行更多工作。請務必在[正式版本](running-on-device.md#building-your-app-for-production)中測試效能表現。

### 使用 `console.log` 語句

當運行打包後的應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包括來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，因此請確保在打包前移除它們。你也可以使用這個 [Babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。首先需透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

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

### `FlatList` 渲染過慢或大型列表滾動效能不佳

如果 [`FlatList`](flatlist.md) 渲染速度緩慢，請確保實作了 [`getItemLayout`](flatlist.md#getitemlayout) 以跳過渲染項目的測量步驟來優化速度。

還有其他針對效能優化的第三方列表函式庫，包括 [FlashList](https://github.com/shopify/flash-list) 和 [Legend List](https://github.com/legendapp/legend-list)。

### Dropping JS thread FPS because of doing a lot of work on the JavaScript thread at the same time

「導覽器轉場緩慢」是最常見的表現形式，但其他情況也可能發生。使用 [`InteractionManager`](interactionmanager.md) 是個不錯的解決方案，但如果延遲動畫期間的工作對使用者體驗影響過大，可以考慮改用 [`LayoutAnimation`](layoutanimation.md)。

目前 [`Animated API`](animated.md) 會在 JavaScript 執行緒上按需計算每個關鍵影格（除非[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)），而 [`LayoutAnimation`](layoutanimation.md) 則利用 Core Animation 實現，不受 JavaScript 執行緒和主執行緒掉幀影響。

一個適用場景是：當模態視窗以動畫形式呈現（從頂部滑下並淡入半透明遮罩）時，同時初始化多個網路請求、渲染模態內容並更新開啟模態的原始視圖。詳見[動畫指南](animations.md)瞭解如何使用 `LayoutAnimation`。

**注意事項：**

- `LayoutAnimation` 僅適用於「一次性動畫」（靜態動畫）——若需支援中斷操作，必須改用 [`Animated`](animated.md)。

### 移動畫面中的視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

在 Android 平台上尤其明顯：當文字帶有透明背景疊加在圖片上，或任何需要每幀重新合成 Alpha 通道的場景時。啟用 `renderToHardwareTextureAndroid` 屬性可顯著改善此問題。iOS 的 `shouldRasterizeIOS` 預設已啟用。

注意避免過度使用這些屬性，否則記憶體佔用可能暴增。使用時請監控效能和記憶體狀況。若視圖不再需要移動，請關閉此屬性。

### 圖片尺寸動畫導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 [`Image` 元件](image.md) 的寬度或高度時，都會從原始圖片重新裁剪和縮放。這對於大型圖片來說可能非常耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫效果。一個典型的使用場景是點擊圖片後將其放大至全螢幕。

### 我的 TouchableX 元件響應遲鈍

有時，如果我們在調整元件透明度或觸控高亮效果的同一幀中執行操作（該元件正在響應觸控事件），那麼在 `onPress` 函數執行完畢前，這些視覺效果不會立即顯現。當 `onPress` 觸發的狀態更新導致繁重的重新渲染並因此丟失幾幀時，就可能發生這種情況。解決方法是將 `onPress` 處理函數內的所有操作包裹在 `requestAnimationFrame` 中：

```tsx
function handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```