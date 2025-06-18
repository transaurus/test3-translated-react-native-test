---
id: appstate
title: AppState
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`AppState` 可以判斷應用程式處於前景或背景狀態，並在狀態變更時發出通知。

AppState 常用於處理推送通知時，判斷意圖並採取適當行為。

### 應用程式狀態

- `active` - 應用程式在前景運行
- `background` - 應用程式在背景運行。使用者可能：
  - 正在使用其他應用程式
  - 處於主畫面
  - [Android] 處於其他 `Activity`（即使是由您的應用程式啟動）
- [iOS] `inactive` - 此狀態發生在前景與背景轉換期間，或處於非活動狀態時（例如進入多工視圖、開啟通知中心或來電時）

更多資訊請參閱 [Apple 官方文件](https://developer.apple.com/documentation/uikit/app_and_scenes/managing_your_app_s_life_cycle)

## 基本用法

要查看當前狀態，可檢查 `AppState.currentState`，該值會保持最新狀態。但啟動時 `currentState` 會暫時為 null，直到 `AppState` 透過橋接器取得狀態。

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=AppState%20Function%20Component%20Example
import React, { useRef, useState, useEffect } from "react";
import { AppState, StyleSheet, Text, View } from "react-native";

const AppStateExample = () => {
  const appState = useRef(AppState.currentState);
  const [appStateVisible, setAppStateVisible] = useState(appState.current);

  useEffect(() => {
    const subscription = AppState.addEventListener("change", nextAppState => {
      if (
        appState.current.match(/inactive|background/) &&
        nextAppState === "active"
      ) {
        console.log("App has come to the foreground!");
      }

      appState.current = nextAppState;
      setAppStateVisible(appState.current);
      console.log("AppState", appState.current);
    });

    return () => {
      subscription.remove();
    };
  }, []);

  return (
    <View style={styles.container}>
      <Text>Current state is: {appStateVisible}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },
});

export default AppStateExample;
```

If you don't want to see the AppState update from `active` to `inactive` on iOS you can remove the state variable and use the `appState.current` value.

</TabItem>
<TabItem value="classical">

```SnackPlayer name=AppState%20Class%20Component%20Example
import React, { Component } from "react";
import { AppState, StyleSheet, Text, View } from "react-native";

class AppStateExample extends Component {
  state = {
    appState: AppState.currentState
  };

  componentDidMount() {
    this.appStateSubscription = AppState.addEventListener(
      "change",
      nextAppState => {
        if (
          this.state.appState.match(/inactive|background/) &&
          nextAppState === "active"
        ) {
          console.log("App has come to the foreground!");
        }
        this.setState({ appState: nextAppState });
      }
    );
  }

  componentWillUnmount() {
    this.appStateSubscription.remove();
  }

  render() {
    return (
      <View style={styles.container}>
        <Text>Current state is: {this.state.appState}</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center"
  }
});

export default AppStateExample;
```

</TabItem>
</Tabs>

此範例永遠只會顯示「當前狀態為: active」，因為應用程式僅在 `active` 狀態時對使用者可見，null 狀態只會短暫出現。建議使用實機而非嵌入式預覽進行測試。

---

# 參考文件

## 事件

### `change`

當應用程式狀態變更時觸發此事件。監聽器會收到[當前應用程式狀態值](appstate#app-states)之一。

### `memoryWarning`

此事件用於需要發出記憶體警告或釋放記憶體時。

### `focus` <div class="label android">Android</div>

當應用程式獲得焦點時觸發（使用者正在與應用程式互動）。

### `blur` <div class="label android">Android</div>

當使用者未主動與應用程式互動時觸發。適用於使用者下拉[通知抽屜](https://developer.android.com/guide/topics/ui/notifiers/notifications#bar-and-drawer)的情況。此時 `AppState` 不會改變，但會觸發 `blur` 事件。

## 方法

### `addEventListener()`

```jsx
addEventListener(eventType, listener);
```

設置當指定事件類型發生時呼叫的函數。有效 `eventType` 值請參閱[上述事件列表](#events)。返回 `EventSubscription` 物件。

---

### `removeEventListener()`

```jsx
removeEventListener(eventType, listener);
```

> **已棄用。** 請改用 [`addEventListener()`](#addeventlistener) 返回的 `EventSubscription` 上的 `remove()` 方法。

## 屬性

### `currentState`

```jsx
AppState.currentState;
```