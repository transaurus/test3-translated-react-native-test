---
id: viewtoken
title: ViewToken Object Type
---

`ViewToken` 物件會作為 `onViewableItemsChanged` 回調函數的屬性之一返回，例如在 [FlatList](flatlist) 元件中。它由 [`ViewabilityHelper.js`](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Lists/ViewabilityHelper.js) 導出。

## 範例

```js
{
  item: {key: "key-12"},
  key: "key-12",
  index: 11,
  isViewable: true
}
```

## 鍵與值

### `index`

分配給資料元素的唯一數字識別符。

| Type   | Optional |
| ------ | -------- |
| number | Yes      |

### `isViewable`

指定列表元素是否至少有一部分在視窗中可見。

| Type    | Optional |
| ------- | -------- |
| boolean | No       |

### `item`

項目資料

| Type | Optional |
| ---- | -------- |
| any  | No       |

### `key`

提取到頂層的資料元素的鍵識別符。

| Type   | Optional |
| ------ | -------- |
| string | No       |

### `section`

與 `SectionList` 一起使用時的項目區段資料。

| Type | Optional |
| ---- | -------- |
| any  | Yes      |

## 使用於

- [`FlatList`](flatlist)
- [`SectionList`](sectionlist)
- [`VirtualizedList`](virtualizedlist)