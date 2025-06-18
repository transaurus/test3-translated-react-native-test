---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

## 術語

- **VirtualizedList:** 作為 `FlatList` 底層的元件（React Native 對 [`虛擬列表`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現）

- **記憶體消耗:** 列表相關資訊佔用的記憶體量，過高可能導致應用程式崩潰。

- **響應速度:** 應用程式對互動的反應能力。例如低響應速度表現為觸控元件後延遲響應，而非預期的立即反應。

- **空白區域:** 當 `VirtualizedList` 無法快速渲染項目時，列表可能出現未渲染元件形成的空白區域。

- **視窗區域:** 實際渲染為畫素的可見內容區域。

- **渲染窗口:** 應掛載項目的區域範圍，通常遠大於視窗區域。

## 屬性

以下是有助提升 `FlatList` 效能的屬性列表：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

設為 `true` 時，視窗區域外的視圖會從原生視圖層級中分離。

**優點:** 透過排除視窗區域外的視圖，減少主執行緒處理時間，從而降低掉幀風險。

**缺點:** 需注意此實現可能存在錯誤（尤其在iOS平台），例如內容缺失（常見於使用複雜變換或絕對定位時）。另請注意此設定不會顯著節省記憶體，因視圖僅被分離而非釋放。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

此為可透過 `FlatList` 傳遞的 `VirtualizedList` 屬性，控制每批次渲染的項目數量（即每次滾動時渲染的新項目區塊）。

**優點:** 設定較大數值可減少滾動時的視覺空白區域（提升填充率）。

**缺點:** 每批次處理更多項目可能導致JavaScript執行時間過長，阻塞點擊等事件處理，影響響應速度。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 控制每批次渲染數量，而 `updateCellsBatchingPeriod` 則設定批次渲染間的毫秒延遲（控制元件渲染窗口項目的頻率）。

**優點:** 結合此屬性與 `maxToRenderPerBatch` 可實現靈活配置，例如「高數量低頻次」或「低數量高頻次」的批次渲染。

**缺點:** 低頻次批次可能導致空白區域，高頻次批次可能引發響應速度問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 精確定義覆蓋各裝置螢幕的項目數，可大幅提升初始渲染效能。

**缺點:** 設定過低的 `initialNumToRender` 可能導致空白區域，特別是初始渲染時無法完整覆蓋視窗區域的情況。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

此數值以視窗高度為單位（1等於視窗高度），預設值為21（上方10視窗、下方10視窗及中間1視窗）。

**優點:** 較大的 `windowSize` 可降低滾動時出現空白的機率；較小的 `windowSize` 則減少同時掛載的項目數以節省記憶體。

**缺點:** 較大的 `windowSize` 會增加記憶體消耗，較小的 `windowSize` 則提高出現空白區域的可能性。

## 列表項目

以下是關於列表項元件的一些建議。它們是列表的核心，因此需要保持高效。

### 使用基礎元件

元件越複雜，渲染速度越慢。盡量避免在列表項中使用過多邏輯和嵌套結構。如果該列表項元件在應用中頻繁重用，建議為大型列表專門創建一個元件，並盡可能減少邏輯和嵌套層級。

### 使用輕量級元件

元件越龐大，渲染速度越慢。避免使用過重的圖片（在列表項中使用裁剪版或縮略圖，尺寸盡可能小）。與設計團隊溝通，在列表中盡量減少特效、互動和資訊量，可將這些內容保留在項目詳情頁中展示。

### 使用 `memo()`

`React.memo()` 會創建一個記憶化元件，僅當傳入元件的 props 發生變化時才會重新渲染。我們可以利用此函數來優化 FlatList 中的元件。

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

在此範例中，我們已確定 MyListItem 僅在 title 變更時需要重新渲染。我們將比較函數作為第二個參數傳遞給 React.memo()，這樣元件就只會在指定 prop 變化時重新渲染。如果比較函數返回 true，則元件不會重新渲染。

### 使用緩存優化的圖片

可使用社群套件（例如 [@DylanVann](https://github.com/DylanVann) 的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）來實現更高性能的圖片載入。列表中的每個圖片都是 `new Image()` 實例，越快達到 `loaded` 鉤子，JavaScript 線程就能越快釋放。

### 使用 getItemLayout

如果所有列表項元件具有相同高度（水平列表則為寬度），提供 [getItemLayout](flatlist#getitemlayout) prop 可免除 `FlatList` 管理異步佈局計算的需求，這是非常理想的優化技術。

若元件尺寸動態變化且確實需要性能提升，可考慮請設計團隊評估是否進行重新設計以提升性能。

### 使用 keyExtractor 或 key

可為 `FlatList` 元件設置 [`keyExtractor`](flatlist#keyextractor)。此 prop 用於緩存及作為 React `key` 來追蹤項目重新排序。

也可在項目元件中使用 `key` prop。

### 避免在 renderItem 中使用匿名函數

對於函數式元件，應將 `renderItem` 函數移至返回的 JSX 外部。同時確保使用 `useCallback` 鉤子包裹，以防止每次渲染時重新創建。

對於類別元件，應將 `renderItem` 函數移至 render 函數外部，避免每次調用 render 時重新創建。

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