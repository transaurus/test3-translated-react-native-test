---
id: scrollview
title: ScrollView
---

封裝平台原生 ScrollView 並整合觸控鎖定「響應者」系統的元件。

請注意，ScrollView 必須具有固定高度才能正常運作，因為它們將無限高度的子元件放入有限容器中（透過滾動互動）。要限制 ScrollView 的高度，可以直接設定視圖高度（不建議）或確保所有父視圖都具有固定高度。忘記沿視圖堆疊向下傳遞 `{flex: 1}` 可能導致錯誤，此時元素檢查器能快速除錯。

目前尚不支援阻止其他包含的響應者使此滾動視圖成為響應者。

`<ScrollView>` 與 [`<FlatList>`](flatlist.md) 該如何選擇？

`ScrollView` 會一次性渲染所有子元件，但這會帶來效能上的缺點。

假設您需要顯示一個非常長的項目列表，可能涵蓋多個螢幕的內容。一次性為所有內容創建 JS 元件和原生視圖（其中許多可能根本不會顯示），將導致渲染速度變慢和記憶體使用量增加。

這時 `FlatList` 就派上用場了。`FlatList` 會惰性渲染項目（僅在即將顯示時才渲染），並移除滾動出螢幕外的項目以節省記憶體和處理時間。

如果您需要渲染項目間的分隔線、多欄佈局、無限滾動載入或其他開箱即用的功能，`FlatList` 也非常方便。

## 範例

```SnackPlayer name=ScrollView%20Example
import React from 'react';
import {StyleSheet, Text, ScrollView, StatusBar} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container} edges={['top']}>
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
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
  },
  scrollView: {
    backgroundColor: 'pink',
  },
  text: {
    fontSize: 42,
    padding: 12,
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

用於渲染黏性標頭的 React 元件，需與 `stickyHeaderIndices` 搭配使用。若您的黏性標頭使用自訂變形（例如想實現動畫效果或可隱藏標頭），可能需要設定此元件。若未提供元件，將使用預設的 [`ScrollViewStickyHeader`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/ScrollView/ScrollViewStickyHeader.js) 元件。

| Type               |
| ------------------ |
| component, element |

---

### `alwaysBounceHorizontal` <div class="label ios">iOS</div>

設為 true 時，滾動視圖在到達水平邊界時會彈跳，即使內容小於滾動視圖本身。

| Type | Default                                               |
| ---- | ----------------------------------------------------- |
| bool | `true` when `horizontal={true}`<hr/>`false` otherwise |

---

### `alwaysBounceVertical` <div class="label ios">iOS</div>

設為 true 時，滾動視圖在到達垂直邊界時會彈跳，即使內容小於滾動視圖本身。

| Type | Default                                             |
| ---- | --------------------------------------------------- |
| bool | `false` when `vertical={true}`<hr/>`true` otherwise |

---

### `automaticallyAdjustContentInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整位於導覽列或標籤列/工具列後方之滾動視圖的內容插邊距。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `automaticallyAdjustKeyboardInsets` <div class="label ios">iOS</div>

控制當鍵盤尺寸變化時，ScrollView 是否應自動調整其 `contentInset` 和 `scrollViewInsets`。

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

當設為 true 時，無論手勢速度多快，滾動視圖都會在釋放時相對於滾動位置的下一個索引處停止。這可用於當頁面寬度小於水平 ScrollView 或高度小於垂直 ScrollView 時的分頁效果。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableScrollViewPanResponder`

當設為 true 時，會禁用 ScrollView 上預設的 JS 手勢響應器，將 ScrollView 內部的觸控完全交由子元件控制。這在啟用 `snapToInterval` 時特別有用，因為該功能不符合常規觸控模式。請勿在未啟用 `snapToInterval` 的一般 ScrollView 使用情境中使用此屬性，否則可能導致滾動時發生非預期的觸控行為。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `endFillColor` <div class="label android">Android</div>

當滾動視圖佔用的空間大於內容區域時，此屬性會用指定顏色填充剩餘空間，避免設置背景造成不必要的過度繪製。這屬於進階優化技巧，一般情況下不需要使用。

| Type            |
| --------------- |
| [color](colors) |

---

### `fadingEdgeLength` <div class="label android">Android</div>

淡出滾動內容的邊緣。

若值大於 `0`，會根據當前滾動方向與位置顯示淡出邊緣效果，提示是否還有更多內容可查看。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `horizontal`

當設為 true 時，滾動視圖的子元件會水平排列成行，而非垂直排列成列。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `indicatorStyle` <div class="label ios">iOS</div>

滾動指示器的樣式。

- `'default'` 與 `black` 相同。
- `'black'`，滾動指示器為黑色。此樣式適合淺色背景。
- `'white'`，滾動指示器為白色。此樣式適合深色背景。

| Type                                    | Default     |
| --------------------------------------- | ----------- |
| enum(`'default'`, `'black'`, `'white'`) | `'default'` |

---

### `invertStickyHeaders`

是否讓黏性標頭固定在 ScrollView 底部而非頂部。通常與反向 ScrollView 搭配使用。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `keyboardDismissMode`

決定鍵盤是否會因拖曳動作而關閉。

- `'none'`，拖曳不會關閉鍵盤。
- `'on-drag'`，開始拖曳時關閉鍵盤。

**僅限 iOS**

- `'interactive'`，鍵盤會隨拖曳動作互動式關閉，並與觸控同步移動，向上拖曳可取消關閉。Android 不支援此模式，行為將與 `'none'` 相同。

| Type                                                                                                                                                            | Default  |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'on-drag'`) <div className="label android">Android</div><hr />enum(`'none'`, `'on-drag'`, `'interactive'`) <div className="label ios">iOS</div> | `'none'` |

---

### `keyboardShouldPersistTaps`

決定點擊後鍵盤是否應保持可見。

- `'never'` 當鍵盤開啟時，點擊聚焦的文字輸入框外部會關閉鍵盤。此時子元件不會接收到點擊事件。
- `'always'` 鍵盤不會自動關閉，且滾動視圖不會攔截點擊事件，但滾動視圖的子元件可以捕獲點擊。
- `'handled'` 當點擊事件被子元件（或被上層元件捕獲）處理時，鍵盤不會自動關閉。
- `false`，**_已棄用_**，請改用 `'never'`
- `true`，**_已棄用_**，請改用 `'always'`

| Type                                                      | Default   |
| --------------------------------------------------------- | --------- |
| enum(`'always'`, `'never'`, `'handled'`, `false`, `true`) | `'never'` |

---

### `maintainVisibleContentPosition`

設定後，滾動視圖會調整滾動位置，使當前可見且位於或超過 `minIndexForVisible` 的第一個子元件保持位置不變。這適用於雙向載入內容的列表（例如聊天串），避免新訊息進入時導致滾動位置跳動。常見值為 0，但也可設為 1 以跳過不應保持位置的載入指示器或其他內容。

可選參數 `autoscrollToTopThreshold` 可在調整後，若使用者原本位於頂部閾值範圍內時，自動將內容滾動至頂部。這同樣適用於聊天類應用，希望新訊息能平滑滾入，但避免在使用者已向上滾動較多時造成干擾。

注意事項 1：啟用此功能時重新排序滾動視圖中的元件可能會導致跳動。此問題可修復，但目前無相關計畫。現階段請勿對使用此功能的滾動視圖或列表重新排序內容。

注意事項 2：此功能透過原生代碼中的 `contentOffset` 和 `frame.origin` 計算可見性。遮擋、變形等複雜因素不會被納入判斷內容是否「可見」的考量。

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

當滾動視圖的可滾動內容視圖尺寸變化時呼叫。

處理函式會接收兩個參數：內容寬度與內容高度 `(contentWidth, contentHeight)`。

其實作方式是透過附加到滾動視圖渲染的內容容器上的 onLayout 處理器。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollBegin`

當動量滾動開始時呼叫（即滾動視圖開始滑行時的滾動）。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollEnd`

當動量滾動結束時呼叫（即滾動視圖滑行至停止時的滾動）。

| Type     |
| -------- |
| function |

---

### `onScroll`

滾動期間每幀最多觸發一次。事件格式如下（所有值均為數字）：

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

當用戶停止拖動滾動視圖且滾動視圖停止或開始滑動時調用。

| Type     |
| -------- |
| function |

---

### `onScrollToTop` <div class="label ios">iOS</div>

當滾動視圖因點擊狀態列而滾動至頂部時觸發。

| Type     |
| -------- |
| function |

---

### `overScrollMode` <div class="label android">Android</div>

用於覆蓋默認的過度滾動模式值。

可能的值：

- `'auto'` - 僅當內容足夠大時，才允許用戶對此視圖進行過度滾動。
- `'always'` - 始終允許用戶對此視圖進行過度滾動。
- `'never'` - 永不允許用戶對此視圖進行過度滾動。

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'always'`, `'never'`) | `'auto'` |

---

### `pagingEnabled`

當設置為 true 時，滾動視圖會在滾動時停在滾動視圖大小的倍數位置上。這可用於實現水平分頁效果。

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

當設置為 true 時，ScrollView 允許使用捏合手勢來放大和縮小。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `refreshControl`

一個 RefreshControl 組件，用於為 ScrollView 提供下拉刷新功能。僅適用於垂直方向的 ScrollView（`horizontal` 屬性必須為 `false`）。

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

當設置為 false 時，無法通過觸摸交互滾動視圖。

注意：通過調用 `scrollTo` 仍可滾動視圖。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `scrollEventThrottle`

限制滾動時觸發滾動事件的頻率，以毫秒為單位指定時間間隔。這在滾動時需要執行昂貴操作時可能有用。值 ≤ `16` 將禁用節流，無論設備的刷新率如何。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `scrollIndicatorInsets` <div class="label ios">iOS</div>

滾動視圖指示器從滾動視圖邊緣的內嵌距離。通常應設置為與 `contentInset` 相同的值。

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `scrollPerfTag` <div class="label android">Android</div>

用於記錄此滾動視圖性能的標籤。將強制啟用動量事件（參見sendMomentumEvents）。預設情況下此功能不會生效，需實作自訂的原生FpsListener才能發揮作用。

| Type   |
| ------ |
| string |

---

### `scrollToOverflowEnabled` <div class="label ios">iOS</div>

設為`true`時，可通過程式控制將滾動視圖滾動超出其內容尺寸。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollsToTop` <div class="label ios">iOS</div>

設為`true`時，點擊狀態列會使滾動視圖滾動至頂部。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `showsHorizontalScrollIndicator`

設為`true`時，顯示水平滾動指示器。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `showsVerticalScrollIndicator`

設為`true`時，顯示垂直滾動指示器。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToAlignment`

當設置`snapToInterval`時，`snapToAlignment`會定義對齊方式與滾動視圖的關係。

可能的值：

- `'start'` 將對齊左側（水平）或頂部（垂直）。
- `'center'` 將對齊中心。
- `'end'` 將對齊右側（水平）或底部（垂直）。

| Type                                 | Default   |
| ------------------------------------ | --------- |
| enum(`'start'`, `'center'`, `'end'`) | `'start'` |

---

### `snapToEnd`

需與`snapToOffsets`配合使用。預設情況下，列表末端會被視為對齊點。設為`false`可禁用此行為，允許列表在末端與最後一個`snapToOffsets`偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToInterval`

設置後，滾動視圖會在`snapToInterval`值的倍數處停止。可用於分頁瀏覽小於滾動視圖尺寸的子項目。通常與`snapToAlignment`和`decelerationRate="fast"`配合使用。會覆蓋較不靈活的`pagingEnabled`屬性。

| Type   |
| ------ |
| number |

---

### `snapToOffsets`

設置後，滾動視圖會在定義的偏移量處停止。可用於分頁瀏覽尺寸各異且小於滾動視圖的子項目。通常與`decelerationRate="fast"`配合使用。會覆蓋較不靈活的`pagingEnabled`和`snapToInterval`屬性。

| Type            |
| --------------- |
| array of number |

---

### `snapToStart`

需與`snapToOffsets`配合使用。預設情況下，列表起始處會被視為對齊點。設為`false`可禁用此行為，允許列表在起始處與第一個`snapToOffsets`偏移點之間自由滾動。

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

一個子元素索引陣列，決定哪些子元素在滾動時會固定在畫面頂部。例如傳入 `stickyHeaderIndices={[0]}` 會讓第一個子元素固定在滾動視圖頂部。也可使用 [x,y,z] 格式讓多個元素在到達頂部時保持固定。此屬性不支援與 `horizontal={true}` 同時使用。

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

> 注意：此函數簽名的特殊形式是由於歷史原因，該函數也接受獨立參數作為選項物件的替代方案。由於存在歧義（y 在 x 之前），此用法已被棄用，不應繼續使用。

---

### `scrollToEnd()`

```tsx
scrollToEnd(options?: {animated?: boolean});
```

如果是垂直 ScrollView 則滾動到底部，如果是水平 ScrollView 則滾動到右側。

使用 `scrollToEnd({animated: true})` 可實現平滑動畫滾動，`scrollToEnd({animated: false})` 則立即滾動。若未傳入選項，`animated` 預設為 `true`。