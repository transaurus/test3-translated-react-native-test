---
id: statusbar
title: StatusBar
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

控制應用程式狀態列的元件。狀態列通常是螢幕頂部的區域，顯示當前時間、Wi-Fi和行動網路資訊、電池電量和其他狀態圖示。

### 與導航器共同使用

可以同時掛載多個 `StatusBar` 元件。屬性將按照 `StatusBar` 元件掛載的順序進行合併。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=StatusBar%20Component%20Example&supportedPlatforms=android,ios&ext=js
import React, {useState} from 'react';
import {
  Button,
  Platform,
  SafeAreaView,
  StatusBar,
  StyleSheet,
  Text,
  View,
} from 'react-native';

const STYLES = ['default', 'dark-content', 'light-content'];
const TRANSITIONS = ['fade', 'slide', 'none'];

const App = () => {
  const [hidden, setHidden] = useState(false);
  const [statusBarStyle, setStatusBarStyle] = useState(STYLES[0]);
  const [statusBarTransition, setStatusBarTransition] = useState(
    TRANSITIONS[0],
  );

  const changeStatusBarVisibility = () => setHidden(!hidden);

  const changeStatusBarStyle = () => {
    const styleId = STYLES.indexOf(statusBarStyle) + 1;
    if (styleId === STYLES.length) {
      setStatusBarStyle(STYLES[0]);
    } else {
      setStatusBarStyle(STYLES[styleId]);
    }
  };

  const changeStatusBarTransition = () => {
    const transition = TRANSITIONS.indexOf(statusBarTransition) + 1;
    if (transition === TRANSITIONS.length) {
      setStatusBarTransition(TRANSITIONS[0]);
    } else {
      setStatusBarTransition(TRANSITIONS[transition]);
    }
  };

  return (
    <SafeAreaView style={styles.container}>
      <StatusBar
        animated={true}
        backgroundColor="#61dafb"
        barStyle={statusBarStyle}
        showHideTransition={statusBarTransition}
        hidden={hidden}
      />
      <Text style={styles.textStyle}>
        StatusBar Visibility:{'\n'}
        {hidden ? 'Hidden' : 'Visible'}
      </Text>
      <Text style={styles.textStyle}>
        StatusBar Style:{'\n'}
        {statusBarStyle}
      </Text>
      {Platform.OS === 'ios' ? (
        <Text style={styles.textStyle}>
          StatusBar Transition:{'\n'}
          {statusBarTransition}
        </Text>
      ) : null}
      <View style={styles.buttonsContainer}>
        <Button title="Toggle StatusBar" onPress={changeStatusBarVisibility} />
        <Button title="Change StatusBar Style" onPress={changeStatusBarStyle} />
        {Platform.OS === 'ios' ? (
          <Button
            title="Change StatusBar Transition"
            onPress={changeStatusBarTransition}
          />
        ) : null}
      </View>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#ECF0F1',
  },
  buttonsContainer: {
    padding: 10,
  },
  textStyle: {
    textAlign: 'center',
    marginBottom: 8,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=StatusBar%20Component%20Example&supportedPlatforms=android,ios&ext=tsx
import React, {useState} from 'react';
import {
  Button,
  Platform,
  SafeAreaView,
  StatusBar,
  StyleSheet,
  Text,
  View,
} from 'react-native';
import type {StatusBarStyle} from 'react-native';

const STYLES = ['default', 'dark-content', 'light-content'] as const;
const TRANSITIONS = ['fade', 'slide', 'none'] as const;

const App = () => {
  const [hidden, setHidden] = useState(false);
  const [statusBarStyle, setStatusBarStyle] = useState<StatusBarStyle>(
    STYLES[0],
  );
  const [statusBarTransition, setStatusBarTransition] = useState<
    'fade' | 'slide' | 'none'
  >(TRANSITIONS[0]);

  const changeStatusBarVisibility = () => setHidden(!hidden);

  const changeStatusBarStyle = () => {
    const styleId = STYLES.indexOf(statusBarStyle) + 1;
    if (styleId === STYLES.length) {
      setStatusBarStyle(STYLES[0]);
    } else {
      setStatusBarStyle(STYLES[styleId]);
    }
  };

  const changeStatusBarTransition = () => {
    const transition = TRANSITIONS.indexOf(statusBarTransition) + 1;
    if (transition === TRANSITIONS.length) {
      setStatusBarTransition(TRANSITIONS[0]);
    } else {
      setStatusBarTransition(TRANSITIONS[transition]);
    }
  };

  return (
    <SafeAreaView style={styles.container}>
      <StatusBar
        animated={true}
        backgroundColor="#61dafb"
        barStyle={statusBarStyle}
        showHideTransition={statusBarTransition}
        hidden={hidden}
      />
      <Text style={styles.textStyle}>
        StatusBar Visibility:{'\n'}
        {hidden ? 'Hidden' : 'Visible'}
      </Text>
      <Text style={styles.textStyle}>
        StatusBar Style:{'\n'}
        {statusBarStyle}
      </Text>
      {Platform.OS === 'ios' ? (
        <Text style={styles.textStyle}>
          StatusBar Transition:{'\n'}
          {statusBarTransition}
        </Text>
      ) : null}
      <View style={styles.buttonsContainer}>
        <Button title="Toggle StatusBar" onPress={changeStatusBarVisibility} />
        <Button title="Change StatusBar Style" onPress={changeStatusBarStyle} />
        {Platform.OS === 'ios' ? (
          <Button
            title="Change StatusBar Transition"
            onPress={changeStatusBarTransition}
          />
        ) : null}
      </View>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#ECF0F1',
  },
  buttonsContainer: {
    padding: 10,
  },
  textStyle: {
    textAlign: 'center',
    marginBottom: 8,
  },
});

export default App;
```

</TabItem>
</Tabs>

### 命令式 API

對於使用元件不理想的情況，還有一個作為元件靜態函數公開的命令式 API。但不建議同時使用靜態 API 和元件來設置相同的屬性，因為靜態 API 設置的任何值將在下一次渲染時被元件設置的值覆蓋。

---

# 參考

## 常數

### `currentHeight` <div class="label android">Android</div>

狀態列的高度，包括凹口高度（如果存在）。

---

## 屬性

### `animated`

狀態列屬性變更之間的過渡是否應動畫化。支援 `backgroundColor`、`barStyle` 和 `hidden` 屬性。

| Type    | Required | Default |
| ------- | -------- | ------- |
| boolean | No       | `false` |

---

### `backgroundColor` <div class="label android">Android</div>

狀態列的背景顏色。

| Type            | Required | Default                                                                |
| --------------- | -------- | ---------------------------------------------------------------------- |
| [color](colors) | No       | default system StatusBar background color, or `'black'` if not defined |

---

### `barStyle`

設置狀態列文字的顏色。

在 Android 上，這僅對 API 版本 23 及以上版本有效。

| Type                                       | Required | Default     |
| ------------------------------------------ | -------- | ----------- |
| [StatusBarStyle](statusbar#statusbarstyle) | No       | `'default'` |

---

### `hidden`

狀態列是否隱藏。

| Type    | Required | Default |
| ------- | -------- | ------- |
| boolean | No       | `false` |

---

### `networkActivityIndicatorVisible` <div class="label ios">iOS</div>

網路活動指示器是否可見。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `showHideTransition` <div class="label ios">iOS</div>

使用 `hidden` 屬性顯示和隱藏狀態列時的過渡效果。

| Type                                               | Default  |
| -------------------------------------------------- | -------- |
| [StatusBarAnimation](statusbar#statusbaranimation) | `'fade'` |

---

### `translucent` <div class="label android">Android</div>

狀態列是否半透明。當設置為 `true` 時，應用程式將在狀態列下方繪製。這在使用半透明狀態列顏色時非常有用。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

## 方法

### `popStackEntry()`

```tsx
static popStackEntry(entry: StatusBarProps);
```

從堆疊中獲取並移除最後一個 StatusBar 條目。

**參數：**

| Name                                                   | Type | Description                           |
| ------------------------------------------------------ | ---- | ------------------------------------- |
| entry <div class="label basic required">Required</div> | any  | Entry returned from `pushStackEntry`. |

---

### `pushStackEntry()`

```tsx
static pushStackEntry(props: StatusBarProps): StatusBarProps;
```

將一個 StatusBar 條目推入堆疊。返回值應在完成時傳遞給 `popStackEntry`。

**參數：**

| Name                                                   | Type | Description                                                      |
| ------------------------------------------------------ | ---- | ---------------------------------------------------------------- |
| props <div class="label basic required">Required</div> | any  | Object containing the StatusBar props to use in the stack entry. |

---

### `replaceStackEntry()`

```tsx
static replaceStackEntry(
  entry: StatusBarProps,
  props: StatusBarProps
): StatusBarProps;
```

用新的屬性替換現有的 StatusBar 堆疊條目。

**參數：**

| Name                                                   | Type | Description                                                                  |
| ------------------------------------------------------ | ---- | ---------------------------------------------------------------------------- |
| entry <div class="label basic required">Required</div> | any  | Entry returned from `pushStackEntry` to replace.                             |
| props <div class="label basic required">Required</div> | any  | Object containing the StatusBar props to use in the replacement stack entry. |

---

### `setBackgroundColor()` <div class="label android">Android</div>

```tsx
static setBackgroundColor(color: ColorValue, animated?: boolean);
```

設定狀態欄的背景顏色。

**參數：**

| Name                                                   | Type    | Description               |
| ------------------------------------------------------ | ------- | ------------------------- |
| color <div class="label basic required">Required</div> | string  | Background color.         |
| animated                                               | boolean | Animate the style change. |

---

### `setBarStyle()`

```tsx
static setBarStyle(style: StatusBarStyle, animated?: boolean);
```

設定狀態欄的樣式。

**參數：**

| Name                                                   | Type                                       | Description               |
| ------------------------------------------------------ | ------------------------------------------ | ------------------------- |
| style <div class="label basic required">Required</div> | [StatusBarStyle](statusbar#statusbarstyle) | Status bar style to set.  |
| animated                                               | boolean                                    | Animate the style change. |

---

### `setHidden()`

```tsx
static setHidden(hidden: boolean, animation?: StatusBarAnimation);
```

顯示或隱藏狀態欄。

**參數：**

| Name                                                    | Type                                               | Description                                             |
| ------------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------- |
| hidden <div class="label basic required">Required</div> | boolean                                            | Hide the status bar.                                    |
| animation <div class="label ios">iOS</div>              | [StatusBarAnimation](statusbar#statusbaranimation) | Animation when changing the status bar hidden property. |

---

### `setNetworkActivityIndicatorVisible()` <div class="label ios">iOS</div>

```tsx
static setNetworkActivityIndicatorVisible(visible: boolean);
```

控制網路活動指示器的可見性。

**參數：**

| Name                                                     | Type    | Description         |
| -------------------------------------------------------- | ------- | ------------------- |
| visible <div class="label basic required">Required</div> | boolean | Show the indicator. |

---

### `setTranslucent()` <div class="label android">Android</div>

```tsx
static setTranslucent(translucent: boolean);
```

控制狀態欄的透明度。

**參數：**

| Name                                                         | Type    | Description         |
| ------------------------------------------------------------ | ------- | ------------------- |
| translucent <div class="label basic required">Required</div> | boolean | Set as translucent. |

## 類型定義

### StatusBarAnimation

iOS 上狀態欄過渡的動畫類型。

| Type |
| ---- |
| enum |

**常數：**

| Value     | Type   | Description     |
| --------- | ------ | --------------- |
| `'fade'`  | string | Fade animation  |
| `'slide'` | string | Slide animation |
| `'none'`  | string | No animation    |

---

### StatusBarStyle

狀態欄樣式類型。

| Type |
| ---- |
| enum |

**常數：**

| Value             | Type   | Description                                                          |
| ----------------- | ------ | -------------------------------------------------------------------- |
| `'default'`       | string | Default status bar style (dark for iOS, light for Android)           |
| `'light-content'` | string | Dark background, white texts and icons                               |
| `'dark-content'`  | string | Light background, dark texts and icons (requires API>=23 on Android) |