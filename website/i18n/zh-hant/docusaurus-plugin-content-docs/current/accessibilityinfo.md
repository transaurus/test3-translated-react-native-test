---
id: accessibilityinfo
title: AccessibilityInfo
---

Sometimes it's useful to know whether or not the device has a screen reader that is currently active. The `AccessibilityInfo` API is designed for this purpose. You can use it to query the current state of the screen reader as well as to register to be notified when the state of the screen reader changes.

## Example

```SnackPlayer name=AccessibilityInfo%20Example&supportedPlatforms=android,ios
import React, {useState, useEffect} from 'react';
import {AccessibilityInfo, Text, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

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
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text style={styles.status}>
          The reduce motion is {reduceMotionEnabled ? 'enabled' : 'disabled'}.
        </Text>
        <Text style={styles.status}>
          The screen reader is {screenReaderEnabled ? 'enabled' : 'disabled'}.
        </Text>
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
  status: {
    margin: 30,
  },
});

export default App;
```

---

# Reference

## Methods

### `addEventListener()`

```tsx
static addEventListener(
  eventName: AccessibilityChangeEventName | AccessibilityAnnouncementEventName,
  handler: (
    event: AccessibilityChangeEvent | AccessibilityAnnouncementFinishedEvent,
  ) => void,
): EmitterSubscription;
```

Add an event handler. Supported events:

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

Post a string to be announced by the screen reader.

---

### `announceForAccessibilityWithOptions()`

```tsx
static announceForAccessibilityWithOptions(
  announcement: string,
  options: options: {queue?: boolean},
);
```

Post a string to be announced by the screen reader with modification options. By default announcements will interrupt any existing speech, but on iOS they can be queued behind existing speech by setting `queue` to `true` in the options object.

**Parameters:**

| Name                                                          | Type   | Description                                                                              |
| ------------------------------------------------------------- | ------ | ---------------------------------------------------------------------------------------- |
| announcement <div class="label basic required">Required</div> | string | The string to be announced                                                               |
| options <div class="label basic required">Required</div>      | object | `queue` - queue the announcement behind existing speech <div class="label ios">iOS</div> |

---

### `getRecommendedTimeoutMillis()` <div class="label android">Android</div>

```tsx
static getRecommendedTimeoutMillis(originalTimeout: number): Promise<number>;
```

Gets the timeout in millisecond that the user needs.
This value is set in "Time to take action (Accessibility timeout)" of "Accessibility" settings.

**Parameters:**

| Name                                                             | Type   | Description                                                                           |
| ---------------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| originalTimeout <div class="label basic required">Required</div> | number | The timeout to return if "Accessibility timeout" is not set. Specify in milliseconds. |

---

### `isAccessibilityServiceEnabled()` <div class="label android">Android</div>

```tsx
static isAccessibilityServiceEnabled(): Promise<boolean>;
```

Check whether any accessibility service is enabled. This includes TalkBack but also any third-party accessibility app that may be installed. To only check whether TalkBack is enabled, use [isScreenReaderEnabled](#isscreenreaderenabled). Returns a promise which resolves to a boolean. The result is `true` when some accessibility services is enabled and `false` otherwise.

> **Note**: Please use [isScreenReaderEnabled](#isscreenreaderenabled) if you only want to check the status of TalkBack.

---

### `isBoldTextEnabled()` <div class="label ios">iOS</div>

```tsx
static isBoldTextEnabled(): Promise<boolean>:
```

Query whether a bold text is currently enabled. Returns a promise which resolves to a boolean. The result is `true` when bold text is enabled and `false` otherwise.

---

### `isGrayscaleEnabled()` <div class="label ios">iOS</div>

```tsx
static isGrayscaleEnabled(): Promise<boolean>;
```

Query whether grayscale is currently enabled. Returns a promise which resolves to a boolean. The result is `true` when grayscale is enabled and `false` otherwise.

---

### `isInvertColorsEnabled()` <div class="label ios">iOS</div>

```tsx
static isInvertColorsEnabled(): Promise<boolean>;
```

Query whether invert colors is currently enabled. Returns a promise which resolves to a boolean. The result is `true` when invert colors is enabled and `false` otherwise.

---

### `isReduceMotionEnabled()`

```tsx
static isReduceMotionEnabled(): Promise<boolean>;
```

Query whether reduce motion is currently enabled. Returns a promise which resolves to a boolean. The result is `true` when reduce motion is enabled and `false` otherwise.

---

### `isReduceTransparencyEnabled()` <div class="label ios">iOS</div>

```tsx
static isReduceTransparencyEnabled(): Promise<boolean>;
```

查詢當前是否啟用了降低透明度功能。回傳一個解析為布林值的 Promise，當降低透明度功能啟用時結果為 `true`，否則為 `false`。

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

查詢當前是否同時啟用了減少動畫效果與偏好使用淡入淡出轉場設定。回傳一個解析為布林值的 Promise，當偏好淡入淡出轉場功能啟用時結果為 `true`，否則為 `false`。

---

### `setAccessibilityFocus()`

```tsx
static setAccessibilityFocus(reactTag: number);
```

將無障礙焦點設定至指定的 React 元件。

在 Android 平台上，此方法會呼叫 `UIManager.sendAccessibilityEvent`，並傳入指定的 `reactTag` 參數與 `UIManager.AccessibilityEventTypes.typeViewFocused` 參數。

> **注意**：請確保任何要接收無障礙焦點的 `View` 元件都設定了 `accessible={true}` 屬性。