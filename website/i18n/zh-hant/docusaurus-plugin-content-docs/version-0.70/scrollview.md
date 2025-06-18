---
id: scrollview
title: ScrollView
---

一個封裝平台原生 ScrollView 的組件，同時提供與觸控鎖定「響應者」系統的整合。

請注意，ScrollView 必須具有固定高度才能正常工作，因為它們將無限高度的子元素裝入有限容器（透過滾動互動）。要限制 ScrollView 的高度，可以直接設置視圖高度（不推薦）或確保所有父視圖都具有固定高度。忘記在視圖堆疊中向下傳遞 `{flex: 1}` 可能會導致錯誤，元素檢查器能快速調試此類問題。

目前尚不支援其他內含的響應者阻止此滾動視圖成為主響應者。

`<ScrollView>` 與 [`<FlatList>`](flatlist.md) 該如何選擇？

`ScrollView` 會一次性渲染所有子組件，但這會帶來性能上的缺點。

假設您需要顯示一個非常長的項目列表，可能涵蓋多個屏幕的內容。一次性為所有內容創建 JS 組件和原生視圖（其中許多可能根本不會顯示），會導致渲染速度變慢和記憶體使用量增加。

這時 `FlatList` 就派上用場了。`FlatList` 會惰性渲染項目，僅在它們即將出現時才渲染，並移除滾動到屏幕外的項目以節省記憶體和處理時間。

如果您需要在項目之間渲染分隔線、多列佈局、無限滾動加載，或是其他許多開箱即用的功能，`FlatList` 也非常方便。

## 範例

```SnackPlayer name=ScrollView
import React from 'react';
import { StyleSheet, Text, SafeAreaView, ScrollView, StatusBar } from 'react-native';

const App = () => {
  return (
    <SafeAreaView style={styles.container}>
      <ScrollView style={styles.scrollView}>
        <Text style={styles.text}>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat. Duis aute irure dolor in
          reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla
          pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
          culpa qui officia deserunt mollit anim id est laborum.
        </Text>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
  },
  scrollView: {
    backgroundColor: 'pink',
    marginHorizontal: 20,
  },
  text: {
    fontSize: 42,
  },
});

export default App;
```

---

# 參考文獻

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view#props)。

---

### `StickyHeaderComponent`

用於渲染黏性標頭的 React 組件，應與 `stickyHeaderIndices` 搭配使用。如果您的黏性標頭使用自訂變換（例如當您希望列表具有可動畫顯示/隱藏的標頭時），可能需要設置此組件。若未提供組件，將使用預設的 [`ScrollViewStickyHeader`](https://github.com/facebook/react-native/blob/0.70-stable/Libraries/Components/ScrollView/ScrollViewStickyHeader.js) 組件。

| Type               |
| ------------------ |
| component, element |

---

### `alwaysBounceHorizontal` <div class="label ios">iOS</div>

當為 true 時，即使內容小於滾動視圖本身，滾動視圖在水平滾動到底部時也會彈跳。

| Type | Default                                               |
| ---- | ----------------------------------------------------- |
| bool | `true` when `horizontal={true}`<hr/>`false` otherwise |

---

### `alwaysBounceVertical` <div class="label ios">iOS</div>

當為 true 時，即使內容小於滾動視圖本身，滾動視圖在垂直滾動到底部時也會彈跳。

| Type | Default                                             |
| ---- | --------------------------------------------------- |
| bool | `false` when `vertical={true}`<hr/>`true` otherwise |

---

### `automaticallyAdjustContentInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整位於導航欄或標籤欄/工具欄後方的滾動視圖的內容內嵌邊距。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `automaticallyAdjustKeyboardInsets` <div class="label ios">iOS</div>

控制當鍵盤大小改變時，ScrollView 是否應自動調整其 `contentInset` 和 `scrollViewInsets`。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `automaticallyAdjustsScrollIndicatorInsets` <div class="label ios">iOS</div>

Controls whether iOS should automatically adjust the scroll indicator insets. See Apple's [documentation on the property](https://developer.apple.com/documentation/uikit/uiscrollview/3198043-automaticallyadjustsscrollindica).

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bounces` <div class="label ios">iOS</div>

When true, the scroll view bounces when it reaches the end of the content if the content is larger than the scroll view along the axis of the scroll direction. When `false`, it disables all bouncing even if the `alwaysBounce*` props are `true`.

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bouncesZoom` <div class="label ios">iOS</div>

When `true`, gestures can drive zoom past min/max and the zoom will animate to the min/max value at gesture end, otherwise the zoom will not exceed the limits.

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `canCancelContentTouches` <div class="label ios">iOS</div>

When `false`, once tracking starts, won't try to drag if the touch moves.

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `centerContent` <div class="label ios">iOS</div>

When `true`, the scroll view automatically centers the content when the content is smaller than the scroll view bounds; when the content is larger than the scroll view, this property has no effect.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `contentContainerStyle`

These styles will be applied to the scroll view content container which wraps all of the child views. Example:

```
return (
  <ScrollView contentContainerStyle={styles.contentContainer}>
  </ScrollView>
);
...
const styles = StyleSheet.create({
  contentContainer: {
    paddingVertical: 20
  }
});
```

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `contentInset` <div class="label ios">iOS</div>

The amount by which the scroll view content is inset from the edges of the scroll view.

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `contentInsetAdjustmentBehavior` <div class="label ios">iOS</div>

This property specifies how the safe area insets are used to modify the content area of the scroll view. Available on iOS 11 and later.

| Type                                                           | Default   |
| -------------------------------------------------------------- | --------- |
| enum(`'automatic'`, `'scrollableAxes'`, `'never'`, `'always'`) | `'never'` |

---

### `contentOffset`

Used to manually set the starting scroll offset.

| Type  | Default        |
| ----- | -------------- |
| Point | `{x: 0, y: 0}` |

---

### `decelerationRate`

A floating-point number that determines how quickly the scroll view decelerates after the user lifts their finger. You may also use string shortcuts `"normal"` and `"fast"` which match the underlying iOS settings for `UIScrollViewDecelerationRateNormal` and `UIScrollViewDecelerationRateFast` respectively.

- `'normal'` 0.998 on iOS, 0.985 on Android.
- `'fast'`, 0.99 on iOS, 0.9 on Android.

| Type                               | Default    |
| ---------------------------------- | ---------- |
| enum(`'fast'`, `'normal'`), number | `'normal'` |

---

### `directionalLockEnabled` <div class="label ios">iOS</div>

When true, the ScrollView will try to lock to only vertical or horizontal scrolling while dragging.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableIntervalMomentum`

When true, the scroll view stops on the next index (in relation to scroll position at release) regardless of how fast the gesture is. This can be used for pagination when the page is less than the width of the horizontal ScrollView or the height of the vertical ScrollView.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableScrollViewPanResponder`

When true, the default JS pan responder on the ScrollView is disabled, and full control over touches inside the ScrollView is left to its child components. This is particularly useful if `snapToInterval` is enabled, since it does not follow typical touch patterns. Do not use this on regular ScrollView use cases without `snapToInterval` as it may cause unexpected touches to occur while scrolling.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `endFillColor` <div class="label android">Android</div>

Sometimes a scrollview takes up more space than its content fills. When this is the case, this prop will fill the rest of the scrollview with a color to avoid setting a background and creating unnecessary overdraw. This is an advanced optimization that is not needed in the general case.

| Type            |
| --------------- |
| [color](colors) |

---

### `fadingEdgeLength` <div class="label android">Android</div>

Fades out the edges of the scroll content.

If the value is greater than `0`, the fading edges will be set accordingly to the current scroll direction and position, indicating if there is more content to show.

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `horizontal`

When `true`, the scroll view's children are arranged horizontally in a row instead of vertically in a column.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `indicatorStyle` <div class="label ios">iOS</div>

The style of the scroll indicators.

- `'default'` same as `black`.
- `'black'`, scroll indicator is `black`. This style is good against a light background.
- `'white'`, scroll indicator is `white`. This style is good against a dark background.

| Type                                    | Default     |
| --------------------------------------- | ----------- |
| enum(`'default'`, `'black'`, `'white'`) | `'default'` |

---

### `invertStickyHeaders`

If sticky headers should stick at the bottom instead of the top of the ScrollView. This is usually used with inverted ScrollViews.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `keyboardDismissMode`

Determines whether the keyboard gets dismissed in response to a drag.

- `'none'`, drags do not dismiss the keyboard.
- `'on-drag'`, the keyboard is dismissed when a drag begins.

**iOS Only**

- `'interactive'`, the keyboard is dismissed interactively with the drag and moves in synchrony with the touch, dragging upwards cancels the dismissal. On Android this is not supported and it will have the same behavior as `'none'`.

| Type                                                                                                                                                            | Default  |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'on-drag'`) <div className="label android">Android</div><hr />enum(`'none'`, `'on-drag'`, `'interactive'`) <div className="label ios">iOS</div> | `'none'` |

---

### `keyboardShouldPersistTaps`

Determines when the keyboard should stay visible after a tap.

- `'never'` 當鍵盤開啟時，點擊聚焦的文字輸入框外部會關閉鍵盤。此時子元件不會接收到點擊事件。
- `'always'` 鍵盤不會自動關閉，且滾動視圖不會攔截點擊事件，但滾動視圖的子元件可以捕捉點擊。
- `'handled'` 當點擊事件被子元件（或被上層元件捕獲）處理時，鍵盤不會自動關閉。
- `false`，**_已棄用_**，請改用 `'never'`
- `true`，**_已棄用_**，請改用 `'always'`

| Type                                                      | Default   |
| --------------------------------------------------------- | --------- |
| enum(`'always'`, `'never'`, `'handled'`, `false`, `true`) | `'never'` |

---

### `maintainVisibleContentPosition` <div class="label ios">iOS</div>

啟用時，滾動視圖會調整滾動位置，使當前可見且位於或超過 `minIndexForVisible` 的第一個子元件保持位置不變。這適用於雙向載入內容的列表（例如聊天串），避免新訊息進入時導致滾動位置跳動。通常設為 0，但也可設為 1 以跳過不應保持位置的載入動畫等內容。

可選參數 `autoscrollToTopThreshold` 可在調整後，若使用者原本位於頂部閾值範圍內時，自動將內容滾動至頂部。這同樣適用於聊天應用，讓新訊息能平滑載入，但避免在使用者已滾動較遠時造成干擾性跳動。

注意事項 1：啟用此功能時重新排序滾動視圖內容可能導致畫面跳動。目前無修復計劃，請避免在啟用此功能的滾動視圖或列表中重新排序內容。

注意事項 2：此功能透過原生代碼的 `contentOffset` 和 `frame.origin` 計算可見性。遮擋、變形等複雜情況不納入「是否可見」的判斷。

| Type                                                                     |
| ------------------------------------------------------------------------ |
| object: `{minIndexForVisible: number, autoscrollToTopThreshold: number}` |

---

### `maximumZoomScale` <div class="label ios">iOS</div>

允許的最大縮放比例。

| Type   | Default |
| ------ | ------- |
| number | `1.0`   |

---

### `minimumZoomScale` <div class="label ios">iOS</div>

允許的最小縮放比例。

| Type   | Default |
| ------ | ------- |
| number | `1.0`   |

---

### `nestedScrollEnabled` <div class="label android">Android</div>

為 Android API 21+ 啟用嵌套滾動功能。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `onContentSizeChange`

當 ScrollView 的可滾動內容視圖尺寸變化時呼叫。

處理函數接收內容寬度和高度參數：`(contentWidth, contentHeight)`

其實作方式是透過附加到 ScrollView 渲染的內容容器上的 onLayout 處理器。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollBegin`

當動量滾動開始時呼叫（即 ScrollView 開始滑行時的滾動）。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollEnd`

當動量滾動結束時呼叫（即 ScrollView 滑行停止時的滾動）。

| Type     |
| -------- |
| function |

---

### `onScroll`

Fires at most once per frame during scrolling. The frequency of the events can be controlled using the `scrollEventThrottle` prop. The event has the following shape (all values are numbers):

```js
{
  nativeEvent: {
    contentInset: {bottom, left, right, top},
    contentOffset: {x, y},
    contentSize: {height, width},
    layoutMeasurement: {height, width},
    zoomScale
  }
}
```

| Type     |
| -------- |
| function |

---

### `onScrollBeginDrag`

Called when the user begins to drag the scroll view.

| Type     |
| -------- |
| function |

---

### `onScrollEndDrag`

Called when the user stops dragging the scroll view and it either stops or begins to glide.

| Type     |
| -------- |
| function |

---

### `onScrollToTop` <div class="label ios">iOS</div>

Fires when the scroll view scrolls to top after the status bar has been tapped.

| Type     |
| -------- |
| function |

---

### `overScrollMode` <div class="label android">Android</div>

Used to override default value of overScroll mode.

Possible values:

- `'auto'` - Allow a user to over-scroll this view only if the content is large enough to meaningfully scroll.
- `'always'` - Always allow a user to over-scroll this view.
- `'never'` - Never allow a user to over-scroll this view.

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'always'`, `'never'`) | `'auto'` |

---

### `pagingEnabled`

When true, the scroll view stops on multiples of the scroll view's size when scrolling. This can be used for horizontal pagination.

> Note: Vertical pagination is not supported on Android.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `persistentScrollbar` <div class="label android">Android</div>

Causes the scrollbars not to turn transparent when they are not in use.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `pinchGestureEnabled` <div class="label ios">iOS</div>

When true, ScrollView allows use of pinch gestures to zoom in and out.

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `refreshControl`

A RefreshControl component, used to provide pull-to-refresh functionality for the ScrollView. Only works for vertical ScrollViews (`horizontal` prop must be `false`).

See [RefreshControl](refreshcontrol).

| Type    |
| ------- |
| element |

---

### `removeClippedSubviews`

Experimental: When `true`, offscreen child views (whose `overflow` value is `hidden`) are removed from their native backing superview when offscreen. This can improve scrolling performance on long lists.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollEnabled`

When false, the view cannot be scrolled via touch interaction.

Note that the view can always be scrolled by calling `scrollTo`.

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `scrollEventThrottle`

此參數控制滾動時觸發滾動事件的頻率（以毫秒為時間間隔）。數值越低，追蹤滾動位置的程式碼精確度越高，但可能因大量資訊通過橋接層傳輸而導致滾動效能問題。由於 JavaScript 運行循環與螢幕刷新率同步，設定值在 1-16 之間時不會感受到差異。若不需要精確追蹤滾動位置，可設定較高值以限制跨橋接層傳輸的資訊量。預設值為 `0`，表示每次視圖滾動時僅觸發一次滾動事件。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `scrollIndicatorInsets` <div class="label ios">iOS</div>

滾動指示器與滾動視圖邊緣的內嵌距離。通常應設為與 `contentInset` 相同的值。

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `scrollPerfTag` <div class="label android">Android</div>

用於記錄此滾動視圖效能日誌的標籤。將強制啟發動量事件（參見 sendMomentumEvents）。此功能預設無作用，需實作自訂原生 FpsListener 才能發揮效用。

| Type   |
| ------ |
| string |

---

### `scrollToOverflowEnabled` <div class="label ios">iOS</div>

設為 `true` 時，可透過程式控制將滾動視圖滾動至超出其內容尺寸的位置。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollsToTop` <div class="label ios">iOS</div>

設為 `true` 時，點擊狀態列會使滾動視圖滾動至頂部。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `showsHorizontalScrollIndicator`

設為 `true` 時，顯示水平滾動指示器。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `showsVerticalScrollIndicator`

設為 `true` 時，顯示垂直滾動指示器。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToAlignment`

當設定 `snapToInterval` 時，`snapToAlignment` 會定義對齊方式與滾動視圖的關係。

可能的值：

- `'start'` 將對齊左側（水平）或頂部（垂直）。
- `'center'` 將對齊中心。
- `'end'` 將對齊右側（水平）或底部（垂直）。

| Type                                 | Default   |
| ------------------------------------ | --------- |
| enum(`'start'`, `'center'`, `'end'`) | `'start'` |

---

### `snapToEnd`

與 `snapToOffsets` 搭配使用。預設情況下，列表末端會視為對齊點。設為 false 可禁用此行為，允許列表在末端與最後一個 `snapToOffsets` 偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToInterval`

設定後，滾動視圖會在 `snapToInterval` 值的倍數位置停駐。適用於子項目長度小於滾動視圖的分頁式滾動。通常與 `snapToAlignment` 和 `decelerationRate="fast"` 搭配使用。會覆蓋較不靈活的 `pagingEnabled` 屬性。

| Type   |
| ------ |
| number |

---

### `snapToOffsets`

設定此屬性後，捲動視圖會在指定的偏移位置停止。這適用於分頁瀏覽尺寸各異且長度小於捲動視圖的子元件。通常與 `decelerationRate="fast"` 搭配使用。此屬性會覆蓋彈性較低的 `pagingEnabled` 和 `snapToInterval` 屬性。

| Type            |
| --------------- |
| array of number |

---

### `snapToStart`

需與 `snapToOffsets` 搭配使用。預設情況下，列表起始位置會視為吸附偏移點。將 `snapToStart` 設為 `false` 可禁用此行為，讓列表能在起始位置與第一個 `snapToOffsets` 偏移點之間自由捲動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `stickyHeaderHiddenOnScroll`

設為 `true` 時，黏性標頭會在向下捲動列表時隱藏，並在向上捲動時固定在列表頂部。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `stickyHeaderIndices`

此陣列決定哪些子元件在捲動時會固定在畫面頂部。例如傳入 `stickyHeaderIndices={[0]}` 會讓第一個子元件固定在捲動視圖頂部。也可使用 [x,y,z] 格式讓多個元件在頂部時保持黏性。此屬性不支援與 `horizontal={true}` 同時使用。

| Type            |
| --------------- |
| array of number |

---

### `zoomScale` <div class="label ios">iOS</div>

捲動視圖內容當前的縮放比例。

| Type   | Default |
| ------ | ------- |
| number | `1.0`   |

---

## 方法

### `flashScrollIndicators()`

```jsx
flashScrollIndicators();
```

短暫顯示捲動指示器。

---

### `scrollTo()`

```jsx
scrollTo(
  options?: { x?: number, y?: number, animated?: boolean } | number,
  deprecatedX?: number,
  deprecatedAnimated?: boolean,
);
```

捲動至指定的 x、y 偏移位置，可選擇立即執行或使用平滑動畫。

**範例：**

`scrollTo({ x: 0, y: 0, animated: true })`

> 注意：此函數簽名的特殊形式是由於歷史原因，它同時支援以獨立參數替代選項物件。由於存在參數順序歧義（y 在 x 之前），此用法已被棄用，不應繼續使用。

---

### `scrollToEnd()`

```jsx
scrollToEnd(([options]: {animated: boolean}));
```

如果是垂直捲動視圖，會捲動到底部；如果是水平捲動視圖，則捲動到右側。

使用 `scrollToEnd({ animated: true })` 可啟用平滑動畫捲動，`scrollToEnd({ animated: false })` 則會立即捲動。若未傳入選項，`animated` 預設為 `true`。