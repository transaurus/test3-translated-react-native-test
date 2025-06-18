---
id: scrollview
title: ScrollView
---

該元件封裝了平台的 ScrollView，同時提供與觸控鎖定「響應者」系統的整合功能。

請注意，ScrollView 必須具有固定高度才能正常運作，因為它們將無限高度的子元件封裝在一個有限容器中（透過滾動互動）。為了限制 ScrollView 的高度，可以直接設定視圖的高度（不建議）或確保所有父視圖都具有固定高度。忘記將 `{flex: 1}` 傳遞至視圖堆疊可能會導致錯誤，此時使用元素檢查器可以快速除錯。

目前尚不支援其他包含的響應者阻止此滾動視圖成為響應者。

`<ScrollView>` 與 [`<FlatList>`](flatlist.md) 該如何選擇？

`ScrollView` 會一次性渲染所有 React 子元件，但這會帶來效能上的缺點。

想像你有一個非常長的項目列表需要顯示，可能是好幾頁的內容。一次性為所有內容創建 JS 元件和原生視圖，其中許多甚至可能不會顯示，這會導致渲染速度變慢和記憶體使用量增加。

這就是 `FlatList` 的用武之地。`FlatList` 會惰性渲染項目，僅在它們即將出現時才渲染，並移除滾動出螢幕的項目以節省記憶體和處理時間。

如果你需要在項目之間渲染分隔線、多列佈局、無限滾動加載，或是其他許多開箱即用的功能，`FlatList` 也非常方便。

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

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view#props)。

---

### `StickyHeaderComponent`

一個 React 元件，用於渲染黏性標頭，應與 `stickyHeaderIndices` 一起使用。如果你的黏性標頭使用自訂變換（例如當你希望列表具有動畫效果且可隱藏的標頭時），可能需要設定此元件。如果未提供元件，將使用預設的 [`ScrollViewStickyHeader`](https://github.com/facebook/react-native/blob/0.71-stable/Libraries/Components/ScrollView/ScrollViewStickyHeader.js) 元件。

| Type               |
| ------------------ |
| component, element |

---

### `alwaysBounceHorizontal` <div class="label ios">iOS</div>

當設定為 true 時，滾動視圖在到達水平方向的末端時會彈跳，即使內容小於滾動視圖本身。

| Type | Default                                               |
| ---- | ----------------------------------------------------- |
| bool | `true` when `horizontal={true}`<hr/>`false` otherwise |

---

### `alwaysBounceVertical` <div class="label ios">iOS</div>

當設定為 true 時，滾動視圖在到達垂直方向的末端時會彈跳，即使內容小於滾動視圖本身。

| Type | Default                                             |
| ---- | --------------------------------------------------- |
| bool | `false` when `vertical={true}`<hr/>`true` otherwise |

---

### `automaticallyAdjustContentInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整位於導航欄或標籤欄/工具欄後方的滾動視圖的內容內嵌。

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

控制 iOS 是否應自動調整滾動指示器內嵌邊距。詳見 Apple 的[屬性說明文件](https://developer.apple.com/documentation/uikit/uiscrollview/3198043-automaticallyadjustsscrollindica)。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bounces` <div class="label ios">iOS</div>

當設為 true 時，若內容沿滾動方向超出滾動視圖範圍，滾動視圖會在到達內容邊界時產生彈跳效果。設為 false 時，即使 `alwaysBounce*` 屬性為 true 也會禁用所有彈跳效果。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bouncesZoom` <div class="label ios">iOS</div>

當設為 true 時，手勢操作可驅動縮放超出最小/最大值限制，並在手勢結束時動畫回彈至限制值；否則縮放比例不會超過限制範圍。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `canCancelContentTouches` <div class="label ios">iOS</div>

設為 false 時，一旦開始追蹤觸控，即使觸控點移動也不會觸發拖曳操作。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `centerContent` <div class="label ios">iOS</div>

設為 true 時，當內容小於滾動視圖邊界時會自動置中；若內容大於滾動視圖則此屬性無效。

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

設定滾動視圖內容與滾動視圖邊緣的內嵌間距量。

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `contentInsetAdjustmentBehavior` <div class="label ios">iOS</div>

此屬性指定如何利用安全區域內嵌邊距來調整滾動視圖的內容區域。僅限 iOS 11 及以上版本。

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

此浮點數決定用戶抬起手指後滾動視圖的減速速度。亦可使用字串快捷參數 "normal" 和 "fast"，分別對應 iOS 底層設置的 UIScrollViewDecelerationRateNormal 和 UIScrollViewDecelerationRateFast。

- `'normal'` iOS 為 0.998，Android 為 0.985
- `'fast'` iOS 為 0.99，Android 為 0.9

| Type                               | Default    |
| ---------------------------------- | ---------- |
| enum(`'fast'`, `'normal'`), number | `'normal'` |

---

### `directionalLockEnabled` <div class="label ios">iOS</div>

設為 true 時，ScrollView 在拖曳期間會嘗試鎖定僅允許垂直或水平方向的滾動。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableIntervalMomentum`

當設定為 true 時，無論手勢速度多快，滾動視圖都會在釋放時依據滾動位置停止在下一個索引處。這適用於當頁面寬度小於水平 ScrollView 或高度小於垂直 ScrollView 時的分頁效果。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableScrollViewPanResponder`

當設定為 true 時，ScrollView 上預設的 JS pan responder 會被停用，其內部子元件將完全控制觸摸行為。此屬性特別適用於啟用 `snapToInterval` 的情況，因為該模式不符合常規觸摸模式。若未啟用 `snapToInterval`，請勿在常規 ScrollView 使用情境中使用此屬性，否則可能導致滾動時發生非預期觸摸事件。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `endFillColor` <div class="label android">Android</div>

當 ScrollView 佔據的空間大於內容區域時，此屬性會以指定顏色填充剩餘空間，避免設定背景造成不必要的過度繪製。此為進階優化選項，一般情境無需使用。

| Type            |
| --------------- |
| [color](colors) |

---

### `fadingEdgeLength` <div class="label android">Android</div>

淡化滾動內容邊緣效果。

若值大於 `0`，會依據當前滾動方向與位置顯示淡出邊緣，提示尚有更多內容可瀏覽。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `horizontal`

當設定為 `true` 時，ScrollView 的子元件會以水平排列取代預設的垂直排列。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `indicatorStyle` <div class="label ios">iOS</div>

滾動指示器的樣式。

- `'default'` 等同於 `black`。
- `'black'`，黑色滾動指示器，適用於淺色背景。
- `'white'`，白色滾動指示器，適用於深色背景。

| Type                                    | Default     |
| --------------------------------------- | ----------- |
| enum(`'default'`, `'black'`, `'white'`) | `'default'` |

---

### `invertStickyHeaders`

設定黏性標頭是否應固定在 ScrollView 底部而非頂部。通常與反向 ScrollView 搭配使用。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `keyboardDismissMode`

控制鍵盤是否因拖曳動作而關閉。

- `'none'`，拖曳不會關閉鍵盤。
- `'on-drag'`，開始拖曳時關閉鍵盤。

**僅限 iOS**

- `'interactive'`，鍵盤會隨拖曳動作互動式關閉，並與觸摸同步移動，向上拖曳可取消關閉。Android 不支援此模式，行為將與 `'none'` 相同。

| Type                                                                                                                                                            | Default  |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'on-drag'`) <div className="label android">Android</div><hr />enum(`'none'`, `'on-drag'`, `'interactive'`) <div className="label ios">iOS</div> | `'none'` |

---

### `keyboardShouldPersistTaps`

決定點擊後鍵盤是否保持可見。

- `'never'` 當鍵盤彈出時，點擊聚焦的文字輸入框外部會關閉鍵盤。此時子元件不會接收到點擊事件。
- `'always'` 鍵盤不會自動關閉，且滾動視圖不會攔截點擊事件，但滾動視圖的子元件可以捕獲點擊。
- `'handled'` 當點擊事件被滾動視圖的子元件（或被上層元件捕獲）處理時，鍵盤不會自動關閉。
- `false` **_已棄用_**，請改用 `'never'`
- `true` **_已棄用_**，請改用 `'always'`

| Type                                                      | Default   |
| --------------------------------------------------------- | --------- |
| enum(`'always'`, `'never'`, `'handled'`, `false`, `true`) | `'never'` |

---

### `maintainVisibleContentPosition` <div class="label ios">iOS</div>

設定後，滾動視圖會調整滾動位置，使當前可見且位於或超過 `minIndexForVisible` 的第一個子元件保持位置不變。這適用於雙向載入內容的列表（例如聊天串），否則新訊息的加入可能導致滾動位置跳動。常見值為 0，但也可使用 1 等其他值來跳過不應保持位置的載入指示器或其他內容。

可選參數 `autoscrollToTopThreshold` 可在調整後，若使用者原本位於頂部閾值範圍內時，自動將內容滾動至頂部。這同樣適用於聊天類應用，希望新訊息能平滑滾入視野，但若使用者已向上滾動一段距離時，避免造成突兀的滾動。

注意事項 1：啟用此功能時在滾動視圖中重新排序元件可能會導致跳動和卡頓。此問題可修復，但目前暫無相關計劃。現階段請勿對使用此功能的滾動視圖或列表內容重新排序。

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

為 Android API 級別 21+ 啟用嵌套滾動。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `onContentSizeChange`

當 ScrollView 的可滾動內容視圖尺寸變化時呼叫。

處理函式會接收兩個參數：內容寬度與內容高度 `(contentWidth, contentHeight)`。

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

當動量滾動結束時呼叫（即 ScrollView 滑行至停止時的滾動）。

| Type     |
| -------- |
| function |

---

### `onScroll`

滾動期間每幀最多觸發一次。可透過 `scrollEventThrottle` 屬性控制事件觸發頻率。事件格式如下（所有值均為數字）：

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

當用戶開始拖動滾動視圖時觸發。

| Type     |
| -------- |
| function |

---

### `onScrollEndDrag`

當用戶停止拖動滾動視圖且視圖停止或開始滑動時觸發。

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

用於覆寫默認的過度滾動模式設定值。

可選值：

- `'auto'` - 僅當內容足夠長時才允許用戶過度滾動此視圖。
- `'always'` - 始終允許用戶過度滾動此視圖。
- `'never'` - 禁止用戶過度滾動此視圖。

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'always'`, `'never'`) | `'auto'` |

---

### `pagingEnabled`

設為 true 時，滾動視圖會在滾動時停止在視圖大小的整數倍位置。適用於實現水平分頁效果。

> 注意：Android 不支援垂直分頁。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `persistentScrollbar` <div class="label android">Android</div>

使滾動條在非使用狀態時保持顯示（不變透明）。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `pinchGestureEnabled` <div class="label ios">iOS</div>

設為 true 時，允許使用捏合手勢來縮放 ScrollView。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `refreshControl`

RefreshControl 組件，用於為 ScrollView 提供下拉刷新功能。僅適用於垂直 ScrollView（`horizontal` 屬性必須為 `false`）。

參見 [RefreshControl](refreshcontrol)。

| Type    |
| ------- |
| element |

---

### `removeClippedSubviews`

實驗性功能：設為 true 時，會將不可見的子視圖（其 `overflow` 值為 `hidden`）從原生父視圖中移除。可提升長列表的滾動性能。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollEnabled`

設為 false 時，禁止通過觸摸交互滾動視圖。

注意：仍可通過調用 `scrollTo` 方法滾動視圖。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `scrollEventThrottle`

控制滾動事件觸發頻率（單位毫秒）。較低數值可提高滾動位置追蹤的精確度，但可能因橋接通信量過大導致性能問題。由於 JS 運行循環與屏幕刷新率同步，1-16 之間的設定值實際效果無差異。若不需要精確追蹤滾動位置，建議調高此值以減少橋接通信量。默認值為 `0`，表示每次滾動視圖時僅觸發一次滾動事件。

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

用於記錄此滾動視圖性能的標籤。會強制啟用動量事件（參見 sendMomentumEvents）。預設情況下此屬性無效，需實作自定義原生 FpsListener 才能發揮作用。

| Type   |
| ------ |
| string |

---

### `scrollToOverflowEnabled` <div class="label ios">iOS</div>

設為 `true` 時，可通過程式控制滾動超出內容尺寸。

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

當設置 `snapToInterval` 時，`snapToAlignment` 會定義對齊方式與滾動視圖的關係。

可能的值：

- `'start'` 將對齊左側（水平）或頂部（垂直）。
- `'center'` 將對齊中心。
- `'end'` 將對齊右側（水平）或底部（垂直）。

| Type                                 | Default   |
| ------------------------------------ | --------- |
| enum(`'start'`, `'center'`, `'end'`) | `'start'` |

---

### `snapToEnd`

需與 `snapToOffsets` 配合使用。預設情況下，列表末端會作為吸附偏移點。設為 `false` 可禁用此行為，允許列表在末端與最後一個 `snapToOffsets` 偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToInterval`

設置後，滾動視圖會在 `snapToInterval` 值的倍數處停止。適用於子項目尺寸小於滾動視圖的分頁效果。通常與 `snapToAlignment` 和 `decelerationRate="fast"` 配合使用。會覆蓋較不靈活的 `pagingEnabled` 屬性。

| Type   |
| ------ |
| number |

---

### `snapToOffsets`

設置後，滾動視圖會在指定偏移點停止。適用於不同尺寸子項目的分頁效果（子項目尺寸小於滾動視圖）。通常與 `decelerationRate="fast"` 配合使用。會覆蓋 `pagingEnabled` 和 `snapToInterval` 屬性。

| Type            |
| --------------- |
| array of number |

---

### `snapToStart`

需與 `snapToOffsets` 配合使用。預設情況下，列表起始處會作為吸附偏移點。設為 `false` 可禁用此行為，允許列表在起始處與第一個 `snapToOffsets` 偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `stickyHeaderHiddenOnScroll`

當設為 `true` 時，滾動列表向下時會隱藏黏性標頭，向上滾動時則會固定在列表頂部。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `stickyHeaderIndices`

一個子元素索引陣列，決定哪些子元素在滾動時會固定在螢幕頂部。例如，傳入 `stickyHeaderIndices={[0]}` 會使第一個子元素固定在滾動視圖頂部。也可使用 [x,y,z] 格式讓多個項目在頂部時保持黏性。此屬性不支援與 `horizontal={true}` 同時使用。

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

> 注意：此函數簽名的特殊設計是由於歷史原因，它同時接受獨立參數作為選項物件的替代方案。由於存在歧義（y 在 x 之前），此用法已被棄用，不應繼續使用。

---

### `scrollToEnd()`

```tsx
scrollToEnd(options?: {animated?: boolean});
```

如果是垂直 ScrollView 則滾動到底部；如果是水平 ScrollView 則滾動到右側。

使用 `scrollToEnd({animated: true})` 可啟用平滑動畫滾動，`scrollToEnd({animated: false})` 則為立即滾動。若未傳入選項，`animated` 預設為 `true`。