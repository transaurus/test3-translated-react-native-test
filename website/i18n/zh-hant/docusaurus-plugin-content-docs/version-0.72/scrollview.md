---
id: scrollview
title: ScrollView
---

一個封裝平台原生 ScrollView 的組件，同時提供與觸控鎖定「響應者」系統的整合功能。

請注意，ScrollView 必須具有固定高度才能正常運作，因為它們將無限高度的子內容裝載於有限容器中（透過滾動互動）。要限制 ScrollView 的高度，可直接設定視圖高度（不建議）或確保所有父視圖都具有固定高度。忘記沿視圖結構向下傳遞 `{flex: 1}` 可能導致錯誤，此時元素檢查工具能快速除錯。

目前尚不支援阻止其他內嵌響應者搶佔此滾動視圖的響應權。

`<ScrollView>` 與 [`<FlatList>`](flatlist.md) 該如何選擇？

`ScrollView` 會一次性渲染所有子組件，但這會帶來效能負擔。

假設您需要顯示非常長的列表內容，可能橫跨多個螢幕。一次性為所有內容建立 JS 組件和原生視圖（其中多數可能根本不會顯示），將導致渲染速度下降和記憶體用量增加。

此時 `FlatList` 便能發揮作用。`FlatList` 會惰性渲染項目（僅在即將顯示時才處理），並移除滾出螢幕外的項目以節省記憶體和運算資源。

若您需要渲染項目間分隔線、多欄佈局、無限滾動載入等功能，`FlatList` 也提供開箱即用的支援。

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

# 參考文獻

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view#props)。

---

### `StickyHeaderComponent`

用於渲染黏性標頭的 React 組件，需與 `stickyHeaderIndices` 搭配使用。若您的黏性標頭需使用自訂變形效果（例如帶有動畫效果或可隱藏標頭），則需設定此組件。若未提供組件，將使用預設的 [`ScrollViewStickyHeader`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/ScrollView/ScrollViewStickyHeader.js) 組件。

| Type               |
| ------------------ |
| component, element |

---

### `alwaysBounceHorizontal` <div class="label ios">iOS</div>

設為 true 時，即使內容小於滾動視圖本身，水平滾動到邊界時仍會彈跳。

| Type | Default                                               |
| ---- | ----------------------------------------------------- |
| bool | `true` when `horizontal={true}`<hr/>`false` otherwise |

---

### `alwaysBounceVertical` <div class="label ios">iOS</div>

設為 true 時，即使內容小於滾動視圖本身，垂直滾動到邊界時仍會彈跳。

| Type | Default                                             |
| ---- | --------------------------------------------------- |
| bool | `false` when `vertical={true}`<hr/>`true` otherwise |

---

### `automaticallyAdjustContentInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整位於導覽列或標籤列/工具列後方之滾動視圖的內容內嵌邊距。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `automaticallyAdjustKeyboardInsets` <div class="label ios">iOS</div>

控制當鍵盤尺寸變化時，ScrollView 是否應自動調整其 `contentInset` 與 `scrollViewInsets`。

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

當設為 `true` 時，若內容超出滾動方向軸線的範圍，滾動視圖會在到達內容邊界時彈跳。設為 `false` 時，即使 `alwaysBounce*` 屬性為 `true` 也會禁用所有彈跳效果。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bouncesZoom` <div class="label ios">iOS</div>

當設為 `true` 時，手勢操作可驅動縮放超出最小/最大值限制，並在手勢結束時動畫回彈至限制值；否則縮放比例不會超過限制範圍。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `canCancelContentTouches` <div class="label ios">iOS</div>

設為 `false` 時，一旦開始追蹤觸控，即使觸控點移動也不會嘗試拖曳。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `centerContent` <div class="label ios">iOS</div>

設為 `true` 時，當內容小於滾動視圖邊界會自動置中；若內容大於滾動視圖則此屬性無效。

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

設定滾動視圖內容與其邊緣的內縮距離。

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `contentInsetAdjustmentBehavior` <div class="label ios">iOS</div>

此屬性指定如何利用安全區域邊距來調整滾動視圖的內容區域。僅適用於 iOS 11 及以上版本。

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

此浮點數決定用戶放開手指後滾動視圖的減速快慢。亦可使用字串快捷參數 `"normal"` 和 `"fast"`，分別對應 iOS 底層設定 `UIScrollViewDecelerationRateNormal` 與 `UIScrollViewDecelerationRateFast`。

- `'normal'`：iOS 為 0.998，Android 為 0.985。
- `'fast'`：iOS 為 0.99，Android 為 0.9。

| Type                               | Default    |
| ---------------------------------- | ---------- |
| enum(`'fast'`, `'normal'`), number | `'normal'` |

---

### `directionalLockEnabled` <div class="label ios">iOS</div>

設為 `true` 時，拖曳時滾動視圖會鎖定僅允許垂直或水平方向的滾動。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableIntervalMomentum`

當設為 true 時，無論手勢速度多快，滾動視圖都會在釋放時相對於當前滾動位置的下一個索引處停止。這可用於實現分頁效果，尤其當頁面寬度小於水平滾動視圖寬度或頁面高度小於垂直滾動視圖高度時。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableScrollViewPanResponder`

設為 true 時，會禁用滾動視圖上預設的 JS 手勢響應器，將滾動視圖內部的觸控事件完全交由子元件處理。此屬性特別適用於啟用 `snapToInterval` 的情況，因為該模式下觸控行為不符合常規模式。若未啟用 `snapToInterval`，請勿在常規滾動視圖中使用此屬性，否則可能導致滾動時出現非預期觸控行為。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `endFillColor` <div class="label android">Android</div>

當滾動視圖的空間大於內容區域時，此屬性會用指定顏色填充剩餘空間，避免設置背景色造成不必要的過度繪製。這屬於進階優化手段，一般情況下無需使用。

| Type            |
| --------------- |
| [color](colors) |

---

### `fadingEdgeLength` <div class="label android">Android</div>

使滾動內容邊緣產生漸隱效果。

若值大於 0，會根據當前滾動方向與位置顯示漸隱邊緣，提示尚有更多內容可供瀏覽。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `horizontal`

設為 true 時，滾動視圖的子元件會水平排列成行，而非垂直排列成列。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `indicatorStyle` <div class="label ios">iOS</div>

滾動指示器的樣式。

- `'default'` 等同於 `black`
- `'black'` 黑色滾動指示器，適用於淺色背景
- `'white'` 白色滾動指示器，適用於深色背景

| Type                                    | Default     |
| --------------------------------------- | ----------- |
| enum(`'default'`, `'black'`, `'white'`) | `'default'` |

---

### `invertStickyHeaders`

控制黏性標頭是否固定在滾動視圖底部而非頂部，通常與反轉滾動視圖搭配使用。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `keyboardDismissMode`

決定拖曳行為是否觸發鍵盤收起。

- `'none'` 拖曳時不收起鍵盤
- `'on-drag'` 開始拖曳時立即收起鍵盤

**僅限 iOS**

- `'interactive'` 鍵盤會隨拖曳手勢互動式收起，向上拖曳可取消收起動作。Android 不支援此模式，實際行為等同於 `'none'`

| Type                                                                                                                                                            | Default  |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'on-drag'`) <div className="label android">Android</div><hr />enum(`'none'`, `'on-drag'`, `'interactive'`) <div className="label ios">iOS</div> | `'none'` |

---

### `keyboardShouldPersistTaps`

決定點擊後鍵盤是否保持顯示狀態。

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

設定此屬性後，滾動視圖會調整滾動位置，使當前可見且位於或超過 `minIndexForVisible` 的第一個子元件保持位置不變。這適用於雙向載入內容的列表（例如聊天串），避免新訊息進入時導致滾動位置跳動。常見值為 0，但也可設為 1 以跳過載入指示器等不需固定位置的內容。

可選參數 `autoscrollToTopThreshold` 可在調整位置後，若使用者原本位於頂部閾值範圍內時，自動將內容滾動至頂部。這同樣適用於聊天式應用，讓新訊息能平滑載入，但避免在使用者已滾動較遠時造成干擾性大幅滾動。

注意事項 1：啟用此功能時重新排序滾動視圖中的元件可能導致跳動問題。此問題可修復，但目前暫無修復計畫。現階段請勿對使用此功能的滾動視圖或列表進行內容重新排序。

注意事項 2：此功能透過原生代碼中的 `contentOffset` 和 `frame.origin` 計算可見性。遮擋、變形等複雜因素不會被納入內容是否「可見」的判斷。

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

為 Android API 等級 21+ 啟用嵌套滾動功能。

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

可能的值：

- `'auto'` - 僅當內容足夠長時允許用戶過度滾動此視圖。
- `'always'` - 始終允許用戶過度滾動此視圖。
- `'never'` - 禁止用戶過度滾動此視圖。

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'always'`, `'never'`) | `'auto'` |

---

### `pagingEnabled`

設為 true 時，滾動視圖會在滾動時停在自身尺寸的整數倍位置。可用於實現水平分頁效果。

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

設為 true 時，允許使用捏合手勢來縮放 ScrollView 內容。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `refreshControl`

用於為 ScrollView 提供下拉刷新功能的 RefreshControl 組件。僅適用於垂直 ScrollView（必須設為 `horizontal={false}`）。

參見 [RefreshControl](refreshcontrol)。

| Type    |
| ------- |
| element |

---

### `removeClippedSubviews`

實驗性功能：設為 true 時，會將不可見的子視圖（其 `overflow` 值為 `hidden`）從原生父視圖中移除，可提升長列表的滾動性能。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollEnabled`

設為 false 時禁止通過觸摸交互滾動視圖。

注意：仍可通過調用 `scrollTo` 方法強制滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `scrollEventThrottle`

控制滾動事件觸發頻率（單位毫秒）。較低值可提高滾動位置追蹤精度，但可能因橋接數據量過大影響性能。1-16 之間的設定值實際效果相同（因 JS 運行循環與屏幕刷新率同步）。若不需要精確追蹤，建議設較高值以減少橋接數據量。默認值 0 表示每次滾動只觸發一次事件。

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

用於記錄此滾動視圖性能的標籤。會強制啟用動量事件（參見 sendMomentumEvents）。預設情況下此功能無效，需實作自訂原生 FpsListener 才能發揮作用。

| Type   |
| ------ |
| string |

---

### `scrollToOverflowEnabled` <div class="label ios">iOS</div>

設為 `true` 時，可通過程式控制將滾動視圖滾動超出其內容尺寸。

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

當設置 `snapToInterval` 時，`snapToAlignment` 會定義對齊滾動視圖的吸附關係。

可能的值：

- `'start'` 將吸附點對齊左側（水平）或頂部（垂直）。
- `'center'` 將吸附點對齊中心。
- `'end'` 將吸附點對齊右側（水平）或底部（垂直）。

| Type                                 | Default   |
| ------------------------------------ | --------- |
| enum(`'start'`, `'center'`, `'end'`) | `'start'` |

---

### `snapToEnd`

與 `snapToOffsets` 配合使用。預設情況下，列表末端會計為吸附偏移點。設為 `false` 可禁用此行為，允許列表在末端與最後一個 `snapToOffsets` 偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToInterval`

設置後，滾動視圖會在 `snapToInterval` 值的倍數處停止。可用於分頁瀏覽尺寸小於滾動視圖的子項。通常與 `snapToAlignment` 和 `decelerationRate="fast"` 搭配使用。會覆蓋靈活性較低的 `pagingEnabled` 屬性。

| Type   |
| ------ |
| number |

---

### `snapToOffsets`

設置後，滾動視圖會在定義的偏移點處停止。可用於分頁瀏覽尺寸各異且小於滾動視圖的子項。通常與 `decelerationRate="fast"` 搭配使用。會覆蓋靈活性較低的 `pagingEnabled` 和 `snapToInterval` 屬性。

| Type            |
| --------------- |
| array of number |

---

### `snapToStart`

與 `snapToOffsets` 配合使用。預設情況下，列表起始處會計為吸附偏移點。設為 `false` 可禁用此行為，允許列表在起始處與第一個 `snapToOffsets` 偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `stickyHeaderHiddenOnScroll`

設為 `true` 時，當向下滾動列表時，黏性標頭會隱藏；向上滾動時，它會固定在列表頂部。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `stickyHeaderIndices`

一個子元素索引陣列，決定哪些子元素在滾動時會固定在螢幕頂部。例如，傳入 `stickyHeaderIndices={[0]}` 會使第一個子元素固定在滾動視圖頂部。也可以使用 [x,y,z] 這樣的格式讓多個項目在頂部時保持黏性。此屬性不支援與 `horizontal={true}` 同時使用。

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

> 注意：奇怪的函數簽名是由於歷史原因，該函數也接受單獨的參數作為選項物件的替代方案。由於歧義性（y 在 x 之前），此用法已被棄用，不應再使用。

---

### `scrollToEnd()`

```tsx
scrollToEnd(options?: {animated?: boolean});
```

如果是垂直 ScrollView，則滾動到底部；如果是水平 ScrollView，則滾動到右側。

使用 `scrollToEnd({animated: true})` 進行平滑動畫滾動，使用 `scrollToEnd({animated: false})` 立即滾動。如果未傳入選項，`animated` 預設為 `true`。