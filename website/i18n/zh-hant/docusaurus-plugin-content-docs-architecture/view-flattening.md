---
id: view-flattening
title: View Flattening
---

import FabricWarning from './\_fabric-warning.mdx';

<FabricWarning />

#### React Native 渲染器通過視圖扁平化優化來避免深層佈局樹結構

React API 的設計理念是通過組合實現聲明式與可復用性，這為開發者提供了直觀的開發模式。但在實際實現中，這種 API 特性會導致生成深層的 [React 元素樹](architecture-glossary.md#react-element-tree-and-react-element)，其中大量 React 元素節點僅影響視圖佈局而無實際屏幕渲染效果，這類節點我們稱之為「僅佈局」節點。

理論上，React 元素樹的每個節點與屏幕視圖應存在 1:1 對應關係。因此當深層 React 元素樹包含大量「僅佈局」節點時，會導致渲染效能顯著下降。

以下是受「僅佈局」視圖效能影響的典型用例：

假設您需要渲染一個圖像和由 `TitleComponent` 處理的標題，並將該組件作為帶有邊距樣式的 `ContainerComponent` 子組件。組件分解後，React 代碼如下所示：

```jsx
function MyComponent() {
  return (
    <View>                          // ReactAppComponent
      <View style={{margin: 10}} /> // ContainerComponent
        <View style={{margin: 10}}> // TitleComponent
          <Image {...} />
          <Text {...}>This is a title</Text>
        </View>
      </View>
    </View>
  );
}
```

在渲染過程中，React Native 將生成以下樹結構：

![圖表一](/docs/assets/Architecture/view-flattening/diagram-one.png)

請注意視圖 (2) 和 (3) 屬於「僅佈局」視圖，因為它們雖然參與屏幕渲染，但僅對子組件施加 `10 px` 的 `margin` 樣式。

為提升此類 React 元素樹的效能，渲染器實現了視圖扁平化機制，通過合併這類節點來減少屏幕上渲染的 [宿主視圖](architecture-glossary.md#host-view-tree-and-host-view) 層級深度。該算法會綜合考慮 `margin`、`padding`、`backgroundColor`、`opacity` 等屬性。

視圖扁平化算法被設計為渲染器差異比較階段的核心組成部分，這意味著我們無需消耗額外 CPU 週期來優化 React 元素樹的扁平化處理。與渲染器其他核心組件相同，視圖扁平化算法採用 C++ 實現，其效能優勢默認適用於所有支持平台。

在前述示例中，視圖 (2) 和 (3) 將在「差異比較算法」過程中被扁平化，其樣式最終會合併至視圖 (1)：

![圖表二](/docs/assets/Architecture/view-flattening/diagram-two.png)

需特別說明的是，此項優化使渲染器避免了兩個宿主視圖的創建與渲染過程，而用戶在屏幕上不會觀察到任何視覺變化。