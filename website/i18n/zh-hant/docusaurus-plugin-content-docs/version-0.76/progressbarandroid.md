---
id: progressbarandroid
title: '🚧 ProgressBarAndroid'
---

> **已棄用。** 請改用 [社群套件](https://reactnative.directory/?search=progressbar) 中的其中一個。

僅限 Android 的 React 元件，用於表示應用程式正在載入或應用程式中有某些活動。

### 範例

```SnackPlayer name=ProgressBarAndroid&supportedPlatforms=android
import React from 'react';
import {View, StyleSheet, ProgressBarAndroid, Text} from 'react-native';

const App = () => {
  return (
    <View style={styles.container}>
      <View style={styles.example}>
        <Text>Circle Progress Indicator</Text>
        <ProgressBarAndroid />
      </View>
      <View style={styles.example}>
        <Text>Horizontal Progress Indicator</Text>
        <ProgressBarAndroid styleAttr="Horizontal" />
      </View>
      <View style={styles.example}>
        <Text>Colored Progress Indicator</Text>
        <ProgressBarAndroid styleAttr="Horizontal" color="#2196F3" />
      </View>
      <View style={styles.example}>
        <Text>Fixed Progress Value</Text>
        <ProgressBarAndroid
          styleAttr="Horizontal"
          indeterminate={false}
          progress={0.5}
        />
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  example: {
    marginVertical: 24,
  },
});

export default App;
```

---

# 參考

## 屬性

繼承 [View 屬性](view.md#props)。

### `animating`

是否顯示 ProgressBar（true，預設值）或隱藏它（false）。

| Type | Required |
| ---- | -------- |
| bool | No       |

---

### `color`

進度條的顏色。

| Type               | Required |
| ------------------ | -------- |
| [color](colors.md) | No       |

---

### `indeterminate`

進度條是否顯示不確定進度。請注意，僅當 styleAttr 為 Horizontal 時才能設為 false，並且需要指定 `progress` 值。

| Type              | Required |
| ----------------- | -------- |
| indeterminateType | No       |

---

### `progress`

進度值（介於 0 和 1 之間）。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `styleAttr`

ProgressBar 的樣式。可選值為：

- Horizontal
- Normal（預設值）
- Small
- Large
- Inverse
- SmallInverse
- LargeInverse

| Type                                                                                      | Required |
| ----------------------------------------------------------------------------------------- | -------- |
| enum('Horizontal', 'Normal', 'Small', 'Large', 'Inverse', 'SmallInverse', 'LargeInverse') | No       |

---

### `testID`

用於在端對端測試中定位此視圖。

| Type   | Required |
| ------ | -------- |
| string | No       |