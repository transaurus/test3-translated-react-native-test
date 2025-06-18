---
id: performance
title: Performance Overview
---

A compelling reason to use React Native instead of WebView-based tools is to achieve 60 frames per second and provide a native look and feel to your apps. Whenever feasible, we aim for React Native to handle optimizations automatically, allowing you to focus on your app without worrying about performance. However, there are certain areas where we haven't quite reached that level yet, and others where React Native (similar to writing native code directly) cannot determine the best optimization approach for you. In such cases, manual intervention becomes necessary. We strive to deliver buttery-smooth UI performance by default, but there may be instances where that isn't possible.

This guide is intended to teach you some basics to help you to [troubleshoot performance issues](profiling.md), as well as discuss [common sources of problems and their suggested solutions](performance.md#common-sources-of-performance-problems).

## What you need to know about frames

Your grandparents' generation called movies ["moving pictures"](https://www.youtube.com/watch?v=F1i40rnpOsA) for a reason: realistic motion in video is an illusion created by quickly changing static images at a consistent speed. We refer to each of these images as frames. The number of frames that is displayed each second has a direct impact on how smooth and ultimately life-like a video (or user interface) seems to be. iOS devices display 60 frames per second, which gives you and the UI system about 16.67ms to do all of the work needed to generate the static image (frame) that the user will see on the screen for that interval. If you are unable to do the work necessary to generate that frame within the allotted 16.67ms, then you will "drop a frame" and the UI will appear unresponsive.

Now to confuse the matter a little bit, open up the [Dev Menu](debugging.md#accessing-the-dev-menu) in your app and toggle `Show Perf Monitor`. You will notice that there are two different frame rates.

![](/docs/assets/PerfUtil.png)

### JS frame rate (JavaScript thread)

For most React Native applications, your business logic will run on the JavaScript thread. This is where your React application lives, API calls are made, touch events are processed, etc... Updates to native-backed views are batched and sent over to the native side at the end of each iteration of the event loop, before the frame deadline (if all goes well). If the JavaScript thread is unresponsive for a frame, it will be considered a dropped frame. For example, if you were to call `this.setState` on the root component of a complex application and it resulted in re-rendering computationally expensive component subtrees, it's conceivable that this might take 200ms and result in 12 frames being dropped. Any animations controlled by JavaScript would appear to freeze during that time. If anything takes longer than 100ms, the user will feel it.

This often happens during `Navigator` transitions: when you push a new route, the JavaScript thread needs to render all of the components necessary for the scene in order to send over the proper commands to the native side to create the backing views. It's common for the work being done here to take a few frames and cause [jank](https://jankfree.org/) because the transition is controlled by the JavaScript thread. Sometimes components will do additional work on `componentDidMount`, which might result in a second stutter in the transition.

Another example is responding to touches: if you are doing work across multiple frames on the JavaScript thread, you might notice a delay in responding to `TouchableOpacity`, for example. This is because the JavaScript thread is busy and cannot process the raw touch events sent over from the main thread. As a result, `TouchableOpacity` cannot react to the touch events and command the native view to adjust its opacity.

### UI frame rate (main thread)

Many people have noticed that performance of `NavigatorIOS` is better out of the box than `Navigator`. The reason for this is that the animations for the transitions are done entirely on the main thread, and so they are not interrupted by frame drops on the JavaScript thread.

同樣地，當 JavaScript 執行緒被鎖定時，您仍能順暢地在 `ScrollView` 中上下滾動，因為 `ScrollView` 運作於主執行緒上。滾動事件會被派發至 JS 執行緒，但滾動行為的執行並不依賴這些事件的接收。

## 常見效能問題來源

### 處於開發模式 (`dev=true`)

在開發模式下，JavaScript 執行緒的效能會大幅下降。這是不可避免的：為了提供完善的警告和錯誤訊息（例如驗證 propTypes 和其他斷言），運行時需執行更多工作。請務必在[正式版建置](running-on-device.md#building-your-app-for-production)中測試效能。

### 使用 `console.log` 語句

在執行打包後的應用程式時，這些語句會嚴重阻塞 JavaScript 執行緒。這包括來自除錯函式庫（如 [redux-logger](https://github.com/evgenyrodionov/redux-logger)）的呼叫，請務必在打包前移除它們。您也可以使用這個 [Babel 插件](https://babeljs.io/docs/plugins/transform-remove-console/)來移除所有 `console.*` 呼叫。需先透過 `npm i babel-plugin-transform-remove-console --save-dev` 安裝，然後編輯專案目錄下的 `.babelrc` 檔案如下：

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

請改用新的 [`FlatList`](flatlist.md) 或 [`SectionList`](sectionlist.md) 元件。除了簡化 API 外，新列表元件還具有顯著的效能優化，主要特點是無論行數多少，記憶體使用量幾乎保持恆定。

若您的 [`FlatList`](flatlist.md) 渲染速度慢，請確認已實作 [`getItemLayout`](flatlist.md#getitemlayout)，透過跳過渲染項目的測量來優化渲染速度。

### 重新渲染幾乎未變化的視圖時 JS FPS 驟降

若使用 ListView，必須提供 `rowHasChanged` 函式，透過快速判斷行是否需要重新渲染來大幅減少工作量。若使用不可變資料結構，僅需進行參考相等性檢查即可。

同樣地，可實作 `shouldComponentUpdate` 並明確指定觸發元件重新渲染的條件。若編寫純元件（渲染函式的返回值完全依賴於 props 和 state），可利用 PureComponent 自動處理。再次強調，不可變資料結構有助於保持高效——若需對大型物件列表進行深度比較，直接重新渲染整個元件可能更快，且程式碼量更少。

### 因 JavaScript 執行緒同時處理大量工作導致 JS FPS 下降

「導覽器轉場過慢」是最常見的表現形式，但其他情況也可能發生。使用 InteractionManager 是良好的解決方案，但若延遲動畫期間的工作會嚴重影響使用者體驗，則可考慮改用 LayoutAnimation。

目前 Animated API 會在 JavaScript 執行緒上按需計算每個關鍵影格（除非[設定 `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app)），而 LayoutAnimation 則利用 Core Animation，不受 JS 執行緒和主執行緒掉幀影響。

我曾將此技術用於模態視窗的動畫（從頂部滑下並淡入半透明遮罩），同時初始化並可能接收多個網路請求的回應、渲染模態內容，以及更新開啟模態的原始視圖。詳見動畫指南以獲取更多關於 LayoutAnimation 的使用資訊。

注意事項：

- LayoutAnimation 僅適用於「一次性觸發即忘」的動畫（「靜態」動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況尤其發生在文字具有透明背景並疊加在圖片上時，或任何其他需要每幀重新繪製視圖的 alpha 合成場景。您會發現啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 能顯著改善此問題。

請謹慎使用這些屬性，否則記憶體用量可能暴增。在使用這些屬性時，請務必分析效能和記憶體用量。如果不再需要移動視圖，請關閉此屬性。

### 動畫調整圖片大小導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬度或高度時，系統會從原始圖片重新裁剪和縮放。這對大型圖片尤其耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如點擊圖片放大至全螢幕的場景就適合此做法。

### 我的 TouchableX 視圖反應遲鈍

有時，如果我們在調整回應觸控的元件透明度或高亮效果的同一幀中執行操作，這些視覺變化會等到 `onPress` 函數返回後才會顯現。若 `onPress` 觸發的 `setState` 導致大量運算和數幀丟失，就可能發生此狀況。解決方法是將 `onPress` 處理程序內的所有操作包裹在 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導航器轉場動畫卡頓

如前所述，`Navigator` 動畫由 JavaScript 執行緒控制。以「從右側推入」場景轉場為例：每一幀中，新場景從右側（假設初始 x 偏移為 320）向左移動，最終停在 x 偏移為 0 的位置。在此轉場期間，JavaScript 執行緒需每幀向主執行緒發送新的 x 偏移值。若 JavaScript 執行緒被阻塞，動畫就會因無法更新而出現卡頓。

解決方案之一是將基於 JavaScript 的動畫卸載到主執行緒。沿用上述例子，我們可以在轉場開始時預先計算新場景的所有 x 偏移值，並將其發送給主執行緒優化執行。如此釋放 JavaScript 執行緒後，即便它在渲染場景時丟失幾幀也無妨——因為流暢的轉場動畫會讓用戶忽略這些微小卡頓。

新推出的 [React Navigation](navigation.md) 庫主要目標就是解決此問題。它採用原生元件和 [`Animated`](animated.md) 庫來實現原生執行緒運行的 60 FPS 動畫效果。