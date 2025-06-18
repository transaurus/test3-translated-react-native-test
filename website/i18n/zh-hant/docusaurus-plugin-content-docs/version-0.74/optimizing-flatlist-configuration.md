---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

## 術語

- **VirtualizedList:** `FlatList` 背後的組件（React Native 對 [`Virtual List`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現。）

- **記憶體消耗:** 關於你的列表有多少資訊被儲存在記憶體中，這可能導致應用程式崩潰。

- **響應性:** 應用程式對互動的反應能力。低響應性，例如當你觸摸一個組件時，它會等待一下才回應，而不是如預期般立即回應。

- **空白區域:** 當 `VirtualizedList` 無法足夠快地渲染你的項目時，你可能會進入列表的一部分，其中未渲染的組件會顯示為空白空間。

- **視口:** 渲染為像素的可見內容區域。

- **窗口:** 應該掛載項目的區域，通常比視口大得多。

## 屬性

以下是一些可以幫助提升 `FlatList` 性能的屬性：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

如果設為 `true`，視口之外的視圖將從原生視圖層次結構中分離。

**優點:** 這減少了主線程上花費的時間，從而降低了掉幀的風險，通過將視口之外的視圖排除在原生渲染和繪製遍歷之外。

**缺點:** 請注意，這個實現可能存在錯誤，例如內容缺失（主要在 iOS 上觀察到），尤其是如果你正在使用變換和/或絕對定位進行複雜操作。另外請注意，這不會節省大量記憶體，因為視圖並未被釋放，只是被分離。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

這是一個可以通過 `FlatList` 傳遞的 `VirtualizedList` 屬性。它控制每批次渲染的項目數量，即每次滾動時渲染的下一個項目塊。

**優點:** 設置更大的數字意味著滾動時視覺空白區域更少（提高填充率）。

**缺點:** 每批次更多的項目意味著 JavaScript 執行時間更長，可能阻塞其他事件處理，如點擊，影響響應性。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 告訴你每批次渲染的項目數量，而設置 `updateCellsBatchingPeriod` 則告訴你的 `VirtualizedList` 批次渲染之間的延遲時間（以毫秒為單位）（你的組件將多頻繁地渲染窗口中的項目）。

**優點:** 將這個屬性與 `maxToRenderPerBatch` 結合使用，可以讓你例如在較不頻繁的批次中渲染更多項目，或在更頻繁的批次中渲染較少項目。

**缺點:** 較不頻繁的批次可能導致空白區域，更頻繁的批次可能導致響應性問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 為每個設備定義精確的項目數量，以覆蓋屏幕。這可以大大提升初始渲染的性能。

**缺點:** 設置較低的 `initialNumToRender` 可能導致空白區域，特別是如果它在初始渲染時太小而無法覆蓋視口。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

這裡傳遞的數字是一個測量單位，其中 1 相當於你的視口高度。默認值為 21（上方 10 個視口，下方 10 個視口，中間一個視口）。

**優點:** 更大的 `windowSize` 將導致滾動時看到空白空間的機會更少。另一方面，更小的 `windowSize` 將導致同時掛載的項目更少，節省記憶體。

**缺點:** 對於更大的 `windowSize`，你將有更多的記憶體消耗。對於更低的 `windowSize`，你將有更大的機會看到空白區域。

## 列表項目

以下是關於列表項元件的一些技巧。它們是列表的核心，因此需要保持高效。

### 使用基礎元件

元件越複雜，渲染速度就越慢。盡量避免在列表項中加入過多邏輯和嵌套結構。如果這個列表項元件在應用中被大量重複使用，可以專門為大型列表創建一個元件，並盡可能減少邏輯和嵌套層次。

### 使用輕量級元件

元件越重，渲染速度越慢。避免使用過大的圖片（在列表項中使用裁剪版或縮略圖，尺寸越小越好）。與設計團隊溝通，在列表中盡量減少效果、互動和資訊的展示，將這些內容保留在項目的詳細頁面中。

### 使用 `memo()`

`React.memo()` 會創建一個記憶化的元件，只有在傳遞給元件的 props 發生變化時才會重新渲染。我們可以使用這個函數來優化 FlatList 中的元件。

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

在這個例子中，我們確定 MyListItem 只有在標題變化時才需要重新渲染。我們將比較函數作為第二個參數傳遞給 React.memo()，這樣元件就只會在指定的 prop 變化時重新渲染。如果比較函數返回 true，元件將不會重新渲染。

### 使用緩存的優化圖片

你可以使用社區套件（例如 [@DylanVann](https://github.com/DylanVann) 的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）來獲得更高效的圖片。列表中的每個圖片都是一個 `new Image()` 實例。圖片越快達到 `loaded` 鉤子，JavaScript 線程就能越快釋放。

### 使用 getItemLayout

如果所有列表項元件的高度相同（或水平列表的寬度相同），提供 [getItemLayout](flatlist#getitemlayout) prop 可以讓 `FlatList` 無需管理異步佈局計算。這是一個非常理想的優化技巧。

如果元件尺寸動態變化且確實需要性能優化，可以考慮與設計團隊溝通，看看是否能重新設計以提升性能。

### 使用 keyExtractor 或 key

你可以為 `FlatList` 元件設置 [`keyExtractor`](flatlist#keyextractor)。這個 prop 用於緩存和作為 React `key` 來追蹤項目的重新排序。

你也可以在項目元件中使用 `key` prop。

### 避免在 renderItem 中使用匿名函數

對於函數式元件，將 `renderItem` 函數移到返回的 JSX 之外。同時，確保它被 `useCallback` 鉤子包裹，以防止每次渲染時重新創建。

對於類元件，將 `renderItem` 函數移到 render 函數之外，這樣它就不會在每次調用 render 函數時重新創建。

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