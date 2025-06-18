---
id: alertios
title: '🚧 AlertIOS'
---

> **已移除。** 請改用 [`Alert`](alert)。

`AlertIOS` 提供創建 iOS 警示對話框或用戶輸入提示框的功能。

創建 iOS 警示框：

```jsx
AlertIOS.alert(
  'Sync Complete',
  'All your data are belong to us.',
);
```

創建 iOS 提示框：

```jsx
AlertIOS.prompt('Enter a value', null, text =>
  console.log('You entered ' + text),
);
```

若無需創建 iOS 專用提示框，建議使用跨平台支援的 [`Alert.alert`](alert) 方法。

---

# 參考文檔

## 方法

### `alert()`

```jsx
static alert(title: string, [message]: string, [callbackOrButtons]: ?(() => void), ButtonsArray, [type]: AlertType): [object Object]
```

創建並顯示彈出式警示框。

**參數：**

| Name              | Type                                                | Required | Description                                                                                                                                                                                                                                                                                                                                                      |
| ----------------- | --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| title             | string                                              | Yes      | The dialog's title. Passing null or '' will hide the title.                                                                                                                                                                                                                                                                                                      |
| message           | string                                              | No       | An optional message that appears below the dialog's title.                                                                                                                                                                                                                                                                                                       |
| callbackOrButtons | ?(() => void),[ButtonsArray](alertios#buttonsarray) | No       | This optional argument should be either a single-argument function or an array of buttons. If passed a function, it will be called when the user taps 'OK'. If passed an array of button configurations, each button should include a `text` key, as well as optional `onPress` and `style` keys. `style` should be one of 'default', 'cancel' or 'destructive'. |
| type              | [AlertType](alertios#alerttype)                     | No       | Deprecated, do not use.                                                                                                                                                                                                                                                                                                                                          |

自定義按鈕範例：

```jsx
AlertIOS.alert(
  'Update available',
  'Keep your app up to date to enjoy the latest features',
  [
    {
      text: 'Cancel',
      onPress: () => console.log('Cancel Pressed'),
      style: 'cancel',
    },
    {
      text: 'Install',
      onPress: () => console.log('Install Pressed'),
    },
  ],
);
```

---

### `prompt()`

```jsx
static prompt(title: string, [message]: string, [callbackOrButtons]: ?((text: string) => void), ButtonsArray, [type]: AlertType, [defaultValue]: string, [keyboardType]: string): [object Object]
```

創建並顯示文字輸入提示框。

**參數：**

| Name              | Type                                                            | Required | Description                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------- | --------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| title             | string                                                          | Yes      | The dialog's title.                                                                                                                                                                                                                                                                                                                                                                                    |
| message           | string                                                          | No       | An optional message that appears above the text input.                                                                                                                                                                                                                                                                                                                                                 |
| callbackOrButtons | ?((text: string) => void),[ButtonsArray](alertios#buttonsarray) | No       | This optional argument should be either a single-argument function or an array of buttons. If passed a function, it will be called with the prompt's value when the user taps 'OK'. If passed an array of button configurations, each button should include a `text` key, as well as optional `onPress` and `style` keys (see example). `style` should be one of 'default', 'cancel' or 'destructive'. |
| type              | [AlertType](alertios#alerttype)                                 | No       | This configures the text input. One of 'plain-text', 'secure-text' or 'login-password'.                                                                                                                                                                                                                                                                                                                |
| defaultValue      | string                                                          | No       | The default text in text input.                                                                                                                                                                                                                                                                                                                                                                        |
| keyboardType      | string                                                          | No       | The keyboard type of first text field(if exists). One of 'default', 'email-address', 'numeric', 'phone-pad', 'ascii-capable', 'numbers-and-punctuation', 'url', 'number-pad', 'name-phone-pad', 'decimal-pad', 'twitter' or 'web-search'.                                                                                                                                                              |

自定義按鈕範例：

```jsx
AlertIOS.prompt(
  'Enter password',
  'Enter your password to claim your $1.5B in lottery winnings',
  [
    {
      text: 'Cancel',
      onPress: () => console.log('Cancel Pressed'),
      style: 'cancel',
    },
    {
      text: 'OK',
      onPress: password =>
        console.log('OK Pressed, password: ' + password),
    },
  ],
  'secure-text',
);
```

，

預設按鈕與自定義回調範例：

```jsx
AlertIOS.prompt(
  'Update username',
  null,
  text => console.log('Your username is ' + text),
  null,
  'default',
);
```

## 類型定義

### AlertType

警示框按鈕類型

| Type  |
| ----- |
| $Enum |

**常數：**

| Value          | Description                  |
| -------------- | ---------------------------- |
| default        | Default alert with no inputs |
| plain-text     | Plain text input alert       |
| secure-text    | Secure text input alert      |
| login-password | Login and password alert     |

---

### AlertButtonStyle

警示框按鈕樣式

| Type  |
| ----- |
| $Enum |

**常數：**

| Value       | Description              |
| ----------- | ------------------------ |
| default     | Default button style     |
| cancel      | Cancel button style      |
| destructive | Destructive button style |

---

### ButtonsArray

按鈕陣列

| Type  |
| ----- |
| Array |

**屬性：**

| Name      | Type                                          | Description                           |
| --------- | --------------------------------------------- | ------------------------------------- |
| [text]    | string                                        | Button label                          |
| [onPress] | function                                      | Callback function when button pressed |
| [style]   | [AlertButtonStyle](alertios#alertbuttonstyle) | Button style                          |

**常數：**

| Value   | Description                           |
| ------- | ------------------------------------- |
| text    | Button label                          |
| onPress | Callback function when button pressed |
| style   | Button style                          |