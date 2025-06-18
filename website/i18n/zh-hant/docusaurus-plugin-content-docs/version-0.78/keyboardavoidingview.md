---
id: keyboardavoidingview
title: KeyboardAvoidingView
---

此元件會根據鍵盤高度自動調整其高度、位置或底部內邊距，以確保在虛擬鍵盤顯示時仍保持可見。

## 範例

```SnackPlayer name=KeyboardAvoidingView&supportedPlatforms=android,ios
import React from 'react';
import {
  View,
  KeyboardAvoidingView,
  TextInput,
  StyleSheet,
  Text,
  Platform,
  TouchableWithoutFeedback,
  Button,
  Keyboard,
} from 'react-native';

const KeyboardAvoidingComponent = () => {
  return (
    <KeyboardAvoidingView
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
      style={styles.container}>
      <TouchableWithoutFeedback onPress={Keyboard.dismiss}>
        <View style={styles.inner}>
          <Text style={styles.header}>Header</Text>
          <TextInput placeholder="Username" style={styles.textInput} />
          <View style={styles.btnContainer}>
            <Button title="Submit" onPress={() => null} />
          </View>
        </View>
      </TouchableWithoutFeedback>
    </KeyboardAvoidingView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  inner: {
    padding: 24,
    flex: 1,
    justifyContent: 'space-around',
  },
  header: {
    fontSize: 36,
    marginBottom: 48,
  },
  textInput: {
    height: 40,
    borderColor: '#000000',
    borderBottomWidth: 1,
    marginBottom: 36,
  },
  btnContainer: {
    backgroundColor: 'white',
    marginTop: 12,
  },
});

export default KeyboardAvoidingComponent;
```

---

# 參考文獻

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view.md#props)。

---

### `behavior`

指定如何對鍵盤的存在做出反應。

> Android 和 iOS 對此屬性的處理方式不同。建議在 iOS 和 Android 上都設置 `behavior`。

| Type                                        |
| ------------------------------------------- |
| enum(`'height'`, `'position'`, `'padding'`) |

---

### `contentContainerStyle`

當 behavior 為 `'position'` 時，內容容器 (View) 的樣式。

| Type                              |
| --------------------------------- |
| [View Style](view-style-props.md) |

---

### `enabled`

啟用或停用 KeyboardAvoidingView。

| Type    | Default |
| ------- | ------- |
| boolean | `true`  |

---

### `keyboardVerticalOffset`

這是使用者螢幕頂部與 React Native 視圖之間的距離，在某些使用情境中可能不為零。

| Type   | Default |
| ------ | ------- |
| number | `0`     |