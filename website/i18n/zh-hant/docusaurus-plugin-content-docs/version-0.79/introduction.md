---
id: getting-started
title: Introduction
description: This helpful guide lays out the prerequisites for learning React Native, using these docs, and setting up your environment.
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<div className="content-banner">
  Welcome to the very start of your React Native journey! If you're looking for getting started instructions, they've moved to <a href="environment-setup">their own section</a>. Continue reading for an introduction to the documentation, Native Components, React, and more!
  <img className="content-banner-img" src="/docs/assets/p_android-ios-devices.svg" alt=" " />
</div>

許多不同背景的人都在使用 React Native：從資深的 iOS 開發者到 React 初學者，甚至是職業生涯中首次接觸程式設計的人。這些文件是為所有學習者而寫，無論其經驗水平或背景如何。

## 如何使用這些文件

你可以從這裡開始，像閱讀一本書一樣線性閱讀這些文件；或者直接閱讀你需要的特定章節。已經熟悉 React？你可以跳過[該章節](intro-react)——或閱讀它作為簡單的複習。

## 先備知識

要使用 React Native，你需要理解 JavaScript 的基礎知識。如果你是 JavaScript 新手或需要複習，可以在 Mozilla Developer Network [深入學習](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 或 [複習](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)。

> 雖然我們盡量假設讀者不具備 React、Android 或 iOS 開發的知識，但這些主題對於有志成為 React Native 開發者的人來說是值得學習的。在適當的地方，我們提供了更深入的資源和文章連結。

## 互動式範例

這篇介紹讓你可以立即在瀏覽器中透過互動式範例開始學習，例如以下這個：

```SnackPlayer name=Hello%20World
import React from 'react';
import {Text, View} from 'react-native';

const YourApp = () => {
  return (
    <View
      style={{
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
      }}>
      <Text>Try editing me! 🎉</Text>
    </View>
  );
};

export default YourApp;
```

上面是一個 Snack Player。它是由 Expo 創建的一個方便工具，用於嵌入和運行 React Native 專案，並展示它們在 Android 和 iOS 等平台上的渲染效果。程式碼是即時且可編輯的，因此你可以直接在瀏覽器中操作它。試著將上面的「Try editing me!」文字改為「Hello, world!」吧！

> 如果你想設定本地開發環境，可以[按照我們的指南在本地機器上設定環境](set-up-your-environment)，並將程式碼範例貼到你的專案中。（如果你是網頁開發者，可能已經設定好本地環境來測試行動瀏覽器！）

## 開發者注意事項

來自不同開發背景的人都在學習 React Native。你可能具備從網頁到 Android、iOS 等多種技術的經驗。我們嘗試為所有背景的開發者撰寫內容。有時我們會提供特定平台的說明，例如：

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["android","ios","web"])}>

<TabItem value="android">

> Android developers may be familiar with this concept.

</TabItem>
<TabItem value="ios">

> iOS developers may be familiar with this concept.

</TabItem>
<TabItem value="web">

> Web developers may be familiar with this concept.

</TabItem>
</Tabs>

## 格式說明

選單路徑以粗體顯示，並使用插入符號導航子選單。例如：**Android Studio > Preferences**

---

現在你已經了解本指南的使用方式，是時候認識 React Native 的基礎：[原生元件](intro-react-native-components.md)了。