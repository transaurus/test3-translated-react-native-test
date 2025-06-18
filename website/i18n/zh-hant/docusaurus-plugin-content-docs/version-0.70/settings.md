---
id: settings
title: Settings
---

`Settings` 作為 [`NSUserDefaults`](https://developer.apple.com/documentation/foundation/nsuserdefaults) 的封裝層，該功能是僅在 iOS 上可用的持久化鍵值儲存系統。

## 範例

```SnackPlayer name=Settings%20Example&supportedPlatforms=ios
import React, { useState } from "react";
import { Button, Settings, StyleSheet, Text, View } from "react-native";

const App = () => {
  const [data, setData] = useState(Settings.get("data"));

  const storeData = data => {
    Settings.set(data);
    setData(Settings.get("data"));
  };

  return (
    <View style={styles.container}>
      <Text>Stored value:</Text>
      <Text style={styles.value}>{data}</Text>
      <Button
        onPress={() => storeData({ data: "React" })}
        title="Store 'React'"
      />
      <Button
        onPress={() => storeData({ data: "Native" })}
        title="Store 'Native'"
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center"
  },
  value: {
    fontSize: 24,
    marginVertical: 12
  }
});

export default App;
```

---

# 參考文獻

## 方法

### `clearWatch()`

```jsx
static clearWatch(watchId: number)
```

`watchId` 是當初訂閱時由 `watchKeys()` 返回的數字識別碼。

---

### `get()`

```jsx
static get(key: string): mixed
```

從 `NSUserDefaults` 中獲取指定 `key` 的當前值。

---

### `set()`

```jsx
static set(settings: object)
```

在 `NSUserDefaults` 中設定一個或多個值。

---

### `watchKeys()`

```jsx
static watchKeys(keys: string | array<string>, callback: function): number
```

訂閱以接收當 `keys` 參數指定的任何鍵值在 `NSUserDefaults` 中被更改時的通知。返回一個 `watchId` 數字，可與 `clearWatch()` 搭配使用來取消訂閱。

> **注意：** `watchKeys()` 在設計上會忽略內部 `set()` 的調用，僅在 React Native 程式碼外部發生的變更時觸發回調函數。