---
id: i18nmanager
title: I18nManager
---

# I18nManager

`I18nManager` 模組提供管理從右至左（RTL）佈局支援的實用工具，適用於阿拉伯語、希伯來語等語言。它提供控制 RTL 行為及檢查當前佈局方向的方法。

## 範例

### 根據 RTL 調整位置與動畫

若您使用絕對定位元素來對齊其他 flexbox 元素，在 RTL 語言中可能無法正確對齊。可使用 `isRTL` 來調整對齊方式或動畫效果。

```SnackPlayer name=I18nManager%20Change%20Absolute%20Positions%20And%20Animations
import React from 'react';
import {I18nManager, Text, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  // Change to `true` to see the effect in a non-RTL language
  const isRTL = I18nManager.isRTL;
  return (
    <SafeAreaProvider>
      <SafeAreaView>
        <View style={{ position: 'absolute', left: isRTL ? undefined : 0, right: isRTL ? 0 : undefined }}>
          {isRTL ? (
            <Text>Back &gt;</Text>
          ) : (
            <Text>&lt; Back</Text>
          )}
        </View>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

export default App;
```

### 開發期間

```SnackPlayer name=I18nManager%20During%20Development
import React, {useState} from 'react';
import {Alert, I18nManager, StyleSheet, Switch, Text, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [rtl, setRTL] = useState(I18nManager.isRTL);
  return (
    <SafeAreaProvider>
      <SafeAreaView>
        <View style={styles.container}>
          <View style={styles.forceRtl}>
            <Text>Force RTL in Development:</Text>
            <Switch value={rtl} onValueChange={(value) => {
              setRTL(value);
              I18nManager.forceRTL(value);
              Alert.alert(
                'Reload this page',
                'Please reload this page to change the UI direction! ' +
                  'All examples in this app will be affected. ' +
                  'Check them out to see what they look like in RTL layout.',
              );
            }} />
          </View>
        </View>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    padding: 20,
  },
  forceRtl: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
});

export default App;
```

# 參考

## 屬性

### `isRTL`

```typescript
static isRTL: boolean;
```

布林值，表示應用程式當前是否處於 RTL 佈局模式。

`isRTL` 的值由以下邏輯決定：

- 若 `forceRTL` 為 `true`，`isRTL` 返回 `true`
- 若 `allowRTL` 為 `false`，`isRTL` 返回 `false`
- 否則，`isRTL` 將在以下情況下為 `true`：
  - **iOS：**
    - 裝置上使用者偏好的語言是 RTL 語言
    - 應用程式定義的本地化包含使用者選擇的語言（如 Xcode 專案檔中定義的 `knownRegions = (...)`
  - **Android：**
    - 裝置上使用者偏好的語言是 RTL 語言
    - 應用程式的 `AndroidManifest.xml` 在 `<application>` 元素中定義了 `android:supportsRTL="true"`

### `doLeftAndRightSwapInRTL`

```typescript
static doLeftAndRightSwapInRTL: boolean;
```

布林值，表示在 RTL 模式下是否應自動交換左右樣式屬性。啟用時，在 RTL 佈局中左側會變為右側，右側變為左側。

## 方法

### `allowRTL()`

```typescript
static allowRTL: (allowRTL: boolean) => void;
```

啟用或停用應用程式的 RTL 佈局支援。

**參數：**

- `allowRTL` (布林值)：是否允許 RTL 佈局

**重要注意事項：**

- 變更將在下一次應用程式啟動時生效，而非立即
- 此設定會跨應用程式重啟持續保存

### `forceRTL()`

```typescript
static forceRTL: (forced: boolean) => void;
```

強制應用程式使用 RTL 佈局，無論裝置語言設定為何。主要用於開發期間測試 RTL 佈局。

避免在正式版應用程式中強制 RTL，因為這需要完整重啟應用程式才能生效，會造成不佳的使用者體驗。

**參數：**

- `forced` (布林值)：是否強制 RTL 佈局

**重要注意事項：**

- 變更將在下一次應用程式啟動時完全生效，而非立即
- 設定會跨應用程式重啟持續保存
- 僅適用於開發與測試。正式環境中應完全停用 RTL 或適當處理（參見 `isRTL`）

### `swapLeftAndRightInRTL()`

```typescript
static swapLeftAndRightInRTL: (swapLeftAndRight: boolean) => void;
```

在 RTL 模式下交換左右樣式屬性。啟用時，在 RTL 佈局中左側會變為右側，右側變為左側。不影響 `isRTL` 的值。