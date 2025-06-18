---
id: toastandroid
title: ToastAndroid
---

React Native 的 ToastAndroid API 將 Android 平台的 ToastAndroid 模組作為 JS 模組公開。它提供方法 `show(message, duration)`，接收以下參數：

- _message_ 要顯示的提示文字字串
- _duration_ 提示顯示的持續時間——可選 `ToastAndroid.SHORT` 或 `ToastAndroid.LONG`

您也可以使用 `showWithGravity(message, duration, gravity)` 來指定提示在螢幕佈局中的顯示位置。可選值為 `ToastAndroid.TOP`、`ToastAndroid.BOTTOM` 或 `ToastAndroid.CENTER`。

方法 'showWithGravityAndOffset(message, duration, gravity, xOffset, yOffset)' 增加了以像素為單位指定偏移量的功能。

```SnackPlayer name=Toast%20Android%20API%20Example&supportedPlatforms=android
import React from "react";
import { View, StyleSheet, ToastAndroid, Button, StatusBar } from "react-native";

const App = () => {
  const showToast = () => {
    ToastAndroid.show("A pikachu appeared nearby !", ToastAndroid.SHORT);
  };

  const showToastWithGravity = () => {
    ToastAndroid.showWithGravity(
      "All Your Base Are Belong To Us",
      ToastAndroid.SHORT,
      ToastAndroid.CENTER
    );
  };

  const showToastWithGravityAndOffset = () => {
    ToastAndroid.showWithGravityAndOffset(
      "A wild toast appeared!",
      ToastAndroid.LONG,
      ToastAndroid.BOTTOM,
      25,
      50
    );
  };

  return (
    <View style={styles.container}>
      <Button title="Toggle Toast" onPress={() => showToast()} />
      <Button
        title="Toggle Toast With Gravity"
        onPress={() => showToastWithGravity()}
      />
      <Button
        title="Toggle Toast With Gravity & Offset"
        onPress={() => showToastWithGravityAndOffset()}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    paddingTop: StatusBar.currentHeight,
    backgroundColor: "#888888",
    padding: 8
  }
});

export default App;
```

### 命令式用法

ToastAndroid API 是命令式的，但可以透過以下範例將其封裝為宣告式元件：

```SnackPlayer name=Advanced%20Toast%20Android%20API%20Example&supportedPlatforms=android
import React, { useState, useEffect } from "react";
import { View, StyleSheet, ToastAndroid, Button, StatusBar } from "react-native";

const Toast = ({ visible, message }) => {
  if (visible) {
    ToastAndroid.showWithGravityAndOffset(
      message,
      ToastAndroid.LONG,
      ToastAndroid.BOTTOM,
      25,
      50
    );
    return null;
  }
  return null;
};

const App = () => {
  const [visibleToast, setVisibleToast] = useState(false);

  useEffect(() => setVisibleToast(false), [visibleToast]);

  const handleButtonPress = () => {
    setVisibleToast(true);
  };

  return (
    <View style={styles.container}>
      <Toast visible={visibleToast} message="Example" />
      <Button title="Toggle Toast" onPress={() => handleButtonPress()} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    paddingTop: StatusBar.currentHeight,
    backgroundColor: "#888888",
    padding: 8
  }
});

export default App;
```

---

# 參考文獻

## 方法

### `show()`

```jsx
static show(message, duration)
```

---

### `showWithGravity()`

```jsx
static showWithGravity(message, duration, gravity)
```

---

### `showWithGravityAndOffset()`

```jsx
static showWithGravityAndOffset(message, duration, gravity, xOffset, yOffset)
```

## 屬性

### `SHORT`

表示提示在螢幕上顯示的持續時間。

```jsx
ToastAndroid.SHORT;
```

---

### `LONG`

表示提示在螢幕上顯示的持續時間。

```jsx
ToastAndroid.LONG;
```

---

### `TOP`

表示提示在螢幕上的顯示位置。

```jsx
ToastAndroid.TOP;
```

---

### `BOTTOM`

表示提示在螢幕上的顯示位置。

```jsx
ToastAndroid.BOTTOM;
```

---

### `CENTER`

表示提示在螢幕上的顯示位置。

```jsx
ToastAndroid.CENTER;
```