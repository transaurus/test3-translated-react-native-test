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

若不需要區塊支援且希望更簡潔的介面，請使用 [`<FlatList>`](flatlist.md)。

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

此元件是 [`<VirtualizedList>`](virtualizedlist.md) 的便利封裝，因此繼承了其未在此明確列出的所有屬性（以及 [`<ScrollView>`](scrollview.md) 的屬性），並需注意以下事項：

- 當內容滾出渲染視窗時，內部狀態不會保留。請確保所有資料都儲存在項目資料或外部狀態庫（如 Flux、Redux 或 Relay）中。
- 此為 `PureComponent`，意味著若 `props` 保持淺層相等則不會重新渲染。請確保 `renderItem` 函數依賴的所有內容都通過不會在更新後 `===` 的 prop（例如 `extraData`）傳遞，否則變更時 UI 可能不會更新。這包含 `data` prop 和父元件狀態。
- 為限制記憶體使用並實現平滑滾動，內容會以異步方式在螢幕外渲染。這可能導致滾動速度超過填充速率時短暫出現空白內容，此為可依應用需求調整的取捨，我們也正持續優化此機制。
- 預設情況下，列表會尋找每個項目的 `key` prop 作為 React key。您也可透過 `keyExtractor` prop 提供自訂邏輯。

---

# 參考文獻

## 屬性

### [VirtualizedList 屬性](virtualizedlist.md#props)

繼承 [VirtualizedList 屬性](virtualizedlist.md#props)。

---

### <div class="label required basic">必填</div>**`renderItem`**

每個區塊中每個項目的預設渲染器。可針對單一區塊覆寫設定，需返回 React 元素。

| Type     |
| -------- |
| function |

渲染函數將接收包含以下鍵值的物件：

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

實際要渲染的資料，類似 [`FlatList`](flatlist.md) 中的 `data` 屬性。

| Type                                        |
| ------------------------------------------- |
| array of [Section](sectionlist.md#section)s |

---

### `extraData`

用於通知列表重新渲染的標記屬性（因其實現了 `PureComponent`）。若您的 `renderItem`、標頭、頁尾等函數依賴於 `data` prop 之外的任何內容，請將其置於此處並視為不可變資料。

| Type |
| ---- |
| any  |

---

### `initialNumToRender`

初始批次要渲染多少項目。這個數量應該足以填滿螢幕但不要過多。請注意，這些項目永遠不會因視窗化渲染而被卸載，以提升滾動至頂部的操作體驗。

| Type   | Default |
| ------ | ------- |
| number | `10`    |

---

### `inverted`

反轉滾動方向。使用-1的比例變換實現。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `ItemSeparatorComponent`

在每個項目之間渲染（但不包含頂部或底部）。預設會提供`highlighted`、`section`和`[leading/trailing][Item/Section]`屬性。`renderItem`提供的`separators.highlight`/`unhighlight`會更新`highlighted`屬性，但您也可以透過`separators.updateProps`添加自訂屬性。可以是React元件（例如`SomeComponent`）或React元素（例如`<SomeComponent />`）。

| Type                         |
| ---------------------------- |
| component, function, element |

---

### `keyExtractor`

用於為指定索引的項目提取唯一鍵值。該鍵值用於快取及作為React追蹤項目重新排序的鍵值。預設提取器會檢查`item.key`，然後是`item.id`，最後回退使用索引（與React行為相同）。請注意，這會為每個項目設置鍵值，但每個區段仍需有自己的鍵值。

| Type                                    |
| --------------------------------------- |
| (item: object, index: number) => string |

---

### `ListEmptyComponent`

當列表為空時渲染。可以是React元件（例如`SomeComponent`）或React元素（例如`<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponent`

在列表最末端渲染。可以是React元件（例如`SomeComponent`）或React元素（例如`<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListHeaderComponent`

在列表最開頭渲染。可以是React元件（例如`SomeComponent`）或React元素（例如`<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `onRefresh`

若提供此屬性，將添加標準RefreshControl以實現「下拉刷新」功能。請確保同時正確設置`refreshing`屬性。若要讓RefreshControl從頂部偏移（例如100點），請使用`progressViewOffset={100}`。

| Type     |
| -------- |
| function |

---

### `onViewableItemsChanged`

當行的可見性發生變化時呼叫（由`viewabilityConfig`屬性定義）。

| Type                                                                                                  |
| ----------------------------------------------------------------------------------------------------- |
| `md (callback: {changed: [ViewToken](viewtoken)[], viewableItems: [ViewToken](viewtoken)[]}) => void` |

---

### `refreshing`

在等待刷新資料時將此值設為true。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `removeClippedSubviews`

> 注意：在某些情況下可能會有bug（內容缺失）— 使用時請自行承擔風險。

這可能會提升大型列表的滾動效能。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `renderSectionFooter`

在每個區段底部渲染。

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

渲染於每個區段的頂部和底部（注意這與僅在項目間渲染的`ItemSeparatorComponent`不同）。這些組件旨在分隔區段與前後標頭，通常具有與`ItemSeparatorComponent`相同的高亮響應效果。同樣會接收`highlighted`、`[leading/trailing][Item/Section]`屬性，以及來自`separators.updateProps`的任何自訂屬性。

| Type               |
| ------------------ |
| component, element |

---

### `stickySectionHeadersEnabled`

使區段標頭黏附在螢幕頂部，直到下一個標頭將其推離。此功能預設僅在iOS平台啟用，因該平台有此標準設計。

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

通知列表發生交互事件，這將觸發可見性計算（例如當`waitForInteractions`為true且用戶未滾動時）。通常由點擊項目或導航操作調用。

---

### `scrollToLocation()`

```tsx
scrollToLocation(params: SectionListScrollParams);
```

滾動至指定`sectionIndex`和`itemIndex`（區段內索引）的項目，並將其置於可視區域：`viewPosition`為0時置於頂部（可能被黏性標頭遮擋），1為底部，0.5為中間。

> 注意：若未指定`getItemLayout`或`onScrollToIndexFailed`屬性，則無法滾動至渲染窗口外的位置。

**參數：**

| Name                                                    | Type   |
| ------------------------------------------------------- | ------ |
| params <div class="label basic required">Required</div> | object |

有效的`params`鍵值包括：

- 'animated' (布林值) - 滾動時是否啟用動畫。預設為`true`。
- 'itemIndex' (數字) - 要滾動至的項目在區段內的索引。必填。
- 'sectionIndex' (數字) - 包含目標項目的區段索引。必填。
- 'viewOffset' (數字) - 最終目標位置的固定像素偏移量（例如用於補償黏性標頭）。
- 'viewPosition' (數字) - 值為`0`時將指定索引項目置於頂部，`1`為底部，`0.5`為中間。

## 型別定義

### Section

用於標識特定區段渲染資料的物件。

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