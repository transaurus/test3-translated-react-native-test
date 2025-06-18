---
id: platformcolor
title: PlatformColor
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

```js
PlatformColor(color1, [color2, ...colorN]);
```

您可以使用 `PlatformColor` 函數來存取目標平台的原生顏色，方法是提供對應的原生顏色字串值。將字串傳遞給 `PlatformColor` 函數後，如果該平台存在該顏色，它將返回對應的原生顏色，您可以在應用程式的任何部分應用該顏色。

如果您向 `PlatformColor` 函數傳遞多個字串值，它會將第一個值視為預設值，其餘值作為備用。

```js
PlatformColor('bogusName', 'linkColor');
```

由於原生顏色可能對主題和/或高對比度敏感，這種平台特定的邏輯也會在您的元件內部轉換。

### 支援的顏色

有關支援的系統顏色類型的完整列表，請參閱：

- Android：
  - [R.attr](https://developer.android.com/reference/android/R.attr) - 前綴為 `?attr`
  - [R.color](https://developer.android.com/reference/android/R.color) - 前綴為 `@android:color`
- iOS（Objective-C 和 Swift 表示法）：
  - [UIColor 標準顏色](https://developer.apple.com/documentation/uikit/uicolor/standard_colors)
  - [UIColor UI 元素顏色](https://developer.apple.com/documentation/uikit/uicolor/ui_element_colors)

#### 開發者注意事項

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["web"])}>

<TabItem value="web">

> If you’re familiar with design systems, another way of thinking about this is that `PlatformColor` lets you tap into the local design system's color tokens so your app can blend right in!

</TabItem>
</Tabs>

## 範例

```SnackPlayer name=PlatformColor%20Example&supportedPlatforms=android,ios
import React from 'react';
import {
  Platform,
  PlatformColor,
  StyleSheet,
  Text,
  View
} from 'react-native';

const App = () => (
  <View style={styles.container}>
    <Text style={styles.label}>
      I am a special label color!
    </Text>
  </View>
);

const styles = StyleSheet.create({
  label: {
    padding: 16,
    ...Platform.select({
      ios: {
        color: PlatformColor('label'),
        backgroundColor:
          PlatformColor('systemTealColor'),
      },
      android: {
        color: PlatformColor('?android:attr/textColor'),
        backgroundColor:
          PlatformColor('@android:color/holo_blue_bright'),
      },
      default: { color: 'black' }
    })
  },
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  }
});

export default App;
```

提供給 `PlatformColor` 函數的字串值必須與應用程式運行的原生平台上的字串完全匹配。為了避免運行時錯誤，該函數應包裝在平台檢查中，可以通過 `Platform.OS === 'platform'` 或 `Platform.select()` 來實現，如上例所示。

> **注意：** 您可以在 [PlatformColorExample.js](https://github.com/facebook/react-native/blob/0.70-stable/packages/rn-tester/js/examples/PlatformColor/PlatformColorExample.js) 中找到一個完整的範例，該範例展示了 `PlatformColor` 的正確和預期用法。