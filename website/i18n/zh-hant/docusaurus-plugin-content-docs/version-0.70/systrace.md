---
id: systrace
title: Systrace
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`Systrace` 是 Android 平台標準的基於標記的分析工具（安裝 Android platform-tools 套件時會自動安裝）。被分析的程式碼區塊會被開始/結束標記包圍，並以彩色圖表形式可視化。Android SDK 和 React Native 框架都提供了可視化的標準標記。

## 範例

`Systrace` 允許您用標籤和整數值標記 JavaScript (JS) 事件。在 EasyProfiler 中捕獲非計時的 JS 事件。

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Systrace%20Function%20Component%20Example
import React from "react";
import { Button, Text, View, SafeAreaView, StyleSheet, Systrace } from "react-native";

const App = () =>  {

  const enableProfiling = () => {
    Systrace.setEnabled(true); // Call setEnabled to turn on the profiling.
    Systrace.beginEvent('event_label')
    Systrace.counterEvent('event_label', 10);
  }

  const stopProfiling = () => {
    Systrace.endEvent()
  }

  return (
    <SafeAreaView style={styles.container}>
      <Text style={[styles.header, styles.paragraph]}>React Native Systrace API</Text>
    <View style={styles.buttonRow}>
      <Button title="Capture the non-Timed JS events in EasyProfiler" onPress={()=> enableProfiling()}/>
      <Button title="Stop capturing" onPress={()=> stopProfiling()} color="#FF0000"/>
    </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
    paddingTop: 44,
    padding: 8
  },
   header: {
    fontSize: 18,
    fontWeight: "bold",
    textAlign: "center"
  },
  paragraph: {
    margin: 24,
    fontSize: 25,
    textAlign: "center"
  },
  buttonRow: {
    flexBasis: 150,
    marginVertical: 16,
    justifyContent: 'space-evenly'
  }
});

export default App;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Systrace%20Class%20Component%20Example
import React, { Component } from "react";
import { Button, Text, View, SafeAreaView, StyleSheet, Systrace } from "react-native";

class App extends Component {

  enableProfiling = () => {
    Systrace.setEnabled(true); // Call setEnabled to turn on the profiling.
    Systrace.beginEvent('event_label')
    Systrace.counterEvent('event_label', 10);
  }

  stopProfiling = () => {
    Systrace.endEvent()
  }

  render() {
    return (
      <SafeAreaView style={styles.container}>
        <Text style={[styles.header, styles.paragraph]}>React Native Systrace API</Text>
      <View style={styles.buttonRow}>
        <Button title="Capture the non-Timed JS events in EasyProfiler" onPress={()=> this.enableProfiling()}/>
        <Button title="Stop capturing" onPress={()=> this.stopProfiling()} color="#FF0000"/>
      </View>
      </SafeAreaView>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
    paddingTop: 44,
    padding: 8
  },
   header: {
    fontSize: 18,
    fontWeight: "bold",
    textAlign: "center"
  },
  paragraph: {
    margin: 24,
    fontSize: 25,
    textAlign: "center"
  },
  buttonRow: {
    flexBasis: 150,
    marginVertical: 16,
    justifyContent: 'space-evenly'
  }
});

export default App;
```

</TabItem>
</Tabs>

---

# 參考資料

## 方法

### `installReactHook()`

```jsx
static installReactHook(useFiber)
```

---

### `setEnabled()`

```jsx
static setEnabled(enabled)
```

---

### `isEnabled()`

```jsx
static isEnabled()
```

---

### `beginEvent()`

```jsx
static beginEvent(profileName?, args?)
```

beginEvent/endEvent 用於在同一個呼叫堆疊框架內開始和結束分析。

---

### `endEvent()`

```jsx
static endEvent()
```

---

### `beginAsyncEvent()`

```jsx
static beginAsyncEvent(profileName?)
```

beginAsyncEvent/endAsyncEvent 用於開始和結束分析，其中結束可能發生在另一個執行緒或當前堆疊框架之外，例如 await 返回的 cookie 變量應作為輸入參數傳遞給 endAsyncEvent 呼叫以結束分析。

---

### `endAsyncEvent()`

```jsx
static endAsyncEvent(profileName?, cookie?)
```

---

### `counterEvent()`

```jsx
static counterEvent(profileName?, value?)
```

將數值註冊到 systrace 時間軸上的 profileName。