---
id: virtualizedlist
title: VirtualizedList
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

此為更便利的 [`<FlatList>`](flatlist.md) 和 [`<SectionList>`](sectionlist.md) 元件的基礎實作，這些元件也有更完整的文件說明。通常僅在需要比 [`FlatList`](flatlist.md) 提供更高彈性時才使用，例如需搭配不可變數據而非普通陣列的情況。

虛擬化技術透過維護一個有限的可見項目渲染窗口，並將窗口外的項目替換為適當大小的空白空間，大幅改善大型列表的記憶體消耗與效能表現。此窗口會根據滾動行為動態調整，遠離可見區域的項目會以低優先級（在運行中的互動之後）漸進式渲染，而靠近可見區域的項目則以高優先級渲染，以最小化出現空白空間的可能性。

## 範例

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=VirtualizedListExample&ext=js
import React from 'react';
import {
  SafeAreaView,
  View,
  VirtualizedList,
  StyleSheet,
  Text,
  StatusBar,
} from 'react-native';

const getItem = (_data, index) => ({
  id: Math.random().toString(12).substring(0),
  title: `Item ${index + 1}`,
});

const getItemCount = _data => 50;

const Item = ({title}) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);

const App = () => {
  return (
    <SafeAreaView style={styles.container}>
      <VirtualizedList
        initialNumToRender={4}
        renderItem={({item}) => <Item title={item.title} />}
        keyExtractor={item => item.id}
        getItemCount={getItemCount}
        getItem={getItem}
      />
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight,
  },
  item: {
    backgroundColor: '#f9c2ff',
    height: 150,
    justifyContent: 'center',
    marginVertical: 8,
    marginHorizontal: 16,
    padding: 20,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=VirtualizedListExample&ext=tsx
import React from 'react';
import {
  SafeAreaView,
  View,
  VirtualizedList,
  StyleSheet,
  Text,
  StatusBar,
} from 'react-native';

type ItemData = {
  id: string;
  title: string;
};

const getItem = (_data: unknown, index: number): ItemData => ({
  id: Math.random().toString(12).substring(0),
  title: `Item ${index + 1}`,
});

const getItemCount = (_data: unknown) => 50;

type ItemProps = {
  title: string;
};

const Item = ({title}: ItemProps) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);

const App = () => {
  return (
    <SafeAreaView style={styles.container}>
      <VirtualizedList
        initialNumToRender={4}
        renderItem={({item}) => <Item title={item.title} />}
        keyExtractor={item => item.id}
        getItemCount={getItemCount}
        getItem={getItem}
      />
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight,
  },
  item: {
    backgroundColor: '#f9c2ff',
    height: 150,
    justifyContent: 'center',
    marginVertical: 8,
    marginHorizontal: 16,
    padding: 20,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

</TabItem>
</Tabs>

---

需注意事項：

- 當內容滾出渲染窗口時，內部狀態不會保留。請確保所有數據都儲存在項目數據或外部儲存（如 Flux、Redux 或 Relay）中。
- 此為 `PureComponent`，意味著若 `props` 進行淺層比較後相等，則不會重新渲染。請確認 `renderItem` 函數依賴的所有內容都作為 prop 傳遞（例如 `extraData`），且更新後不會 `===`，否則變更時 UI 可能不會更新。這包含 `data` prop 和父元件狀態。
- 為限制記憶體使用並實現平滑滾動，內容會以異步方式在畫面外渲染。這可能導致滾動速度超過填充速率時短暫出現空白內容。此為可根據應用需求調整的權衡取捨，我們也正持續改進底層機制。
- 預設情況下，列表會尋找每個項目的 `key` prop 作為 React 的 key。您也可透過自訂 `keyExtractor` prop 提供。

---

# 參考文獻

## 屬性

### [ScrollView 屬性](scrollview.md#props)

繼承 [ScrollView 屬性](scrollview.md#props)。

---

### `data`

傳遞給 `getItem` 和 `getItemCount` 以獲取項目的不透明數據類型。

| Type |
| ---- |
| any  |

---

### <div class="label required basic">必填</div> **`getItem`**

```tsx
(data: any, index: number) => any;
```

用於從任意數據塊提取項目的通用存取器。

| Type     |
| -------- |
| function |

---

### <div class="label required basic">必填</div> **`getItemCount`**

```tsx
(data: any) => number;
```

決定數據塊中包含多少項目。

| Type     |
| -------- |
| function |

---

### <div class="label required basic">必填</div> **`renderItem`**

```tsx
(info: any) => ?React.Element<any>
```

從 `data` 提取項目並渲染至列表中

| Type     |
| -------- |
| function |

---

### `CellRendererComponent`

每個單元格皆使用此元件渲染。可為 React 元件類別或渲染函數，預設使用 [`View`](view.md)。

| Type                |
| ------------------- |
| component, function |

---

### `ItemSeparatorComponent`

在每個項目之間渲染，但不會出現在頂部或底部。默認情況下會提供 `highlighted` 和 `leadingItem` 屬性。`renderItem` 提供的 `separators.highlight`/`unhighlight` 會更新 `highlighted` 屬性，但您也可以透過 `separators.updateProps` 添加自定義屬性。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type                         |
| ---------------------------- |
| component, function, element |

---

### `ListEmptyComponent`

當列表為空時渲染。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListItemComponent`

每個數據項目都使用此元素渲染。可以是 React 元件類別或渲染函數。

| Type                |
| ------------------- |
| component, function |

---

### `ListFooterComponent`

在所有項目的底部渲染。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponentStyle`

為 `ListFooterComponent` 的內部 View 設定樣式。

| Type          | Required |
| ------------- | -------- |
| ViewStyleProp | No       |

---

### `ListHeaderComponent`

在所有項目的頂部渲染。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListHeaderComponentStyle`

為 `ListHeaderComponent` 的內部 View 設定樣式。

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `debug`

`debug` 會開啟額外的日誌記錄和視覺疊加層，以幫助調試使用和實現，但會顯著影響性能。

| Type    |
| ------- |
| boolean |

---

### `disableVirtualization`

> **已棄用。** 虛擬化提供了顯著的性能和內存優化，但會完全卸載渲染窗口之外的 React 實例。您應該僅在調試時需要禁用此功能。

| Type    |
| ------- |
| boolean |

---

### `extraData`

一個標記屬性，用於告訴列表重新渲染（因為它實現了 `PureComponent`）。如果您的 `renderItem`、Header、Footer 等函數依賴於 `data` 屬性之外的任何內容，請將其放在這裡並將其視為不可變的。

| Type |
| ---- |
| any  |

---

### `getItemLayout`

```tsx
(
  data: any,
  index: number,
) => {length: number, offset: number, index: number}
```

| Type     |
| -------- |
| function |

---

### `horizontal`

如果為 `true`，則將項目水平並排渲染，而不是垂直堆疊。

| Type    |
| ------- |
| boolean |

---

### `initialNumToRender`

初始批次中要渲染的項目數量。這應該足以填滿屏幕，但不要太多。請注意，這些項目永遠不會作為窗口渲染的一部分被卸載，以提高滾動到頂部操作的感知性能。

| Type   | Default |
| ------ | ------- |
| number | `10`    |

---

### `initialScrollIndex`

不從頂部的第一個項目開始，而是從 `initialScrollIndex` 指定的索引處開始。這會停用「滾動至頂部」的優化功能（該功能會始終渲染前 `initialNumToRender` 個項目），並立即從此初始索引開始渲染項目。需要實作 `getItemLayout`。

| Type   |
| ------ |
| number |

---

### `inverted`

反轉滾動方向。使用 `-1` 的縮放變換。

| Type    |
| ------- |
| boolean |

---

### `keyExtractor`

```tsx
(item: any, index: number) => string;
```

用於為指定索引處的項目提取唯一鍵。該鍵用於緩存和作為 React 鍵來追蹤項目重新排序。預設提取器會檢查 `item.key`，然後是 `item.id`，最後回退到使用索引（與 React 的行為相同）。

| Type     |
| -------- |
| function |

---

### `maxToRenderPerBatch`

每個增量渲染批次中要渲染的最大項目數。一次渲染的項目越多，填充率越好，但響應性可能會受到影響，因為渲染內容可能會干擾按鈕點擊或其他互動的響應。

| Type   |
| ------ |
| number |

---

### `onEndReached`

```tsx
(info: {distanceFromEnd: number}) => void;
```

當滾動位置接近已渲染內容的 `onEndReachedThreshold` 範圍內時，會調用一次。

| Type     |
| -------- |
| function |

---

### `onEndReachedThreshold`

列表底部邊緣與內容末尾之間的距離（以列表可見長度為單位）必須達到此值才會觸發 `onEndReached` 回調。例如，值為 0.5 表示當內容末尾位於列表可見長度的一半以內時，會觸發 `onEndReached`。

| Type   |
| ------ |
| number |

---

### `onRefresh`

```tsx
() => void;
```

如果提供此屬性，將添加一個標準的 `RefreshControl` 以實現「下拉刷新」功能。請確保同時正確設置 `refreshing` 屬性。

| Type     |
| -------- |
| function |

---

### `onScrollToIndexFailed`

```tsx
(info: {
  index: number,
  highestMeasuredFrameIndex: number,
  averageItemLength: number,
}) => void;
```

用於處理滾動到尚未測量的索引時發生的失敗。建議的操作是計算自己的偏移量並滾動到該位置，或者滾動到最遠可能的位置，然後在渲染更多項目後再次嘗試。

| Type     |
| -------- |
| function |

---

### `onViewableItemsChanged`

當行的可見性發生變化時調用，由 `viewabilityConfig` 屬性定義。

| Type                                                                                                  |
| ----------------------------------------------------------------------------------------------------- |
| `md (callback: {changed: [ViewToken](viewtoken)[], viewableItems: [ViewToken](viewtoken)[]}) => void` |

---

### `persistentScrollbar`

| Type |
| ---- |
| bool |

---

### `progressViewOffset`

當需要偏移量以使加載指示器正確顯示時，設置此屬性。

| Type   |
| ------ |
| number |

---

### `refreshControl`

自定義的刷新控制元件。設置後，它會覆蓋內部構建的預設 `<RefreshControl>` 元件。`onRefresh` 和 `refreshing` 屬性也會被忽略。僅適用於垂直方向的 VirtualizedList。

| Type    |
| ------- |
| element |

---

### `refreshing`

在等待刷新數據時將其設置為 true。

| Type    |
| ------- |
| boolean |

---

### `removeClippedSubviews`

這可能會提升大型列表的滾動效能。

> 注意：在某些情況下可能會有錯誤（內容缺失）——使用時需自行承擔風險。

| Type    |
| ------- |
| boolean |

---

### `renderScrollComponent`

```tsx
(props: object) => element;
```

渲染自訂的滾動元件，例如使用不同樣式的 `RefreshControl`。

| Type     |
| -------- |
| function |

---

### `viewabilityConfig`

請參閱 `ViewabilityHelper.js` 以獲取流類型及進一步文檔。

| Type              |
| ----------------- |
| ViewabilityConfig |

---

### `viewabilityConfigCallbackPairs`

`ViewabilityConfig`/`onViewableItemsChanged` 配對列表。當對應的 `ViewabilityConfig` 條件滿足時，特定的 `onViewableItemsChanged` 將被調用。請參閱 `ViewabilityHelper.js` 以獲取流類型及進一步文檔。

| Type                                   |
| -------------------------------------- |
| array of ViewabilityConfigCallbackPair |

---

### `updateCellsBatchingPeriod`

低優先級項目渲染批次之間的時間間隔，例如用於渲染遠離螢幕的項目。與 `maxToRenderPerBatch` 類似，需在填充率與響應性之間取得平衡。

| Type   |
| ------ |
| number |

---

### `windowSize`

決定在可見區域外渲染的項目最大數量，以可見長度為單位。例如，若您的列表填滿螢幕，則 `windowSize={21}`（預設值）將渲染可見螢幕區域加上視窗上方和下方各 10 個螢幕的內容。減少此數值將降低記憶體消耗並可能提升效能，但會增加快速滾動時短暫出現未渲染內容空白區域的可能性。

| Type   |
| ------ |
| number |

## 方法

### `flashScrollIndicators()`

```tsx
flashScrollIndicators();
```

---

### `getScrollableNode()`

```tsx
getScrollableNode(): any;
```

---

### `getScrollRef()`

```tsx
getScrollRef():
  | React.ElementRef<typeof ScrollView>
  | React.ElementRef<typeof View>
  | null;
```

---

### `getScrollResponder()`

```tsx
getScrollResponder () => ScrollResponderMixin | null;
```

提供底層滾動響應器的控制權。請注意 `this._scrollRef` 可能不是 `ScrollView`，因此在調用前需確認其是否響應 `getScrollResponder`。

---

### `scrollToEnd()`

```tsx
scrollToEnd(params?: {animated?: boolean});
```

滾動至內容末端。若未設置 `getItemLayout` 屬性，可能會出現卡頓。

**參數：**

| Name   | Type   |
| ------ | ------ |
| params | object |

有效的 `params` 鍵為：

- `'animated'` (boolean) - 滾動時是否執行動畫。預設為 `true`。

---

### `scrollToIndex()`

```tsx
scrollToIndex(params: {
  index: number;
  animated?: boolean;
  viewOffset?: number;
  viewPosition?: number;
});
```

有效的 `params` 包含：

- 'index' (number). 必填。
- 'animated' (boolean). 選填。
- 'viewOffset' (number). 選填。
- 'viewPosition' (number). 選填。

---

### `scrollToItem()`

```tsx
scrollToItem(params: {
  item: ItemT;
  animated?: boolean;
  viewOffset?: number;
  viewPosition?: number;
);
```

有效的 `params` 參數包含：

- 'item' (Item)。必填。
- 'animated' (boolean)。選填。
- 'viewOffset' (number)。選填。
- 'viewPosition' (number)。選填。

---

### `scrollToOffset()`

```tsx
scrollToOffset(arams: {
  offset: number;
  animated?: boolean;
});
```

滾動至列表中的特定內容像素偏移位置。

參數 `offset` 需指定要滾動至的偏移量。若 `horizontal` 為 true，偏移量為 x 值；其他情況下則為 y 值。

參數 `animated`（預設為 `true`）定義列表滾動時是否執行動畫效果。