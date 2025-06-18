---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

## 術語

- **VirtualizedList:** 作為 `FlatList` 底層的元件（React Native 對 [`Virtual List`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現。）

- **記憶體消耗:** 列表相關資訊佔用的記憶體量，過高可能導致應用程式崩潰。

- **響應速度:** 應用程式對互動的反應能力。例如低響應速度表現為觸控元件後延遲響應，而非預期的立即反應。

- **空白區域:** 當 `VirtualizedList` 無法快速渲染項目時，列表可能出現未渲染元件形成的空白區域。

- **視窗區域 (Viewport):** 實際渲染為畫素的可見內容區域。

- **渲染窗口 (Window):** 應掛載項目的區域範圍，通常遠大於視窗區域。

## 屬性

以下是有助提升 `FlatList` 效能的屬性列表：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

設為 `true` 時，視窗區域外的視圖會從原生視圖層級中分離。

**優點:** 通過排除視窗區域外的視圖，減少主執行緒處理時間，從而降低掉幀風險。

**缺點:** 需注意此實現可能存在錯誤（尤其在 iOS 上），例如內容缺失（常見於使用變形或絕對定位的複雜情境）。另請注意此設定不會顯著節省記憶體，因視圖僅被分離而非釋放。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

此為可透過 `FlatList` 傳遞的 `VirtualizedList` 屬性，控制每批次渲染的項目數量（即每次滾動時渲染的下一個項目區塊）。

**優點:** 較大數值可減少滾動時的視覺空白區域（提升填充率）。

**缺點:** 每批次更多項目意味著 JavaScript 執行時間更長，可能阻塞點擊等事件處理，影響響應速度。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 控制每批次渲染數量，而 `updateCellsBatchingPeriod` 則設定批次渲染間的毫秒延遲（決定元件更新窗口項目的頻率）。

**優點:** 結合此屬性與 `maxToRenderPerBatch` 可實現靈活配置，例如「高數量低頻次」或「低數量高頻次」的批次渲染策略。

**缺點:** 低頻次批次可能導致空白區域，高頻次批次可能引發響應速度問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 精確定義覆蓋各裝置螢幕的項目數，可大幅提升初始渲染效能。

**缺點:** 過低的 `initialNumToRender` 可能導致空白區域，尤其在初始渲染時無法覆蓋整個視窗區域的情況下。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

此數值以視窗高度為單位（預設值為 21，表示上方 10 個視窗、下方 10 個視窗及中間 1 個視窗的範圍）。

**優點:** 較大的 `windowSize` 可降低滾動時出現空白的機率；較小的 `windowSize` 則減少同時掛載的項目數，節省記憶體。

**缺點:** 較大的 `windowSize` 會增加記憶體消耗，較小的 `windowSize` 則提高出現空白區域的可能性。

## 列表項目

以下是關於列表項元件的一些技巧。它們是列表的核心，因此需要保持高效。

### 使用基礎元件

元件越複雜，渲染速度越慢。盡量避免在列表項中加入過多邏輯和嵌套結構。如果該列表項元件在應用中被頻繁重用，可以專門為大型列表創建一個元件，並盡可能減少邏輯和嵌套層級。

### 使用輕量級元件

元件越「重」，渲染速度越慢。避免使用高解析度圖片（改用裁剪版或縮略圖，尺寸越小越好）。與設計團隊溝通，在列表中盡量減少特效、互動和資訊量，將這些內容保留在項目的詳細頁面中展示。

### 使用 `memo()`

`React.memo()` 會創建一個記憶化元件，僅當傳入的 props 發生變化時才會重新渲染。我們可以利用此函數來優化 FlatList 中的元件。

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

在此範例中，我們確定 MyListItem 僅在 title 改變時需要重新渲染。通過將比較函數作為第二個參數傳給 React.memo()，可以確保元件只在指定 prop 變化時更新。如果比較函數返回 true，元件將不會重新渲染。

### 使用緩存優化圖片

可選用社群套件（如 [@d11/react-native-fast-image](https://github.com/dream-sports-labs/react-native-fast-image)）來提升圖片效能。列表中的每個圖片都是 `new Image()` 實例，越快觸發 `loaded` 鉤子，JavaScript 線程就能越快釋放。

### 使用 getItemLayout

若所有列表項高度相同（水平列表則為寬度），提供 [getItemLayout](flatlist#getitemlayout) prop 可免除 FlatList 的異步佈局計算需求，這是非常理想的優化技巧。

若元件尺寸動態變化且極需效能提升，可考慮請設計團隊評估是否調整設計方案以提升表現。

### 使用 keyExtractor 或 key

可為 FlatList 元件設置 [`keyExtractor`](flatlist#keyextractor)。此 prop 用於緩存及作為 React `key` 來追蹤項目重排序。

也可直接在項目元件中使用 `key` prop。

### 避免在 renderItem 使用匿名函數

對於函數式元件，應將 `renderItem` 函數移至返回的 JSX 外部，並用 `useCallback` 鉤子包裹以避免每次渲染時重建。

對於類別元件，應將 `renderItem` 函數移至 render 函數外部，防止每次調用 render 時重新創建。

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