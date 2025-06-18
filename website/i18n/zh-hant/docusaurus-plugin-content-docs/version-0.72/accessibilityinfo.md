---
id: accessibilityinfo
title: AccessibilityInfo
---

有時了解設備當前是否有啟用螢幕閱讀器非常有用。`AccessibilityInfo` API 正是為此目的而設計。您可以使用它來查詢螢幕閱讀器的當前狀態，並註冊以在螢幕閱讀器狀態變化時接收通知。

## 範例

```SnackPlayer name=AccessibilityInfo%20Example&supportedPlatforms=android,ios
import React, {useState, useEffect} from 'react';
import {AccessibilityInfo, View, Text, StyleSheet} from 'react-native';

const App = () => {
  const [reduceMotionEnabled, setReduceMotionEnabled] = useState(false);
  const [screenReaderEnabled, setScreenReaderEnabled] = useState(false);

  useEffect(() => {
    const reduceMotionChangedSubscription = AccessibilityInfo.addEventListener(
      'reduceMotionChanged',
      isReduceMotionEnabled => {
        setReduceMotionEnabled(isReduceMotionEnabled);
      },
    );
    const screenReaderChangedSubscription = AccessibilityInfo.addEventListener(
      'screenReaderChanged',
      isScreenReaderEnabled => {
        setScreenReaderEnabled(isScreenReaderEnabled);
      },
    );

    AccessibilityInfo.isReduceMotionEnabled().then(isReduceMotionEnabled => {
      setReduceMotionEnabled(isReduceMotionEnabled);
    });
    AccessibilityInfo.isScreenReaderEnabled().then(isScreenReaderEnabled => {
      setScreenReaderEnabled(isScreenReaderEnabled);
    });

    return () => {
      reduceMotionChangedSubscription.remove();
      screenReaderChangedSubscription.remove();
    };
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.status}>
        The reduce motion is {reduceMotionEnabled ? 'enabled' : 'disabled'}.
      </Text>
      <Text style={styles.status}>
        The screen reader is {screenReaderEnabled ? 'enabled' : 'disabled'}.
      </Text>
    </View>
  );
};

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

export default App;
```

---

# 參考

## 方法

### `addEventListener()`

```tsx
static addEventListener(
  eventName: AccessibilityChangeEventName | AccessibilityAnnouncementEventName,
  handler: (
    event: AccessibilityChangeEvent | AccessibilityAnnouncementFinishedEvent,
  ) => void,
): EmitterSubscription;
```

添加事件處理程序。支援的事件：

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

```tsx
static announceForAccessibility(announcement: string);
```

發布一個字串供螢幕閱讀器朗讀。

---

### `announceForAccessibilityWithOptions()`

```tsx
static announceForAccessibilityWithOptions(
  announcement: string,
  options: options: {queue?: boolean},
);
```

發布一個字串供螢幕閱讀器朗讀，並可設定修改選項。預設情況下，朗讀會中斷任何現有的語音，但在 iOS 上，可以通過在選項對象中將 `queue` 設為 `true` 來將朗讀排在現有語音之後。

**參數：**

| Name                                                          | Type   | Description                                                                              |
| ------------------------------------------------------------- | ------ | ---------------------------------------------------------------------------------------- |
| announcement <div class="label basic required">Required</div> | string | The string to be announced                                                               |
| options <div class="label basic required">Required</div>      | object | `queue` - queue the announcement behind existing speech <div class="label ios">iOS</div> |

---

### `getRecommendedTimeoutMillis()` <div class="label android">Android</div>

```tsx
static getRecommendedTimeoutMillis(originalTimeout: number): Promise<number>;
```

獲取用戶需要的超時時間（毫秒）。
此值在「無障礙」設置中的「操作時間（無障礙超時）」中設定。

**參數：**

| Name                                                             | Type   | Description                                                                           |
| ---------------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| originalTimeout <div class="label basic required">Required</div> | number | The timeout to return if "Accessibility timeout" is not set. Specify in milliseconds. |

---

### `isAccessibilityServiceEnabled()` <div class="label android">Android</div>

```tsx
static isAccessibilityServiceEnabled(): Promise<boolean>;
```

檢查是否有任何無障礙服務已啟用。這包括 TalkBack 以及可能安裝的任何第三方無障礙應用程序。若僅檢查 TalkBack 是否啟用，請使用 [isScreenReaderEnabled](#isscreenreaderenabled)。返回一個解析為布林值的 Promise。當某些無障礙服務啟用時結果為 `true`，否則為 `false`。

> **注意**：如果您只想檢查 TalkBack 的狀態，請使用 [isScreenReaderEnabled](#isscreenreaderenabled)。

---

### `isBoldTextEnabled()` <div class="label ios">iOS</div>

```tsx
static isBoldTextEnabled(): Promise<boolean>:
```

查詢粗體文字當前是否啟用。返回一個解析為布林值的 Promise。當粗體文字啟用時結果為 `true`，否則為 `false`。

---

### `isGrayscaleEnabled()` <div class="label ios">iOS</div>

```tsx
static isGrayscaleEnabled(): Promise<boolean>;
```

查詢灰階模式當前是否啟用。返回一個解析為布林值的 Promise。當灰階模式啟用時結果為 `true`，否則為 `false`。

---

### `isInvertColorsEnabled()` <div class="label ios">iOS</div>

```tsx
static isInvertColorsEnabled(): Promise<boolean>;
```

查詢反轉顏色當前是否啟用。返回一個解析為布林值的 Promise。當反轉顏色啟用時結果為 `true`，否則為 `false`。

---

### `isReduceMotionEnabled()`

```tsx
static isReduceMotionEnabled(): Promise<boolean>;
```

查詢減少動畫效果當前是否啟用。返回一個解析為布林值的 Promise。當減少動畫效果啟用時結果為 `true`，否則為 `false`。

---

### `isReduceTransparencyEnabled()` <div class="label ios">iOS</div>

```tsx
static isReduceTransparencyEnabled(): Promise<boolean>;
```

查詢當前是否啟用了減少透明度功能。回傳一個解析為布林值的 Promise，當減少透明度啟用時結果為 `true`，否則為 `false`。

---

### `isScreenReaderEnabled()`

```tsx
static isScreenReaderEnabled(): Promise<boolean>;
```

查詢當前是否啟用了螢幕閱讀器。回傳一個解析為布林值的 Promise，當螢幕閱讀器啟用時結果為 `true`，否則為 `false`。

---

### `prefersCrossFadeTransitions()` <div class="label ios">iOS</div>

```tsx
static prefersCrossFadeTransitions(): Promise<boolean>;
```

查詢當前是否同時啟用了減少動畫效果和偏好使用淡入淡出過場設定。回傳一個解析為布林值的 Promise，當偏好淡入淡出過場啟用時結果為 `true`，否則為 `false`。

---

### `setAccessibilityFocus()`

```tsx
static setAccessibilityFocus(reactTag: number);
```

將無障礙焦點設置到 React 元件上。

在 Android 上，此方法會呼叫 `UIManager.sendAccessibilityEvent`，並傳入 `reactTag` 和 `UIManager.AccessibilityEventTypes.typeViewFocused` 參數。

> **注意**：請確保任何要接收無障礙焦點的 `View` 都設定了 `accessible={true}`。