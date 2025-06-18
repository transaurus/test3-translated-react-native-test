---
id: performance
title: Performance Overview
---

選擇使用 React Native 而非基於 WebView 的工具，一個關鍵原因在於它能實現至少每秒 60 幀的流暢度，並為應用提供原生外觀與操作體驗。在可行情況下，我們希望 React Native 能自動處理優化工作，讓開發者能專注於應用功能而無需擔心效能問題。然而，目前仍有部分領域尚未達到此理想狀態，也有些情況（如同直接編寫原生代碼時）React Native 無法自動判斷最佳優化方案。此時便需要手動介入調整。我們致力於預設提供如絲般順滑的 UI 效能，但某些情況下可能無法完全實現。

本指南旨在傳授基礎知識，協助您[排查效能問題](profiling.md)，並探討[常見問題根源及其解決方案](performance.md#common-sources-of-performance-problems)。

## 關於幀率的必備知識

老一輩人將電影稱為「活動影像」有其道理：影片中流暢的動態效果實為靜態畫面以固定速度快速切換所營造的視覺幻象。我們稱這些靜態畫面為「幀」。每秒顯示的幀數直接影響影片（或使用者介面）的流暢度與真實感。iOS 裝置的畫面更新率至少為 60 FPS，這意味著您與 UI 系統最多只有 16.67 毫秒來完成生成單一靜態畫面（幀）所需的所有工作。若無法在該時間區間內完成幀生成，就會發生「掉幀」，導致介面出現卡頓。

為進一步說明，請在應用中開啟[開發者選單](debugging.md#opening-the-dev-menu)並切換「顯示效能監控器」。您會注意到畫面上顯示兩種不同的幀率。

![](/docs/assets/PerfUtil.png)

### JS 幀率（JavaScript 執行緒）

多數 React Native 應用的業務邏輯都運行於 JavaScript 執行緒。這裡是 React 應用的核心運作環境：API 呼叫、觸控事件處理等操作都在此執行。對原生視圖的更新會批次處理，並在事件循環的每次迭代結束時（理想情況下於幀截止時間前）發送至原生端。若 JavaScript 執行緒在某一幀期間無響應，該幀即被視為掉幀。例如，當您在複雜應用的根元件呼叫 `this.setState`，導致需要重新渲染計算密集的子元件樹時，可能耗時 200 毫秒並造成 12 幀丟失。此期間所有由 JavaScript 控制的動畫都將出現凍結現象。任何操作若耗時超過 100 毫秒，使用者都會明顯感知卡頓。

這種情況常見於 `Navigator` 轉場動畫期間：當推送新路由時，JavaScript 執行緒需渲染場景所需的所有元件，才能向原生端發送正確指令來建立底層視圖。此過程的工作常會耗費數幀時間，導致[卡頓](https://jankfree.org/)（因轉場由 JavaScript 執行緒控制）。有時元件在 `componentDidMount` 中執行額外工作，可能造成轉場過程中二次卡頓。

另一典型案例是觸控響應：若 JavaScript 執行緒持續處理跨越多幀的任務，您可能會注意到 `TouchableOpacity` 等元件對觸控的反應延遲。這是因為 JavaScript 執行緒繁忙，無法及時處理從主執行緒傳來的原始觸控事件。因此 `TouchableOpacity` 無法響應觸控事件並通知原生視圖調整透明度。

### UI 幀率（主執行緒）

許多開發者注意到 `NavigatorIOS` 的預設效能優於 `Navigator`，關鍵原因在於其轉場動畫完全在主執行緒執行，因此不會受 JavaScript 執行緒掉幀影響。

Similarly, you can happily scroll up and down through a `ScrollView` when the JavaScript thread is locked up because the `ScrollView` lives on the main thread. The scroll events are dispatched to the JS thread, but their receipt is not necessary for the scroll to occur.

## Common sources of performance problems

### Running in development mode (`dev=true`)

JavaScript thread performance suffers greatly when running in dev mode. This is unavoidable: a lot more work needs to be done at runtime to provide you with good warnings and error messages. Always make sure to test performance in [release builds](running-on-device.md#building-your-app-for-production).

### Using `console.log` statements

When running a bundled app, these statements can cause a big bottleneck in the JavaScript thread. This includes calls from debugging libraries such as [redux-logger](https://github.com/evgenyrodionov/redux-logger), so make sure to remove them before bundling. You can also use this [babel plugin](https://babeljs.io/docs/plugins/transform-remove-console/) that removes all the `console.*` calls. You need to install it first with `npm i babel-plugin-transform-remove-console --save-dev`, and then edit the `.babelrc` file under your project directory like this:

```json
{
  "env": {
    "production": {
      "plugins": ["transform-remove-console"]
    }
  }
}
```

This will automatically remove all `console.*` calls in the release (production) versions of your project.

It is recommended to use the plugin even if no `console.*` calls are made in your project. A third party library could also call them.

### `ListView` initial rendering is too slow or scroll performance is bad for large lists

Use the new [`FlatList`](flatlist.md) or [`SectionList`](sectionlist.md) component instead. Besides simplifying the API, the new list components also have significant performance enhancements, the main one being nearly constant memory usage for any number of rows.

If your [`FlatList`](flatlist.md) is rendering slow, be sure that you've implemented [`getItemLayout`](flatlist.md#getitemlayout) to optimize rendering speed by skipping measurement of the rendered items.

### JS FPS plunges when re-rendering a view that hardly changes

If you are using a ListView, you must provide a `rowHasChanged` function that can reduce a lot of work by quickly determining whether or not a row needs to be re-rendered. If you are using immutable data structures, this would only need to be a reference equality check.

Similarly, you can implement `shouldComponentUpdate` and indicate the exact conditions under which you would like the component to re-render. If you write pure components (where the return value of the render function is entirely dependent on props and state), you can leverage PureComponent to do this for you. Once again, immutable data structures are useful to keep this fast -- if you have to do a deep comparison of a large list of objects, it may be that re-rendering your entire component would be quicker, and it would certainly require less code.

### Dropping JS thread FPS because of doing a lot of work on the JavaScript thread at the same time

"Slow Navigator transitions" is the most common manifestation of this, but there are other times this can happen. Using InteractionManager can be a good approach, but if the user experience cost is too high to delay work during an animation, then you might want to consider LayoutAnimation.

The Animated API currently calculates each keyframe on-demand on the JavaScript thread unless you [set `useNativeDriver: true`](/blog/2017/02/14/using-native-driver-for-animated#how-do-i-use-this-in-my-app), while LayoutAnimation leverages Core Animation and is unaffected by JS thread and main thread frame drops.

One case where I have used this is for animating in a modal (sliding down from top and fading in a translucent overlay) while initializing and perhaps receiving responses for several network requests, rendering the contents of the modal, and updating the view where the modal was opened from. See the Animations guide for more information about how to use LayoutAnimation.

Caveats:

- LayoutAnimation 僅適用於一次性觸發的動畫（「靜態」動畫）——如果需要中斷動畫，則必須使用 `Animated`。

### 在螢幕上移動視圖（滾動、平移、旋轉）導致 UI 執行緒 FPS 下降

這種情況尤其發生在文字帶有透明背景並疊加在圖片上方時，或任何其他需要每幀重新繪製視圖的 alpha 合成場景。啟用 `shouldRasterizeIOS` 或 `renderToHardwareTextureAndroid` 屬性可以顯著改善此問題。

注意不要過度使用這些屬性，否則記憶體用量可能會暴增。使用這些屬性時，請務必分析效能和記憶體使用情況。如果不再需要移動視圖，請關閉這些屬性。

### 動畫調整圖片大小導致 UI 執行緒 FPS 下降

在 iOS 上，每次調整 Image 元件的寬度或高度時，系統會從原始圖片重新裁剪和縮放。這對大型圖片尤其耗費資源。建議改用 `transform: [{scale}]` 樣式屬性來實現尺寸動畫。例如，點擊圖片後將其放大至全螢幕的場景。

### 我的 TouchableX 視圖反應遲鈍

有時，如果在同一幀中執行動作並同時調整回應觸控的元件透明度或高亮效果，這些效果可能要到 `onPress` 函數執行完畢後才會顯示。如果 `onPress` 中的 `setState` 導致大量工作並丟失幾幀，就可能發生這種情況。解決方法是將 `onPress` 處理程序中的任何動作包裹在 `requestAnimationFrame` 中：

```tsx
handleOnPress() {
  requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

### 導航器過場動畫緩慢

如前所述，`Navigator` 的動畫由 JavaScript 執行緒控制。以「從右側推入」的場景過場為例：每一幀中，新場景從右側（假設初始 x 偏移為 320）向左移動，最終停在 x 偏移為 0 的位置。在此過場的每一幀，JavaScript 執行緒都需要向主執行緒發送新的 x 偏移值。如果 JavaScript 執行緒被鎖定，就無法完成此操作，導致該幀沒有更新，動畫就會卡頓。

解決方法之一是將基於 JavaScript 的動畫卸載到主執行緒。沿用上述例子，我們可以在開始過場時預先計算新場景的所有 x 偏移值，並將其發送到主執行緒以優化方式執行。這樣釋放了 JavaScript 執行緒的負擔，即使在渲染場景時丟失幾幀也不會有明顯影響——你可能會被流暢的過場動畫吸引而忽略這些問題。

解決此問題是新版 [React Navigation](navigation.md) 庫的主要目標之一。React Navigation 中的視圖使用原生元件和 [`Animated`](animated.md) 庫，以至少 60 FPS 的流暢度在原生執行緒上執行動畫。