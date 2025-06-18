---
id: switch
title: Switch
---

渲染一個布林值輸入元件。

這是一個受控元件，需要透過 `onValueChange` 回調函數來更新 `value` 屬性，才能使元件反映用戶操作。如果未更新 `value` 屬性，元件將繼續顯示提供的 `value` 屬性值，而非用戶操作的預期結果。

## 範例

```SnackPlayer name=Switch&supportedPlatforms=android,ios
import React, {useState} from 'react';
import {View, Switch, StyleSheet} from 'react-native';

const App = () => {
  const [isEnabled, setIsEnabled] = useState(false);
  const toggleSwitch = () => setIsEnabled(previousState => !previousState);

  return (
    <View style={styles.container}>
      <Switch
        trackColor={{false: '#767577', true: '#81b0ff'}}
        thumbColor={isEnabled ? '#f5dd4b' : '#f4f3f4'}
        ios_backgroundColor="#3e3e3e"
        onValueChange={toggleSwitch}
        value={isEnabled}
      />
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

---

# 參考文獻

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view.md#props)。

---

### `disabled`

若設為 true，用戶將無法切換開關狀態。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `ios_backgroundColor` <div class="label ios">iOS</div>

在 iOS 平台上，可自訂背景顏色。當開關值為 `false` 或開關被禁用時（且開關呈半透明狀態），可看到此背景色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `onChange`

當用戶嘗試變更開關值時觸發。接收變更事件作為參數。若只需獲取新值，請改用 `onValueChange`。

| Type     |
| -------- |
| function |

---

### `onValueChange`

當用戶嘗試變更開關值時觸發。接收新值作為參數。若需獲取事件物件，請改用 `onChange`。

| Type     |
| -------- |
| function |

---

### `thumbColor`

開關滑塊的前景顏色。在 iOS 上設定此屬性會使滑塊失去陰影效果。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `trackColor`

開關軌道的自訂顏色。

_iOS_: 當開關值為 `false` 時，軌道會向邊界收縮。若要調整收縮軌道露出的背景色，請使用 [`ios_backgroundColor`](switch.md#ios_backgroundColor)。

| Type                                                         |
| ------------------------------------------------------------ |
| `md object: {false: [color](colors), true: [color](colors)}` |

---

### `value`

開關的當前值。若為 true 則開關呈開啟狀態。預設值為 false。

| Type |
| ---- |
| bool |