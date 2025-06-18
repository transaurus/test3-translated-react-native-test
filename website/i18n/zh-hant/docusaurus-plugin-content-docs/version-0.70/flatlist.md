---
id: flatlist
title: FlatList
---

一個高效能的介面，用於渲染基礎的平面列表，支援最便利的功能：

- 完全跨平台。
- 可選的水平模式。
- 可配置的可見性回調。
- 支援標頭。
- 支援頁尾。
- 支援分隔線。
- 下拉刷新。
- 滾動載入。
- 支援滾動至索引。
- 支援多欄。

如果需要分節支援，請使用 [`<SectionList>`](sectionlist.md)。

## 範例

```SnackPlayer name=flatlist-simple
import React from 'react';
import { SafeAreaView, View, FlatList, StyleSheet, Text, StatusBar } from 'react-native';

const DATA = [
  {
    id: 'bd7acbea-c1b1-46c2-aed5-3ad53abb28ba',
    title: 'First Item',
  },
  {
    id: '3ac68afc-c605-48d3-a4f8-fbd91aa97f63',
    title: 'Second Item',
  },
  {
    id: '58694a0f-3da1-471f-bd96-145571e29d72',
    title: 'Third Item',
  },
];

const Item = ({ title }) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);

const App = () => {
  const renderItem = ({ item }) => (
    <Item title={item.title} />
  );

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={renderItem}
        keyExtractor={item => item.id}
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

要渲染多欄，請使用 [`numColumns`](flatlist.md#numcolumns) 屬性。使用這種方法而不是 `flexWrap` 佈局可以避免與項目高度邏輯的衝突。

下方是更複雜、可選取的範例。

- 通過將 `extraData={selectedId}` 傳遞給 `FlatList`，我們確保 `FlatList` 本身會在狀態改變時重新渲染。如果不設置這個屬性，`FlatList` 不會知道它需要重新渲染任何項目，因為它是一個 `PureComponent`，且屬性比較不會顯示任何變化。
- `keyExtractor` 告訴列表使用 `id` 作為 React 鍵，而不是預設的 `key` 屬性。

```SnackPlayer name=flatlist-selectable
import React, { useState } from "react";
import { FlatList, SafeAreaView, StatusBar, StyleSheet, Text, TouchableOpacity } from "react-native";

const DATA = [
  {
    id: "bd7acbea-c1b1-46c2-aed5-3ad53abb28ba",
    title: "First Item",
  },
  {
    id: "3ac68afc-c605-48d3-a4f8-fbd91aa97f63",
    title: "Second Item",
  },
  {
    id: "58694a0f-3da1-471f-bd96-145571e29d72",
    title: "Third Item",
  },
];

const Item = ({ item, onPress, backgroundColor, textColor }) => (
  <TouchableOpacity onPress={onPress} style={[styles.item, backgroundColor]}>
    <Text style={[styles.title, textColor]}>{item.title}</Text>
  </TouchableOpacity>
);

const App = () => {
  const [selectedId, setSelectedId] = useState(null);

  const renderItem = ({ item }) => {
    const backgroundColor = item.id === selectedId ? "#6e3b6e" : "#f9c2ff";
    const color = item.id === selectedId ? 'white' : 'black';

    return (
      <Item
        item={item}
        onPress={() => setSelectedId(item.id)}
        backgroundColor={{ backgroundColor }}
        textColor={{ color }}
      />
    );
  };

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}
        extraData={selectedId}
      />
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

這是 [`<VirtualizedList>`](virtualizedlist.md) 的一個便利包裝，因此繼承了它的屬性（以及 [`<ScrollView>`](scrollview.md) 的屬性），這些屬性未在此明確列出，同時還有以下注意事項：

- 當內容滾動出渲染窗口時，內部狀態不會保留。確保所有數據都捕獲在項目數據或外部存儲（如 Flux、Redux 或 Relay）中。
- 這是一個 `PureComponent`，意味著如果 `props` 保持淺層相等，它不會重新渲染。確保 `renderItem` 函數依賴的所有內容都作為屬性傳遞（例如 `extraData`），且在更新後不 `===`，否則您的 UI 可能不會在變化時更新。這包括 `data` 屬性和父組件狀態。
- 為了限制內存並實現平滑滾動，內容會異步離屏渲染。這意味著可能會滾動得比填充速率快，並暫時看到空白內容。這是一個可以根據每個應用程序的需求進行調整的權衡，我們正在幕後努力改進它。
- 默認情況下，列表會查找每個項目上的 `key` 屬性並將其用作 React 鍵。或者，您可以提供自定義的 `keyExtractor` 屬性。

---

# 參考

## 屬性

### [VirtualizedList 屬性](virtualizedlist.md#props)

繼承 [VirtualizedList 屬性](virtualizedlist.md#props)。

---

### <div class="label required basic">必填</div> **`renderItem`**

```jsx
renderItem({item, index, separators});
```

從 `data` 中取出一個項目並將其渲染到列表中。

提供額外的元數據，如 `index`（如果需要），以及更通用的 `separators.updateProps` 函數，讓您可以設置任何屬性來更改前導分隔線或尾隨分隔線的渲染，以防更常見的 `highlight` 和 `unhighlight`（設置 `highlighted: boolean` 屬性）不足以滿足您的使用情況。

| Type     |
| -------- |
| function |

- `item` (Object)：從 `data` 中渲染的項目。
- `index` (number)：對應於 `data` 數組中此項目的索引。
- `separators` (Object)
  - `highlight` (Function)
  - `unhighlight` (Function)
  - `updateProps` (Function)
    - `select` (enum('leading', 'trailing'))
    - `newProps` (Object)

使用範例：

```jsx
<FlatList
  ItemSeparatorComponent={
    Platform.OS !== 'android' &&
    (({highlighted}) => (
      <View
        style={[style.separator, highlighted && {marginLeft: 0}]}
      />
    ))
  }
  data={[{title: 'Title Text', key: 'item1'}]}
  renderItem={({item, index, separators}) => (
    <TouchableHighlight
      key={item.key}
      onPress={() => this._onPress(item)}
      onShowUnderlay={separators.highlight}
      onHideUnderlay={separators.unhighlight}>
      <View style={{backgroundColor: 'white'}}>
        <Text>{item.title}</Text>
      </View>
    </TouchableHighlight>
  )}
/>
```

---

### <div class="label required basic">Required</div> **`data`**

For simplicity, data is a plain array. If you want to use something else, like an immutable list, use the underlying [`VirtualizedList`](virtualizedlist.md) directly.

| Type  |
| ----- |
| array |

---

### `ItemSeparatorComponent`

Rendered in between each item, but not at the top or bottom. By default, `highlighted` and `leadingItem` props are provided. `renderItem` provides `separators.highlight`/`unhighlight` which will update the `highlighted` prop, but you can also add custom props with `separators.updateProps`. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type                         |
| ---------------------------- |
| component, function, element |

---

### `ListEmptyComponent`

Rendered when the list is empty. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponent`

Rendered at the bottom of all the items. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponentStyle`

Styling for internal View for `ListFooterComponent`.

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `ListHeaderComponent`

Rendered at the top of all the items. Can be a React Component (e.g. `SomeComponent`), or a React element (e.g. `<SomeComponent />`).

| Type               |
| ------------------ |
| component, element |

---

### `ListHeaderComponentStyle`

Styling for internal View for `ListHeaderComponent`.

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `columnWrapperStyle`

Optional custom style for multi-item rows generated when `numColumns > 1`.

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `extraData`

A marker property for telling the list to re-render (since it implements `PureComponent`). If any of your `renderItem`, Header, Footer, etc. functions depend on anything outside of the `data` prop, stick it here and treat it immutably.

| Type |
| ---- |
| any  |

---

### `getItemLayout`

```jsx
(data, index) => {length: number, offset: number, index: number}
```

`getItemLayout` is an optional optimization that allows skipping the measurement of dynamic content if you know the size (height or width) of items ahead of time. `getItemLayout` is efficient if you have fixed size items, for example:

```jsx
  getItemLayout={(data, index) => (
    {length: ITEM_HEIGHT, offset: ITEM_HEIGHT * index, index}
  )}
```

Adding `getItemLayout` can be a great performance boost for lists of several hundred items. Remember to include separator length (height or width) in your offset calculation if you specify `ItemSeparatorComponent`.

| Type     |
| -------- |
| function |

---

### `horizontal`

If `true`, renders items next to each other horizontally instead of stacked vertically.

| Type    |
| ------- |
| boolean |

---

### `initialNumToRender`

How many items to render in the initial batch. This should be enough to fill the screen but not much more. Note these items will never be unmounted as part of the windowed rendering in order to improve perceived performance of scroll-to-top actions.

| Type   | Default |
| ------ | ------- |
| number | `10`    |

---

### `initialScrollIndex`

Instead of starting at the top with the first item, start at `initialScrollIndex`. This disables the "scroll to top" optimization that keeps the first `initialNumToRender` items always rendered and immediately renders the items starting at this initial index. Requires `getItemLayout` to be implemented.

| Type   |
| ------ |
| number |

---

### `inverted`

Reverses the direction of scroll. Uses scale transforms of `-1`.

| Type    |
| ------- |
| boolean |

---

### `keyExtractor`

```jsx
(item: object, index: number) => string;
```

Used to extract a unique key for a given item at the specified index. Key is used for caching and as the react key to track item re-ordering. The default extractor checks `item.key`, then `item.id`, and then falls back to using the index, like React does.

| Type     |
| -------- |
| function |

---

### `numColumns`

Multiple columns can only be rendered with `horizontal={false}` and will zig-zag like a `flexWrap` layout. Items should all be the same height - masonry layouts are not supported.

| Type   |
| ------ |
| number |

---

### `onEndReached`

```jsx
(info: {distanceFromEnd: number}) => void
```

Called once when the scroll position gets within `onEndReachedThreshold` of the rendered content.

| Type     |
| -------- |
| function |

---

### `onEndReachedThreshold`

How far from the end (in units of visible length of the list) the bottom edge of the list must be from the end of the content to trigger the `onEndReached` callback. Thus a value of 0.5 will trigger `onEndReached` when the end of the content is within half the visible length of the list.

| Type   |
| ------ |
| number |

---

### `onRefresh`

```jsx
() => void
```

If provided, a standard RefreshControl will be added for "Pull to Refresh" functionality. Make sure to also set the `refreshing` prop correctly.

| Type     |
| -------- |
| function |

---

### `onViewableItemsChanged`

Called when the viewability of rows changes, as defined by the `viewabilityConfig` prop.

| Type                                                                                                  |
| ----------------------------------------------------------------------------------------------------- |
| `md (callback: {changed: [ViewToken](viewtoken)[], viewableItems: [ViewToken](viewtoken)[]} => void;` |

---

### `progressViewOffset`

Set this when offset is needed for the loading indicator to show correctly.

| Type   |
| ------ |
| number |

---

### `refreshing`

Set this true while waiting for new data from a refresh.

| Type    |
| ------- |
| boolean |

---

### `removeClippedSubviews`

This may improve scroll performance for large lists. On Android the default value is `true`.

> Note: May have bugs (missing content) in some circumstances - use at your own risk.

| Type    |
| ------- |
| boolean |

---

### `viewabilityConfig`

See [`ViewabilityHelper.js`](https://github.com/facebook/react-native/blob/0.70-stable/Libraries/Lists/ViewabilityHelper.js) for flow type and further documentation.

| Type              |
| ----------------- |
| ViewabilityConfig |

`viewabilityConfig` takes a type `ViewabilityConfig` an object with following properties

| Property                         | Type    |
| -------------------------------- | ------- |
| minimumViewTime                  | number  |
| viewAreaCoveragePercentThreshold | number  |
| itemVisiblePercentThreshold      | number  |
| waitForInteraction               | boolean |

At least one of the `viewAreaCoveragePercentThreshold` or `itemVisiblePercentThreshold` is required. This needs to be done in the `constructor` to avoid following error ([ref](https://github.com/facebook/react-native/issues/17408)):

```
  Error: Changing viewabilityConfig on the fly is not supported
```

```jsx
constructor (props) {
  super(props)

  this.viewabilityConfig = {
      waitForInteraction: true,
      viewAreaCoveragePercentThreshold: 95
  }
}
```

```jsx
<FlatList
    viewabilityConfig={this.viewabilityConfig}
  ...
```

#### minimumViewTime

Minimum amount of time (in milliseconds) that an item must be physically viewable before the viewability callback will be fired. A high number means that scrolling through content without stopping will not mark the content as viewable.

#### viewAreaCoveragePercentThreshold

Percent of viewport that must be covered for a partially occluded item to count as "viewable", 0-100. Fully visible items are always considered viewable. A value of 0 means that a single pixel in the viewport makes the item viewable, and a value of 100 means that an item must be either entirely visible or cover the entire viewport to count as viewable.

#### itemVisiblePercentThreshold

Similar to `viewAreaCoveragePercentThreshold`, but considers the percent of the item that is visible, rather than the fraction of the viewable area it covers.

#### waitForInteraction

Nothing is considered viewable until the user scrolls or `recordInteraction` is called after render.

---

### `viewabilityConfigCallbackPairs`

List of `ViewabilityConfig`/`onViewableItemsChanged` pairs. A specific `onViewableItemsChanged` will be called when its corresponding `ViewabilityConfig`'s conditions are met. See `ViewabilityHelper.js` for flow type and further documentation.

| Type                                   |
| -------------------------------------- |
| array of ViewabilityConfigCallbackPair |

## Methods

### `flashScrollIndicators()`

```jsx
flashScrollIndicators();
```

Displays the scroll indicators momentarily.

---

### `getNativeScrollRef()`

```jsx
getNativeScrollRef();
```

Provides a reference to the underlying scroll component

---

### `getScrollResponder()`

```jsx
getScrollResponder();
```

Provides a handle to the underlying scroll responder.

---

### `getScrollableNode()`

```jsx
getScrollableNode();
```

Provides a handle to the underlying scroll node.

---

### `recordInteraction()`

```jsx
recordInteraction();
```

Tells the list an interaction has occurred, which should trigger viewability calculations, e.g. if `waitForInteractions` is true and the user has not scrolled. This is typically called by taps on items or by navigation actions.

---

### `scrollToEnd()`

```jsx
scrollToEnd(params);
```

Scrolls to the end of the content. May be janky without `getItemLayout` prop.

**Parameters:**

| Name   | Type   |
| ------ | ------ |
| params | object |

Valid `params` keys are:

- 'animated' (boolean) - Whether the list should do an animation while scrolling. Defaults to `true`.

---

### `scrollToIndex()`

```jsx
scrollToIndex(params);
```

Scrolls to the item at the specified index such that it is positioned in the viewable area such that `viewPosition` 0 places it at the top, 1 at the bottom, and 0.5 centered in the middle.

> Note: Cannot scroll to locations outside the render window without specifying the `getItemLayout` prop.

**Parameters:**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

Valid `params` keys are:

- 'animated' (boolean) - Whether the list should do an animation while scrolling. Defaults to `true`.
- 'index' (number) - The index to scroll to. Required.
- 'viewOffset' (number) - A fixed number of pixels to offset the final target position.
- 'viewPosition' (number) - A value of `0` places the item specified by index at the top, `1` at the bottom, and `0.5` centered in the middle.

---

### `scrollToItem()`

```jsx
scrollToItem(params);
```

Requires linear scan through data - use `scrollToIndex` instead if possible.

> Note: Cannot scroll to locations outside the render window without specifying the `getItemLayout` prop.

**Parameters:**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

Valid `params` keys are:

- 'animated' (boolean) - Whether the list should do an animation while scrolling. Defaults to `true`.
- 'item' (object) - The item to scroll to. Required.
- 'viewPosition' (number)

---

### `scrollToOffset()`

```jsx
scrollToOffset(params);
```

Scroll to a specific content pixel offset in the list.

**Parameters:**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

Valid `params` keys are:

- 'offset' (number) - The offset to scroll to. In case of `horizontal` being true, the offset is the x-value, in any other case the offset is the y-value. Required.
- 'animated' (boolean) - Whether the list should do an animation while scrolling. Defaults to `true`.