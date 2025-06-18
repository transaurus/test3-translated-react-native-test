---
id: height-and-width
title: Height and Width
---

元件的高度和寬度決定了它在螢幕上的顯示尺寸。

## 固定尺寸

設定元件尺寸的通用方法是透過樣式添加固定的 `width` 和 `height`。React Native 中的所有尺寸皆為無單位數值，代表與螢幕密度無關的像素。

```SnackPlayer name=Height%20and%20Width
import React from 'react';
import {View} from 'react-native';

const FixedDimensionsBasics = () => {
  return (
    <View>
      <View
        style={{
          width: 50,
          height: 50,
          backgroundColor: 'powderblue',
        }}
      />
      <View
        style={{
          width: 100,
          height: 100,
          backgroundColor: 'skyblue',
        }}
      />
      <View
        style={{
          width: 150,
          height: 150,
          backgroundColor: 'steelblue',
        }}
      />
    </View>
  );
};

export default FixedDimensionsBasics;
```

此種設定方式常見於需固定為特定點數尺寸、不需根據螢幕大小計算的元件。

:::caution
點數與實際物理測量單位之間沒有通用換算標準。這意味著具有固定尺寸的元件在不同裝置和螢幕尺寸下，可能不會呈現相同的物理大小。但多數使用情境下此差異並不明顯。
:::

## 彈性尺寸

在元件樣式中使用 `flex` 可讓元件根據可用空間動態擴展或收縮。通常會使用 `flex: 1`，這會使元件填滿所有可用空間，並與同層級的其他元件均分空間。`flex` 值越大，該元件相對於兄弟元件所佔空間比例越高。

:::info
元件僅能在父層尺寸大於 `0` 時擴展以填滿空間。若父層未設定固定 `width` 和 `height` 或 `flex` 屬性，父層尺寸將為 `0`，導致彈性子元件不可見。
:::

```SnackPlayer name=Flex%20Dimensions
import React from 'react';
import {View} from 'react-native';

const FlexDimensionsBasics = () => {
  return (
    // Try removing the `flex: 1` on the parent View.
    // The parent will not have dimensions, so the children can't expand.
    // What if you add `height: 300` instead of `flex: 1`?
    <View style={{flex: 1}}>
      <View style={{flex: 1, backgroundColor: 'powderblue'}} />
      <View style={{flex: 2, backgroundColor: 'skyblue'}} />
      <View style={{flex: 3, backgroundColor: 'steelblue'}} />
    </View>
  );
};

export default FlexDimensionsBasics;
```

掌握元件尺寸控制後，下一步是[學習如何在螢幕上排版](flexbox.md)。

## 百分比尺寸

若想填滿螢幕特定比例但_不_使用 `flex` 佈局，可於元件樣式中使用**百分比值**。與彈性尺寸類似，百分比尺寸需父層具有明確定義的尺寸。

```SnackPlayer name=Percentage%20Dimensions
import React from 'react';
import {View} from 'react-native';

const PercentageDimensionsBasics = () => {
  // Try removing the `height: '100%'` on the parent View.
  // The parent will not have dimensions, so the children can't expand.
  return (
    <View style={{height: '100%'}}>
      <View
        style={{
          height: '15%',
          backgroundColor: 'powderblue',
        }}
      />
      <View
        style={{
          width: '66%',
          height: '35%',
          backgroundColor: 'skyblue',
        }}
      />
      <View
        style={{
          width: '33%',
          height: '50%',
          backgroundColor: 'steelblue',
        }}
      />
    </View>
  );
};

export default PercentageDimensionsBasics;
```