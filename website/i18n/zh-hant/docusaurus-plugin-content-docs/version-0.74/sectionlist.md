---
id: sectionlist
title: SectionList
---

一個高效能的介面，用於渲染分區列表，支援最實用的功能：

- 完全跨平台。
- 可配置的可見性回調。
- 列表標頭支援。
- 列表頁尾支援。
- 項目分隔線支援。
- 分區標頭支援。
- 分區分隔線支援。
- 異質數據和項目渲染支援。
- 下拉刷新。
- 滾動載入。

如果您不需要分區支援且希望更簡單的介面，請使用 [`<FlatList>`](flatlist.md)。

## 範例

```SnackPlayer name=SectionList%20Example
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
  SafeAreaView,
  SectionList,
  StatusBar,
} from 'react-native';

const DATA = [
  {
    title: 'Main dishes',
    data: ['Pizza', 'Burger', 'Risotto'],
  },
  {
    title: 'Sides',
    data: ['French Fries', 'Onion Rings', 'Fried Shrimps'],
  },
  {
    title: 'Drinks',
    data: ['Water', 'Coke', 'Beer'],
  },
  {
    title: 'Desserts',
    data: ['Cheese Cake', 'Ice Cream'],
  },
];

const App = () => (
  <SafeAreaView style={styles.container}>
    <SectionList
      sections={DATA}
      keyExtractor={(item, index) => item + index}
      renderItem={({item}) => (
        <View style={styles.item}>
          <Text style={styles.title}>{item}</Text>
        </View>
      )}
      renderSectionHeader={({section: {title}}) => (
        <Text style={styles.header}>{title}</Text>
      )}
    />
  </SafeAreaView>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
    marginHorizontal: 16,
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
  },
  header: {
    fontSize: 32,
    backgroundColor: '#fff',
  },
  title: {
    fontSize: 24,
  },
});

export default App;
```

這是 [`<VirtualizedList>`](virtualizedlist.md) 的便利封裝，因此繼承了其未在此明確列出的屬性（以及 [`<ScrollView>`](scrollview.md) 的屬性），並有以下注意事項：

- 當內容滾動出渲染視窗時，內部狀態不會保留。請確保所有數據都捕獲在項目數據或外部存儲（如 Flux、Redux 或 Relay）中。
- 這是一個 `PureComponent`，意味著如果 `props` 保持淺層相等，它不會重新渲染。確保您的 `renderItem` 函數依賴的所有內容都作為 prop 傳遞（例如 `extraData`），且在更新後不會 `===`，否則您的 UI 可能不會更新。這包括 `data` prop 和父組件狀態。
- 為了限制內存使用並實現平滑滾動，內容會異步渲染到屏幕外。這意味著可能會滾動得比填充速率快，並暫時看到空白內容。這是一個可以根據每個應用需求調整的權衡，我們正在幕後努力改進它。
- 默認情況下，列表會查找每個項目上的 `key` prop 並將其用作 React key。或者，您可以提供自定義的 `keyExtractor` prop。

---

# 參考

## 屬性

### [VirtualizedList 屬性](virtualizedlist.md#props)

繼承 [VirtualizedList 屬性](virtualizedlist.md#props)。

---

### <div class="label required basic">必填</div>**`renderItem`**

每個分區中每個項目的默認渲染器。可以按分區覆蓋。應返回一個 React 元素。

| Type     |
| -------- |
| function |

渲染函數將傳入一個包含以下鍵的對象：

- 'item' (object) - 該分區 `data` 鍵中指定的項目對象
- 'index' (number) - 項目在分區中的索引。
- 'section' (object) - `sections` 中指定的完整分區對象。
- 'separators' (object) - 包含以下鍵的對象：
  - 'highlight' (function) - `() => void`
  - 'unhighlight' (function) - `() => void`
  - 'updateProps' (function) - `(select, newProps) => void`
    - 'select' (enum) - 可能的值為 'leading', 'trailing'
    - 'newProps' (object)

---

### <div class="label required basic">必填</div>**`sections`**

實際要渲染的數據，類似於 [`FlatList`](flatlist.md) 中的 `data` prop。

| Type                                        |
| ------------------------------------------- |
| array of [Section](sectionlist.md#section)s |

---

### `extraData`

一個標記屬性，用於告訴列表重新渲染（因為它實現了 `PureComponent`）。如果您的 `renderItem`、標頭、頁尾等函數依賴於 `data` prop 之外的任何內容，請將其放在這裡並視為不可變。

| Type |
| ---- |
| any  |

---

### `initialNumToRender`

初始批次中要渲染的項目數量。這個數量應該足以填滿螢幕，但不宜過多。請注意，這些項目作為視窗化渲染的一部分，永遠不會被卸載，以提升滾動至頂部操作的感知性能。

| Type   | Default |
| ------ | ------- |
| number | `10`    |

---

### `inverted`

反轉滾動方向。使用 -1 的縮放變換。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `ItemSeparatorComponent`

在每個項目之間渲染，但不在頂部或底部渲染。預設情況下，會提供 `highlighted`、`section` 和 `[leading/trailing][Item/Section]` 屬性。`renderItem` 提供 `separators.highlight`/`unhighlight` 來更新 `highlighted` 屬性，但您也可以透過 `separators.updateProps` 添加自訂屬性。可以是 React 組件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type                         |
| ---------------------------- |
| component, function, element |

---

### `keyExtractor`

用於在指定索引處為給定項目提取唯一鍵。鍵用於快取和作為 React 鍵來追蹤項目重新排序。預設提取器檢查 `item.key`，然後回退到使用索引，就像 React 所做的那樣。請注意，這為每個項目設置了鍵，但每個整體部分仍然需要自己的鍵。

| Type                                    |
| --------------------------------------- |
| (item: object, index: number) => string |

---

### `ListEmptyComponent`

當列表為空時渲染。可以是 React 組件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponent`

在列表的最末端渲染。可以是 React 組件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListHeaderComponent`

在列表的最開頭渲染。可以是 React 組件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `onRefresh`

如果提供，將添加標準的 RefreshControl 以實現「下拉刷新」功能。確保同時正確設置 `refreshing` 屬性。要將 RefreshControl 從頂部偏移（例如 100 點），請使用 `progressViewOffset={100}`。

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

### `refreshing`

在等待刷新數據時將其設置為 true。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `removeClippedSubviews`

> 注意：在某些情況下可能存在錯誤（內容缺失）— 使用時請自行承擔風險。

這可能會提升大型列表的滾動性能。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `renderSectionFooter`

在每個部分的底部渲染。

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `renderSectionHeader`

在每個區段的頂部渲染。在 iOS 上預設會固定在 `ScrollView` 頂部。詳見 `stickySectionHeadersEnabled`。

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `SectionSeparatorComponent`

在每個區段的頂部和底部渲染（注意這與 `ItemSeparatorComponent` 不同，後者僅在項目之間渲染）。這些組件旨在將區段與上方和下方的標頭分隔開，通常具有與 `ItemSeparatorComponent` 相同的高亮響應。也會接收 `highlighted`、`[leading/trailing][Item/Section]` 以及來自 `separators.updateProps` 的任何自定義屬性。

| Type               |
| ------------------ |
| component, element |

---

### `stickySectionHeadersEnabled`

讓區段標頭固定在螢幕頂部，直到下一個標頭將其推離。預設僅在 iOS 上啟用，因為這是該平台的標準行為。

| Type    | Default                                                                                          |
| ------- | ------------------------------------------------------------------------------------------------ |
| boolean | `false` <div class="label android">Android</div><hr/>`true` <div className="label ios">iOS</div> |

## 方法

### `flashScrollIndicators()` <div class="label ios">iOS</div>

```tsx
flashScrollIndicators();
```

短暫顯示滾動指示器。

---

### `recordInteraction()`

```tsx
recordInteraction();
```

通知列表發生了互動，這應觸發可見性計算，例如當 `waitForInteractions` 為 true 且用戶尚未滾動時。通常由點擊項目或導航操作調用。

---

### `scrollToLocation()`

```tsx
scrollToLocation(params: SectionListScrollParams);
```

滾動到指定 `sectionIndex` 和 `itemIndex`（區段內）的項目，並將其定位在可見區域中，其中 `viewPosition` 為 0 時將其置於頂部（可能被固定標頭覆蓋），1 時置於底部，0.5 時置於中間。

> 注意：若未指定 `getItemLayout` 或 `onScrollToIndexFailed` 屬性，則無法滾動到渲染窗口外的位置。

**參數：**

| Name                                                    | Type   |
| ------------------------------------------------------- | ------ |
| params <div class="label basic required">Required</div> | object |

有效的 `params` 鍵為：

- 'animated' (boolean) - 列表滾動時是否執行動畫。預設為 `true`。
- 'itemIndex' (number) - 要滾動到的項目在區段內的索引。必填。
- 'sectionIndex' (number) - 包含要滾動到項目的區段索引。必填。
- 'viewOffset' (number) - 固定像素數以偏移最終目標位置，例如用於補償固定標頭。
- 'viewPosition' (number) - 值為 `0` 時將指定索引的項目置於頂部，`1` 時置於底部，`0.5` 時置於中間。

## 類型定義

### Section

標識要為特定區段渲染的數據的對象。

| Type |
| ---- |
| any  |

**屬性：**

| Name                                                  | Type               | Description                                                                                                                                                         |
| ----------------------------------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| data <div class="label basic required">Required</div> | array              | The data for rendering items in this section. Array of objects, much like [`FlatList`'s data prop](flatlist#required-data).                                         |
| key                                                   | string             | Optional key to keep track of section re-ordering. If you don't plan on re-ordering sections, the array index will be used by default.                              |
| renderItem                                            | function           | Optionally define an arbitrary item renderer for this section, overriding the default [`renderItem`](sectionlist#renderitem) for the list.                          |
| ItemSeparatorComponent                                | component, element | Optionally define an arbitrary item separator for this section, overriding the default [`ItemSeparatorComponent`](sectionlist#itemseparatorcomponent) for the list. |
| keyExtractor                                          | function           | Optionally define an arbitrary key extractor for this section, overriding the default [`keyExtractor`](sectionlist#keyextractor).                                   |