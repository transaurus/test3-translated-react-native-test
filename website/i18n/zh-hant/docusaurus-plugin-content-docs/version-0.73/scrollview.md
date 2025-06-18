---
id: scrollview
title: ScrollView
---

封裝平台原生 ScrollView 並整合觸控鎖定「響應者」系統的元件。

請注意，ScrollView 必須具有固定高度才能正常運作，因為它們將無限高度的子元件裝載到有限容器中（透過滾動互動）。若要限制 ScrollView 的高度，可以直接設定視圖高度（不建議）或確保所有父視圖都具有固定高度。忘記將 `{flex: 1}` 向下傳遞至視圖結構可能導致錯誤，此時元素檢查器能快速除錯。

目前尚不支援其他內含的響應者阻止此滾動視圖成為主響應者。

`<ScrollView>` 與 [`<FlatList>`](flatlist.md) 該如何選擇？

`ScrollView` 會立即渲染所有 React 子元件，但這會帶來效能負擔。

假設您需要顯示非常長的項目列表，可能橫跨多個螢幕內容。一次性為所有內容建立 JS 元件和原生視圖（其中許多可能根本不會顯示），將導致渲染速度變慢和記憶體使用量增加。

此時 `FlatList` 就能發揮作用。`FlatList` 會惰性渲染項目（僅在即將顯示時才渲染），並移除滾出螢幕外的項目以節省記憶體和處理時間。

若您需要渲染項目間分隔線、多欄佈局、無限滾動載入或其他開箱即用功能，`FlatList` 也十分便利。

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

用於渲染黏性標題的 React 元件，需與 `stickyHeaderIndices` 搭配使用。若黏性標題需使用自訂變形效果（例如帶有動畫效果或可隱藏標題的列表），則需設定此元件。若未提供元件，將使用預設的 [`ScrollViewStickyHeader`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/ScrollView/ScrollViewStickyHeader.js) 元件。

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

控制當鍵盤尺寸變化時，ScrollView 是否應自動調整其 `contentInset` 與 `scrollViewInsets`。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `automaticallyAdjustsScrollIndicatorInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整滾動指示器的內邊距。詳見蘋果官方[屬性說明文件](https://developer.apple.com/documentation/uikit/uiscrollview/3198043-automaticallyadjustsscrollindica)。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bounces` <div class="label ios">iOS</div>

當設為 true 時，若內容沿滾動方向超出滾動視圖範圍，滾動視圖會在邊界處產生彈跳效果。設為 false 時，即使 `alwaysBounce*` 屬性為 true 也會禁用所有彈跳效果。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bouncesZoom` <div class="label ios">iOS</div>

當設為 true 時，手勢操作可暫時突破最小/最大縮放限制，並在手勢結束時動畫回彈至限制值；否則縮放比例絕不會超出限制範圍。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `canCancelContentTouches` <div class="label ios">iOS</div>

設為 false 時，一旦開始追蹤觸控點，即使觸控點移動也不會觸發拖拽操作。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `centerContent` <div class="label ios">iOS</div>

設為 true 時，當內容小於滾動視圖邊界時會自動居中；若內容大於滾動視圖則此屬性無效。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `contentContainerStyle`

這些樣式將應用於包裹所有子視圖的滾動視圖內容容器。範例：

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

設定滾動視圖內容與滾動視圖邊緣的內縮距離。

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

用於手動設定初始滾動偏移量。

| Type  | Default        |
| ----- | -------------- |
| Point | `{x: 0, y: 0}` |

---

### `decelerationRate`

此浮點數決定用戶手指離開後滾動視圖的減速快慢。也可使用快捷字符串 "normal" 和 "fast"，分別對應 iOS 底層設置的 UIScrollViewDecelerationRateNormal 和 UIScrollViewDecelerationRateFast。

- `'normal'` iOS 為 0.998，Android 為 0.985
- `'fast'` iOS 為 0.99，Android 為 0.9

| Type                               | Default    |
| ---------------------------------- | ---------- |
| enum(`'fast'`, `'normal'`), number | `'normal'` |

---

### `directionalLockEnabled` <div class="label ios">iOS</div>

設為 true 時，滾動視圖在拖拽過程中會嘗試鎖定單一方向（垂直或水平）的滾動。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableIntervalMomentum`

當設定為 true 時，無論手勢速度多快，滾動視圖都會在釋放時相對於滾動位置的下一個索引處停止。這可用於當頁面寬度小於水平 ScrollView 或高度小於垂直 ScrollView 時的分頁效果。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableScrollViewPanResponder`

當設定為 true 時，ScrollView 上預設的 JS 手勢響應器會被停用，ScrollView 內部的觸控事件將完全由其子元件控制。這在啟用 `snapToInterval` 時特別有用，因為該功能不符合常規觸控模式。若未啟用 `snapToInterval`，請勿在常規 ScrollView 使用情境中啟用此屬性，否則可能導致滾動時發生非預期觸控行為。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `endFillColor` <div class="label android">Android</div>

當滾動視圖佔用空間大於內容區域時，此屬性會以指定顏色填充剩餘空間，避免設定背景色造成不必要的過度繪製。此為進階優化選項，一般情境無需使用。

| Type            |
| --------------- |
| [color](colors) |

---

### `fadingEdgeLength` <div class="label android">Android</div>

使滾動內容邊緣產生漸淡效果。

若值大於 `0`，系統會根據當前滾動方向與位置顯示漸淡邊緣，提示尚有更多內容可瀏覽。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `horizontal`

設定為 `true` 時，滾動視圖的子元件會水平排列成行，而非垂直排列成列。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `indicatorStyle` <div class="label ios">iOS</div>

滾動指示器的樣式。

- `'default'` 等同 `black`
- `'black'` 黑色滾動指示器，適合淺色背景
- `'white'` 白色滾動指示器，適合深色背景

| Type                                    | Default     |
| --------------------------------------- | ----------- |
| enum(`'default'`, `'black'`, `'white'`) | `'default'` |

---

### `invertStickyHeaders`

設定黏性標頭是否應固定在 ScrollView 底部而非頂部。通常與反向滾動的 ScrollView 搭配使用。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `keyboardDismissMode`

決定鍵盤是否因拖曳動作而關閉。

- `'none'` 拖曳時不關閉鍵盤
- `'on-drag'` 開始拖曳時關閉鍵盤

**僅限 iOS**

- `'interactive'` 鍵盤會隨拖曳動作互動式關閉，且觸點移動會同步影響鍵盤位置，向上拖曳可取消關閉。Android 不支援此模式，行為將與 `'none'` 相同。

| Type                                                                                                                                                            | Default  |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'on-drag'`) <div className="label android">Android</div><hr />enum(`'none'`, `'on-drag'`, `'interactive'`) <div className="label ios">iOS</div> | `'none'` |

---

### `keyboardShouldPersistTaps`

決定點擊後鍵盤應保持顯示的時機。

- `'never'` 當鍵盤開啟時，點擊聚焦的文字輸入框外部會關閉鍵盤。此時子元件不會接收到點擊事件。
- `'always'` 鍵盤不會自動關閉，且滾動視圖不會攔截點擊事件，但滾動視圖的子元件可以捕捉點擊。
- `'handled'` 當點擊事件被子元件（或被上層元件捕獲）處理時，鍵盤不會自動關閉。
- `false`，**_已棄用_**，請改用 `'never'`
- `true`，**_已棄用_**，請改用 `'always'`

| Type                                                      | Default   |
| --------------------------------------------------------- | --------- |
| enum(`'always'`, `'never'`, `'handled'`, `false`, `true`) | `'never'` |

---

### `maintainVisibleContentPosition`

設定後，滾動視圖會調整滾動位置，使當前可見且位於或超過 `minIndexForVisible` 的第一個子元件保持原位。這適用於雙向載入內容的列表（如聊天串），避免新內容導致滾動位置跳動。常見值為 0，但也可設為 1 以跳過不應保持位置的載入指示器等內容。

可選參數 `autoscrollToTopThreshold` 可在調整後，若使用者原本位於頂部閾值範圍內時，自動將內容滾動至頂部。這同樣適用於聊天場景，既讓新訊息平滑出現，又避免使用者手動滾動後造成干擾。

注意事項 1：啟用此功能時重新排序滾動視圖內容可能導致跳動問題。雖可修復，但目前無相關計劃。現階段請勿對使用此功能的滾動視圖或列表重新排序。

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

當 ScrollView 的可滾動內容視圖尺寸變化時觸發。

處理函式接收兩個參數：內容寬度與高度 `(contentWidth, contentHeight)`。

其實現方式是透過附加到 ScrollView 渲染的內容容器上的 onLayout 處理器。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollBegin`

當動量滾動開始時觸發（即 ScrollView 開始滑行時的滾動）。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollEnd`

當動量滾動結束時觸發（即 ScrollView 滑行至停止時的滾動）。

| Type     |
| -------- |
| function |

---

### `onScroll`

滾動期間每幀最多觸發一次。可透過 `scrollEventThrottle` 屬性控制事件頻率。事件格式如下（所有值均為數字）：

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

用於覆寫過度滾動模式的預設值。

可能的值：

- `'auto'` - 僅當內容足夠大時才允許用戶過度滾動此視圖。
- `'always'` - 始終允許用戶過度滾動此視圖。
- `'never'` - 禁止用戶過度滾動此視圖。

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'always'`, `'never'`) | `'auto'` |

---

### `pagingEnabled`

設為 true 時，滾動視圖會在滾動時停在視圖大小的整數倍位置。適用於水平分頁效果。

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

設為 true 時，允許使用捏合手勢縮放 ScrollView 內容。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `refreshControl`

用於實現下拉刷新功能的 RefreshControl 組件。僅適用於垂直 ScrollView（`horizontal` 屬性必須為 `false`）。

參見 [RefreshControl](refreshcontrol)。

| Type    |
| ------- |
| element |

---

### `removeClippedSubviews`

實驗性功能：設為 true 時，會將不可見的子視圖（其 `overflow` 值為 `hidden`）從原生父視圖中移除，可提升長列表的滾動效能。

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

控制滾動事件觸發頻率（單位毫秒）。數值越低，滾動位置追蹤越精確，但可能因橋接數據量過大影響效能。1-16 之間的數值實際效果相同（因 JS 運行循環與屏幕刷新率同步）。若不需要精確追蹤，可提高此值以減少橋接數據量。預設值 `0` 表示每次滾動視圖時僅觸發一次事件。

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

用於記錄此滾動視圖性能的標籤。會強制啟用動量事件（參見 sendMomentumEvents）。此屬性預設無作用，需實作自訂的原生 FpsListener 才能發揮效用。

| Type   |
| ------ |
| string |

---

### `scrollToOverflowEnabled` <div class="label ios">iOS</div>

設為 `true` 時，可透過程式將滾動視圖滾動至超出其內容大小的位置。

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

當設定 `snapToInterval` 時，`snapToAlignment` 會定義對齊滾動視圖的吸附關係。

可能的值：

- `'start'` 會將吸附點對齊左側（水平）或頂部（垂直）。
- `'center'` 會將吸附點對齊中心。
- `'end'` 會將吸附點對齊右側（水平）或底部（垂直）。

| Type                                 | Default   |
| ------------------------------------ | --------- |
| enum(`'start'`, `'center'`, `'end'`) | `'start'` |

---

### `snapToEnd`

需與 `snapToOffsets` 搭配使用。預設情況下，列表末端會計為吸附偏移點。設為 `false` 可禁用此行為，允許列表在末端與最後一個 `snapToOffsets` 偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToInterval`

設定後，會使滾動視圖在 `snapToInterval` 值的倍數位置停止。適用於分頁瀏覽小於滾動視圖尺寸的子項目。通常與 `snapToAlignment` 和 `decelerationRate="fast"` 組合使用。會覆蓋較不靈活的 `pagingEnabled` 屬性。

| Type   |
| ------ |
| number |

---

### `snapToOffsets`

設定後，會使滾動視圖在指定的偏移點停止。適用於分頁瀏覽尺寸各異且小於滾動視圖的子項目。通常與 `decelerationRate="fast"` 組合使用。會覆蓋較不靈活的 `pagingEnabled` 和 `snapToInterval` 屬性。

| Type            |
| --------------- |
| array of number |

---

### `snapToStart`

需與 `snapToOffsets` 搭配使用。預設情況下，列表起始處會計為吸附偏移點。設為 `false` 可禁用此行為，允許列表在起始處與第一個 `snapToOffsets` 偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `stickyHeaderHiddenOnScroll`

當設定為 `true` 時，黏性標頭會在向下滾動列表時隱藏，並在向上滾動時停靠在列表頂部。

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

> 注意：奇怪的函數簽名是由於歷史原因，該函數也接受獨立參數作為選項物件的替代方案。由於存在歧義（y 在 x 之前），此用法已被棄用，不應繼續使用。

---

### `scrollToEnd()`

```tsx
scrollToEnd(options?: {animated?: boolean});
```

如果是垂直 ScrollView 則滾動到底部；如果是水平 ScrollView 則滾動到右側。

使用 `scrollToEnd({animated: true})` 可實現平滑動畫滾動，使用 `scrollToEnd({animated: false})` 則立即滾動。若未傳入選項，`animated` 預設為 `true`。