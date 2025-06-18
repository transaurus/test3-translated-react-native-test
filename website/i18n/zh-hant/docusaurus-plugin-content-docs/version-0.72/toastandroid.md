---
id: toastandroid
title: ToastAndroid
---

React Native 的 ToastAndroid API 將 Android 平台的 ToastAndroid 模組作為 JS 模組公開。它提供了方法 `show(message, duration)`，該方法接受以下參數：

- _message_ 要顯示的提示文字字串
- _duration_ 提示顯示的持續時間——可以是 `ToastAndroid.SHORT` 或 `ToastAndroid.LONG`

您也可以使用 `showWithGravity(message, duration, gravity)` 來指定提示在螢幕佈局中的顯示位置。可選值為 `ToastAndroid.TOP`、`ToastAndroid.BOTTOM` 或 `ToastAndroid.CENTER`。

方法 'showWithGravityAndOffset(message, duration, gravity, xOffset, yOffset)' 增加了以像素為單位指定偏移量的功能。

```SnackPlayer name=Toast%20Android%20API%20Example&supportedPlatforms=android
import React from 'react';
import {View, StyleSheet, ToastAndroid, Button, StatusBar} from 'react-native';

const App = () => {
  const showToast = () => {
    ToastAndroid.show('A pikachu appeared nearby !', ToastAndroid.SHORT);
  };

  const showToastWithGravity = () => {
    ToastAndroid.showWithGravity(
      'All Your Base Are Belong To Us',
      ToastAndroid.SHORT,
      ToastAndroid.CENTER,
    );
  };

  const showToastWithGravityAndOffset = () => {
    ToastAndroid.showWithGravityAndOffset(
      'A wild toast appeared!',
      ToastAndroid.LONG,
      ToastAndroid.BOTTOM,
      25,
      50,
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
    justifyContent: 'center',
    paddingTop: StatusBar.currentHeight,
    backgroundColor: '#888888',
    padding: 8,
  },
});

export default App;
```

---

# 參考文獻

## 方法

### `show()`

```tsx
static show(message: string, duration: number);
```

---

### `showWithGravity()`

```tsx
static showWithGravity(message: string, duration: number, gravity: number);
```

---

### `showWithGravityAndOffset()`

```tsx
static showWithGravityAndOffset(
  message: string,
  duration: number,
  gravity: number,
  xOffset: number,
  yOffset: number,
);
```

## 屬性

### `SHORT`

表示提示在螢幕上的顯示時間。

```tsx
static SHORT: number;
```

---

### `LONG`

表示提示在螢幕上的顯示時間。

```tsx
static LONG: number;
```

---

### `TOP`

表示提示在螢幕上的位置。

```tsx
static TOP: number;
```

---

### `BOTTOM`

表示提示在螢幕上的位置。

```tsx
static BOTTOM: number;
```

---

### `CENTER`

表示提示在螢幕上的位置。

```tsx
static CENTER: number;
```