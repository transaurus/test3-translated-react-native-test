---
id: usecolorscheme
title: useColorScheme
---

```tsx
import {useColorScheme} from 'react-native';
```

`useColorScheme` React 鉤子會提供並訂閱來自 [`Appearance`](appearance) 模組的色彩方案更新。回傳值表示使用者當前偏好的色彩方案。該值可能會在後續更新，可能是透過使用者直接操作（例如在裝置設定中選擇主題），或是根據排程（例如跟隨日夜循環的淺色與深色主題）。

### 支援的色彩方案

- `"light"`：使用者偏好淺色主題。
- `"dark"`：使用者偏好深色主題。
- `null`：使用者未指定偏好色彩方案。

> **注意：** 目前由於技術限制，當啟用 Chrome 偵錯工具時，此鉤子將_始終_回傳 `"light"`。

---

## 範例

```SnackPlayer
import React from 'react';
import {Text, StyleSheet, useColorScheme, View} from 'react-native';

const App = () => {
  const colorScheme = useColorScheme();
  return (
    <View style={styles.container}>
      <Text>useColorScheme(): {colorScheme}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default App;
```

您可以在 [`AppearanceExample.js`](https://github.com/facebook/react-native/blob/0.71-stable/packages/rn-tester/js/examples/Appearance/AppearanceExample.js) 中找到完整範例，該範例演示如何將此鉤子與 React 上下文結合使用，為您的應用程式添加淺色與深色主題支援。