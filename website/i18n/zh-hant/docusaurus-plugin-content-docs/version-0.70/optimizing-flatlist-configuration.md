---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 術語

- **VirtualizedList:** `FlatList` 底層的組件（React Native 對 [`虛擬列表`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現。）

- **記憶體消耗:** 列表相關資訊在記憶體中的佔用量，可能導致應用程式崩潰。

- **響應性:** 應用程式對互動的反應能力。低響應性例如當你點擊組件時，它會延遲回應而非立即反應。

- **空白區域:** 當 `VirtualizedList` 無法快速渲染項目時，列表可能出現未渲染組件的空白區域。

- **視口:** 實際渲染為像素的可見內容區域。

- **窗口:** 應掛載項目的區域，通常比視口大得多。

## 屬性

以下是有助提升 `FlatList` 效能的屬性列表：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

若設為 `true`，視口外的視圖會從原生視圖層級中分離。

**優點:** 減少主執行緒處理時間，從而降低掉幀風險，因為排除了視口外視圖的原生渲染和繪製遍歷。

**缺點:** 請注意此實現可能存在錯誤，例如內容缺失（主要在iOS上觀察到），特別是當你進行複雜的變換和/或絕對定位時。另外這不會顯著節省記憶體，因為視圖未被釋放，僅被分離。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

這是可透過 `FlatList` 傳遞的 `VirtualizedList` 屬性。控制每批次渲染的項目數量，即每次滾動時渲染的下一個項目塊。

**優點:** 設置較大數值可減少滾動時的視覺空白區域（提高填充率）。

**缺點:** 每批次更多項目意味著JavaScript執行時間更長，可能阻塞其他事件處理（如點擊），影響響應性。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 控制每批次渲染的項目數，而 `updateCellsBatchingPeriod` 設置 `VirtualizedList` 批次渲染間的延遲毫秒數（組件渲染窗口項目的頻率）。

**優點:** 結合此屬性與 `maxToRenderPerBatch` 可實現靈活控制，例如以較低頻率渲染更多項目，或以較高頻率渲染較少項目。

**缺點:** 較低頻率批次可能導致空白區域，較高頻率批次可能引發響應性問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 精確定義能覆蓋所有設備螢幕的項目數，可大幅提升初始渲染效能。

**缺點:** 設置過低的 `initialNumToRender` 可能導致空白區域，特別是當初始渲染無法覆蓋整個視口時。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

此處數值是以視口高度為單位的度量，預設值為21（上方10視口、下方10視口及中間1視口）。

**優點:** 較大的 `windowSize` 可降低滾動時出現空白區域的機率。較小的 `windowSize` 則會同時掛載更少項目，節省記憶體。

**缺點：** 較大的 `windowSize` 會消耗更多記憶體。較小的 `windowSize` 則可能增加出現空白區域的機率。

## 列表項目

以下是一些關於列表項目元件的建議。它們是列表的核心，因此需要保持高效運作。

### 使用基礎元件

元件越複雜，渲染速度越慢。盡量避免在列表項目中加入過多邏輯或嵌套結構。若該列表項目元件在應用中頻繁使用，可專門為大型列表創建一個元件，並盡可能減少邏輯和嵌套層級。

### 使用輕量元件

元件越龐大，渲染速度越慢。避免使用高解析度圖片（改用裁剪版或縮圖，尺寸越小越好）。與設計團隊溝通，在列表中盡量減少特效、互動和資訊量，可將這些內容保留在項目詳情頁面展示。

### 使用 shouldComponentUpdate

為元件實作更新驗證機制。React 的 `PureComponent` 已透過淺比較實作了 [`shouldComponentUpdate`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)，但此方法需檢查所有 props 較為耗能。若要追求位元級效能，應為列表項目元件制定嚴格的檢查規則，僅監控可能變動的 props。若列表結構簡單，甚至可直接使用

```jsx
shouldComponentUpdate() {
  return false
}
```

### 使用快取優化圖片

可選用社群套件（如 [@DylanVann](https://github.com/DylanVann) 的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）來提升圖片效能。列表中每個圖片都是獨立的 `new Image()` 實例，越快觸發 `loaded` 鉤子，JavaScript 執行緒就能越快釋放。

### 使用 getItemLayout

若所有列表項目高度相同（水平列表則為寬度），提供 [getItemLayout](flatlist#getitemlayout) 屬性可免除 `FlatList` 的非同步佈局計算需求，這是極具價值的優化技巧。

若元件尺寸動態變化且亟需效能提升，可考慮與設計團隊討論是否調整設計以優化表現。

### 使用 keyExtractor 或 key

可為 `FlatList` 元件設定 [`keyExtractor`](flatlist#keyextractor) 屬性，此屬性用於快取及作為 React 的 `key` 來追蹤項目重新排序。

也可直接在項目元件中使用 `key` 屬性。

### 避免在 renderItem 使用匿名函式

將 `renderItem` 函式移至 render 函式外部，避免每次呼叫 render 時重新創建函式實例。

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="classical">

```jsx
renderItem = ({ item }) => (<View key={item.key}><Text>{item.title}</Text></View>);

render(){
  // ...

  <FlatList
    data={items}
    renderItem={this.renderItem}
  />

  // ...
}

```

</TabItem>
<TabItem value="functional">

```jsx
const renderItem = ({ item }) => (
   <View key={item.key}>
      <Text>{item.title}</Text>
   </View>
 );

return (
  // ...

  <FlatList data={items} renderItem={renderItem} />;
  // ...
);
```

</TabItem>
</Tabs>