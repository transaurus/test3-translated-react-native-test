---
id: alert
title: Alert
---

顯示一個圓形的載入指示器。

## 範例

This is an API that works both on Android and iOS and can show static alerts. Alert that prompts the user to enter some information is available on iOS only.

## Example

```SnackPlayer name=Alert%20Example&supportedPlatforms=ios,android
import React from 'react';
import {StyleSheet, Button, Alert} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const createTwoButtonAlert = () =>
    Alert.alert('Alert Title', 'My Alert Msg', [
      {
        text: 'Cancel',
        onPress: () => console.log('Cancel Pressed'),
        style: 'cancel',
      },
      {text: 'OK', onPress: () => console.log('OK Pressed')},
    ]);

  const createThreeButtonAlert = () =>
    Alert.alert('Alert Title', 'My Alert Msg', [
      {
        text: 'Ask me later',
        onPress: () => console.log('Ask me later pressed'),
      },
      {
        text: 'Cancel',
        onPress: () => console.log('Cancel Pressed'),
        style: 'cancel',
      },
      {text: 'OK', onPress: () => console.log('OK Pressed')},
    ]);

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Button title={'2-Button Alert'} onPress={createTwoButtonAlert} />
        <Button title={'3-Button Alert'} onPress={createThreeButtonAlert} />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'space-around',
    alignItems: 'center',
  },
});

export default App;
```

# 參考

## 屬性

### [View 屬性](view#props)

繼承 [View 屬性](view#props)。

---

### `animating`

是否顯示指示器（`true`）或隱藏它（`false`）。

### Example <div class="label android">Android</div>

```SnackPlayer name=Alert%20Android%20Dissmissable%20Example&supportedPlatforms=android
import React from 'react';
import {StyleSheet, Button, Alert} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const showAlert = () =>
  Alert.alert(
    'Alert Title',
    'My Alert Msg',
    [
      {
        text: 'Cancel',
        onPress: () => Alert.alert('Cancel Pressed'),
        style: 'cancel',
      },
    ],
    {
      cancelable: true,
      onDismiss: () =>
        Alert.alert(
          'This alert was dismissed by tapping outside of the alert dialog.',
        ),
    },
  );

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Button title="Show alert" onPress={showAlert} />
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

---

### `color`

指示器的前景顏色。

### `hidesWhenStopped` <div class="label ios">iOS</div>

```tsx
static alert (
  title: string,
  message?: string,
  buttons?: AlertButton[],
  options?: AlertOptions,
);
```

---

| Name                                                   | Type                               | Description                                                             |
| ------------------------------------------------------ | ---------------------------------- | ----------------------------------------------------------------------- |
| title <div class="label basic required">Required</div> | string                             | The dialog's title. Passing `null` or empty string will hide the title. |
| message                                                | string                             | An optional message that appears below the dialog's title.              |
| buttons                                                | [AlertButton](alert#alertbutton)[] | An optional array containing buttons configuration.                     |
| options                                                | [AlertOptions](alert#alertoptions) | An optional Alert configuration.                                        |

### `size`

### `prompt()` <div class="label ios">iOS</div>

```tsx
static prompt: (
  title: string,
  message?: string,
  callbackOrButtons?: ((text: string) => void) | AlertButton[],
  type?: AlertType,
  defaultValue?: string,
  keyboardType?: string,
);
```

---

**Parameters:**

| Name                                                   | Type                                            | Description                                                                                                                                                                                           |
| ------------------------------------------------------ | ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| title <div class="label basic required">Required</div> | string                                          | The dialog's title.                                                                                                                                                                                   |
| message                                                | string                                          | An optional message that appears above the text input.                                                                                                                                                |
| callbackOrButtons                                      | function<hr/>[AlertButton](alert#alertButton)[] | If passed a function, it will be called with the prompt's value<br/>`(text: string) => void`, when the user taps 'OK'.<hr/>If passed an array, buttons will be configured based on the array content. |
| type                                                   | [AlertType](alert#alerttype-ios)                | This configures the text input.                                                                                                                                                                       |
| defaultValue                                           | string                                          | The default text in text input.                                                                                                                                                                       |
| keyboardType                                           | string                                          | The keyboard type of first text field (if exists). One of TextInput [keyboardTypes](textinput#keyboardtype).                                                                                          |
| options                                                | [AlertOptions](alert#alertoptions)              | An optional Alert configuration.                                                                                                                                                                      |

---

> **已移除。** 請改用 [`Alert`](alert)。

`AlertIOS` 提供功能來創建一個帶有訊息的 iOS 警示對話框，或創建一個用於用戶輸入的提示。

創建一個 iOS 警示：

| Type |
| ---- |
| enum |

---

| Value           | Description               |
| --------------- | ------------------------- |
| `'default'`     | Default button style.     |
| `'cancel'`      | Cancel button style.      |
| `'destructive'` | Destructive button style. |

創建一個 iOS 提示：

# 參考

---

| Type |
| ---- |
| enum |

如果您不需要創建僅限於 iOS 的提示，我們建議使用 [`Alert.alert`](alert) 方法以獲得跨平台支援。

| Value              | Description                  |
| ------------------ | ---------------------------- |
| `'default'`        | Default alert with no inputs |
| `'plain-text'`     | Plain text input alert       |
| `'secure-text'`    | Secure text input alert      |
| `'login-password'` | Login and password alert     |

### `alert()`

### AlertButton

**參數：**

| Type             |
| ---------------- |
| array of objects |

帶有自定義按鈕的範例：

| Name                                         | Type                                           | Description                                                                    |
| -------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------ |
| text                                         | string                                         | Button label.                                                                  |
| onPress                                      | function                                       | Callback function when button is pressed.                                      |
| style <div class="label ios">iOS</div>       | [AlertButtonStyle](alert#alertbuttonstyle-ios) | Button style, on Android this property will be ignored.                        |
| isPreferred <div class="label ios">iOS</div> | boolean                                        | Whether button should be emphasized, on Android this property will be ignored. |

按鈕數組

**屬性：**

| Type   |
| ------ |
| object |

**常量：**

| Name                                                | Type     | Description                                                                                                               |
| --------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------- |
| cancelable <div class="label android">Android</div> | boolean  | Defines if alert can be dismissed by tapping outside of the alert box.                                                    |
| userInterfaceStyle <div class="label ios">iOS</div> | string   | The interface style used for the alert, can be set to `light` or `dark`, otherwise the default system style will be used. |
| onDismiss <div class="label android">Android</div>  | function | Callback function fired when alert has been dismissed.                                                                    |