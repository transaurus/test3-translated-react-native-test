---
id: settings
title: Settings
---

`Settings` 作為 [`NSUserDefaults`](https://developer.apple.com/documentation/foundation/nsuserdefaults) 的封裝層，該持久化鍵值儲存僅適用於 iOS 平台。

## 範例

```SnackPlayer name=Settings%20Example&supportedPlatforms=ios
import React, {useState} from 'react';
import {Button, Settings, StyleSheet, Text} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [data, setData] = useState(() => Settings.get('data'));

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text>Stored value:</Text>
        <Text style={styles.value}>{data}</Text>
        <Button
          onPress={() => {
            Settings.set({data: 'React'});
            setData(Settings.get('data'));
          }}
          title="Store 'React'"
        />
        <Button
          onPress={() => {
            Settings.set({data: 'Native'});
            setData(Settings.get('data'));
          }}
          title="Store 'Native'"
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  value: {
    fontSize: 24,
    marginVertical: 12,
  },
});

export default App;
```

---

# 參考文獻

## 方法

### `clearWatch()`

```tsx
static clearWatch(watchId: number);
```

`watchId` 為當初訂閱時由 `watchKeys()` 回傳的識別碼。

---

### `get()`

```tsx
static get(key: string): any;
```

從 `NSUserDefaults` 取得指定 `key` 的當前值。

---

### `set()`

```tsx
static set(settings: Record<string, any>);
```

在 `NSUserDefaults` 中設定一或多個鍵值。

---

### `watchKeys()`

```tsx
static watchKeys(keys: string | array<string>, callback: () => void): number;
```

訂閱 `NSUserDefaults` 中指定 `keys` 參數的鍵值變更通知。回傳 `watchId` 識別碼供 `clearWatch()` 取消訂閱使用。

> **注意：** `watchKeys()` 設計上會忽略 React Native 程式碼內部的 `set()` 呼叫，僅在外部變更時觸發回調函數。