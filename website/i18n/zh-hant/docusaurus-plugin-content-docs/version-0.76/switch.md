---
id: switch
title: Switch
---

渲染一個布林值輸入元件。

這是一個受控元件，需要透過 `onValueChange` 回調函數來更新 `value` 屬性值，才能使元件正確反映使用者操作。若未更新 `value` 屬性，元件將持續顯示初始設定的 `value` 值，而非使用者操作後的預期結果。

## 範例

```SnackPlayer name=Switch&supportedPlatforms=android,ios
import React, {useState} from 'react';
import {Switch, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [isEnabled, setIsEnabled] = useState(false);
  const toggleSwitch = () => setIsEnabled(previousState => !previousState);

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Switch
          trackColor={{false: '#767577', true: '#81b0ff'}}
          thumbColor={isEnabled ? '#f5dd4b' : '#f4f3f4'}
          ios_backgroundColor="#3e3e3e"
          onValueChange={toggleSwitch}
          value={isEnabled}
        />
      </SafeAreaView>
    </SafeAreaProvider>
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

### [View 元件屬性](view.md#props)

繼承 [View 元件所有屬性](view.md#props)。

---

### `disabled`

設為 true 時，使用者將無法切換開關狀態。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `ios_backgroundColor` <div class="label ios">iOS</div>

在 iOS 平台自訂背景顏色。當開關值為 `false` 或開關被禁用時（此時開關呈半透明狀態），可看到此背景色。

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

開關滑塊的前景顏色。在 iOS 平台設定此值會使滑塊失去陰影效果。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `trackColor`

自訂開關軌道的顏色。

_iOS_: 當開關值為 `false` 時，軌道會縮至邊框內。若要修改收縮後軌道露出的背景色，請使用 [`ios_backgroundColor`](switch.md#ios_backgroundColor)。

| Type                                                         |
| ------------------------------------------------------------ |
| `md object: {false: [color](colors), true: [color](colors)}` |

---

### `value`

開關的當前值。設為 true 時開關將呈開啟狀態。預設值為 false。

| Type |
| ---- |
| bool |