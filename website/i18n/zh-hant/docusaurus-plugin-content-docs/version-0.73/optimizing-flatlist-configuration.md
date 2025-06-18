---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

## 術語

- **VirtualizedList:** 作為 `FlatList` 底層的元件（React Native 對 [`Virtual List`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現。）

- **記憶體消耗:** 列表相關資訊佔用的記憶體量，過高可能導致應用程式崩潰。

- **響應性:** 應用程式對互動的反應能力。例如低響應性會表現為觸控元件後延遲響應，而非預期的立即反應。

- **空白區域:** 當 `VirtualizedList` 無法快速渲染項目時，列表可能出現未渲染元件的空白區塊。

- **視口:** 實際渲染為像素的可見內容區域。

- **窗口:** 應掛載項目的區域範圍，通常遠大於視口。

## 屬性

以下是有助提升 `FlatList` 效能的屬性列表：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

設為 `true` 時，視口外的視圖會從原生視圖層級中分離。

**優點:** 通過排除視口外的視圖，減少主執行緒處理時間，從而降低掉幀風險。

**缺點:** 需注意此實現可能存在錯誤（尤其在iOS平台），例如內容缺失（常見於使用變形或絕對定位的複雜場景）。另請注意這不會顯著節省記憶體，因為視圖僅被分離而非釋放。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

此為可透過 `FlatList` 傳遞的 `VirtualizedList` 屬性，控制每批次渲染的項目數量（即每次滾動時渲染的下一組項目）。

**優點:** 設置較大數值可減少滾動時的視覺空白區域（提升填充率）。

**缺點:** 每批次處理更多項目意味著JavaScript執行時間更長，可能阻塞點擊等事件處理，影響響應性。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 控制每批次渲染數量，而 `updateCellsBatchingPeriod` 則設定批次渲染間的毫秒延遲（控制元件渲染窗口項目的頻率）。

**優點:** 結合此屬性與 `maxToRenderPerBatch` 可實現靈活配置，例如以較低頻率渲染更多項目，或以較高頻率渲染較少項目。

**缺點:** 低頻批次可能導致空白區域，高頻批次可能引發響應性問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 精確定義覆蓋各裝置螢幕的項目數，可大幅提升初始渲染效能。

**缺點:** 設置過低的 `initialNumToRender` 可能導致空白區域，尤其在初始渲染時無法覆蓋視口的情況下。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

此數值以視口高度為單位（預設值21代表上方10視口、下方10視口及中間1視口）。

**優點:** 較大的 `windowSize` 可降低滾動時出現空白的機率，較小的 `windowSize` 則能減少同時掛載的項目數以節省記憶體。

**缺點:** 較大的 `windowSize` 會增加記憶體消耗，較小的 `windowSize` 則可能出現更多空白區域。

## 列表項目

以下是一些關於列表項目元件的技巧。它們是列表的核心，因此需要保持高效。

### 使用基本元件

元件越複雜，渲染速度就越慢。盡量避免在列表項目中加入過多邏輯和嵌套。如果在應用程式中頻繁重用該列表項目元件，請專門為大型列表創建一個元件，並使其邏輯和嵌套盡可能簡單。

### 使用輕量元件

元件越重，渲染速度越慢。避免使用過大的圖片（在列表項目中使用裁剪版本或縮略圖，盡可能小）。與設計團隊溝通，在列表中盡量減少效果、互動和信息的展示，將它們放在項目的詳細頁面中。

### 使用 `memo()`

`React.memo()` 會創建一個記憶化的元件，只有在傳遞給元件的屬性發生變化時才會重新渲染。我們可以使用此函數來優化 FlatList 中的元件。

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

在此範例中，我們確定 MyListItem 僅在標題變更時才需要重新渲染。我們將比較函數作為第二個參數傳遞給 React.memo()，這樣元件僅在指定的屬性變更時才會重新渲染。如果比較函數返回 true，則元件不會重新渲染。

### 使用緩存的優化圖片

可以使用社區套件（例如來自 [@DylanVann](https://github.com/DylanVann) 的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）來獲得更高效的圖片。列表中的每個圖片都是一個 `new Image()` 實例。圖片越快達到 `loaded` 鉤子，JavaScript 線程就能越快釋放。

### 使用 getItemLayout

如果所有列表項目元件的高度相同（或水平列表的寬度相同），提供 [getItemLayout](flatlist#getitemlayout) 屬性可以讓 `FlatList` 無需管理異步佈局計算。這是一種非常理想的優化技術。

如果元件具有動態大小且確實需要性能，可以考慮詢問設計團隊是否願意重新設計以提高性能。

### 使用 keyExtractor 或 key

可以為 `FlatList` 元件設置 [`keyExtractor`](flatlist#keyextractor) 屬性。此屬性用於緩存和作為 React `key` 來追蹤項目重新排序。

也可以在項目元件中使用 `key` 屬性。

### 避免在 renderItem 中使用匿名函數

對於函數式元件，將 `renderItem` 函數移到返回的 JSX 之外。同時，確保將其包裹在 `useCallback` 鉤子中，以防止每次渲染時重新創建。

對於類別元件，將 `renderItem` 函數移到 render 函數之外，這樣每次調用 render 函數時就不會重新創建。

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