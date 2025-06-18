---
id: image-style-props
title: Image Style Props
---

## 範例

### 圖片縮放模式

```SnackPlayer name=Image%20Resize%20Modes%20Example
import React from 'react';
import {View, Image, Text, StyleSheet, ScrollView} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const asset = require('@expo/snack-static/react-native-logo.png');

const DisplayAnImageWithStyle = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container} edges={['top']}>
      <ScrollView style={styles.scrollView}>
        <View>
          <Image style={[styles.image, {resizeMode: 'cover'}]} source={asset} />
          <Text style={styles.text}>resizeMode : cover</Text>
        </View>
        <View>
          <Image
            style={[styles.image, {resizeMode: 'contain'}]}
            source={asset}
          />
          <Text style={styles.text}>resizeMode : contain</Text>
        </View>
        <View>
          <Image
            style={[styles.image, {resizeMode: 'stretch'}]}
            source={asset}
          />
          <Text style={styles.text}>resizeMode : stretch</Text>
        </View>
        <View>
          <Image
            style={[styles.image, {resizeMode: 'repeat'}]}
            source={asset}
          />
          <Text style={styles.text}>resizeMode : repeat</Text>
        </View>
        <View>
          <Image
            style={[styles.image, {resizeMode: 'center'}]}
            source={asset}
          />
          <Text style={styles.text}>resizeMode : center</Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  scrollView: {
    padding: 12,
    alignItems: 'center',
    gap: 16,
  },
  image: {
    borderWidth: 1,
    borderColor: 'red',
    height: 100,
    width: 200,
  },
  text: {
    textAlign: 'center',
    marginBottom: 12,
  },
});

export default DisplayAnImageWithStyle;
```

### 圖片邊框

```SnackPlayer name=Style%20BorderWidth%20and%20BorderColor%20Example
import React from 'react';
import {Image, StyleSheet, Text} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const DisplayAnImageWithStyle = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Image
        style={{
          borderColor: 'red',
          borderWidth: 5,
          height: 100,
          width: 200,
        }}
        source={require('@expo/snack-static/react-native-logo.png')}
      />
      <Text>borderColor & borderWidth</Text>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default DisplayAnImageWithStyle;
```

### 圖片圓角

```SnackPlayer name=Style%20Border%20Radius%20Example
import React from 'react';
import {View, Image, StyleSheet, Text, ScrollView} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const asset = require('@expo/snack-static/react-native-logo.png');

const DisplayAnImageWithStyle = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <ScrollView style={styles.scrollView}>
        <View>
          <Image
            style={[styles.image, {borderTopRightRadius: 20}]}
            source={asset}
          />
          <Text>borderTopRightRadius</Text>
        </View>
        <View>
          <Image
            style={[styles.image, {borderBottomRightRadius: 20}]}
            source={asset}
          />
          <Text>borderBottomRightRadius</Text>
        </View>
        <View>
          <Image
            style={[styles.image, {borderBottomLeftRadius: 20}]}
            source={asset}
          />
          <Text>borderBottomLeftRadius</Text>
        </View>
        <View>
          <Image
            style={[styles.image, {borderTopLeftRadius: 20}]}
            source={asset}
          />
          <Text>borderTopLeftRadius</Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  scrollView: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  image: {
    borderWidth: 1,
    borderColor: 'red',
    height: 100,
    width: 200,
  },
});

export default DisplayAnImageWithStyle;
```

### 圖片色調

```SnackPlayer name=Style%20tintColor%20Function%20Component
import React from 'react';
import {Image, StyleSheet, Text} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const DisplayAnImageWithStyle = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Image
        style={{
          tintColor: '#000000',
          resizeMode: 'contain',
          height: 100,
          width: 200,
        }}
        source={require('@expo/snack-static/react-native-logo.png')}
      />
      <Text>tintColor</Text>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default DisplayAnImageWithStyle;
```

# 參考文獻

## 屬性

### `backfaceVisibility`

此屬性定義旋轉圖片的背面是否可見。

| Type                          | Default     |
| ----------------------------- | ----------- |
| enum(`'visible'`, `'hidden'`) | `'visible'` |

---

### `backgroundColor`

| Type               |
| ------------------ |
| [color](colors.md) |

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

### `borderColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderRadius`

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

### `borderWidth`

| Type   |
| ------ |
| number |

---

### `opacity`

設定圖片的透明度值。數值範圍應介於 `0.0` 至 `1.0` 之間。

| Type   | Default |
| ------ | ------- |
| number | `1.0`   |

---

### `overflow`

| Type                          | Default     |
| ----------------------------- | ----------- |
| enum(`'visible'`, `'hidden'`) | `'visible'` |

---

### `overlayColor` <div class="label android">Android</div>

當圖片具有圓角時，指定 overlayColor 會使角落剩餘空間以純色填充。這在以下 Android 圓角實作不支援的情況中特別有用：

- 特定縮放模式，例如 `'contain'`
- 動態 GIF

典型用法是將此屬性設為與背景相同的顏色，適用於顯示在純色背景上的圖片。

底層實作細節請參閱 [Fresco 文件](https://frescolib.org/docs/rounded-corners-and-circles.html)。

| Type   |
| ------ |
| string |

---

### `resizeMode`

決定當圖框與原始圖片尺寸不符時如何調整圖片大小。預設值為 `cover`。

- `cover`: 均勻縮放圖片（保持圖片長寬比），使得：

  - 圖片的兩個維度（寬度和高度）都將等於或大於視圖的相應維度（減去內邊距）
  - 縮放後的圖片至少有一個維度會等於視圖的相應維度（減去內邊距）

- `contain`: 均勻縮放圖片（保持圖片長寬比），使得圖片的兩個維度（寬度和高度）都將等於或小於視圖的相應維度（減去內邊距）。

- `stretch`: 獨立縮放寬度和高度，這可能會改變原始圖片的長寬比。

- `repeat`: 重複圖片以覆蓋視圖的框架。圖片將保持其大小和長寬比，除非它比視圖大，在這種情況下它將被均勻縮小以使其包含在視圖中。

- `center`: 在視圖的兩個維度上居中圖片。如果圖片比視圖大，則均勻縮小以使其包含在視圖中。

| Type                                                              | Default   |
| ----------------------------------------------------------------- | --------- |
| enum(`'cover'`, `'contain'`, `'stretch'`, `'repeat'`, `'center'`) | `'cover'` |

---

### `objectFit`

決定當框架與原始圖片尺寸不匹配時如何調整圖片大小。

| Type                                                   | Default   |
| ------------------------------------------------------ | --------- |
| enum(`'cover'`, `'contain'`, `'fill'`, `'scale-down'`) | `'cover'` |

---

### `tintColor`

將所有非透明像素的顏色更改為指定的 tintColor。

| Type               |
| ------------------ |
| [color](colors.md) |