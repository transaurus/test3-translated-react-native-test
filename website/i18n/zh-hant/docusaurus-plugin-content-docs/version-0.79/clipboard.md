---
id: clipboard
title: '🚧 Clipboard'
---

> **已移除。** 請改用其中一個[社群套件](https://reactnative.directory/?search=clipboard)。

`Clipboard` 提供了一個介面，用於在 Android 和 iOS 上設定和取得剪貼簿內容

---

## 範例

```SnackPlayer name=Clipboard%20API%20Example&supportedPlatforms=ios,android
import React, {useState} from 'react';
import {
  View,
  Text,
  TouchableOpacity,
  Clipboard,
  StyleSheet,
} from 'react-native';

const App = () => {
  const [copiedText, setCopiedText] = useState('');

  const copyToClipboard = () => {
    Clipboard.setString('hello world');
  };

  const fetchCopiedText = async () => {
    const text = await Clipboard.getString();
    setCopiedText(text);
  };

  return (
    <View style={{flex: 1}}>
      <View style={styles.container}>
        <TouchableOpacity onPress={() => copyToClipboard()}>
          <Text>Click here to copy to Clipboard</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={() => fetchCopiedText()}>
          <Text>View copied text</Text>
        </TouchableOpacity>

        <Text style={styles.copiedText}>{copiedText}</Text>
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
  copiedText: {
    marginTop: 10,
    color: 'red',
  },
});

export default App;
```

# 參考

## 方法

### `getString()`

```jsx
static getString()
```

取得字串類型的內容，此方法回傳一個 `Promise`，因此你可以使用以下程式碼來取得剪貼簿內容

```jsx
async _getContent() {
  const content = await Clipboard.getString();
}
```

---

### `setString()`

```jsx
static setString(content)
```

設定字串類型的內容。你可以使用以下程式碼來設定剪貼簿內容

```jsx
_setContent() {
  Clipboard.setString('hello world');
}
```

**參數：**

| Name    | Type   | Required | Description                               |
| ------- | ------ | -------- | ----------------------------------------- |
| content | string | Yes      | The content to be stored in the clipboard |

_注意_

當你嘗試複製非 `string` 和 `number` 類型的資料到剪貼簿時要特別小心，某些資料需要額外的字串化處理。例如，如果你嘗試複製陣列，Android 會拋出異常，但 iOS 不會。