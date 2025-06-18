---
id: settings
title: Settings
---

`Settings` 作為 [`NSUserDefaults`](https://developer.apple.com/documentation/foundation/nsuserdefaults) 的封裝層，該持久化鍵值儲存機制僅適用於 iOS 平台。

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

`watchId` 為 `watchKeys()` 初始設定訂閱時返回的數字識別碼。

---

### `get()`

```tsx
static get(key: string): any;
```

從 `NSUserDefaults` 中取得指定 `key` 的當前值。

---

### `set()`

```tsx
static set(settings: Record<string, any>);
```

在 `NSUserDefaults` 中設定一或多個數值。

---

### `watchKeys()`

```tsx
static watchKeys(keys: string | array<string>, callback: () => void): number;
```

訂閱 `NSUserDefaults` 中由 `keys` 參數指定的任何鍵值變更通知。返回 `watchId` 數字識別碼，可搭配 `clearWatch()` 取消訂閱。

> **注意：** `watchKeys()` 的設計會忽略內部 `set()` 呼叫，僅在 React Native 程式碼外部觸發變更時執行回調函數。