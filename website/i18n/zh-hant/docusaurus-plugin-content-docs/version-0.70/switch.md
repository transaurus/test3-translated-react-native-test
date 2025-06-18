---
id: switch
title: Switch
---

渲染一個布林值輸入元件。

這是一個受控元件，需要透過 `onValueChange` 回調函數來更新 `value` 屬性，才能使元件反映使用者操作。如果未更新 `value` 屬性，元件將持續顯示提供的 `value` 屬性值，而非使用者操作的預期結果。

## 範例

```SnackPlayer name=Switch&supportedPlatforms=android,ios
import React, { useState } from "react";
import { View, Switch, StyleSheet } from "react-native";

const App = () => {
  const [isEnabled, setIsEnabled] = useState(false);
  const toggleSwitch = () => setIsEnabled(previousState => !previousState);

  return (
    <View style={styles.container}>
      <Switch
        trackColor={{ false: "#767577", true: "#81b0ff" }}
        thumbColor={isEnabled ? "#f5dd4b" : "#f4f3f4"}
        ios_backgroundColor="#3e3e3e"
        onValueChange={toggleSwitch}
        value={isEnabled}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center"
  }
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

若設為 true，使用者將無法切換開關狀態。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `ios_backgroundColor` <div class="label ios">iOS</div>

在 iOS 上，可自訂背景顏色。此背景色會在開關值為 `false` 或開關被禁用時（且開關呈半透明狀態）顯示。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `onChange`

當使用者嘗試變更開關值時觸發。接收變更事件作為參數。若只需取得新值，請改用 `onValueChange`。

| Type     |
| -------- |
| function |

---

### `onValueChange`

當使用者嘗試變更開關值時觸發。接收新值作為參數。若需取得事件物件，請改用 `onChange`。

| Type     |
| -------- |
| function |

---

### `thumbColor`

開關滑塊的前景顏色。若在 iOS 設定此屬性，滑塊將失去陰影效果。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `trackColor`

開關軌道的自訂顏色。

_iOS_：當開關值為 `false` 時，軌道會縮至邊框內。若要改變縮小軌道露出的背景色，請使用 [`ios_backgroundColor`](switch.md#ios_backgroundColor)。

| Type                                                         |
| ------------------------------------------------------------ |
| `md object: {false: [color](colors), true: [color](colors)}` |

---

### `value`

開關的值。若為 true 則開關將呈開啟狀態。預設值為 false。

| Type |
| ---- |
| bool |