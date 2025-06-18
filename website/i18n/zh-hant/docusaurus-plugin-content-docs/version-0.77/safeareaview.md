---
id: safeareaview
title: SafeAreaView
---

`SafeAreaView` 的目的是在裝置的安全區域邊界內渲染內容。目前僅適用於 iOS 11 或更高版本的 iOS 裝置。

`SafeAreaView` 會渲染嵌套內容，並自動套用內邊距以反映未被導覽列、標籤列、工具列和其他上層視圖覆蓋的視圖部分。更重要的是，安全區域的內邊距反映了螢幕的物理限制，例如圓角或相機凹口（即 iPhone 13 上的感測器區域）。

## 範例

使用時，請將您的頂層視圖用 `SafeAreaView` 包裹，並套用 `flex: 1` 的樣式。您可能還需要使用與應用程式設計相匹配的背景顏色。

```SnackPlayer name=SafeAreaView&supportedPlatforms=ios
import React from 'react';
import {StyleSheet, Text, SafeAreaView} from 'react-native';

const App = () => {
  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.text}>Page content</Text>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  text: {
    fontSize: 25,
    fontWeight: '500',
  },
});

export default App;
```

---

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view.md#props)。

> 由於內邊距用於實現元件的行為，套用於 `SafeAreaView` 的樣式中的內邊距規則將被忽略，並可能根據平台導致不同的結果。詳情請參閱 [#22211](https://github.com/facebook/react-native/issues/22211)。