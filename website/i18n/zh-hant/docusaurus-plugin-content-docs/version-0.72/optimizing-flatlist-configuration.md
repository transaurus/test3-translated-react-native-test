---
id: optimizing-flatlist-configuration
title: Optimizing Flatlist Configuration
---

## 術語

- **VirtualizedList:** `FlatList` 底層的元件（React Native 對 [`虛擬列表`](https://bvaughn.github.io/react-virtualized/#/components/List) 概念的實現。）

- **記憶體消耗:** 列表相關資訊佔用的記憶體量，過高可能導致應用程式崩潰。

- **響應性:** 應用程式對互動的反應能力。低響應性例如點擊元件後延遲反應，而非預期的立即回應。

- **空白區域:** 當 `VirtualizedList` 無法快速渲染項目時，列表可能出現未渲染元件的空白區塊。

- **視窗區域:** 實際渲染為像素的可見內容區域。

- **渲染窗口:** 應掛載項目的區域範圍，通常遠大於視窗區域。

## 屬性

以下是有助提升 `FlatList` 效能的屬性列表：

### removeClippedSubviews

| Type    | Default |
| ------- | ------- |
| Boolean | False   |

若設為 `true`，視窗區域外的視圖會從原生視圖層級中分離。

**優點:** 透過排除視窗區域外的視圖，減少主執行緒處理時間，降低掉幀風險。

**缺點:** 需注意此實作可能存在錯誤（尤其在 iOS 上），例如內容缺失（特別是在使用複雜變形或絕對定位時）。另請注意這不會顯著節省記憶體，因視圖僅被分離而非釋放。

### maxToRenderPerBatch

| Type   | Default |
| ------ | ------- |
| Number | 10      |

此為可透過 `FlatList` 傳遞的 `VirtualizedList` 屬性，控制每批次渲染的項目數量（每次滾動時渲染的下一個項目區塊）。

**優點:** 較大數值可減少滾動時的視覺空白區域（提升填充率）。

**缺點:** 每批次更多項目意味著 JavaScript 執行時間更長，可能阻塞點擊等事件處理，影響響應性。

### updateCellsBatchingPeriod

| Type   | Default |
| ------ | ------- |
| Number | 50      |

`maxToRenderPerBatch` 設定每批次渲染量，而 `updateCellsBatchingPeriod` 則設定批次渲染間的毫秒延遲（控制窗口化項目的渲染頻率）。

**優點:** 搭配 `maxToRenderPerBatch` 可靈活調配，例如「高頻次少項目」或「低頻次多項目」的渲染策略。

**缺點:** 低頻次批次可能導致空白區域，高頻次批次可能引發響應性問題。

### initialNumToRender

| Type   | Default |
| ------ | ------- |
| Number | 10      |

初始渲染的項目數量。

**優點:** 精確定義覆蓋各裝置螢幕的項目數，可大幅提升初始渲染效能。

**缺點:** 過低的 `initialNumToRender` 可能導致空白區域，尤其在初始渲染時無法覆蓋視窗區域的情況下。

### windowSize

| Type   | Default |
| ------ | ------- |
| Number | 21      |

此數值以視窗高度為單位（預設值 21 表示上方 10 單位、下方 10 單位及中間 1 單位）。

**優點:** 較大 `windowSize` 可減少滾動空白區域；較小值則減少同時掛載項目數，節省記憶體。

**缺點:** 較大 `windowSize` 增加記憶體消耗，較小值則提高出現空白區域的機率。

## 列表項目

以下是關於列表項元件的一些技巧。它們是列表的核心，因此需要保持高效運作。

### 使用基礎元件

元件結構越複雜，渲染速度越慢。請避免在列表項中放入過多邏輯或嵌套層次。若需在應用中重複使用該列表項元件，建議為大型列表專門建立一個元件，並盡可能減少邏輯與嵌套結構。

### 使用輕量元件

元件負載越重，渲染速度越慢。避免使用高解析度圖片（改用裁剪版或縮略圖，尺寸越小越好）。與設計團隊溝通，在列表項中盡量減少特效、互動元素和資訊量，可將這些內容保留至項目詳情頁面展示。

### 使用 shouldComponentUpdate

為元件實作更新驗證機制。React 的 `PureComponent` 已透過淺比較實作了 [`shouldComponentUpdate`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)。但此方法在此場景下成本較高，因為需檢查所有 props。若要追求位元級效能優化，請為列表項元件制定最嚴格的更新規則，僅檢查可能變動的 props。若列表結構足夠簡單，甚至可以直接使用

```tsx
shouldComponentUpdate() {
  return false
}
```

### 使用快取優化圖片

可選用社群套件（例如 [@DylanVann](https://github.com/DylanVann) 開發的 [react-native-fast-image](https://github.com/DylanVann/react-native-fast-image)）來提升圖片效能。列表中的每張圖片都是獨立的 `new Image()` 實例。圖片越快觸發 `loaded` 鉤子，JavaScript 執行緒就能越快釋放。

### 使用 getItemLayout

若所有列表項元件高度相同（水平列表則為寬度），提供 [getItemLayout](flatlist#getitemlayout) prop 可免除 `FlatList` 進行非同步佈局計算的需求，這是非常理想的優化技巧。

若元件尺寸動態變化且亟需效能提升，可考慮與設計團隊討論是否透過重新設計來改善效能表現。

### 使用 keyExtractor 或 key

可為 `FlatList` 元件設定 [`keyExtractor`](flatlist#keyextractor)。此 prop 用於快取機制，並作為 React 的 `key` 來追蹤項目重新排序。

也可直接在項目元件中使用 `key` prop。

### 避免在 renderItem 使用匿名函數

對於函數式元件，請將 `renderItem` 函數移至返回的 JSX 外部。同時確保使用 `useCallback` 鉤子包裹，防止每次渲染時重新建立函數。

對於類別元件，請將 `renderItem` 函數移至 render 函數外部，避免每次呼叫 render 時重新建立函數。

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