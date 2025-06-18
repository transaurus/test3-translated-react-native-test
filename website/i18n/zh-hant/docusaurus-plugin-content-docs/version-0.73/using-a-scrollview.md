---
id: using-a-scrollview
title: Using a ScrollView
---

[ScrollView](scrollview.md) 是一個通用的滾動容器，可包含多個組件和視圖。可滾動的項目可以是異構的，您可以垂直和水平滾動（通過設置 `horizontal` 屬性）。

此範例創建了一個垂直的 `ScrollView`，其中混合了圖片和文字。

```SnackPlayer name=Using%20ScrollView
import React from 'react';
import {Image, ScrollView, Text} from 'react-native';

const logo = {
  uri: 'https://reactnative.dev/img/tiny_logo.png',
  width: 64,
  height: 64,
};

const App = () => (
  <ScrollView>
    <Text style={{fontSize: 96}}>Scroll me plz</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>If you like</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>Scrolling down</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>What's the best</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 96}}>Framework around?</Text>
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Image source={logo} />
    <Text style={{fontSize: 80}}>React Native</Text>
  </ScrollView>
);

export default App;
```

可以通過使用 `pagingEnabled` 屬性來配置 ScrollView，允許用戶通過滑動手勢在視圖之間翻頁。在 Android 上，也可以使用 [ViewPager](https://github.com/react-native-community/react-native-viewpager) 組件實現水平滑動切換視圖。

在 iOS 上，具有單個項目的 ScrollView 可用於允許用戶縮放內容。設置 `maximumZoomScale` 和 `minimumZoomScale` 屬性，用戶將能夠使用捏合和展開手勢來放大和縮小。

ScrollView 最適合呈現數量有限且大小有限的內容。`ScrollView` 的所有元素和視圖都會被渲染，即使它們當前未顯示在屏幕上。如果您有一個無法在屏幕上顯示的長列表，則應改用 `FlatList`。接下來讓我們[學習列表視圖](using-a-listview.md)。