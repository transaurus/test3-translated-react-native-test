---
id: view-style-props
title: View Style Props
---

### 範例

```SnackPlayer name=ViewStyleProps
import React from 'react';
import {View, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <View style={styles.top} />
      <View style={styles.middle} />
      <View style={styles.bottom} />
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'space-between',
    padding: 20,
    margin: 10,
  },
  top: {
    flex: 0.3,
    backgroundColor: 'grey',
    borderWidth: 5,
    borderTopLeftRadius: 20,
    borderTopRightRadius: 20,
  },
  middle: {
    flex: 0.3,
    backgroundColor: 'beige',
    borderWidth: 5,
  },
  bottom: {
    flex: 0.3,
    backgroundColor: 'pink',
    borderWidth: 5,
    borderBottomLeftRadius: 20,
    borderBottomRightRadius: 20,
  },
});

export default App;
```

# 參考文獻

## 屬性

### `backfaceVisibility`

| Type                          |
| ----------------------------- |
| enum(`'visible'`, `'hidden'`) |

---

### `backgroundColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderBottomColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderBottomEndRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomLeftRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomRightRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomStartRadius`

| Type   |
| ------ |
| number |

---

### `borderStartEndRadius`

| Type   |
| ------ |
| number |

---

### `borderStartStartRadius`

| Type   |
| ------ |
| number |

---

### `borderEndEndRadius`

| Type   |
| ------ |
| number |

---

### `borderEndStartRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomWidth`

| Type   |
| ------ |
| number |

---

### `borderColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderCurve` <div class="label ios">iOS</div>

在 iOS 13+ 上，可以變更邊框的角曲線。

| Type                               |
| ---------------------------------- |
| enum(`'circular'`, `'continuous'`) |

---

### `borderEndColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderLeftColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderLeftWidth`

| Type   |
| ------ |
| number |

---

### `borderRadius`

若圓角邊框未顯示，請嘗試同時套用 `overflow: 'hidden'`。

| Type   |
| ------ |
| number |

---

### `borderRightColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderRightWidth`

| Type   |
| ------ |
| number |

---

### `borderStartColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderStyle`

| Type                                    |
| --------------------------------------- |
| enum(`'solid'`, `'dotted'`, `'dashed'`) |

---

### `borderTopColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderTopEndRadius`

| Type   |
| ------ |
| number |

---

### `borderTopLeftRadius`

| Type   |
| ------ |
| number |

---

### `borderTopRightRadius`

| Type   |
| ------ |
| number |

---

### `borderTopStartRadius`

| Type   |
| ------ |
| number |

---

### `borderTopWidth`

| Type   |
| ------ |
| number |

---

### `borderWidth`

| Type   |
| ------ |
| number |

### `boxShadow`

:::note
`boxShadow` 僅在[新架構](/architecture/landing-page)中可用。外陰影僅支援 **Android 9+**。內陰影僅支援 **Android 10+**。
:::

為元素添加陰影效果，可控制陰影的位置、顏色、大小和模糊程度。此陰影可根據是否設置為_內陰影_而顯示在元素邊框盒的外部或內部。這是符合規範的[同名網頁樣式屬性](https://developer.mozilla.org/en-US/docs/Web/CSS/box-shadow)實現。詳細參數說明請參閱[BoxShadowValue](./boxshadowvalue)文件。

這些陰影可以組合使用，因此單一`boxShadow`可包含多種不同的陰影效果。

`boxShadow`接受模仿[網頁語法](https://developer.mozilla.org/en-US/docs/Web/CSS/box-shadow#syntax)的字串，或[BoxShadowValue](./boxshadowvalue)物件的陣列。

| Type |
| --------------------------- |
| array of BoxShadowValue ojects \| string |

### `cursor` <div class="label ios">iOS</div>

在iOS 17+上，設置為`pointer`可在指標（如觸控板、Apple Pencil或visionOS的視線）懸停於視圖時顯示懸停效果。

| Type                        |
| --------------------------- |
| enum(`'auto'`, `'pointer'`) |

---

### `elevation` <div class="label android">Android</div>

設定視圖的高度，使用Android底層的[高度API](https://developer.android.com/training/material/shadows-clipping.html#Elevation)。這會為項目添加投射陰影並影響重疊視圖的z軸順序。僅支援Android 5.0+，早期版本無效。

| Type   |
| ------ |
| number |

---

### `filter`

:::note
`filter` 僅在[新架構](/architecture/landing-page)中可用
:::

為`View`添加圖形濾鏡。此濾鏡由任意數量的_濾鏡函數_組成，每個函數代表對視圖圖形合成的原子級修改。完整濾鏡函數列表如下。`filter`將同時應用於`View`及其子視圖。`filter`隱含`overflow: hidden`，因此子視圖將被裁剪以適應`View`的邊界。

以下濾鏡函數在所有平台均可用：

- `brightness`: 調整視圖亮度。接受非負數或百分比。
- `opacity`: 調整視圖透明度（alpha值）。接受非負數或百分比。

:::note
由於效能與規範符合性問題，iOS僅支援上述兩種濾鏡函數。未來計劃探索使用SwiftUI替代UIKit的潛在解決方案。
:::

<div class="label basic android">Android</div>

以下濾鏡函數僅在Android平台有效：

- `blur`: 使用[高斯模糊](https://en.wikipedia.org/wiki/Gaussian_blur)對視圖進行模糊處理，指定數值代表模糊演算法中使用的半徑。任何非負的DIP值均有效（不支援百分比）。數值越大，模糊效果越明顯。
- `contrast`: 調整視圖的對比度。接受非負數值或百分比。
- `dropShadow`: 在視圖的alpha遮罩周圍添加陰影（僅視圖中非零alpha像素會投射陰影）。可選參數包括代表陰影顏色的色值，以及2或3個長度值。若指定2個長度，則分別解釋為`offsetX`和`offsetY`，表示陰影在X和Y軸上的位移。若提供第3個長度，則解釋為高斯模糊的標準差——數值越大陰影越模糊。詳細參數說明請參閱[DropShadowValue](./dropshadowvalue.md)。
- `grayscale`: 將視圖轉換為[灰階](https://en.wikipedia.org/wiki/Grayscale)，轉換程度由指定數值控制。接受非負數值或百分比，其中`1`或`100%`表示完全灰階。
- `hueRotate`: 調整視圖的[色相](https://en.wikipedia.org/wiki/Hue)。此函數的參數定義色輪旋轉角度（例如`360deg`將無效果），角度單位可為`deg`或`rad`。
- `invert`: 反轉視圖顏色。接受非負數值或百分比，`1`或`100%`表示完全反轉。
- `sepia`: 將視圖轉換為[深褐色調](<https://en.wikipedia.org/wiki/Sepia_(color)>)。接受非負數值或百分比，`1`或`100%`表示完全轉換。
- `saturate`: 調整視圖的[飽和度](https://en.wikipedia.org/wiki/Colorfulness)。接受非負數值或百分比。

:::note
`blur`和`dropShadow`僅支援 **Android 12+** 版本
:::

`filter`可接受由上述濾鏡函數組成的物件陣列，或模仿[網頁語法](https://developer.mozilla.org/en-US/docs/Web/CSS/filter#syntax)的字串。

| Type |
| ------ |
| array of objects: `{brightness: number\|string}`, `{opacity: number\|string}`, `{blur: number\|string}`, `{contrast: number\|string}`, `{dropShadow: DropShadowValue\|string}`, `{grayscale: number\|string}`, `{hueRotate: number\|string}`, `{invert: number\|string}`, `{sepia: number\|string}`, `{saturate: number\|string}` or string|

---

### `opacity`

| Type   |
| ------ |
| number |

### `pointerEvents`

控制視圖是否能成為觸控事件的目標。

- `'auto'`: 視圖可成為觸控事件目標。
- `'none'`: 視圖絕不成為觸控事件目標。
- `'box-none'`: 視圖本身不接收觸控事件，但其子視圖可以。
- `'box-only'`: 視圖本身可接收觸控事件，但其子視圖不可。

| Type                                                  |
| ----------------------------------------------------- |
| enum(`'auto'`, `'box-none'`, `'box-only'`, `'none'` ) |