---
id: flatlist
title: FlatList
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

一個高效能的介面，用於渲染基礎的平面列表，支援最便利的功能：

- 完全跨平台。
- 可選的水平模式。
- 可配置的可見性回調。
- 支援標頭。
- 支援頁尾。
- 支援分隔線。
- 下拉刷新。
- 滾動載入。
- 支援 ScrollToIndex。
- 多欄支援。

如果需要分節支援，請使用 [`<SectionList>`](sectionlist.md)。

## 範例

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Simple%20FlatList%20Example&ext=js
import React from 'react';
import {View, FlatList, StyleSheet, Text, StatusBar} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const DATA = [
  {
    id: 'bd7acbea-c1b1-46c2-aed5-3ad53abb28ba',
    title: 'First Item',
  },
  {
    id: '3ac68afc-c605-48d3-a4f8-fbd91aa97f63',
    title: 'Second Item',
  },
  {
    id: '58694a0f-3da1-471f-bd96-145571e29d72',
    title: 'Third Item',
  },
];

const Item = ({title}) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={({item}) => <Item title={item.title} />}
        keyExtractor={item => item.id}
      />
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Simple%20FlatList%20Example&ext=tsx
import React from 'react';
import {View, FlatList, StyleSheet, Text, StatusBar} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const DATA = [
  {
    id: 'bd7acbea-c1b1-46c2-aed5-3ad53abb28ba',
    title: 'First Item',
  },
  {
    id: '3ac68afc-c605-48d3-a4f8-fbd91aa97f63',
    title: 'Second Item',
  },
  {
    id: '58694a0f-3da1-471f-bd96-145571e29d72',
    title: 'Third Item',
  },
];

type ItemProps = {title: string};

const Item = ({title}: ItemProps) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={({item}) => <Item title={item.title} />}
        keyExtractor={item => item.id}
      />
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

</TabItem>
</Tabs>

要渲染多欄，請使用 [`numColumns`](flatlist.md#numcolumns) 屬性。使用這種方法而非 `flexWrap` 佈局可以避免與項目高度邏輯發生衝突。

下方為更複雜、可選取的範例。

- 通過將 `extraData={selectedId}` 傳遞給 `FlatList`，我們確保 `FlatList` 本身會在狀態變更時重新渲染。若不設置此屬性，`FlatList` 將不知道需要重新渲染任何項目，因為它是一個 `PureComponent`，且屬性比較不會顯示任何變更。
- `keyExtractor` 告訴列表使用 `id` 作為 React 鍵，而非預設的 `key` 屬性。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=flatlist-selectable&ext=js
import React, {useState} from 'react';
import {
  FlatList,
  StatusBar,
  StyleSheet,
  Text,
  TouchableOpacity,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const DATA = [
  {
    id: 'bd7acbea-c1b1-46c2-aed5-3ad53abb28ba',
    title: 'First Item',
  },
  {
    id: '3ac68afc-c605-48d3-a4f8-fbd91aa97f63',
    title: 'Second Item',
  },
  {
    id: '58694a0f-3da1-471f-bd96-145571e29d72',
    title: 'Third Item',
  },
];

const Item = ({item, onPress, backgroundColor, textColor}) => (
  <TouchableOpacity onPress={onPress} style={[styles.item, {backgroundColor}]}>
    <Text style={[styles.title, {color: textColor}]}>{item.title}</Text>
  </TouchableOpacity>
);

const App = () => {
  const [selectedId, setSelectedId] = useState();

  const renderItem = ({item}) => {
    const backgroundColor = item.id === selectedId ? '#6e3b6e' : '#f9c2ff';
    const color = item.id === selectedId ? 'white' : 'black';

    return (
      <Item
        item={item}
        onPress={() => setSelectedId(item.id)}
        backgroundColor={backgroundColor}
        textColor={color}
      />
    );
  };

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <FlatList
          data={DATA}
          renderItem={renderItem}
          keyExtractor={item => item.id}
          extraData={selectedId}
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=flatlist-selectable&ext=tsx
import React, {useState} from 'react';
import {
  FlatList,
  StatusBar,
  StyleSheet,
  Text,
  TouchableOpacity,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

type ItemData = {
  id: string;
  title: string;
};

const DATA: ItemData[] = [
  {
    id: 'bd7acbea-c1b1-46c2-aed5-3ad53abb28ba',
    title: 'First Item',
  },
  {
    id: '3ac68afc-c605-48d3-a4f8-fbd91aa97f63',
    title: 'Second Item',
  },
  {
    id: '58694a0f-3da1-471f-bd96-145571e29d72',
    title: 'Third Item',
  },
];

type ItemProps = {
  item: ItemData;
  onPress: () => void;
  backgroundColor: string;
  textColor: string;
};

const Item = ({item, onPress, backgroundColor, textColor}: ItemProps) => (
  <TouchableOpacity onPress={onPress} style={[styles.item, {backgroundColor}]}>
    <Text style={[styles.title, {color: textColor}]}>{item.title}</Text>
  </TouchableOpacity>
);

const App = () => {
  const [selectedId, setSelectedId] = useState<string>();

  const renderItem = ({item}: {item: ItemData}) => {
    const backgroundColor = item.id === selectedId ? '#6e3b6e' : '#f9c2ff';
    const color = item.id === selectedId ? 'white' : 'black';

    return (
      <Item
        item={item}
        onPress={() => setSelectedId(item.id)}
        backgroundColor={backgroundColor}
        textColor={color}
      />
    );
  };

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <FlatList
          data={DATA}
          renderItem={renderItem}
          keyExtractor={item => item.id}
          extraData={selectedId}
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: StatusBar.currentHeight || 0,
  },
  item: {
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});

export default App;
```

</TabItem>
</Tabs>

這是 [`<VirtualizedList>`](virtualizedlist.md) 的便利封裝，因此繼承了其屬性（以及 [`<ScrollView>`](scrollview.md) 的屬性），未在此明確列出的屬性也包含在內，同時有以下注意事項：

- 當內容滾動出渲染視窗時，內部狀態不會保留。請確保所有數據都捕獲在項目數據或外部存儲（如 Flux、Redux 或 Relay）中。
- 這是一個 `PureComponent`，意味著如果 `props` 保持淺層相等，它將不會重新渲染。請確保 `renderItem` 函數依賴的所有內容都作為屬性傳遞（例如 `extraData`），且在更新後不 `===`，否則您的 UI 可能不會在變更時更新。這包括 `data` 屬性和父組件狀態。
- 為了限制內存並實現平滑滾動，內容會異步渲染到屏幕外。這意味著可能滾動速度快於填充速率，並暫時看到空白內容。這是一個可以根據每個應用需求調整的權衡，我們正在幕後努力改進它。
- 預設情況下，列表會查找每個項目上的 `key` 屬性並將其用作 React 鍵。或者，您可以提供自定義的 `keyExtractor` 屬性。

---

# 參考

## 屬性

### [VirtualizedList 屬性](virtualizedlist.md#props)

繼承 [VirtualizedList 屬性](virtualizedlist.md#props)。

---

### <div class="label required basic">必填</div> **`renderItem`**

```tsx
renderItem({
  item: ItemT,
  index: number,
  separators: {
    highlight: () => void;
    unhighlight: () => void;
    updateProps: (select: 'leading' | 'trailing', newProps: any) => void;
  }
}): JSX.Element;
```

從 `data` 中取得一個項目並將其渲染到列表中。

提供額外的元數據，如 `index`（如果需要），以及更通用的 `separators.updateProps` 函數，讓您可以設置任何屬性來變更前導分隔線或尾隨分隔線的渲染，以防更常見的 `highlight` 和 `unhighlight`（設置 `highlighted: boolean` 屬性）不足以滿足您的使用情境。

| Type     |
| -------- |
| function |

- `item` (Object)：正在渲染的 `data` 中的項目。
- `index` (number)：`data` 陣列中對應此項目的索引。
- `separators` (Object)
  - `highlight` (Function)
  - `unhighlight` (Function)
  - `updateProps` (Function)
    - `select` (enum('leading', 'trailing'))
    - `newProps` (Object)

使用範例：

```tsx
<FlatList
  ItemSeparatorComponent={
    Platform.OS !== 'android' &&
    (({highlighted}) => (
      <View
        style={[style.separator, highlighted && {marginLeft: 0}]}
      />
    ))
  }
  data={[{title: 'Title Text', key: 'item1'}]}
  renderItem={({item, index, separators}) => (
    <TouchableHighlight
      key={item.key}
      onPress={() => this._onPress(item)}
      onShowUnderlay={separators.highlight}
      onHideUnderlay={separators.unhighlight}>
      <View style={{backgroundColor: 'white'}}>
        <Text>{item.title}</Text>
      </View>
    </TouchableHighlight>
  )}
/>
```

---

### <div class="label required basic">必填</div> **`data`**

用於渲染的項目陣列（或類陣列結構）。若需使用其他資料類型，可直接使用 [`VirtualizedList`](virtualizedlist.md)。

| Type      |
| --------- |
| ArrayLike |

---

### `ItemSeparatorComponent`

渲染於每個項目之間（但不包含頂部與底部）。預設會提供 `highlighted` 和 `leadingItem` 屬性。`renderItem` 提供的 `separators.highlight`/`unhighlight` 可更新 `highlighted` 屬性，亦可透過 `separators.updateProps` 添加自訂屬性。可接受 React 元件（如 `SomeComponent`）或 React 元素（如 `<SomeComponent />`）。

| Type                         |
| ---------------------------- |
| component, function, element |

---

### `ListEmptyComponent`

當列表為空時渲染。可接受 React 元件（如 `SomeComponent`）或 React 元素（如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponent`

渲染於所有項目底部。可接受 React 元件（如 `SomeComponent`）或 React 元素（如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListFooterComponentStyle`

用於 `ListFooterComponent` 內部 View 的樣式設定。

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `ListHeaderComponent`

渲染於所有項目頂部。可接受 React 元件（如 `SomeComponent`）或 React 元素（如 `<SomeComponent />`）。

| Type               |
| ------------------ |
| component, element |

---

### `ListHeaderComponentStyle`

用於 `ListHeaderComponent` 內部 View 的樣式設定。

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `columnWrapperStyle`

當 `numColumns > 1` 時，用於自訂多項目列的樣式（選用）。

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `extraData`

由於列表實作 `PureComponent`，此標記屬性用於觸發重新渲染。若 `renderItem`、Header、Footer 等函式依賴於 `data` 屬性外的任何資料，請將此資料置於此處並視為不可變。

| Type |
| ---- |
| any  |

---

### `getItemLayout`

```tsx
(data, index) => {length: number, offset: number, index: number}
```

`getItemLayout` 為選用優化項，若已知項目尺寸（高度或寬度），可跳過動態內容測量。對於固定尺寸項目（如下例）特別有效：

```tsx
  getItemLayout={(data, index) => (
    {length: ITEM_HEIGHT, offset: ITEM_HEIGHT * index, index}
  )}
```

對數百項目的列表而言，添加 `getItemLayout` 能顯著提升效能。若指定 `ItemSeparatorComponent`，請記得在偏移計算中包含分隔線長度（高度或寬度）。

| Type     |
| -------- |
| function |

---

### `horizontal`

設為 `true` 時，項目將水平並排渲染而非垂直堆疊。

| Type    |
| ------- |
| boolean |

---

### `initialNumToRender`

初始批次中要渲染的項目數量。這個數量應該足夠填滿螢幕，但不宜過多。請注意，這些項目永遠不會因為視窗化渲染而被卸載，以提升滾動至頂部的感知效能。

| Type   | Default |
| ------ | ------- |
| number | `10`    |

---

### `initialScrollIndex`

不從頂部的第一個項目開始，而是從 `initialScrollIndex` 開始。這會停用「滾動至頂部」的優化（該優化會始終渲染前 `initialNumToRender` 個項目），並立即從此初始索引開始渲染項目。需要實作 `getItemLayout`。

| Type   |
| ------ |
| number |

---

### `inverted`

反轉滾動方向。使用 `-1` 的比例變換。

| Type    |
| ------- |
| boolean |

---

### `keyExtractor`

```tsx
(item: ItemT, index: number) => string;
```

用於為指定索引的項目提取唯一鍵。該鍵用於快取及作為 React 的鍵來追蹤項目重新排序。預設提取器會檢查 `item.key`，然後是 `item.id`，最後回退到使用索引（與 React 的行為相同）。

| Type     |
| -------- |
| function |

---

### `numColumns`

多欄佈局只能在 `horizontal={false}` 時渲染，並會以類似 `flexWrap` 的鋸齒狀方式排列。所有項目應具有相同高度——不支援磚牆式佈局。

| Type   |
| ------ |
| number |

---

### `onRefresh`

```tsx
() => void;
```

若提供此屬性，將添加標準的 RefreshControl 以實現「下拉刷新」功能。請確保同時正確設定 `refreshing` 屬性。

| Type     |
| -------- |
| function |

---

### `onViewableItemsChanged`

當行的可見性發生變化時呼叫（由 `viewabilityConfig` 屬性定義）。

| Type                                                                                                  |
| ----------------------------------------------------------------------------------------------------- |
| `md (callback: {changed: [ViewToken](viewtoken)[], viewableItems: [ViewToken](viewtoken)[]} => void;` |

---

### `progressViewOffset`

當載入指示器需要偏移量才能正確顯示時，設定此屬性。

| Type   |
| ------ |
| number |

---

### `refreshing`

在等待刷新資料時，將此屬性設為 true。

| Type    |
| ------- |
| boolean |

---

### `removeClippedSubviews`

這可能會提升大型列表的滾動效能。在 Android 上預設值為 `true`。

> 注意：在某些情況下可能會有錯誤（內容缺失）——使用時需自行承擔風險。

| Type    |
| ------- |
| boolean |

---

### `viewabilityConfig`

參見 [`ViewabilityHelper.js`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Lists/ViewabilityHelper.js) 以獲取流程類型及進一步文件說明。

| Type              |
| ----------------- |
| ViewabilityConfig |

`viewabilityConfig` 接受類型為 `ViewabilityConfig` 的物件，其具有以下屬性

| Property                         | Type    |
| -------------------------------- | ------- |
| minimumViewTime                  | number  |
| viewAreaCoveragePercentThreshold | number  |
| itemVisiblePercentThreshold      | number  |
| waitForInteraction               | boolean |

至少需要設定 `viewAreaCoveragePercentThreshold` 或 `itemVisiblePercentThreshold` 其中一項。需在 `constructor` 中完成以避免以下錯誤（參見[此問題](https://github.com/facebook/react-native/issues/17408)）：

```
  Error: Changing viewabilityConfig on the fly is not supported
```

```tsx
constructor (props) {
  super(props)

  this.viewabilityConfig = {
      waitForInteraction: true,
      viewAreaCoveragePercentThreshold: 95
  }
}
```

```tsx
<FlatList
    viewabilityConfig={this.viewabilityConfig}
  ...
```

#### minimumViewTime

項目必須在物理上可見的最短時間（以毫秒為單位），才會觸發可見性回調。數值越高，表示快速滾動內容而不停止時，不會將內容標記為可見。

#### viewAreaCoveragePercentThreshold

視窗覆蓋百分比閾值，範圍為0-100。部分遮擋的項目需達到此百分比才會被視為「可見」。完全可見的項目始終被視為可見。值為0表示項目只要有一個像素在視窗內即為可見，值為100表示項目必須完全可見或覆蓋整個視窗才會被視為可見。

#### itemVisiblePercentThreshold

與`viewAreaCoveragePercentThreshold`類似，但考慮的是項目本身的可見百分比，而非其覆蓋視窗區域的比例。

#### waitForInteraction

在用戶滾動或調用`recordInteraction`之前，不會將任何內容視為可見。

---

### `viewabilityConfigCallbackPairs`

`ViewabilityConfig`與`onViewableItemsChanged`的配對列表。當對應的`ViewabilityConfig`條件滿足時，會調用特定的`onViewableItemsChanged`。詳見`ViewabilityHelper.js`的流程類型與進一步文檔。

| Type                                   |
| -------------------------------------- |
| array of ViewabilityConfigCallbackPair |

## 方法

### `flashScrollIndicators()`

```tsx
flashScrollIndicators();
```

短暫顯示滾動指示器。

---

### `getNativeScrollRef()`

```tsx
getNativeScrollRef(): React.ElementRef<typeof ScrollViewComponent>;
```

提供底層滾動組件的引用。

---

### `getScrollResponder()`

```tsx
getScrollResponder(): ScrollResponderMixin;
```

提供底層滾動響應器的句柄。

---

### `getScrollableNode()`

```tsx
getScrollableNode(): any;
```

提供底層滾動節點的句柄。

### `scrollToEnd()`

```tsx
scrollToEnd(params?: {animated?: boolean});
```

滾動到內容末尾。若未提供`getItemLayout`屬性，可能會出現卡頓。

**參數：**

| Name   | Type   |
| ------ | ------ |
| params | object |

有效的`params`鍵為：

- 'animated' (boolean) - 滾動時是否執行動畫。默認為`true`。

---

### `scrollToIndex()`

```tsx
scrollToIndex: (params: {
  index: number;
  animated?: boolean;
  viewOffset?: number;
  viewPosition?: number;
});
```

滾動到指定索引的項目，使其位於視窗區域內。`viewPosition`為0時置頂，1時置底，0.5時居中。

> 注意：若未指定`getItemLayout`屬性，無法滾動到渲染視窗外的位置。

**參數：**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

有效的`params`鍵為：

- 'animated' (boolean) - 滾動時是否執行動畫。默認為`true`。
- 'index' (number) - 要滾動到的索引。必填。
- 'viewOffset' (number) - 最終目標位置的固定像素偏移量。
- 'viewPosition' (number) - 值為`0`時將指定索引的項目置頂，`1`時置底，`0.5`時居中。

---

### `scrollToItem()`

```tsx
scrollToItem(params: {
  animated?: ?boolean,
  item: Item,
  viewPosition?: number,
});
```

需要對數據進行線性掃描 - 如果可能的話，請改用 `scrollToIndex`。

> 注意：若未指定 `getItemLayout` 屬性，則無法滾動到渲染窗口之外的位置。

**參數：**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

有效的 `params` 鍵值包括：

- 'animated' (布林值) - 列表滾動時是否執行動畫。默認為 `true`。
- 'item' (物件) - 要滾動到的項目。必需。
- 'viewPosition' (數字)

---

### `scrollToOffset()`

```tsx
scrollToOffset(params: {
  offset: number;
  animated?: boolean;
});
```

滾動到列表中的特定內容像素偏移位置。

**參數：**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| params <div className="label basic required">Required</div> | object |

有效的 `params` 鍵值包括：

- 'offset' (數字) - 要滾動到的偏移量。若 `horizontal` 為 true，則偏移量為 x 值，其他情況下為 y 值。必需。
- 'animated' (布林值) - 列表滾動時是否執行動畫。默認為 `true`。