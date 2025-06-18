---
id: scrollview
title: ScrollView
---

封裝平台原生 ScrollView 並整合觸控鎖定「響應者」系統的元件。

請注意，ScrollView 必須具有固定高度才能正常運作，因為它們將無限高度的子元件封裝在有限容器中（透過滾動互動）。要限制 ScrollView 的高度，可以直接設定視圖高度（不建議）或確保所有父視圖都具有固定高度。忘記將 `{flex: 1}` 傳遞至視圖結構下方可能導致錯誤，此時使用元素檢查器可快速除錯。

目前尚不支援防止其他包含的響應者阻擋此滾動視圖成為主要響應者。

`<ScrollView>` 與 [`<FlatList>`](flatlist.md) 該如何選擇？

`ScrollView` 會一次性渲染所有子元件，但這會帶來效能上的缺點。

假設您需要顯示一個非常長的項目列表，可能涵蓋多個螢幕的內容。一次性為所有內容建立 JS 元件和原生視圖（其中許多可能根本不會顯示），會導致渲染速度變慢並增加記憶體使用量。

這時 `FlatList` 就派上用場。`FlatList` 會惰性渲染項目，僅在它們即將出現時才渲染，並移除滾動超出螢幕範圍的項目以節省記憶體和處理時間。

如果您需要在項目間渲染分隔線、多欄佈局、無限滾動載入，或是其他開箱即用的功能，`FlatList` 也非常方便。

## 範例

```SnackPlayer name=ScrollView
import React from 'react';
import {
  StyleSheet,
  Text,
  SafeAreaView,
  ScrollView,
  StatusBar,
} from 'react-native';

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
};

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

# 參考資料

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view#props)。

---

### `StickyHeaderComponent`

用於渲染黏性標頭的 React 元件，需與 `stickyHeaderIndices` 搭配使用。若您的黏性標頭使用自訂變形效果（例如想讓列表具有可動畫顯示/隱藏的標頭），可能需要設定此元件。若未提供元件，將使用預設的 [`ScrollViewStickyHeader`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/ScrollView/ScrollViewStickyHeader.js) 元件。

| Type               |
| ------------------ |
| component, element |

---

### `alwaysBounceHorizontal` <div class="label ios">iOS</div>

設為 true 時，即使內容小於滾動視圖本身，滾動視圖在水平滾動到底部時仍會彈跳。

| Type | Default                                               |
| ---- | ----------------------------------------------------- |
| bool | `true` when `horizontal={true}`<hr/>`false` otherwise |

---

### `alwaysBounceVertical` <div class="label ios">iOS</div>

設為 true 時，即使內容小於滾動視圖本身，滾動視圖在垂直滾動到底部時仍會彈跳。

| Type | Default                                             |
| ---- | --------------------------------------------------- |
| bool | `false` when `vertical={true}`<hr/>`true` otherwise |

---

### `automaticallyAdjustContentInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整位於導覽列或標籤列/工具列後方的滾動視圖的內容插邊距。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `automaticallyAdjustKeyboardInsets` <div class="label ios">iOS</div>

控制當鍵盤大小變更時，ScrollView 是否應自動調整其 `contentInset` 和 `scrollViewInsets`。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `automaticallyAdjustsScrollIndicatorInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整滾動指示器的內邊距。詳見 Apple 的[屬性說明文件](https://developer.apple.com/documentation/uikit/uiscrollview/3198043-automaticallyadjustsscrollindica)。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bounces` <div class="label ios">iOS</div>

當設為 `true` 時，若內容沿滾動方向超出滾動視圖範圍，滾動視圖會在到達內容邊界時反彈。設為 `false` 時，即使 `alwaysBounce*` 屬性為 `true` 也會禁用所有反彈效果。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bouncesZoom` <div class="label ios">iOS</div>

當設為 `true` 時，手勢操作可暫時超出最小/最大縮放限制，並在手勢結束時動畫回彈至限制值；否則縮放比例不會超過限制範圍。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `canCancelContentTouches` <div class="label ios">iOS</div>

設為 `false` 時，一旦開始追蹤觸控，即使手指移動也不會觸發拖動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `centerContent` <div class="label ios">iOS</div>

設為 `true` 時，當內容小於滾動視圖邊界時會自動居中；若內容大於滾動視圖則此屬性無效。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `contentContainerStyle`

這些樣式將套用至包裹所有子視圖的滾動視圖內容容器。範例：

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

設定滾動視圖內容與滾動視圖邊緣的內邊距量。

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `contentInsetAdjustmentBehavior` <div class="label ios">iOS</div>

此屬性指定如何利用安全區域內邊距來調整滾動視圖的內容區域。僅適用於 iOS 11 及以上版本。

| Type                                                           | Default   |
| -------------------------------------------------------------- | --------- |
| enum(`'automatic'`, `'scrollableAxes'`, `'never'`, `'always'`) | `'never'` |

---

### `contentOffset`

用於手動設定起始滾動偏移量。

| Type  | Default        |
| ----- | -------------- |
| Point | `{x: 0, y: 0}` |

---

### `decelerationRate`

此浮點數決定用戶鬆開手指後滾動視圖的減速速度。也可使用字串快捷參數 `"normal"` 和 `"fast"`，分別對應 iOS 底層設置的 `UIScrollViewDecelerationRateNormal` 和 `UIScrollViewDecelerationRateFast`。

- `'normal'`：iOS 為 0.998，Android 為 0.985
- `'fast'`：iOS 為 0.99，Android 為 0.9

| Type                               | Default    |
| ---------------------------------- | ---------- |
| enum(`'fast'`, `'normal'`), number | `'normal'` |

---

### `directionalLockEnabled` <div class="label ios">iOS</div>

設為 `true` 時，滾動視圖在拖曳時會嘗試鎖定僅垂直或水平滾動。

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
- `'always'` 鍵盤不會自動關閉，且滾動視圖不會攔截點擊事件，但滾動視圖的子元件可以捕獲點擊。
- `'handled'` 當點擊事件被滾動視圖的子元件（或被上層元件捕獲）處理時，鍵盤不會自動關閉。
- `false`，**_已棄用_**，請改用 `'never'`
- `true`，**_已棄用_**，請改用 `'always'`

| Type                                                      | Default   |
| --------------------------------------------------------- | --------- |
| enum(`'always'`, `'never'`, `'handled'`, `false`, `true`) | `'never'` |

---

### `maintainVisibleContentPosition`

設定後，滾動視圖會調整滾動位置，使當前可見且位於或超過 `minIndexForVisible` 的第一個子元件保持位置不變。這適用於雙向載入內容的列表（例如聊天串），否則新訊息進入時可能導致滾動位置跳動。常見值為 0，但也可使用 1 等值來跳過不應保持位置的載入指示器或其他內容。

可選參數 `autoscrollToTopThreshold` 可在調整後，若使用者原本位於頂部閾值範圍內時，自動將內容滾動至頂部。這同樣適用於聊天類應用，您希望看到新訊息滾動到位，但若使用者已向上滾動一段距離，突然滾動大量內容會造成干擾。

注意事項 1：啟用此功能時在滾動視圖中重新排序元件可能會導致跳動和卡頓。此問題可修復，但目前無相關計劃。現階段請勿對使用此功能的 ScrollView 或 List 內容重新排序。

注意事項 2：此功能在原生代碼中使用 `contentOffset` 和 `frame.origin` 計算可見性。遮擋、變形等複雜因素不會被納入內容是否「可見」的判斷。

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

為 Android API 等級 21+ 啟用嵌套滾動。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `onContentSizeChange`

當 ScrollView 的可滾動內容視圖大小變化時呼叫。

處理函數會接收兩個參數：內容寬度和內容高度 `(contentWidth, contentHeight)`。

其實現方式是透過附加到 ScrollView 渲染的內容容器的 onLayout 處理器。

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

滾動期間每幀最多觸發一次。事件結構如下（所有值均為數字）：

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

當用戶開始拖動滾動視圖時調用。

| Type     |
| -------- |
| function |

---

### `onScrollEndDrag`

當用戶停止拖動滾動視圖並使其停止或開始滑動時調用。

| Type     |
| -------- |
| function |

---

### `onScrollToTop` <div class="label ios">iOS</div>

當滾動視圖在點擊狀態欄後滾動至頂部時觸發。

| Type     |
| -------- |
| function |

---

### `overScrollMode` <div class="label android">Android</div>

用於覆蓋默認的過度滾動模式值。

可能的值：

- `'auto'` - 僅當內容足夠大以有意義地滾動時，才允許用戶對此視圖進行過度滾動。
- `'always'` - 始終允許用戶對此視圖進行過度滾動。
- `'never'` - 永不允許用戶對此視圖進行過度滾動。

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'always'`, `'never'`) | `'auto'` |

---

### `pagingEnabled`

當設置為 true 時，滾動視圖在滾動時會停在滾動視圖大小的倍數位置。這可用於水平分頁。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `persistentScrollbar` <div class="label android">Android</div>

使滾動條在不使用時不會變透明。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `pinchGestureEnabled` <div class="label ios">iOS</div>

當設置為 true 時，ScrollView 允許使用捏合手勢進行縮放。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `refreshControl`

一個 RefreshControl 組件，用於為 ScrollView 提供下拉刷新功能。僅適用於垂直 ScrollView（`horizontal` 屬性必須為 `false`）。

參見 [RefreshControl](refreshcontrol)。

| Type    |
| ------- |
| element |

---

### `removeClippedSubviews`

實驗性功能：當設置為 `true` 時，離屏的子視圖（其 `overflow` 值為 `hidden`）將從其原生父視圖中移除。這可以提高長列表的滾動性能。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollEnabled`

當設置為 false 時，視圖無法通過觸摸交互滾動。

注意：視圖仍可通過調用 `scrollTo` 來滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `scrollEventThrottle`

限制滾動時觸發滾動事件的頻率，以毫秒為單位指定時間間隔。這在滾動時執行昂貴操作時可能有用。值 ≤ `16` 將禁用節流，無論設備的刷新率如何。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `scrollIndicatorInsets` <div class="label ios">iOS</div>

滾動視圖指示器從滾動視圖邊緣的內嵌量。通常應設置為與 `contentInset` 相同的值。

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `scrollPerfTag` <div class="label android">Android</div>

用於記錄此滾動視圖性能的標籤。將強制開啟動量事件（參見sendMomentumEvents）。此功能默認不生效，需實現自定義的原生FpsListener才能發揮作用。

| Type   |
| ------ |
| string |

---

### `scrollToOverflowEnabled` <div class="label ios">iOS</div>

當設為`true`時，可通過程序控制滾動視圖超出其內容尺寸。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollsToTop` <div class="label ios">iOS</div>

當設為`true`時，點擊狀態欄會使滾動視圖滾動至頂部。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `showsHorizontalScrollIndicator`

當設為`true`時，顯示水平滾動指示器。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `showsVerticalScrollIndicator`

當設為`true`時，顯示垂直滾動指示器。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToAlignment`

當設置`snapToInterval`時，`snapToAlignment`將定義對齊方式與滾動視圖的關係。

可能的值：

- `'start'` 將對齊到左側（水平）或頂部（垂直）。
- `'center'` 將對齊到中心。
- `'end'` 將對齊到右側（水平）或底部（垂直）。

| Type                                 | Default   |
| ------------------------------------ | --------- |
| enum(`'start'`, `'center'`, `'end'`) | `'start'` |

---

### `snapToEnd`

與`snapToOffsets`配合使用。默認情況下，列表末端會作為吸附偏移點。設為`false`可禁用此行為，允許列表在末端與最後一個`snapToOffsets`偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToInterval`

設置後，滾動視圖將在`snapToInterval`值的倍數處停止。可用於分頁瀏覽小於滾動視圖尺寸的子項。通常與`snapToAlignment`和`decelerationRate="fast"`配合使用。會覆蓋配置較少的`pagingEnabled`屬性。

| Type   |
| ------ |
| number |

---

### `snapToOffsets`

設置後，滾動視圖將在定義的偏移點停止。可用於分頁瀏覽尺寸各異且小於滾動視圖的子項。通常與`decelerationRate="fast"`配合使用。會覆蓋配置較少的`pagingEnabled`和`snapToInterval`屬性。

| Type            |
| --------------- |
| array of number |

---

### `snapToStart`

與`snapToOffsets`配合使用。默認情況下，列表起始處會作為吸附偏移點。設為`false`可禁用此行為，允許列表在起始處與第一個`snapToOffsets`偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `stickyHeaderHiddenOnScroll`

設為`true`時，向下滾動列表會隱藏黏性標頭，向上滾動時會將標頭固定在列表頂部。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `stickyHeaderIndices`

一個子元素索引陣列，決定哪些子元素在滾動時會固定在螢幕頂部。例如，傳遞 `stickyHeaderIndices={[0]}` 會使第一個子元素固定在滾動視圖頂部。你也可以使用 [x,y,z] 格式讓多個項目在頂部時保持固定。此屬性不支援與 `horizontal={true}` 同時使用。

| Type            |
| --------------- |
| array of number |

---

### `zoomScale` <div class="label ios">iOS</div>

滾動視圖內容的當前縮放比例。

| Type   | Default |
| ------ | ------- |
| number | `1.0`   |

---

## 方法

### `flashScrollIndicators()`

```tsx
flashScrollIndicators();
```

短暫顯示滾動指示器。

---

### `scrollTo()`

```tsx
scrollTo(
  options?: {x?: number, y?: number, animated?: boolean} | number,
  deprecatedX?: number,
  deprecatedAnimated?: boolean,
);
```

滾動到指定的 x、y 偏移位置，可選擇立即執行或使用平滑動畫。

**範例：**

`scrollTo({x: 0, y: 0, animated: true})`

> 注意：此函數的奇特簽名是由於歷史原因，它同時接受獨立參數作為選項物件的替代方案。由於存在歧義（y 在 x 之前），此用法已被棄用，不應繼續使用。

---

### `scrollToEnd()`

```tsx
scrollToEnd(options?: {animated?: boolean});
```

如果是垂直 ScrollView，則滾動到底部；如果是水平 ScrollView，則滾動到右側。

使用 `scrollToEnd({animated: true})` 可實現平滑動畫滾動，使用 `scrollToEnd({animated: false})` 則立即滾動。若未傳遞選項，`animated` 預設為 `true`。