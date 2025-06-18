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

此為 [`<VirtualizedList>`](virtualizedlist.md) 的便利封裝元件，因此繼承了其未在此明確列出的屬性（以及 [`<ScrollView>`](scrollview.md) 的屬性），並附帶以下注意事項：

- 當內容滾動超出渲染視窗時，內部狀態不會保留。請確保所有資料都儲存在項目資料或外部狀態庫（如 Flux、Redux 或 Relay）中。
- 此為 `PureComponent`，意味著若 `props` 保持淺層相等時不會重新渲染。請確認 `renderItem` 函式依賴的所有內容都通過 prop 傳遞（例如 `extraData`），且更新後不會保持 `===` 關係，否則變更時 UI 可能不會更新。這包含 `data` prop 和父元件狀態。
- 為限制記憶體使用並實現平滑滾動，內容會以異步方式在螢幕外渲染。這可能導致滾動速度超過填充速率時暫時看到空白內容。此為可根據應用需求調整的權衡方案，我們正在幕後持續改進。
- 預設情況下，列表會尋找每個項目的 `key` prop 作為 React key。您也可以提供自訂的 `keyExtractor` prop。

---

# 參考文件

## 屬性

### [VirtualizedList 屬性](virtualizedlist.md#props)

繼承 [VirtualizedList 屬性](virtualizedlist.md#props)。

---

### <div class="label required basic">必填</div>**`renderItem`**

每個區塊中每個項目的預設渲染器。可針對單一區塊覆寫。應返回一個 React 元素。

| Type     |
| -------- |
| function |

渲染函式將接收包含以下鍵值的物件：

- 'item' (物件) - 該區塊 `data` 鍵中指定的項目物件
- 'index' (數字) - 項目在區塊中的索引
- 'section' (物件) - `sections` 中指定的完整區塊物件
- 'separators' (物件) - 包含以下鍵值的物件：
  - 'highlight' (函式) - `() => void`
  - 'unhighlight' (函式) - `() => void`
  - 'updateProps' (函式) - `(select, newProps) => void`
    - 'select' (枚舉) - 可能值為 'leading'、'trailing'
    - 'newProps' (物件)

---

### <div class="label required basic">必填</div>**`sections`**

實際要渲染的資料，類似 [`FlatList`](flatlist.md) 中的 `data` 屬性。

| Type                                        |
| ------------------------------------------- |
| array of [Section](sectionlist.md#section)s |

---

### `extraData`

用於通知列表重新渲染的標記屬性（因其實現了 `PureComponent`）。若您的 `renderItem`、標頭、頁尾等函式依賴於 `data` 屬性之外的任何內容，請將其置於此處並視為不可變資料。

| Type |
| ---- |
| any  |

---

### `initialNumToRender`

How many items to render in the initial batch. This should be enough to fill the screen but not much more. Note these items will never be unmounted as part of the windowed rendering in order to improve perceived performance of scroll-to-top actions.

| Type   | Default |
| ------ | ------- |
| number | `10`    |

---

### `inverted`

Reverses the direction of scroll. Uses scale transforms of -1.

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `ItemSeparatorComponent`

Rendered in between each item, but not at the top or bottom. By default, `highlighted`, `section`, and `[leading/trailing][Item/Section]` props are provided. `renderItem` provides `separators.highlight`/`unhighlight` which will update the `highlighted` prop, but you can also add custom props with `separators.updateProps`. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type                         |
| ---------------------------- |
| component, function, element |

---

### `keyExtractor`

Used to extract a unique key for a given item at the specified index. Key is used for caching and as the React key to track item re-ordering. The default extractor checks `item.key`, then falls back to using the index, like React does. Note that this sets keys for each item, but each overall section still needs its own key.

| Type                                    |
| --------------------------------------- |
| (item: object, index: number) => string |

---

### `ListEmptyComponent`

Rendered when the list is empty. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponent`

Rendered at the very end of the list. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

---

### `ListHeaderComponent`

Rendered at the very beginning of the list. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

---

### `onRefresh`

If provided, a standard RefreshControl will be added for "Pull to Refresh" functionality. Make sure to also set the `refreshing` prop correctly. To offset the RefreshControl from the top (e.g. by 100 pts), use `progressViewOffset={100}`.

| Type     |
| -------- |
| function |

---

### `onViewableItemsChanged`

Called when the viewability of rows changes, as defined by the `viewabilityConfig` prop.

| Type                                                                                                  |
| ----------------------------------------------------------------------------------------------------- |
| `md (callback: {changed: [ViewToken](viewtoken)[], viewableItems: [ViewToken](viewtoken)[]}) => void` |

---

### `refreshing`

Set this true while waiting for new data from a refresh.

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `removeClippedSubviews`

> Note: may have bugs (missing content) in some circumstances - use at your own risk.

This may improve scroll performance for large lists.

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `renderSectionFooter`

Rendered at the bottom of each section.

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `renderSectionHeader`

Rendered at the top of each section. These stick to the top of the `ScrollView` by default on iOS. See `stickySectionHeadersEnabled`.

| Type                                                                      |
| ------------------------------------------------------------------------- |
| `md (info: {section: [Section](sectionlist#section)}) => element ｜ null` |

---

### `SectionSeparatorComponent`

Rendered at the top and bottom of each section (note this is different from `ItemSeparatorComponent` which is only rendered between items). These are intended to separate sections from the headers above and below and typically have the same highlight response as `ItemSeparatorComponent`. Also receives `highlighted`, `[leading/trailing][Item/Section]`, and any custom props from `separators.updateProps`.

| Type               |
| ------------------ |
| component, element |

---

### `stickySectionHeadersEnabled`

Makes section headers stick to the top of the screen until the next one pushes it off. Only enabled by default on iOS because that is the platform standard there.

| Type    | Default                                                                                          |
| ------- | ------------------------------------------------------------------------------------------------ |
| boolean | `false` <div class="label android">Android</div><hr/>`true` <div className="label ios">iOS</div> |

## Methods

### `flashScrollIndicators()` <div class="label ios">iOS</div>

```tsx
flashScrollIndicators();
```

Displays the scroll indicators momentarily.

---

### `recordInteraction()`

```tsx
recordInteraction();
```

Tells the list an interaction has occurred, which should trigger viewability calculations, e.g. if `waitForInteractions` is true and the user has not scrolled. This is typically called by taps on items or by navigation actions.

---

### `scrollToLocation()`

```tsx
scrollToLocation(params: SectionListScrollParams);
```

Scrolls to the item at the specified `sectionIndex` and `itemIndex` (within the section) positioned in the viewable area such that `viewPosition` 0 places it at the top (and may be covered by a sticky header), 1 at the bottom, and 0.5 centered in the middle.

> Note: Cannot scroll to locations outside the render window without specifying the `getItemLayout` or `onScrollToIndexFailed` prop.

**Parameters:**

| Name                                                    | Type   |
| ------------------------------------------------------- | ------ |
| params <div class="label basic required">Required</div> | object |

Valid `params` keys are:

- 'animated' (boolean) - Whether the list should do an animation while scrolling. Defaults to `true`.
- 'itemIndex' (number) - Index within section for the item to scroll to. Required.
- 'sectionIndex' (number) - Index for section that contains the item to scroll to. Required.
- 'viewOffset' (number) - A fixed number of pixels to offset the final target position, e.g. to compensate for sticky headers.
- 'viewPosition' (number) - A value of `0` places the item specified by index at the top, `1` at the bottom, and `0.5` centered in the middle.

## Type Definitions

### Section

An object that identifies the data to be rendered for a given section.

| Type |
| ---- |
| any  |

**Properties:**

| Name                                                  | Type               | Description                                                                                                                                                         |
| ----------------------------------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| data <div class="label basic required">Required</div> | array              | The data for rendering items in this section. Array of objects, much like [`FlatList`'s data prop](flatlist#required-data).                                         |
| key                                                   | string             | Optional key to keep track of section re-ordering. If you don't plan on re-ordering sections, the array index will be used by default.                              |
| renderItem                                            | function           | Optionally define an arbitrary item renderer for this section, overriding the default [`renderItem`](sectionlist#renderitem) for the list.                          |
| ItemSeparatorComponent                                | component, element | Optionally define an arbitrary item separator for this section, overriding the default [`ItemSeparatorComponent`](sectionlist#itemseparatorcomponent) for the list. |
| keyExtractor                                          | function           | Optionally define an arbitrary key extractor for this section, overriding the default [`keyExtractor`](sectionlist#keyextractor).                                   |