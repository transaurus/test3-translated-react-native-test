---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 術語

- **VirtualizedList:** `FlatList` 底層的元件（React Native 對 [`虛擬列表`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現）

- **記憶體消耗:** 列表相關資訊在記憶體中的佔用量，過高可能導致應用程式崩潰。

- **響應性:** 應用程式對互動的反應能力。低響應性例如點擊元件後需等待片刻才有反應，而非預期的立即響應。

- **空白區域:** 當 `VirtualizedList` 無法快速渲染項目時，列表可能出現未渲染元件形成的空白區塊。

- **視窗區域:** 實際渲染為像素的可見內容區域。

- **渲染窗口:** 應掛載項目的區域範圍，通常遠大於視窗區域。

## 屬性

以下是有助提升 `FlatList` 效能的屬性列表：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

設為 `true` 時，視窗區域外的子視圖會從原生視圖層級中分離。

**優點:** 減少主執行緒處理時間，透過排除視窗區域外的渲染與繪製流程，降低掉幀風險。

**缺點:** 此實作可能存在錯誤，例如內容缺失（主要在 iOS 觀察到），特別是當使用複雜的變形或絕對定位時。需注意此屬性不會顯著節省記憶體，因為視圖僅被分離而非釋放。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

此為可透過 `FlatList` 傳遞的 `VirtualizedList` 屬性，控制每批次渲染的項目數量（每次滾動時渲染的下一個項目區塊）。

**優點:** 較大的數值可減少滾動時的視覺空白區域（提升填充率）。

**缺點:** 每批次處理更多項目意味著 JavaScript 執行時間更長，可能阻塞點擊等事件處理，影響響應性。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 控制每批次渲染數量，而 `updateCellsBatchingPeriod` 則設定批次渲染間的延遲時間（單位毫秒），決定元件更新窗口項目的頻率。

**優點:** 結合此屬性與 `maxToRenderPerBatch` 可靈活調配，例如在較低頻率批次中渲染更多項目，或在較高頻率批次中渲染較少項目。

**缺點:** 低頻率批次可能導致空白區域，高頻率批次可能引發響應性問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 精確定義覆蓋各裝置螢幕的項目數，可大幅提升初始渲染效能。

**缺點:** 過低的 `initialNumToRender` 可能導致空白區域，特別是初始渲染時不足以覆蓋視窗區域的情況。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

此數值為測量單位，1 等於視窗高度。預設值為 21（上方 10 個視窗、下方 10 個視窗，中間 1 個視窗）。

**優點:** 較大的 `windowSize` 可降低滾動時出現空白的機率；較小的 `windowSize` 則能減少同時掛載的項目數，節省記憶體。

**缺點：** 較大的 `windowSize` 會消耗更多記憶體。較小的 `windowSize` 則會增加出現空白區域的機率。

## 列表項目

以下是一些關於列表項目元件的建議。它們是列表的核心，因此需要保持高效能。

### 使用基礎元件

元件越複雜，渲染速度越慢。盡量避免在列表項目中使用過多邏輯和嵌套。如果該列表項目元件在應用中重複使用多次，建議為大型列表專門創建一個元件，並盡可能減少邏輯和嵌套。

### 使用輕量級元件

元件越重，渲染速度越慢。避免使用高解析度圖片（在列表項目中使用裁剪版或縮圖，尺寸越小越好）。與設計團隊溝通，盡量減少列表中的特效、互動和資訊量，將這些內容保留在項目詳情頁面中顯示。

### 使用 shouldComponentUpdate

為元件實作更新驗證機制。React 的 `PureComponent` 已內建透過淺比較實作的 [`shouldComponentUpdate`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)。但這在列表情境中成本較高，因為需要檢查所有 props。若要追求位元級效能優化，應為列表項目元件制定最嚴格的更新規則，僅檢查可能變動的 props。如果列表結構非常簡單，甚至可以直接使用

```tsx
shouldComponentUpdate() {
  return false
}
```

### 使用快取優化圖片

可選用社群套件（例如 [@DylanVann](https://github.com/DylanVann) 開發的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）來載入高效能圖片。列表中的每張圖片都是獨立的 `new Image()` 實例，越快觸發 `loaded` 鉤子，JavaScript 執行緒就能越快釋放。

### 使用 getItemLayout

若所有列表項目元件具有相同高度（水平列表則為寬度），提供 [getItemLayout](flatlist#getitemlayout) prop 可讓 `FlatList` 跳過非同步佈局計算，這是非常理想的優化技巧。

若元件尺寸動態變化且亟需效能提升，可考慮與設計團隊討論是否調整設計以提升效能。

### 使用 keyExtractor 或 key

可為 `FlatList` 元件設定 [`keyExtractor`](flatlist#keyextractor)。此 prop 用於快取機制，並作為 React `key` 來追蹤項目重新排序。

也可直接在項目元件中使用 `key` prop。

### 避免在 renderItem 使用匿名函式

將 `renderItem` 函式移至 render 函式外部，避免每次呼叫 render 時重新創建函式實例。

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="classical">

```tsx
renderItem = ({item}) => (<View key={item.key}><Text>{item.title}</Text></View>);

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

</TabItem>
</Tabs>