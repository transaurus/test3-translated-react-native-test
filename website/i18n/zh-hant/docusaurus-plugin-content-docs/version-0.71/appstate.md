---
id: appstate
title: AppState
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`AppState` 能告知您應用程式目前處於前景或背景狀態，並在狀態變更時發出通知。

AppState 常被用於處理推送通知時，判斷意圖與決定適當行為。

### 應用程式狀態

- `active` - 應用程式正在前景運行
- `background` - 應用程式正在背景運行。使用者可能處於：
  - 其他應用程式中
  - 主畫面
  - [Android] 其他 `Activity`（即使是由您的應用程式啟動）
- [iOS] `inactive` - 此狀態發生在前景與背景轉換期間，以及非活動期間（例如進入多工視圖、開啟通知中心或來電時）。

更多資訊請參閱 [Apple 官方文件](https://developer.apple.com/documentation/uikit/app_and_scenes/managing_your_app_s_life_cycle)

## 基本用法

要查看當前狀態，您可以檢查 `AppState.currentState`，該值會保持最新狀態。但需注意，在啟動時 `currentState` 會暫時為 null，直到 `AppState` 透過橋接取得狀態。

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=AppState%20Function%20Component%20Example
import React, {useRef, useState, useEffect} from 'react';
import {AppState, StyleSheet, Text, View} from 'react-native';

const AppStateExample = () => {
  const appState = useRef(AppState.currentState);
  const [appStateVisible, setAppStateVisible] = useState(appState.current);

  useEffect(() => {
    const subscription = AppState.addEventListener('change', nextAppState => {
      if (
        appState.current.match(/inactive|background/) &&
        nextAppState === 'active'
      ) {
        console.log('App has come to the foreground!');
      }

      appState.current = nextAppState;
      setAppStateVisible(appState.current);
      console.log('AppState', appState.current);
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
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default AppStateExample;
```

If you don't want to see the AppState update from `active` to `inactive` on iOS you can remove the state variable and use the `appState.current` value.

</TabItem>
<TabItem value="classical">

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=AppState%20Class%20Component%20Example&ext=js
import React, {Component} from 'react';
import {AppState, StyleSheet, Text, View} from 'react-native';

class AppStateExample extends Component {
  state = {
    appState: AppState.currentState,
  };

  componentDidMount() {
    this.appStateSubscription = AppState.addEventListener(
      'change',
      nextAppState => {
        if (
          this.state.appState.match(/inactive|background/) &&
          nextAppState === 'active'
        ) {
          console.log('App has come to the foreground!');
        }
        this.setState({appState: nextAppState});
      },
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
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default AppStateExample;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=AppState%20Class%20Component%20Example&ext=tsx
import React, {Component} from 'react';
import {AppState, StyleSheet, Text, View} from 'react-native';
import type {NativeEventSubscription} from 'react-native';

class AppStateExample extends Component {
  appStateSubscription?: NativeEventSubscription;
  state = {
    appState: AppState.currentState,
  };

  componentDidMount() {
    this.appStateSubscription = AppState.addEventListener(
      'change',
      nextAppState => {
        if (
          this.state.appState.match(/inactive|background/) &&
          nextAppState === 'active'
        ) {
          console.log('App has come to the foreground!');
        }
        this.setState({appState: nextAppState});
      },
    );
  }

  componentWillUnmount() {
    this.appStateSubscription?.remove();
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
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default AppStateExample;
```

</TabItem>
</Tabs>

</TabItem>
</Tabs>

此範例永遠只會顯示「當前狀態為：active」，因為應用程式只有在 `active` 狀態時對使用者可見，而 null 狀態僅會短暫出現。若想實驗此程式碼，建議使用實機而非嵌入式預覽。

---

# 參考資料

## 事件

### `change`

當應用程式狀態變更時觸發此事件。監聽器會收到[當前應用程式狀態值](appstate#app-states)之一。

### `memoryWarning`

此事件用於需要發出記憶體警告或釋放記憶體時。

### `focus` <div class="label android">Android</div>

當應用程式獲得焦點時觸發（使用者正在與應用程式互動）。

### `blur` <div class="label android">Android</div>

當使用者未主動與應用程式互動時觸發。適用於使用者下拉[通知抽屜](https://developer.android.com/guide/topics/ui/notifiers/notifications#bar-and-drawer)的情境。此時 `AppState` 不會改變，但會觸發 `blur` 事件。

## 方法

### `addEventListener()`

```tsx
static addEventListener(
  type: AppStateEvent,
  listener: (state: AppStateStatus) => void,
): NativeEventSubscription;
```

設定當指定事件類型在 AppState 上發生時將呼叫的函式。有效 `eventType` 值請參閱[上述事件列表](#events)。回傳 `EventSubscription`。

## 屬性

### `currentState`

```tsx
static currentState: AppStateStatus;
```