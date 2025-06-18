---
id: boxshadowvalue
title: BoxShadowValue Object Type
---

`BoxShadowValue` 物件由 [`boxShadow`](./view-style-props.md#boxshadow) 樣式屬性接收。它由 2-4 個長度值、一個可選的顏色和一個可選的 `inset` 布林值組成。這些值共同定義了盒陰影的顏色、位置、大小和模糊程度。

## 範例

```js
{
  offsetX: 10,
  offsetY: -3,
  blurRadius: '15px',
  spreadDistance: '10px',
  color: 'red',
  inset: true,
}
```

## 鍵與值

### `offsetX`

x 軸上的偏移量。可以是正數或負數。正值表示向右偏移，負值表示向左偏移。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `offsetY`

y 軸上的偏移量。可以是正數或負數。正值表示向上偏移，負值表示向下偏移。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `blurRadius`

代表用於[高斯模糊](https://en.wikipedia.org/wiki/Gaussian_blur)演算法的半徑。值越大，陰影越模糊。僅允許非負值。預設值為 0。

| Type            | Optional |
| --------------- | -------- |
| numer \| string | Yes      |

### `spreadDistance`

陰影擴張或收縮的程度。正值會使陰影擴張，負值會使陰影收縮。

| Type            | Optional |
| --------------- | -------- |
| numer \| string | Yes      |

### `color`

陰影的顏色。預設為 `black`（黑色）。

| Type                 | Optional |
| -------------------- | -------- |
| [color](./colors.md) | Yes      |

### `inset`

陰影是否為內嵌。內嵌陰影會出現在元素邊框盒的內部，而非外部。

| Type    | Optional |
| ------- | -------- |
| boolean | Yes      |

## 使用於

- [`boxShadow`](./view-style-props.md#boxshadow)