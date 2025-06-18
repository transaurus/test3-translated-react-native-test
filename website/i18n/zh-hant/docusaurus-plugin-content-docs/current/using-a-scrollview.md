---
id: using-a-scrollview
title: Using a ScrollView
---

[ScrollView](scrollview.md) 是一個通用的滾動容器，可包含多個組件和視圖。可滾動的項目可以是異構的，您既可以垂直滾動（預設），也可以水平滾動（通過設置 `horizontal` 屬性）。

此範例創建了一個垂直的 `ScrollView`，其中混合了圖片和文字內容。

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

可通過設置 `pagingEnabled` 屬性將 ScrollView 配置為允許使用滑動手勢在視圖間翻頁。在 Android 上也可使用 [ViewPager](https://github.com/react-native-community/react-native-viewpager) 組件實現水平滑動切換視圖。

在 iOS 上，單一項目的 ScrollView 可用於讓用戶縮放內容。設置 `maximumZoomScale` 和 `minimumZoomScale` 屬性後，用戶即可使用捏合手勢進行縮放操作。

ScrollView 最適合呈現數量有限且尺寸不大的內容。所有 `ScrollView` 的子元素和視圖都會被渲染，即使它們當前未顯示在畫面上。若需顯示超出螢幕範圍的長列表數據，應改用 `FlatList` 組件。接下來讓我們[學習列表視圖的使用方法](using-a-listview.md)。