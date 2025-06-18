---
id: backhandler
title: BackHandler
---

Backhandler API 用於偵測 Android 裝置的實體返回按鈕操作，允許您註冊系統返回動作的事件監聽器，並控制應用程式的回應行為。此功能僅限 Android 平台使用。

事件監聽器的觸發順序為反向（即最後註冊的監聽器會最先被呼叫）。

- **若某個監聽器返回 true**，則先前註冊的監聽器將不會被呼叫。
- **若沒有任何監聽器返回 true 或未註冊監聽器**，系統將預設執行返回按鈕功能來關閉應用程式。

> **模態視窗使用者請注意**：若應用程式正在顯示開啟的 `Modal`，`BackHandler` 將不會發布任何事件（參見 [`Modal` 文件](modal#onrequestclose)）。

## 使用模式

```tsx
BackHandler.addEventListener('hardwareBackPress', function () {
  /**
   * this.onMainScreen and this.goBack are just examples,
   * you need to use your own implementation here.
   *
   * Typically you would use the navigator here to go to the last state.
   */

  if (!this.onMainScreen()) {
    this.goBack();
    /**
     * When true is returned the event will not be bubbled up
     * & no other back action will execute
     */
    return true;
  }
  /**
   * Returning false will let the event to bubble up & let other event listeners
   * or the system's default back action to be executed.
   */
  return false;
});
```

## 範例

以下範例實作確認使用者是否要退出應用程式的場景：

```SnackPlayer name=BackHandler&supportedPlatforms=android
import React, {useEffect} from 'react';
import {Text, View, StyleSheet, BackHandler, Alert} from 'react-native';

const App = () => {
  useEffect(() => {
    const backAction = () => {
      Alert.alert('Hold on!', 'Are you sure you want to go back?', [
        {
          text: 'Cancel',
          onPress: () => null,
          style: 'cancel',
        },
        {text: 'YES', onPress: () => BackHandler.exitApp()},
      ]);
      return true;
    };

    const backHandler = BackHandler.addEventListener(
      'hardwareBackPress',
      backAction,
    );

    return () => backHandler.remove();
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.text}>Click Back button!</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    fontSize: 18,
    fontWeight: 'bold',
  },
});

export default App;
```

`BackHandler.addEventListener` 會建立事件監聽器並返回一個 `NativeEventSubscription` 物件，需透過 `NativeEventSubscription.remove` 方法清除。

## 與 React Navigation 搭配使用

若您使用 React Navigation 進行畫面導航，可參閱其指南 [自訂 Android 返回按鈕行為](https://reactnavigation.org/docs/custom-android-back-button-handling/)。

## Backhandler 鉤子

[React Native Hooks](https://github.com/react-native-community/hooks#usebackhandler) 提供便利的 `useBackHandler` 鉤子，可簡化事件監聽器的設定流程。

---

# 參考文件

## 方法

### `addEventListener()`

```tsx
static addEventListener(
  eventName: BackPressEventName,
  handler: () => boolean | null | undefined,
): NativeEventSubscription;
```

---

### `exitApp()`

```tsx
static exitApp();
```

---

### `removeEventListener()`

```tsx
static removeEventListener(
  eventName: BackPressEventName,
  handler: () => boolean | null | undefined,
);
```