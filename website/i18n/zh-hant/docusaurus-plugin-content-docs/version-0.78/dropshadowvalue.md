---
id: dropshadowvalue
title: DropShadowValue Object Type
---

`DropShadowValue` 物件由 [`filter`](./view-style-props.md#filter) 樣式屬性用於 `dropShadow` 函數。它由 2 或 3 個長度值和一個可選的顏色組成。這些值共同定義了陰影的顏色、位置和模糊程度。

## 範例

```js
{
  offsetX: 10,
  offsetY: -3,
  standardDeviation: '15px',
  color: 'blue',
}
```

## 鍵與值

### `offsetX`

x 軸上的偏移量。可以是正數或負數。正值表示向右，負值表示向左。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `offsetY`

y 軸上的偏移量。可以是正數或負數。正值表示向上，負值表示向下。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `standardDeviation`

代表 [高斯模糊](https://en.wikipedia.org/wiki/Gaussian_blur) 演算法中使用的標準差。值越大，陰影越模糊。僅允許非負值，預設為 0。

| Type            | Optional |
| --------------- | -------- |
| numer \| string | Yes      |

### `color`

陰影的顏色。預設為 `black`（黑色）。

| Type                 | Optional |
| -------------------- | -------- |
| [color](./colors.md) | Yes      |

## 使用於

- [`filter`](./view-style-props.md#filter)