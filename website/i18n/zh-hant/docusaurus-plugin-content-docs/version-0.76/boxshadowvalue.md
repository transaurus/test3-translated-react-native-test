---
id: boxshadowvalue
title: BoxShadowValue Object Type
---

`BoxShadowValue` 物件由 [`boxShadow`](./view-style-props.md#boxshadow) 樣式屬性接收，包含 2-4 個長度值、一個可選顏色及可選的 `inset` 布林值。這些數值共同定義了盒陰影的顏色、位置、尺寸與模糊程度。

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

## 鍵值與數值

### `offsetX`

X 軸偏移量。可為正負值，正值表示向右偏移，負值表示向左偏移。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `offsetY`

Y 軸偏移量。可為正負值，正值表示向上偏移，負值表示向下偏移。

| Type             | Optional |
| ---------------- | -------- |
| number \| string | No       |

### `blurRadius`

代表[高斯模糊](https://en.wikipedia.org/wiki/Gaussian_blur)演算法使用的半徑。數值越大陰影越模糊，僅允許非負值，預設為 0。

| Type            | Optional |
| --------------- | -------- |
| numer \| string | Yes      |

### `spreadDistance`

控制陰影擴張或收縮的程度。正值會擴張陰影，負值會收縮陰影。

| Type            | Optional |
| --------------- | -------- |
| numer \| string | Yes      |

### `color`

陰影顏色，預設為 `black`（黑色）。

| Type                 | Optional |
| -------------------- | -------- |
| [color](./colors.md) | Yes      |

### `inset`

設定陰影是否為內嵌式。內嵌陰影會出現在元素邊框盒內部，而非外部。

| Type    | Optional |
| ------- | -------- |
| boolean | Yes      |

## 使用此物件的屬性

- [`boxShadow`](./view-style-props.md#boxshadow)