---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

## Terms

- **VirtualizedList:** The component behind `FlatList` (React Native's implementation of the [`Virtual List`](https://bvaughn.github.io/react-virtualized/#/components/List) concept.)

- **Memory consumption:** How much information about your list is being stored in memory, which could lead to an app crash.

- **Responsiveness:** Application ability to respond to interactions. Low responsiveness, for instance, is when you touch on a component and it waits a bit to respond, instead of responding immediately as expected.

- **Blank areas:** When `VirtualizedList` can't render your items fast enough, you may enter a part of your list with non-rendered components that appear as blank space.

- **Viewport:** The visible area of content that is rendered to pixels.

- **Window:** The area in which items should be mounted, which is generally much larger than the viewport.

## Props

Here are a list of props that can help to improve `FlatList` performance:

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

If `true`, views that are outside of the viewport are detached from the native view hierarchy.

**Pros:** This reduces time spent on the main thread, and thus reduces the risk of dropped frames, by excluding views outside of the viewport from the native rendering and drawing traversals.

**Cons:** Be aware that this implementation can have bugs, such as missing content (mainly observed on iOS), especially if you are doing complex things with transforms and/or absolute positioning. Also note this does not save significant memory because the views are not deallocated, only detached.

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

It is a `VirtualizedList` prop that can be passed through `FlatList`. This controls the amount of items rendered per batch, which is the next chunk of items rendered on every scroll.

**Pros:** Setting a bigger number means less visual blank areas when scrolling (increases the fill rate).

**Cons:** More items per batch means longer periods of JavaScript execution potentially blocking other event processing, like presses, hurting responsiveness.

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

While `maxToRenderPerBatch` tells the amount of items rendered per batch, setting `updateCellsBatchingPeriod` tells your `VirtualizedList` the delay in milliseconds between batch renders (how frequently your component will be rendering the windowed items).

**Pros:** Combining this prop with `maxToRenderPerBatch` gives you the power to, for example, render more items in a less frequent batch, or less items in a more frequent batch.

**Cons:** Less frequent batches may cause blank areas, More frequent batches may cause responsiveness issues.

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

The initial amount of items to render.

**Pros:** Define precise number of items that would cover the screen for every device. This can be a big performance boost for the initial render.

**Cons:** Setting a low `initialNumToRender` may cause blank areas, especially if it's too small to cover the viewport on initial render.

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

The number passed here is a measurement unit where 1 is equivalent to your viewport height. The default value is 21 (10 viewports above, 10 below, and one in between).

**Pros:** Bigger `windowSize` will result in less chance of seeing blank space while scrolling. On the other hand, smaller `windowSize` will result in fewer items mounted simultaneously, saving memory.

**Cons:** For a bigger `windowSize`, you will have more memory consumption. For a lower `windowSize`, you will have a bigger chance of seeing blank areas.

## List items

以下是關於列表項元件的一些技巧。它們是列表的核心，因此需要保持高效。

### 使用基礎元件

元件越複雜，渲染速度越慢。請盡量避免在列表項中使用大量邏輯和嵌套結構。如果在應用中頻繁重用該列表項元件，請專門為大型列表創建一個元件，並盡可能減少邏輯和嵌套層次。

### 使用輕量級元件

元件越重，渲染速度越慢。避免使用高解析度圖片（在列表項中使用裁剪版或縮略圖，尺寸盡可能小）。與設計團隊溝通，在列表中盡量減少特效、互動和資訊展示，可將這些內容放在項目詳情頁中顯示。

### 使用 `memo()`

`React.memo()` 會創建一個記憶化元件，僅當傳入元件的 props 發生變化時才會重新渲染。我們可以使用此函數來優化 FlatList 中的元件。

```tsx
import React, {memo} from 'react';
import {View, Text} from 'react-native';

const MyListItem = memo(
  ({title}: {title: string}) => (
    <View>
      <Text>{title}</Text>
    </View>
  ),
  (prevProps, nextProps) => {
    return prevProps.title === nextProps.title;
  },
);

export default MyListItem;
```

在此範例中，我們確定 MyListItem 僅在 title 變化時才需要重新渲染。我們將比較函數作為第二個參數傳遞給 React.memo()，這樣元件就只會在指定 prop 改變時重新渲染。如果比較函數返回 true，元件將不會重新渲染。

### 使用緩存優化的圖片

可以使用社區套件（例如 [@DylanVann](https://github.com/DylanVann) 的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）來實現更高性能的圖片加載。列表中的每個圖片都是一個 `new Image()` 實例。圖片越快達到 `loaded` 鉤子，JavaScript 線程就能越快釋放。

### 使用 getItemLayout

如果所有列表項元件具有相同的高度（或水平列表的寬度），提供 [getItemLayout](flatlist#getitemlayout) prop 可以讓 `FlatList` 無需處理異步佈局計算。這是一種非常理想的優化技術。

如果元件具有動態尺寸且確實需要性能優化，可以考慮請設計團隊評估是否可能通過重新設計來提升性能。

### 使用 keyExtractor 或 key

可以為 `FlatList` 元件設置 [`keyExtractor`](flatlist#keyextractor)。此 prop 用於緩存和作為 React `key` 來追蹤項目重新排序。

也可以在項目元件中使用 `key` prop。

### 避免在 renderItem 中使用匿名函數

對於函數式元件，請將 `renderItem` 函數移到返回的 JSX 外部。同時，確保使用 `useCallback` 鉤子包裹該函數，以防止每次渲染時重新創建。

對於類別元件，請將 `renderItem` 函數移到 render 函數外部，這樣它就不會在每次調用 render 函數時重新創建。

```tsx
const renderItem = useCallback(({item}) => (
   <View key={item.key}>
      <Text>{item.title}</Text>
   </View>
 ), []);

return (
  // ...

  <FlatList data={items} renderItem={renderItem} />;
  // ...
);
```