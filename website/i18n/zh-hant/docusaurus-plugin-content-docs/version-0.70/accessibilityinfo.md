---
id: accessibilityinfo
title: AccessibilityInfo
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

有時了解設備當前是否有啟用螢幕閱讀器會很有幫助。`AccessibilityInfo` API 就是為此目的設計的。您可以用它來查詢螢幕閱讀器的當前狀態，並註冊在螢幕閱讀器狀態變化時接收通知。

## 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=AccessibilityInfo%20Function%20Component%20Example&supportedPlatforms=android,ios
import React, { useState, useEffect } from "react";
import { AccessibilityInfo, View, Text, StyleSheet } from "react-native";

const App = () => {
  const [reduceMotionEnabled, setReduceMotionEnabled] = useState(false);
  const [screenReaderEnabled, setScreenReaderEnabled] = useState(false);

  useEffect(() => {
    const reduceMotionChangedSubscription = AccessibilityInfo.addEventListener(
      "reduceMotionChanged",
      reduceMotionEnabled => {
        setReduceMotionEnabled(reduceMotionEnabled);
      }
    );
    const screenReaderChangedSubscription = AccessibilityInfo.addEventListener(
      "screenReaderChanged",
      screenReaderEnabled => {
        setScreenReaderEnabled(screenReaderEnabled);
      }
    );

    AccessibilityInfo.isReduceMotionEnabled().then(
      reduceMotionEnabled => {
        setReduceMotionEnabled(reduceMotionEnabled);
      }
    );
    AccessibilityInfo.isScreenReaderEnabled().then(
      screenReaderEnabled => {
        setScreenReaderEnabled(screenReaderEnabled);
      }
    );

    return () => {
      reduceMotionChangedSubscription.remove();
      screenReaderChangedSubscription.remove();
    };
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.status}>
        The reduce motion is {reduceMotionEnabled ? "enabled" : "disabled"}.
      </Text>
      <Text style={styles.status}>
        The screen reader is {screenReaderEnabled ? "enabled" : "disabled"}.
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center"
  },
  status: {
    margin: 30
  }
});

export default App;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=AccessibilityInfo%20Class%20Component%20Example&supportedPlatforms=android,ios
import React, { Component } from 'react';
import { AccessibilityInfo, View, Text, StyleSheet } from 'react-native';

class AccessibilityStatusExample extends Component {
  state = {
    reduceMotionEnabled: false,
    screenReaderEnabled: false,
  };

  componentDidMount() {
    this.reduceMotionChangedSubscription = AccessibilityInfo.addEventListener(
      'reduceMotionChanged',
      reduceMotionEnabled => {
        this.setState({ reduceMotionEnabled });
      }
    );
    this.screenReaderChangedSubscription = AccessibilityInfo.addEventListener(
      'screenReaderChanged',
      screenReaderEnabled => {
        this.setState({ screenReaderEnabled });
      }
    );

    AccessibilityInfo.isReduceMotionEnabled().then(reduceMotionEnabled => {
      this.setState({ reduceMotionEnabled });
    });
    AccessibilityInfo.isScreenReaderEnabled().then(screenReaderEnabled => {
      this.setState({ screenReaderEnabled });
    });
  }

  componentWillUnmount() {
    this.reduceMotionChangedSubscription.remove();
    this.screenReaderChangedSubscription.remove();
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.status}>
          The reduce motion is{' '}
          {this.state.reduceMotionEnabled ? 'enabled' : 'disabled'}.
        </Text>
        <Text style={styles.status}>
          The screen reader is{' '}
          {this.state.screenReaderEnabled ? 'enabled' : 'disabled'}.
        </Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  status: {
    margin: 30,
  },
});

export default AccessibilityStatusExample;
```

</TabItem>
</Tabs>

---

# 參考文檔

## 方法

### `addEventListener()`

```jsx
static addEventListener(eventName, handler)
```

添加事件處理器。支援的事件：

| Event name                                                                           | Description                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `accessibilityServiceChanged`<br/><div class="label two-lines android">Android</div> | Fires when some services such as TalkBack, other Android assistive technologies, and third-party accessibility services are enabled. The argument to the event handler is a boolean. The boolean is `true` when a some accessibility services is enabled and `false` otherwise.                          |
| `announcementFinished`<br/><div class="label two-lines ios">iOS</div>                | Fires when the screen reader has finished making an announcement. The argument to the event handler is a dictionary with these keys:<ul><li>`announcement`: The string announced by the screen reader.</li><li>`success`: A boolean indicating whether the announcement was successfully made.</li></ul> |
| `boldTextChanged`<br/><div class="label two-lines ios">iOS</div>                     | Fires when the state of the bold text toggle changes. The argument to the event handler is a boolean. The boolean is `true` when bold text is enabled and `false` otherwise.                                                                                                                             |
| `grayscaleChanged`<br/><div class="label two-lines ios">iOS</div>                    | Fires when the state of the gray scale toggle changes. The argument to the event handler is a boolean. The boolean is `true` when a gray scale is enabled and `false` otherwise.                                                                                                                         |
| `invertColorsChanged`<br/><div class="label two-lines ios">iOS</div>                 | Fires when the state of the invert colors toggle changes. The argument to the event handler is a boolean. The boolean is `true` when invert colors is enabled and `false` otherwise.                                                                                                                     |
| `reduceMotionChanged`                                                                | Fires when the state of the reduce motion toggle changes. The argument to the event handler is a boolean. The boolean is `true` when a reduce motion is enabled (or when "Transition Animation Scale" in "Developer options" is "Animation off") and `false` otherwise.                                  |
| `reduceTransparencyChanged`<br/><div class="label two-lines ios">iOS</div>           | Fires when the state of the reduce transparency toggle changes. The argument to the event handler is a boolean. The boolean is `true` when reduce transparency is enabled and `false` otherwise.                                                                                                         |
| `screenReaderChanged`                                                                | Fires when the state of the screen reader changes. The argument to the event handler is a boolean. The boolean is `true` when a screen reader is enabled and `false` otherwise.                                                                                                                          |

---

### `announceForAccessibility()`

```jsx
static announceForAccessibility(announcement)
```

發送一個字串供螢幕閱讀器朗讀。

---

### `announceForAccessibilityWithOptions()`

```jsx
static announceForAccessibilityWithOptions(announcement, options)
```

發送一個字串供螢幕閱讀器朗讀，並可設定選項。預設情況下，朗讀會中斷當前語音，但在 iOS 上可透過在選項物件中設定 `queue` 為 `true` 來將朗讀排隊在現有語音之後。

**參數：**

| Name                                                          | Type   | Description                                                                              |
| ------------------------------------------------------------- | ------ | ---------------------------------------------------------------------------------------- |
| announcement <div class="label basic required">Required</div> | string | The string to be announced                                                               |
| options <div class="label basic required">Required</div>      | object | `queue` - queue the announcement behind existing speech <div class="label ios">iOS</div> |

---

### `getRecommendedTimeoutMillis()` <div class="label android">Android</div>

```jsx
static getRecommendedTimeoutMillis(originalTimeout)
```

獲取用戶所需的超時時間（毫秒）。  
此值設定於「無障礙」設定中的「操作時間（無障礙超時）」。

**參數：**

| Name                                                             | Type   | Description                                                                           |
| ---------------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| originalTimeout <div class="label basic required">Required</div> | number | The timeout to return if "Accessibility timeout" is not set. Specify in milliseconds. |

---

### `isAccessibilityServiceEnabled()` <div class="label android">Android</div>

```jsx
static isAccessibilityServiceEnabled(): Promise<boolean>
```

檢查是否有任何無障礙服務啟用。這包括 TalkBack 以及可能安裝的任何第三方無障礙應用程式。若僅需檢查 TalkBack 是否啟用，請使用 [isScreenReaderEnabled](#isscreenreaderenabled)。回傳一個解析為布林值的 Promise。當有無障礙服務啟用時結果為 `true`，否則為 `false`。

> **注意**：若僅需檢查 TalkBack 的狀態，請使用 [isScreenReaderEnabled](#isscreenreaderenabled)。

---

### `isBoldTextEnabled()` <div class="label ios">iOS</div>

```jsx
static isBoldTextEnabled()
```

查詢當前是否啟用粗體文字。回傳一個解析為布林值的 Promise。當粗體文字啟用時結果為 `true`，否則為 `false`。

---

### `isGrayscaleEnabled()` <div class="label ios">iOS</div>

```jsx
static isGrayscaleEnabled()
```

查詢當前是否啟用灰階模式。回傳一個解析為布林值的 Promise。當灰階模式啟用時結果為 `true`，否則為 `false`。

---

### `isInvertColorsEnabled()` <div class="label ios">iOS</div>

```jsx
static isInvertColorsEnabled()
```

查詢當前是否啟用反轉顏色。回傳一個解析為布林值的 Promise。當反轉顏色啟用時結果為 `true`，否則為 `false`。

---

### `isReduceMotionEnabled()`

```jsx
static isReduceMotionEnabled()
```

查詢當前是否啟用減少動畫效果。回傳一個解析為布林值的 Promise。當減少動畫效果啟用時結果為 `true`，否則為 `false`。

---

### `isReduceTransparencyEnabled()` <div class="label ios">iOS</div>

```jsx
static isReduceTransparencyEnabled()
```

查詢是否啟用了降低透明度功能。回傳一個解析為布林值的 Promise。當降低透明度功能啟用時結果為 `true`，否則為 `false`。

---

### `isScreenReaderEnabled()`

```jsx
static isScreenReaderEnabled()
```

查詢是否啟用了螢幕閱讀器。回傳一個解析為布林值的 Promise。當螢幕閱讀器啟用時結果為 `true`，否則為 `false`。

---

### `removeEventListener()`

```jsx
static removeEventListener(eventName, handler)
```

> **已棄用。** 請改用 [`addEventListener()`](#addeventlistener) 回傳的事件訂閱物件上的 `remove()` 方法。

---

### `setAccessibilityFocus()`

```jsx
static setAccessibilityFocus(reactTag)
```

將無障礙焦點設定到 React 元件上。

在 Android 上，這會呼叫 `UIManager.sendAccessibilityEvent` 方法，並傳入 `reactTag` 和 `UIManager.AccessibilityEventTypes.typeViewFocused` 參數。

> **注意**：請確保任何要接收無障礙焦點的 `View` 都設定了 `accessible={true}`。