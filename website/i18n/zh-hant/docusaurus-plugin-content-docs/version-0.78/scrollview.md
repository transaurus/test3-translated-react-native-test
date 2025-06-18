---
id: scrollview
title: ScrollView
---

封裝平台原生 ScrollView 並整合觸控鎖定「響應者」系統的元件。

請注意，ScrollView 必須具有固定高度才能正常運作，因為它們將無限高度的子元件裝載到有限容器中（透過滾動互動）。要限制 ScrollView 的高度，可以直接設定視圖高度（不建議）或確保所有父視圖都具有固定高度。忘記在視圖結構中向下傳遞 `{flex: 1}` 可能導致錯誤，此時使用元素檢查器能快速除錯。

目前尚不支援其他內含的響應者阻止此滾動視圖成為主響應者。

`<ScrollView>` 與 [`<FlatList>`](flatlist.md) 該如何選擇？

`ScrollView` 會一次性渲染所有子元件，但這會帶來效能上的缺點。

假設您需要顯示一個非常長的項目列表，內容可能橫跨多個畫面。一次性為所有內容建立 JS 元件和原生視圖（其中許多可能根本不會顯示），將導致渲染速度變慢和記憶體使用量增加。

這時 `FlatList` 就派上用場。`FlatList` 會延遲渲染項目，僅在項目即將出現時才進行渲染，並移除滾動出畫面的項目以節省記憶體和處理時間。

如果您需要渲染項目間的分隔線、多欄佈局、無限滾動載入，或是其他開箱即用的功能，`FlatList` 也非常方便。

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

用於渲染黏性標題的 React 元件，需與 `stickyHeaderIndices` 搭配使用。若您的黏性標題使用自訂變形效果（例如想讓列表具有可動畫顯示/隱藏的標題），可能需要設定此元件。若未提供元件，將使用預設的 [`ScrollViewStickyHeader`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/ScrollView/ScrollViewStickyHeader.js) 元件。

| Type               |
| ------------------ |
| component, element |

---

### `alwaysBounceHorizontal` <div class="label ios">iOS</div>

設為 true 時，即使內容小於滾動視圖本身，滾動視圖在到達水平邊界時仍會彈跳。

| Type | Default                                               |
| ---- | ----------------------------------------------------- |
| bool | `true` when `horizontal={true}`<hr/>`false` otherwise |

---

### `alwaysBounceVertical` <div class="label ios">iOS</div>

設為 true 時，即使內容小於滾動視圖本身，滾動視圖在到達垂直邊界時仍會彈跳。

| Type | Default                                             |
| ---- | --------------------------------------------------- |
| bool | `false` when `vertical={true}`<hr/>`true` otherwise |

---

### `automaticallyAdjustContentInsets` <div class="label ios">iOS</div>

控制 iOS 是否應自動調整位於導覽列或標籤列/工具列後方的滾動視圖的內容內嵌邊距。

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

控制 iOS 是否應自動調整滾動指示器的內邊距。詳見 Apple 的[屬性說明文件](https://developer.apple.com/documentation/uikit/uiscrollview/3198043-automaticallyadjustsscrollindica)。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bounces` <div class="label ios">iOS</div>

當設為 `true` 時，若內容沿滾動方向的尺寸大於滾動視圖，滾動視圖會在到達內容末端時彈跳。設為 `false` 時，即使 `alwaysBounce*` 屬性為 `true` 也會禁用所有彈跳效果。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `bouncesZoom` <div class="label ios">iOS</div>

當設為 `true` 時，手勢操作可讓縮放超出最小/最大值，並在手勢結束時動畫回彈至限制值；否則縮放不會超過限制範圍。

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

這些樣式會套用至包裹所有子視圖的滾動視圖內容容器。範例：

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

此屬性指定如何利用安全區域內邊距來調整滾動視圖的內容區域。僅限 iOS 11 以上版本。

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

此浮點數決定用戶鬆開手指後滾動視圖的減速速度。也可使用字串快捷參數 `"normal"` 和 `"fast"`，分別對應 iOS 底層的 `UIScrollViewDecelerationRateNormal` 和 `UIScrollViewDecelerationRateFast` 設定值。

- `'normal'`：iOS 為 0.998，Android 為 0.985
- `'fast'`：iOS 為 0.99，Android 為 0.9

| Type                               | Default    |
| ---------------------------------- | ---------- |
| enum(`'fast'`, `'normal'`), number | `'normal'` |

---

### `directionalLockEnabled` <div class="label ios">iOS</div>

設為 `true` 時，拖曳時 ScrollView 會嘗試鎖定僅允許垂直或水平滾動。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableIntervalMomentum`

當設定為 true 時，無論手勢速度多快，滾動視圖都會在釋放時根據滾動位置停在下一索引處。這可用於當頁面寬度小於水平滾動視圖寬度或垂直滾動視圖高度時的分頁效果。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `disableScrollViewPanResponder`

當設定為 true 時，會停用 ScrollView 上預設的 JS 手勢回應器，將 ScrollView 內部的觸控事件完全交由子元件控制。這在啟用 `snapToInterval` 時特別有用，因為該功能不符合常規觸控模式。若未啟用 `snapToInterval`，請勿在常規 ScrollView 使用情境中使用此屬性，否則可能導致滾動時發生非預期的觸控行為。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `endFillColor` <div class="label android">Android</div>

當滾動視圖佔用空間大於內容區域時，此屬性會以指定顏色填充剩餘空間，避免設定背景色造成不必要的過度繪製。此為進階優化選項，一般情況下不需使用。

| Type            |
| --------------- |
| [color](colors) |

---

### `fadingEdgeLength` <div class="label android">Android</div>

淡化滾動內容邊緣效果。

若值大於 0，會根據當前滾動方向與位置顯示淡出邊緣，提示尚有更多內容可瀏覽。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `horizontal`

當設定為 true 時，滾動視圖的子元件會水平排列成行，而非垂直排列成列。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `indicatorStyle` <div class="label ios">iOS</div>

滾動指示器的樣式。

- `'default'` 與 `black` 相同。
- `'black'`，黑色滾動指示器，適合淺色背景。
- `'white'`，白色滾動指示器，適合深色背景。

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

- `'none'`，拖曳時不關閉鍵盤。
- `'on-drag'`，開始拖曳時關閉鍵盤。

**僅限 iOS**

- `'interactive'`，鍵盤會隨拖曳動作互動式關閉，並與觸控同步移動，向上拖曳可取消關閉。Android 不支援此模式，行為將與 `'none'` 相同。

| Type                                                                                                                                                            | Default  |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'on-drag'`) <div className="label android">Android</div><hr />enum(`'none'`, `'on-drag'`, `'interactive'`) <div className="label ios">iOS</div> | `'none'` |

---

### `keyboardShouldPersistTaps`

決定點擊後鍵盤是否應保持顯示狀態。

- `'never'` 當鍵盤彈出時，點擊聚焦的文字輸入框外部會關閉鍵盤。此時子元件不會接收到點擊事件。
- `'always'` 鍵盤不會自動關閉，且滾動視圖不會攔截點擊事件，但滾動視圖的子元件可以捕獲點擊。
- `'handled'` 當點擊事件被子元件處理（或被父元件捕獲）時，鍵盤不會自動關閉。
- `false` **_已棄用_**，請改用 `'never'`
- `true` **_已棄用_**，請改用 `'always'`

| Type                                                      | Default   |
| --------------------------------------------------------- | --------- |
| enum(`'always'`, `'never'`, `'handled'`, `false`, `true`) | `'never'` |

---

### `maintainVisibleContentPosition`

設定此屬性後，滾動視圖會調整滾動位置，使當前可見且位於或超過 `minIndexForVisible` 的第一個子元件保持原位。適用於雙向載入內容的列表（如聊天對話），避免新訊息導致滾動位置跳動。通常設為 0，也可設為 1 以跳過不應保持位置的載入動畫等內容。

可選參數 `autoscrollToTopThreshold` 可在調整後，若使用者原本位於頂部閾值範圍內時，自動將內容滾動至頂部。這同樣適用於聊天場景——希望新訊息平滑載入，但避免在使用者手動滾動後造成干擾。

注意事項 1：啟用此功能時重新排序滾動視圖內容可能導致畫面跳動。目前無修復計劃，請勿對使用此功能的滾動視圖或列表進行內容重排序。

注意事項 2：底層使用 `contentOffset` 和 `frame.origin` 計算可見性，不考慮遮罩、變形等複雜因素對「可見」狀態的影響。

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

處理函數接收兩個參數：內容寬度與高度 `(contentWidth, contentHeight)`。

其實現基於附加在內容容器上的 onLayout 處理器。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollBegin`

當慣性滾動開始時觸發（即滾動視圖開始滑行時的滾動）。

| Type     |
| -------- |
| function |

---

### `onMomentumScrollEnd`

當慣性滾動結束時觸發（即滾動視圖滑行停止時）。

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

當用戶停止拖動滾動視圖且其停止或開始滑動時調用。

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

用於覆寫默認的過度滾動模式值。

可能的值：

- `'auto'` - 僅當內容足夠大以有意義地滾動時，才允許用戶對此視圖進行過度滾動。
- `'always'` - 始終允許用戶對此視圖進行過度滾動。
- `'never'` - 永不允許用戶對此視圖進行過度滾動。

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'always'`, `'never'`) | `'auto'` |

---

### `pagingEnabled`

當設為 true 時，滾動視圖在滾動時會停在滾動視圖大小的倍數位置。這可用於水平分頁。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `persistentScrollbar` <div class="label android">Android</div>

使滾動條在不使用時不會變為透明。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `pinchGestureEnabled` <div class="label ios">iOS</div>

當設為 true 時，ScrollView 允許使用捏合手勢進行縮放。

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

實驗性：當設為 true 時，離屏的子視圖（其 `overflow` 值為 `hidden`）將從其原生父視圖中移除。這可以提高長列表的滾動性能。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `scrollEnabled`

當設為 false 時，無法通過觸摸交互滾動視圖。

請注意，視圖仍可通過調用 `scrollTo` 來滾動。

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

滾動視圖指示器從滾動視圖邊緣的內嵌量。通常應設為與 `contentInset` 相同的值。

| Type                                                                 | Default                                  |
| -------------------------------------------------------------------- | ---------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` | `{top: 0, left: 0, bottom: 0, right: 0}` |

---

### `scrollPerfTag` <div class="label android">Android</div>

用於記錄此滾動視圖性能的標籤。將強制開啟動量事件（參見sendMomentumEvents）。預設情況下此功能無效，需實作自定義原生FpsListener才能發揮作用。

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

當設置`snapToInterval`時，`snapToAlignment`定義對齊方式與滾動視圖的關係。

可能的值：

- `'start'` 將對齊左側（水平）或頂部（垂直）。
- `'center'` 將對齊中心。
- `'end'` 將對齊右側（水平）或底部（垂直）。

| Type                                 | Default   |
| ------------------------------------ | --------- |
| enum(`'start'`, `'center'`, `'end'`) | `'start'` |

---

### `snapToEnd`

與`snapToOffsets`配合使用。預設情況下，列表末端會作為吸附點。設為`false`可禁用此行為，允許列表在末端與最後一個`snapToOffsets`偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `snapToInterval`

設置後，滾動視圖會在`snapToInterval`值的倍數處停止。適用於子項尺寸小於滾動視圖的分頁效果。通常與`snapToAlignment`和`decelerationRate="fast"`配合使用。會覆蓋較少配置的`pagingEnabled`屬性。

| Type   |
| ------ |
| number |

---

### `snapToOffsets`

設置後，滾動視圖會在指定偏移量處停止。適用於不同尺寸子項的分頁效果（子項尺寸小於滾動視圖）。通常與`decelerationRate="fast"`配合使用。會覆蓋較少配置的`pagingEnabled`和`snapToInterval`屬性。

| Type            |
| --------------- |
| array of number |

---

### `snapToStart`

與`snapToOffsets`配合使用。預設情況下，列表起始處會作為吸附點。設為`false`可禁用此行為，允許列表在起始處與第一個`snapToOffsets`偏移點之間自由滾動。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `stickyHeaderHiddenOnScroll`

設為`true`時，向下滾動列表會隱藏黏性標頭，向上滾動時會固定在列表頂部。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `stickyHeaderIndices`

一個子元素索引陣列，決定哪些子元素在滾動時會固定在畫面頂部。例如傳入`stickyHeaderIndices={[0]}`會讓第一個子元素固定在滾動視圖頂部。也可以使用[x,y,z]這樣的格式讓多個元素在頂部時保持固定。此屬性不支援與`horizontal={true}`同時使用。

| Type            |
| --------------- |
| array of number |

---

### `zoomScale` <div class="label ios">iOS</div>

當前滾動視圖內容的縮放比例。

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

滾動到指定的x、y偏移位置，可選擇立即執行或使用平滑動畫。

**範例：**

`scrollTo({x: 0, y: 0, animated: true})`

> 注意：這個奇怪的函數簽名是由於歷史原因，該函數也接受獨立參數作為選項物件的替代方案。由於存在歧義（y在x之前），此用法已被棄用，不應繼續使用。

---

### `scrollToEnd()`

```tsx
scrollToEnd(options?: {animated?: boolean});
```

如果是垂直ScrollView則滾動到底部。如果是水平ScrollView則滾動到右側。

使用`scrollToEnd({animated: true})`進行平滑動畫滾動，`scrollToEnd({animated: false})`則立即滾動。若未傳入選項，`animated`預設為`true`。