---
id: alert
title: Alert
---

顯示一個圓形的載入指示器。

可選擇提供按鈕列表。點擊任一按鈕將觸發相應的 onPress 回調並關閉警示框。預設情況下，僅會顯示一個「確定」按鈕。

此為跨 Android 與 iOS 的 API，可顯示靜態警示框。僅 iOS 支援要求使用者輸入資訊的提示型警示框。

## 範例

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

## iOS

iOS 可指定任意數量按鈕。每個按鈕可選擇性設定樣式或強調顯示，選項參見 [AlertButtonStyle](#alertbuttonstyle-ios) 枚舉及 [AlertButton](alert#alertbutton) 的 `isPreferred` 欄位。

## Android

Android 最多指定三個按鈕，分為中性、負向與正向按鈕：

- 單一按鈕為「正向」（如「確定」）
- 雙按鈕為「負向」、「正向」（如「取消」、「確定」）
- 三按鈕為「中性」、「負向」、「正向」（如「稍後」、「取消」、「確定」）

Android 警示框可透過點擊框外區域關閉。此功能預設禁用，可透過在 [AlertOptions](alert#alertoptions) 參數中設定 `{cancelable: true}` 啟用。

取消事件可透過在 `options` 參數中提供 `onDismiss` 回調函數處理。

### 範例 <div class="label android">Android</div>

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

# 參考文獻

## 方法

### `alert()`

```tsx
static alert (
  title: string,
  message?: string,
  buttons?: AlertButton[],
  options?: AlertOptions,
);
```

**參數：**

| Name                                                   | Type                               | Description                                                             |
| ------------------------------------------------------ | ---------------------------------- | ----------------------------------------------------------------------- |
| title <div class="label basic required">Required</div> | string                             | The dialog's title. Passing `null` or empty string will hide the title. |
| message                                                | string                             | An optional message that appears below the dialog's title.              |
| buttons                                                | [AlertButton](alert#alertbutton)[] | An optional array containing buttons configuration.                     |
| options                                                | [AlertOptions](alert#alertoptions) | An optional Alert configuration.                                        |

---

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

建立並顯示文字輸入提示型警示框。

**參數：**

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

## 型別定義

### AlertButtonStyle <div class="label ios">iOS</div>

iOS 警示框按鈕樣式。

| Type |
| ---- |
| enum |

**常數：**

| Value           | Description               |
| --------------- | ------------------------- |
| `'default'`     | Default button style.     |
| `'cancel'`      | Cancel button style.      |
| `'destructive'` | Destructive button style. |

---

### AlertType <div class="label ios">iOS</div>

iOS 警示框類型。

| Type |
| ---- |
| enum |

**常數：**

| Value              | Description                  |
| ------------------ | ---------------------------- |
| `'default'`        | Default alert with no inputs |
| `'plain-text'`     | Plain text input alert       |
| `'secure-text'`    | Secure text input alert      |
| `'login-password'` | Login and password alert     |

---

### AlertButton

描述警示框按鈕配置的物件。

| Type             |
| ---------------- |
| array of objects |

**物件屬性：**

| Name                                         | Type                                           | Description                                                                    |
| -------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------ |
| text                                         | string                                         | Button label.                                                                  |
| onPress                                      | function                                       | Callback function when button is pressed.                                      |
| style <div class="label ios">iOS</div>       | [AlertButtonStyle](alert#alertbuttonstyle-ios) | Button style, on Android this property will be ignored.                        |
| isPreferred <div class="label ios">iOS</div> | boolean                                        | Whether button should be emphasized, on Android this property will be ignored. |

---

### AlertOptions

| Type   |
| ------ |
| object |

**屬性：**

| Name                                                | Type     | Description                                                                                                               |
| --------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------- |
| cancelable <div class="label android">Android</div> | boolean  | Defines if alert can be dismissed by tapping outside of the alert box.                                                    |
| userInterfaceStyle <div class="label ios">iOS</div> | string   | The interface style used for the alert, can be set to `light` or `dark`, otherwise the default system style will be used. |
| onDismiss <div class="label android">Android</div>  | function | Callback function fired when alert has been dismissed.                                                                    |