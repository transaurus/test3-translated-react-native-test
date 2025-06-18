---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

## 術語

- **VirtualizedList:** 作為 `FlatList` 底層的組件（React Native 對 [`虛擬列表`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現。）

- **記憶體消耗:** 關於列表的資訊有多少被儲存在記憶體中，這可能導致應用程式崩潰。

- **響應性:** 應用程式對互動的反應能力。低響應性，例如當你觸摸一個組件時，它會等待一下才回應，而不是如預期般立即回應。

- **空白區域:** 當 `VirtualizedList` 無法足夠快地渲染你的項目時，你可能會進入列表中未渲染組件的部分，顯示為空白空間。

- **視口:** 渲染為像素的可見內容區域。

- **窗口:** 應掛載項目的區域，通常比視口大得多。

## 屬性

以下是可以幫助提升 `FlatList` 性能的屬性列表：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

如果設為 `true`，視口之外的視圖將從原生視圖層次結構中分離。

**優點:** 這減少了主線程上花費的時間，從而降低了掉幀的風險，通過將視口之外的視圖排除在原生渲染和繪製遍歷之外。

**缺點:** 請注意，此實現可能存在錯誤，例如內容缺失（主要在 iOS 上觀察到），特別是如果你正在進行複雜的變換和/或絕對定位操作。另外請注意，這不會顯著節省記憶體，因為視圖並未被釋放，只是被分離。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

這是可以通過 `FlatList` 傳遞的 `VirtualizedList` 屬性。它控制每批次渲染的項目數量，即每次滾動時渲染的下一個項目塊。

**優點:** 設置更大的數字意味著滾動時視覺空白區域更少（提高填充率）。

**缺點:** 每批次更多的項目意味著 JavaScript 執行時間更長，可能阻塞其他事件處理，如點擊，影響響應性。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 告訴每批次渲染的項目數量，而設置 `updateCellsBatchingPeriod` 則告訴你的 `VirtualizedList` 批次渲染之間的延遲時間（以毫秒為單位，即你的組件將多頻繁地渲染窗口化項目）。

**優點:** 將此屬性與 `maxToRenderPerBatch` 結合使用，可以讓你在例如更不頻繁的批次中渲染更多項目，或在更頻繁的批次中渲染更少項目之間進行權衡。

**缺點:** 批次頻率較低可能導致空白區域，批次頻率較高可能導致響應性問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 為每個設備定義精確的項目數量以覆蓋屏幕。這可以大幅提升初始渲染的性能。

**缺點:** 設置較低的 `initialNumToRender` 可能導致空白區域，特別是如果初始渲染時太小而無法覆蓋視口。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

這裡傳遞的數字是一個測量單位，其中 1 相當於你的視口高度。默認值為 21（上方 10 個視口，下方 10 個視口，中間 1 個視口）。

**優點:** 更大的 `windowSize` 將減少滾動時看到空白空間的機會。另一方面，更小的 `windowSize` 將同時掛載更少的項目，節省記憶體。

**缺點:** 對於更大的 `windowSize`，你將有更高的記憶體消耗。對於更低的 `windowSize`，你將有更大的機會看到空白區域。

## 列表項目

以下是一些關於列表項元件的技巧。它們是列表的核心，因此需要保持高效。

### 使用基礎元件

元件越複雜，渲染速度就越慢。盡量避免在列表項中使用大量邏輯和嵌套結構。如果該列表項元件在應用中重複使用頻率高，請專門為大型列表創建一個元件，並盡可能減少邏輯和嵌套層級。

### 使用輕量級元件

元件越龐大，渲染速度越慢。避免使用高解析度圖片（列表項應使用裁剪版或縮略圖，尺寸盡可能小）。與設計團隊溝通，在列表中盡量減少特效、互動和資訊量，這些內容可保留至項目詳情頁展示。

### 使用 `memo()`

`React.memo()` 會創建一個記憶化元件，僅當傳入的 props 發生變化時才會重新渲染。我們可利用此函數優化 FlatList 中的元件效能。

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

此範例中，我們設定 MyListItem 僅在 title 變化時重新渲染。通過向 React.memo() 傳入比較函數作為第二參數，可指定僅在特定 prop 變更時更新元件。若比較函數返回 true，則元件不會重新渲染。

### 使用快取優化圖片

可選用社群套件（如 [@DylanVann](https://github.com/DylanVann) 的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）提升圖片效能。列表中每個圖片都是獨立的 `new Image()` 實例，越快觸達 `loaded` 鉤子，JavaScript 線程就能越快釋放。

### 使用 getItemLayout

若所有列表項元件高度相同（水平列表則為寬度），提供 [getItemLayout](flatlist#getitemlayout) prop 可免除 FlatList 進行異步佈局計算的需求，這是極具價值的優化技巧。

若元件尺寸動態變化且亟需效能提升，可考慮建議設計團隊評估是否調整設計方案以優化表現。

### 使用 keyExtractor 或 key

可為 FlatList 元件設置 [`keyExtractor`](flatlist#keyextractor)。此 prop 用於快取機制，並作為 React 的 `key` 來追蹤項目重排序。

也可直接在項目元件中使用 `key` prop。

### 避免在 renderItem 使用匿名函數

對於函數式元件，應將 `renderItem` 函數移至返回的 JSX 外部，並用 `useCallback` 鉤子包裹以避免每次渲染時重建。

對於類別元件，應將 `renderItem` 函數移出 render 方法，防止每次調用 render 時重新創建。

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