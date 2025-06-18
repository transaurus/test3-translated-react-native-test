---
id: platformcolor
title: PlatformColor
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

```js
PlatformColor(color1, [color2, ...colorN]);
```

您可以使用 `PlatformColor` 函數，通過提供對應平台原生顏色的字符串值來訪問目標平台的原生色彩。將字符串傳遞給 `PlatformColor` 函數後，若該平台存在對應顏色，函數將返回相應的原生色彩，您可將其應用於應用程式的任何部分。

若向 `PlatformColor` 函數傳遞多個字符串值，函數會將第一個值視為預設值，其餘值作為備用選項。

```js
PlatformColor('bogusName', 'linkColor');
```

由於原生色彩可能受到主題或高對比度設定的影響，此平台特定邏輯也會在您的元件內部自動轉換。

### 支援的色彩類型

完整支援的系統色彩類型清單請參閱：

- Android：
  - [R.attr](https://developer.android.com/reference/android/R.attr) - 前綴為 `?attr`
  - [R.color](https://developer.android.com/reference/android/R.color) - 前綴為 `@android:color`
- iOS（Objective-C 與 Swift 標記法）：
  - [UIColor 標準色彩](https://developer.apple.com/documentation/uikit/uicolor/standard_colors)
  - [UIColor UI 元素色彩](https://developer.apple.com/documentation/uikit/uicolor/ui_element_colors)

#### 開發者注意事項

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["web"])}>

<TabItem value="web">

> If you’re familiar with design systems, another way of thinking about this is that `PlatformColor` lets you tap into the local design system's color tokens so your app can blend right in!

</TabItem>
</Tabs>

## 範例

```SnackPlayer name=PlatformColor%20Example&supportedPlatforms=android,ios
import React from 'react';
import {Platform, PlatformColor, StyleSheet, Text, View} from 'react-native';

const App = () => (
  <View style={styles.container}>
    <Text style={styles.label}>I am a special label color!</Text>
  </View>
);

const styles = StyleSheet.create({
  label: {
    padding: 16,
    ...Platform.select({
      ios: {
        color: PlatformColor('label'),
        backgroundColor: PlatformColor('systemTealColor'),
      },
      android: {
        color: PlatformColor('?android:attr/textColor'),
        backgroundColor: PlatformColor('@android:color/holo_blue_bright'),
      },
      default: {color: 'black'},
    }),
  },
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default App;
```

提供給 `PlatformColor` 函數的字符串值必須與應用程式運行時所在原生平台的字符串完全匹配。為避免運行時錯誤，應通過 `Platform.OS === '平台名稱'` 或 `Platform.select()` 進行平台檢查（如上例所示）來包裝此函數。

> **注意：** 您可在 [PlatformColorExample.js](https://github.com/facebook/react-native/blob/0.71-stable/packages/rn-tester/js/examples/PlatformColor/PlatformColorExample.js) 找到完整範例，展示 `PlatformColor` 的正確使用方式。