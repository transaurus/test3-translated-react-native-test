---
id: dropshadowvalue
title: DropShadowValue Object Type
---

`DropShadowValue` 物件由 [`filter`](./view-style-props.md#filter) 樣式屬性中的 `dropShadow` 函式使用。它由 2 或 3 個長度值和一個可選的顏色值組成。這些值共同定義了陰影的顏色、位置和模糊程度。

## 範例

```js
{
  offsetX: 10,
  offsetY: -3,
  standardDeviation: '15px',
  color: 'blue',
}
```

## 鍵值與數值

### `offsetX`

X 軸的偏移量。可為正值或負值。正值表示向右偏移，負值表示向左偏移。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `offsetY`

Y 軸的偏移量。可為正值或負值。正值表示向上偏移，負值表示向下偏移。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `standardDeviation`

代表[高斯模糊](https://en.wikipedia.org/wiki/Gaussian_blur)演算法中使用的標準差。數值越大陰影越模糊。僅允許非負值，預設值為 0。

| Type            | Optional |
| --------------- | -------- |
| numer \| string | Yes      |

### `color`

陰影的顏色。預設為 `black`（黑色）。

| Type                 | Optional |
| -------------------- | -------- |
| [color](./colors.md) | Yes      |

## 使用場景

- [`filter`](./view-style-props.md#filter)