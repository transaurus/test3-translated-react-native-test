---
id: sectionlist
title: SectionList
---

一個高效能的區塊列表渲染介面，支援以下實用功能：

- 完全跨平台
- 可配置的可見性回調
- 列表標頭支援
- 列表頁尾支援
- 項目分隔線支援
- 區塊標頭支援
- 區塊分隔線支援
- 異質資料與項目渲染支援
- 下拉刷新
- 滾動載入

若不需要區塊支援且希望使用更簡潔的介面，請改用 [`<FlatList>`](flatlist.md)。

## 範例

```SnackPlayer name=SectionList%20Example
import React from 'react';
import {StyleSheet, Text, View, SectionList, StatusBar} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

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
  <SafeAreaProvider>
    <SafeAreaView style={styles.container} edges={['top']}>
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
  </SafeAreaProvider>
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

此元件是 [`<VirtualizedList>`](virtualizedlist.md) 的便利封裝，因此繼承了其未在此明確列出的屬性（以及 [`<ScrollView>`](scrollview.md) 的屬性），並附帶以下注意事項：

- 當內容滾動超出渲染視窗時，內部狀態不會保留。請確保所有資料都儲存在項目資料或外部狀態庫（如 Flux、Redux 或 Relay）中。
- 此為 `PureComponent`，意味著若 `props` 保持淺層相等則不會重新渲染。請確認 `renderItem` 函式依賴的所有內容都通過 prop 傳遞（例如 `extraData`），且更新後不會 `===`，否則變更時 UI 可能不會更新。這包含 `data` prop 和父元件狀態。
- 為限制記憶體使用並實現平滑滾動，內容會以非同步方式在螢幕外渲染。這可能導致滾動速度快於填充速率時暫時出現空白內容。此為可根據應用需求調整的取捨方案，我們也正持續優化此機制。
- 預設情況下，列表會尋找每個項目的 `key` prop 作為 React key。您也可透過 `keyExtractor` prop 提供自訂 key。

---

# 參考文件

## 屬性

### [VirtualizedList 屬性](virtualizedlist.md#props)

繼承 [VirtualizedList 屬性](virtualizedlist.md#props)。

---

### <div class="label required basic">必填</div>**`renderItem`**

預設用於渲染每個區塊中每個項目的函式。可針對單一區塊覆寫。應返回一個 React 元素。

| Type     |
| -------- |
| function |

此渲染函式會接收包含以下鍵值的物件：

- 'item' (object) - 該區塊 `data` 鍵中指定的項目物件
- 'index' (number) - 項目在區塊中的索引
- 'section' (object) - `sections` 中指定的完整區塊物件
- 'separators' (object) - 包含以下鍵值的物件：
  - 'highlight' (function) - `() => void`
  - 'unhighlight' (function) - `() => void`
  - 'updateProps' (function) - `(select, newProps) => void`
    - 'select' (enum) - 可能值為 'leading' 或 'trailing'
    - 'newProps' (object)

---

### <div class="label required basic">必填</div>**`sections`**

實際要渲染的資料，類似 [`FlatList`](flatlist.md) 中的 `data` prop。

| Type                                        |
| ------------------------------------------- |
| array of [Section](sectionlist.md#section)s |

---

### `extraData`

用於通知列表重新渲染的標記屬性（因其實作 `PureComponent`）。若您的 `renderItem`、標頭、頁尾等函式依賴於 `data` prop 之外的任何內容，請將其固定在此並視為不可變資料。

| Type |
| ---- |
| any  |

---

### `initialNumToRender`

初始批次中要渲染的項目數量。這個數量應該足夠填滿螢幕，但不需要過多。請注意，這些項目永遠不會因為視窗化渲染而被卸載，以提升滾動至頂部操作時的感知性能。

| Type   | Default |
| ------ | ------- |
| number | `10`    |

---

### `inverted`

反轉滾動方向。使用 -1 的比例變換。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `ItemSeparatorComponent`

在每個項目之間渲染，但不會在頂部或底部渲染。預設情況下，會提供 `highlighted`、`section` 和 `[leading/trailing][Item/Section]` 屬性。`renderItem` 提供 `separators.highlight`/`unhighlight` 來更新 `highlighted` 屬性，但您也可以透過 `separators.updateProps` 添加自訂屬性。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type                         |
| ---------------------------- |
| component, function, element |

---

### `keyExtractor`

用於為指定索引的項目提取唯一鍵。鍵用於快取和作為 React 鍵來追蹤項目重新排序。預設提取器會檢查 `item.key`，然後是 `item.id`，最後回退到使用索引，就像 React 的做法一樣。請注意，這會為每個項目設置鍵，但每個整體區段仍需要自己的鍵。

| Type                                    |
| --------------------------------------- |
| (item: object, index: number) => string |

---

### `ListEmptyComponent`

當列表為空時渲染。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponent`

在列表的最末端渲染。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListHeaderComponent`

在列表的最開頭渲染。可以是 React 元件（例如 `SomeComponent`）或 React 元素（例如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `onRefresh`

如果提供，將添加標準的 RefreshControl 以實現「下拉刷新」功能。請確保同時正確設置 `refreshing` 屬性。若要讓 RefreshControl 從頂部偏移（例如 100 點），請使用 `progressViewOffset={100}`。

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

在每個區段的底部渲染。

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `renderSectionHeader`

渲染於每個區段的頂部。在iOS上預設會黏附在`ScrollView`頂部。詳見`stickySectionHeadersEnabled`屬性。

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `SectionSeparatorComponent`

渲染於每個區段的頂部和底部（注意這與僅在項目間渲染的`ItemSeparatorComponent`不同）。用於分隔區段與前後標頭，通常具有與`ItemSeparatorComponent`相同的高亮響應。同時接收`highlighted`、`[leading/trailing][Item/Section]`屬性，以及透過`separators.updateProps`傳遞的自訂屬性。

| Type               |
| ------------------ |
| component, element |

---

### `stickySectionHeadersEnabled`

使區段標頭黏附在螢幕頂部，直到下一個標頭將其推離。僅在iOS平台預設啟用，因符合該平台標準。

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

通知列表發生交互事件，應觸發可見性計算（例如當`waitForInteractions`為true且用戶未滾動時）。通常由點擊項目或導航操作觸發。

---

### `scrollToLocation()`

```tsx
scrollToLocation(params: SectionListScrollParams);
```

滾動至指定`sectionIndex`和`itemIndex`（區段內）的項目，並根據`viewPosition`參數定位於可視區域：0代表頂部（可能被黏性標頭遮蓋），1代表底部，0.5代表居中。

> 注意：若未指定`getItemLayout`或`onScrollToIndexFailed`屬性，無法滾動至渲染窗口外的位置。

**參數：**

| Name                                                    | Type   |
| ------------------------------------------------------- | ------ |
| params <div class="label basic required">Required</div> | object |

有效參數鍵為：

- 'animated' (布林值) - 滾動時是否啟用動畫。預設為`true`。
- 'itemIndex' (數字) - 目標項目在區段內的索引。必填。
- 'sectionIndex' (數字) - 目標區段的索引。必填。
- 'viewOffset' (數字) - 最終目標位置的固定像素偏移量（例如用於補償黏性標頭）。
- 'viewPosition' (數字) - 0將指定索引項目置於頂部，1置於底部，0.5置於中間。

## 型別定義

### Section

標識特定區段渲染資料的物件。

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