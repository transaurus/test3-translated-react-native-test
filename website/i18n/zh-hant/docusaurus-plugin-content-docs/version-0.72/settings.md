---
id: settings
title: Settings
---

`Settings` 作為 [`NSUserDefaults`](https://developer.apple.com/documentation/foundation/nsuserdefaults) 的封裝層，這是一個僅在 iOS 上可用的持久化鍵值儲存系統。

## 範例

```SnackPlayer name=Settings%20Example&supportedPlatforms=ios
import React, {useState} from 'react';
import {Button, Settings, StyleSheet, Text, View} from 'react-native';

const App = () => {
  const [data, setData] = useState(() => Settings.get('data'));

  return (
    <View style={styles.container}>
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
    </View>
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

`watchId` 是 `watchKeys()` 在最初設定訂閱時返回的數字。

---

### `get()`

```tsx
static get(key: string): any;
```

從 `NSUserDefaults` 中獲取指定 `key` 的當前值。

---

### `set()`

```tsx
static set(settings: Record<string, any>);
```

在 `NSUserDefaults` 中設定一個或多個值。

---

### `watchKeys()`

```tsx
static watchKeys(keys: string | array<string>, callback: () => void): number;
```

訂閱以在 `NSUserDefaults` 中由 `keys` 參數指定的任何鍵的值發生變更時接收通知。返回一個 `watchId` 數字，可與 `clearWatch()` 一起使用以取消訂閱。

> **注意：** `watchKeys()` 設計上會忽略內部的 `set()` 調用，僅在 React Native 代碼外部執行的變更時觸發回調。