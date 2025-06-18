---
id: inputaccessoryview
title: InputAccessoryView
---

此元件可用於客製化 iOS 鍵盤上方的輸入輔助視圖。當 `TextInput` 獲得焦點時，輸入輔助視圖會顯示於鍵盤上方，適合用來建立自訂工具列。

使用時需將自訂工具列以 InputAccessoryView 元件包裹，並設定 `nativeID`。接著在目標 `TextInput` 中傳入相同的 `nativeID` 作為 `inputAccessoryViewID` 屬性。基礎範例：

```SnackPlayer name=InputAccessoryView&supportedPlatforms=ios
import React, {useState} from 'react';
import {Button, InputAccessoryView, ScrollView, TextInput} from 'react-native';

const App = () => {
  const inputAccessoryViewID = 'uniqueID';
  const initialText = '';
  const [text, setText] = useState(initialText);

  return (
    <>
      <ScrollView keyboardDismissMode="interactive">
        <TextInput
          style={{
            padding: 16,
            marginTop: 50,
          }}
          inputAccessoryViewID={inputAccessoryViewID}
          onChangeText={setText}
          value={text}
          placeholder={'Please type here…'}
        />
      </ScrollView>
      <InputAccessoryView nativeID={inputAccessoryViewID}>
        <Button onPress={() => setText(initialText)} title="Clear text" />
      </InputAccessoryView>
    </>
  );
};

export default App;
```

此元件亦可用於建立黏性文字輸入框（固定在鍵盤頂部的文字輸入框）。實作方式是用 `InputAccessoryView` 包裹 `TextInput` 且不設定 `nativeID`。範例可參考 [InputAccessoryViewExample.js](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/InputAccessoryView/InputAccessoryViewExample.js)。

---

# 參考文獻

## 屬性

### `backgroundColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `nativeID`

用於將此 `InputAccessoryView` 與特定 TextInput 元件建立關聯的識別碼。

| Type   |
| ------ |
| string |

---

### `style`

| Type                              |
| --------------------------------- |
| [View Style](view-style-props.md) |

# 已知問題

- [react-native#18997](https://github.com/facebook/react-native/issues/18997): 不支援多行 `TextInput`
- [react-native#20157](https://github.com/facebook/react-native/issues/20157): 無法與底部標籤欄共同使用