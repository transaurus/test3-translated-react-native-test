---
id: layoutevent
title: LayoutEvent Object Type
---

`LayoutEvent` 物件會在元件佈局變更時透過回調函數返回，例如 [View](view) 元件中的 `onLayout` 事件。

## 範例

```js
{
    layout: {
        width: 520,
        height: 70.5,
        x: 0,
        y: 42.5
    },
    target: 1127
}
```

## 鍵值與數值

### `height`

元件在佈局變更後的高度。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `width`

元件在佈局變更後的寬度。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `x`

元件在父元件內部的 X 座標。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `y`

元件在父元件內部的 Y 座標。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `target`

接收 PressEvent 的元素的節點 ID。

| Type                        | Optional |
| --------------------------- | -------- |
| number, `null`, `undefined` | No       |

## 使用此物件的元件

- [`Image`](image)
- [`Pressable`](pressable)
- [`ScrollView`](scrollview)
- [`Text`](text)
- [`TextInput`](textinput)
- [`TouchableWithoutFeedback`](touchablewithoutfeedback)
- [`View`](view)