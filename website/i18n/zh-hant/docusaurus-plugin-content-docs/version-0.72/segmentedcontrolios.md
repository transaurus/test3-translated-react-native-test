---
id: segmentedcontrolios
title: '🚧 SegmentedControlIOS'
---

> **已從 React Native 移除。** 請改用 [社群套件](https://reactnative.directory/?search=segmentedcontrol) 中的替代方案。

使用 `SegmentedControlIOS` 來渲染 iOS 的 UISegmentedControl 控制項。

#### 以程式方式變更選取索引

可透過將 selectedIndex 屬性賦值給狀態變數，再變更該變數來動態調整選取索引。請注意，當使用者選取值並變更索引時，需同步更新該狀態變數，如下方範例所示。

## 範例

```SnackPlayer name=SegmentedControlIOS%20Example&supportedPlatforms=ios&ext=js
import React, {useState} from 'react';
import {SegmentedControlIOS, StyleSheet, Text, View} from 'react-native';

const App = () => {
  const [index, setIndex] = useState(0);
  return (
    <View style={styles.container}>
      <SegmentedControlIOS
        values={['One', 'Two']}
        selectedIndex={index}
        onChange={event => {
          setIndex(event.nativeEvent.selectedSegmentIndex);
        }}
      />
      <Text style={styles.text}>Selected index: {index}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 24,
    justifyContent: 'center',
  },
  text: {
    marginTop: 24,
  },
});

export default App;
```

---

# 參考文件

## 屬性

繼承 [View 元件屬性](view.md#props)。

### `enabled`

若設為 false，使用者將無法與控制項互動。預設值為 true。

| Type | Required |
| ---- | -------- |
| bool | No       |

---

### `momentary`

若設為 true，選取區段時不會保持視覺狀態。`onValueChange` 回調函式仍會正常運作。

| Type | Required |
| ---- | -------- |
| bool | No       |

---

### `onChange`

當使用者點擊區段時觸發的回調函式；會將事件作為參數傳遞

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `onValueChange`

當使用者點擊區段時觸發的回調函式；會將該區段的值作為參數傳遞

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `selectedIndex`

要（預先）選取的區段在 `props.values` 中的索引值。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `tintColor`

> **注意：** iOS 13+ 不支援 `tintColor` 屬性。

控制項的主題色。

| Type   | Required |
| ------ | -------- |
| string | No       |

---

### `values`

控制項各區段按鈕的標籤文字（依序排列）。

| Type            | Required |
| --------------- | -------- |
| array of string | No       |