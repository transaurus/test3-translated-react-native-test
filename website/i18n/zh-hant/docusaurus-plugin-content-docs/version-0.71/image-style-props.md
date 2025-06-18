---
id: image-style-props
title: Image Style Props
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 範例

### 圖片縮放模式

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Image%20Resize%20Modes%20Function%20Component%20Example
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

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Image%20Resize%20Modes%20Class%20Component%20Example
import React, {Component} from 'react';
import {View, Image, StyleSheet, Text} from 'react-native';

class DisplayAnImageWithStyle extends Component {
  render() {
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
  }
}

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

</TabItem>
</Tabs>

### 圖片邊框

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Style%20BorderWidth%20and%20BorderColor%20Function%20Component%20Example
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

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Style%20BorderWidth%20and%20BorderColor%20Class%20Component%20Example
import React, {Component} from 'react';
import {View, Image, StyleSheet, Text} from 'react-native';

class DisplayAnImageWithStyle extends Component {
  render() {
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
  }
}

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

</TabItem>
</Tabs>

### 圖片圓角

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Style%20Border%20Radius%20Function%20Component%20Example
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

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Style%20Border%20Radius%20Class%20Component%20Example
import React, {Component} from 'react';
import {View, Image, StyleSheet, Text} from 'react-native';

class DisplayAnImageWithStyle extends Component {
  render() {
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
  }
}

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

</TabItem>
</Tabs>

### 圖片色調

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

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

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Style%20tintColor%20Class%20Component
import React, {Component} from 'react';
import {View, Image, StyleSheet, Text} from 'react-native';

class DisplayAnImageWithStyle extends Component {
  render() {
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
  }
}

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

</TabItem>
</Tabs>

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

設定圖片的不透明度值。數值範圍應在 `0.0` 到 `1.0` 之間。

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

當圖片具有圓角時，指定 overlayColor 會使角落剩餘的空間以純色填充。這在以下 Android 圓角實現不支援的情況中特別有用：

- 特定縮放模式，例如 `'contain'`
- 動態 GIF

常見的使用方式是將圖片顯示在純色背景上，並將 `overlayColor` 設為與背景相同的顏色。

有關此功能底層運作原理的詳細說明，請參閱 [Fresco 文件](https://frescolib.org/docs/rounded-corners-and-circles.html)。

| Type   |
| ------ |
| string |

---

### `resizeMode`

決定當框架與原始圖片尺寸不符時如何調整圖片大小。預設值為 `cover`。

- `cover`: 均勻縮放圖片（保持圖片長寬比），使得：

  - 圖片的兩個維度（寬度和高度）都將等於或大於視圖的相應維度（減去內邊距）
  - 縮放後的圖片至少有一個維度會等於視圖的相應維度（減去內邊距）

- `contain`: 均勻縮放圖片（保持圖片長寬比），使得圖片的兩個維度（寬度和高度）都將等於或小於視圖的相應維度（減去內邊距）。

- `stretch`: 獨立縮放寬度和高度，這可能會改變原始圖片的長寬比。

- `repeat`: 重複圖片以填滿視圖的框架。圖片將保持其大小和長寬比，除非它比視圖大，在這種情況下它將被均勻縮小以使其包含在視圖中。

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