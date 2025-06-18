---
id: progressviewios
title: '🚧 ProgressViewIOS'
---

> **已棄用。** 請改用 [社群套件](https://reactnative.directory/?search=progressview) 之一。

使用 `ProgressViewIOS` 在 iOS 上渲染 UIProgressView。

### 範例

```SnackPlayer name=ProgressViewIOS&supportedPlatforms=ios
import React from 'react';
import {View, StyleSheet, ProgressViewIOS, Text} from 'react-native';

const App = () => {
  return (
    <View style={styles.container}>
      <View style={styles.example}>
        <Text>Progress Bar - 0%</Text>
        <ProgressViewIOS style={styles.progress} />
      </View>
      <View style={styles.example}>
        <Text>Colored Progress Bar - 50%</Text>
        <ProgressViewIOS
          style={styles.progress}
          progressTintColor=""
          progress={0.5}
        />
      </View>
      <View>
        <Text>Progress Bar - 100%</Text>
        <ProgressViewIOS
          style={styles.progress}
          progressTintColor="black"
          progress={1}
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
    marginVertical: 20,
  },
  progress: {
    width: 200,
  },
});

export default App;
```

---

# 參考

## 屬性

繼承 [View 屬性](view.md#props)。

### `progress`

進度值（介於 0 到 1 之間）。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `progressImage`

可拉伸的圖片，顯示為進度條。

| Type                   | Required |
| ---------------------- | -------- |
| Image.propTypes.source | No       |

---

### `progressTintColor`

進度條本身的色調顏色。

| Type   | Required |
| ------ | -------- |
| string | No       |

---

### `progressViewStyle`

進度條的樣式。

| Type                   | Required |
| ---------------------- | -------- |
| enum('default', 'bar') | No       |

---

### `trackImage`

可拉伸的圖片，顯示在進度條後方。

| Type                   | Required |
| ---------------------- | -------- |
| Image.propTypes.source | No       |

---

### `trackTintColor`

進度條軌道的色調顏色。

| Type   | Required |
| ------ | -------- |
| string | No       |