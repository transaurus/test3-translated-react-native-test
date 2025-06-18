---
id: systrace
title: Systrace
---

`Systrace` 是 Android 標準的基於標記的分析工具（安裝 Android platform-tools 套件時會一併安裝）。被分析的程式碼區塊會被開始/結束標記包圍，並以彩色圖表形式可視化。Android SDK 和 React Native 框架都提供了可視化的標準標記。

## 範例

`Systrace` 允許您用標籤和整數值標記 JavaScript (JS) 事件。在 EasyProfiler 中捕獲非計時的 JS 事件。

```SnackPlayer name=Systrace%20Example
import React from 'react';
import {
  Button,
  Text,
  View,
  SafeAreaView,
  StyleSheet,
  Systrace,
} from 'react-native';

const App = () => {
  const enableProfiling = () => {
    Systrace.setEnabled(true); // Call setEnabled to turn on the profiling.
    Systrace.beginEvent('event_label');
    Systrace.counterEvent('event_label', 10);
  };

  const stopProfiling = () => {
    Systrace.endEvent();
  };

  return (
    <SafeAreaView style={styles.container}>
      <Text style={[styles.header, styles.paragraph]}>
        React Native Systrace API
      </Text>
      <View style={styles.buttonRow}>
        <Button
          title="Capture the non-Timed JS events in EasyProfiler"
          onPress={() => enableProfiling()}
        />
        <Button
          title="Stop capturing"
          onPress={() => stopProfiling()}
          color="#FF0000"
        />
      </View>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
    paddingTop: 44,
    padding: 8,
  },
  header: {
    fontSize: 18,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  paragraph: {
    margin: 24,
    fontSize: 25,
    textAlign: 'center',
  },
  buttonRow: {
    flexBasis: 150,
    marginVertical: 16,
    justifyContent: 'space-evenly',
  },
});

export default App;
```

---

# 參考

## 方法

### `isEnabled()`

```tsx
static isEnabled(): boolean;
```

---

### `beginEvent()`

```tsx
static beginEvent(eventName: string | (() => string), args?: EventArgs);
```

beginEvent/endEvent 用於在同一個呼叫堆疊框架內開始和結束分析。

---

### `endEvent()`

```tsx
static endEvent(args?: EventArgs);
```

---

### `beginAsyncEvent()`

```tsx
static beginAsyncEvent(
  eventName: string | (() => string),
  args?: EventArgs,
): number;
```

beginAsyncEvent/endAsyncEvent 用於開始和結束分析，其中結束可以發生在另一個執行緒或當前堆疊框架之外，例如 await 返回的 cookie 變量應作為輸入傳遞給 endAsyncEvent 呼叫以結束分析。

---

### `endAsyncEvent()`

```tsx
static endAsyncEvent(
  eventName: EventName,
  cookie: number,
  args?: EventArgs,
);
```

---

### `counterEvent()`

```tsx
static counterEvent(eventName: string | (() => string), value: number);
```

將值註冊到 systrace 時間軸上的 profileName。