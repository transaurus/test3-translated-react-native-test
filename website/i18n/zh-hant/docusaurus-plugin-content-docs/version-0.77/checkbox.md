---
id: checkbox
title: '🚧 CheckBox'
---

> **已移除。** 請改用 [社群套件](https://reactnative.directory/?search=checkbox) 之一。

渲染一個布林值輸入元件（僅限 Android）。

這是一個受控元件，需要透過 `onValueChange` 回調函數來更新 `value` 屬性，才能使元件反映使用者操作。如果未更新 `value` 屬性，元件將繼續渲染提供的 `value` 屬性值，而非使用者操作的預期結果。

## 範例

```SnackPlayer name=CheckBox%20Component%20Example&supportedPlatforms=android,web&ext=js
import React, {useState} from 'react';
import {CheckBox, Text, StyleSheet, View} from 'react-native';

const App = () => {
  const [isSelected, setSelection] = useState(false);

  return (
    <View style={styles.container}>
      <View style={styles.checkboxContainer}>
        <CheckBox
          value={isSelected}
          onValueChange={setSelection}
          style={styles.checkbox}
        />
        <Text style={styles.label}>Do you like React Native?</Text>
      </View>
      <Text>Is CheckBox selected: {isSelected ? '👍' : '👎'}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  checkboxContainer: {
    flexDirection: 'row',
    marginBottom: 20,
  },
  checkbox: {
    alignSelf: 'center',
  },
  label: {
    margin: 8,
  },
});

export default App;
```

---

# 參考文獻

## 屬性

繼承 [View 元件屬性](view#props)。

---

### `disabled`

若為 true，使用者將無法切換核取方塊。預設值為 `false`。

| Type | Required |
| ---- | -------- |
| bool | No       |

---

### `onChange`

當屬性變更導致元件被移除時使用。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `onValueChange`

當數值變更時，以新值觸發此函數。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `testID`

用於在端對端測試中定位此視圖。

| Type   | Required |
| ------ | -------- |
| string | No       |

---

### `value`

核取方塊的數值。若為 true，核取方塊將呈現開啟狀態。預設值為 `false`。

| Type | Required |
| ---- | -------- |
| bool | No       |