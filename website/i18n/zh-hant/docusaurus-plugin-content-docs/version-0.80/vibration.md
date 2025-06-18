---
id: vibration
title: Vibration
---

使裝置產生震動。

## 範例

```SnackPlayer name=Vibration%20Example&supportedPlatforms=ios,android
import React from 'react';
import {
  Button,
  Platform,
  Text,
  Vibration,
  View,
  StyleSheet,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const Separator = () => {
  return <View style={Platform.OS === 'android' ? styles.separator : null} />;
};

const App = () => {
  const ONE_SECOND_IN_MS = 1000;

  const PATTERN = [
    1 * ONE_SECOND_IN_MS,
    2 * ONE_SECOND_IN_MS,
    3 * ONE_SECOND_IN_MS,
  ];

  const PATTERN_DESC =
    Platform.OS === 'android'
      ? 'wait 1s, vibrate 2s, wait 3s'
      : 'wait 1s, vibrate, wait 2s, vibrate, wait 3s';

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text style={[styles.header, styles.paragraph]}>Vibration API</Text>
        <View>
          <Button title="Vibrate once" onPress={() => Vibration.vibrate()} />
        </View>
        <Separator />
        {Platform.OS === 'android'
          ? [
              <View>
                <Button
                  title="Vibrate for 10 seconds"
                  onPress={() => Vibration.vibrate(10 * ONE_SECOND_IN_MS)}
                />
              </View>,
              <Separator />,
            ]
          : null}
        <Text style={styles.paragraph}>Pattern: {PATTERN_DESC}</Text>
        <Button
          title="Vibrate with pattern"
          onPress={() => Vibration.vibrate(PATTERN)}
        />
        <Separator />
        <Button
          title="Vibrate with pattern until cancelled"
          onPress={() => Vibration.vibrate(PATTERN, true)}
        />
        <Separator />
        <Button
          title="Stop vibration pattern"
          onPress={() => Vibration.cancel()}
          color="#FF0000"
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
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
    textAlign: 'center',
  },
  separator: {
    marginVertical: 8,
    borderBottomColor: '#737373',
    borderBottomWidth: StyleSheet.hairlineWidth,
  },
});

export default App;
```

> Android 應用程式應在 `AndroidManifest.xml` 中加入 `<uses-permission android:name="android.permission.VIBRATE"/>` 以請求 `android.permission.VIBRATE` 權限。

> 在 iOS 上，震動 API 是透過呼叫 `AudioServicesPlaySystemSound(kSystemSoundID_Vibrate)` 來實現的。

---

# 參考文獻

## 方法

### `cancel()`

```tsx
static cancel();
```

呼叫此方法以停止因啟用重複而觸發的 `vibrate()` 震動。

---

### `vibrate()`

```tsx
static vibrate(
  pattern?: number | number[],
  repeat?: boolean
);
```

觸發固定持續時間的震動。

**在 Android 上，**震動持續時間預設為 400 毫秒，可透過傳遞數字作為 `pattern` 參數的值來指定任意震動持續時間。**在 iOS 上，**震動持續時間固定為約 400 毫秒。

`vibrate()` 方法可接受一個由數字組成的 `pattern` 參數陣列，這些數字代表以毫秒為單位的時間。您可以將 `repeat` 設為 true 以循環執行震動模式，直到呼叫 `cancel()` 為止。

**在 Android 上，**`pattern` 陣列的奇數索引代表震動持續時間，偶數索引代表間隔時間。**在 iOS 上，**`pattern` 陣列中的數字代表間隔時間，因為震動持續時間是固定的。

**參數：**

| Name    | Type                                                                     | Default | Description                                                                                       |
| ------- | ------------------------------------------------------------------------ | ------- | ------------------------------------------------------------------------------------------------- |
| pattern | number <div className="label android">Android</div><hr/>array of numbers | `400`   | Vibration duration in milliseconds.<hr/>Vibration pattern as an array of numbers in milliseconds. |
| repeat  | boolean                                                                  | `false` | Repeat vibration pattern until `cancel()`.                                                        |