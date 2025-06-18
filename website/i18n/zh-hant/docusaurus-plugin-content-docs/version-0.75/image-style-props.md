---
id: image-style-props
title: Image Style Props
---

## 範例

### 圖片縮放模式

```SnackPlayer name=Image%20Resize%20Modes%20Example
import React from 'react';
import {View, Image, Text, StyleSheet} from 'react-native';

const DisplayAnImageWithStyle = () => {
  return (
    <View style={styles.container}>
      <View>
        <Image
          style={{
            resizeMode: 'cover',
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>resizeMode : cover</Text>
      </View>
      <View>
        <Image
          style={{
            resizeMode: 'contain',
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>resizeMode : contain</Text>
      </View>
      <View>
        <Image
          style={{
            resizeMode: 'stretch',
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>resizeMode : stretch</Text>
      </View>
      <View>
        <Image
          style={{
            resizeMode: 'repeat',
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>resizeMode : repeat</Text>
      </View>
      <View>
        <Image
          style={{
            resizeMode: 'center',
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>resizeMode : center</Text>
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    display: 'flex',
    flexDirection: 'column',
    justifyContent: 'space-around',
    alignItems: 'center',
    height: '100%',
    textAlign: 'center',
  },
});

export default DisplayAnImageWithStyle;
```

### 圖片邊框

```SnackPlayer name=Style%20BorderWidth%20and%20BorderColor%20Example
import React from 'react';
import {View, Image, StyleSheet, Text} from 'react-native';

const DisplayAnImageWithStyle = () => {
  return (
    <View style={styles.container}>
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
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    display: 'flex',
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
    height: '100%',
    textAlign: 'center',
  },
});

export default DisplayAnImageWithStyle;
```

### 圖片圓角半徑

```SnackPlayer name=Style%20Border%20Radius%20Example
import React from 'react';
import {View, Image, StyleSheet, Text} from 'react-native';

const DisplayAnImageWithStyle = () => {
  return (
    <View style={styles.container}>
      <View>
        <Image
          style={{
            borderTopRightRadius: 20,
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>borderTopRightRadius</Text>
      </View>
      <View>
        <Image
          style={{
            borderBottomRightRadius: 20,
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>borderBottomRightRadius</Text>
      </View>
      <View>
        <Image
          style={{
            borderBottomLeftRadius: 20,
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>borderBottomLeftRadius</Text>
      </View>
      <View>
        <Image
          style={{
            borderTopLeftRadius: 20,
            height: 100,
            width: 200,
          }}
          source={require('@expo/snack-static/react-native-logo.png')}
        />
        <Text>borderTopLeftRadius</Text>
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    display: 'flex',
    flexDirection: 'column',
    justifyContent: 'space-around',
    alignItems: 'center',
    height: '100%',
    textAlign: 'center',
  },
});

export default DisplayAnImageWithStyle;
```

### 圖片色調

```SnackPlayer name=Style%20tintColor%20Function%20Component
import React from 'react';
import {View, Image, StyleSheet, Text} from 'react-native';

const DisplayAnImageWithStyle = () => {
  return (
    <View style={styles.container}>
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
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    display: 'flex',
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
    height: '100%',
    textAlign: 'center',
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

設定圖片的透明度值。數值範圍應在 `0.0` 到 `1.0` 之間。

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

當圖片具有圓角時，指定 overlayColor 會使角落剩餘的空間填充純色。這在以下 Android 圓角實作不支援的情況下特別有用：

- 某些縮放模式，例如 `'contain'`
- 動態 GIF

典型的使用方式是將圖片顯示在純色背景上，並將 `overlayColor` 設為與背景相同的顏色。

有關此功能底層運作原理的詳細資訊，請參閱 [Fresco 文件](https://frescolib.org/docs/rounded-corners-and-circles.html)。

| Type   |
| ------ |
| string |

---

### `resizeMode`

決定當框架與原始圖片尺寸不符時如何調整圖片大小。預設為 `cover`。

- `cover`: Scale the image uniformly (maintain the image's aspect ratio) so that:

  - Both dimensions (width and height) of the image will be equal to or larger than the corresponding dimension of the view (minus padding)
  - At least one dimension of the scaled image will be equal to the corresponding dimension of the view (minus padding)

- `contain`: Scale the image uniformly (maintain the image's aspect ratio) so that both dimensions (width and height) of the image will be equal to or less than the corresponding dimension of the view (minus padding).

- `stretch`: Scale width and height independently, This may change the aspect ratio of the src.

- `repeat`: Repeat the image to cover the frame of the view. The image will keep its size and aspect ratio, unless it is larger than the view, in which case it will be scaled down uniformly so that it is contained in the view.

- `center`: Center the image in the view along both dimensions. If the image is larger than the view, scale it down uniformly so that it is contained in the view.

| Type                                                              | Default   |
| ----------------------------------------------------------------- | --------- |
| enum(`'cover'`, `'contain'`, `'stretch'`, `'repeat'`, `'center'`) | `'cover'` |

---

### `objectFit`

Determines how to resize the image when the frame doesn't match the raw image dimensions.

| Type                                                   | Default   |
| ------------------------------------------------------ | --------- |
| enum(`'cover'`, `'contain'`, `'fill'`, `'scale-down'`) | `'cover'` |

---

### `tintColor`

Changes the color of all the non-transparent pixels to the tintColor.

| Type               |
| ------------------ |
| [color](colors.md) |