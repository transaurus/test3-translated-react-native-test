---
id: getting-started
title: Introduction
description: This helpful guide lays out the prerequisites for learning React Native, using these docs, and setting up your environment.
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<div className="content-banner">
  Welcome to the very start of your React Native journey! If you're looking for environment setup instructions, they've moved to <a href="environment-setup">their own section</a>. Continue reading for an introduction to the documentation, Native Components, React, and more!
  <img className="content-banner-img" src="/docs/assets/p_android-ios-devices.svg" alt=" " />
</div>

React Native 的使用者背景多元：從資深 iOS 開發者到 React 初學者，乃至職業生涯中首次接觸程式設計的人。本文檔面向所有學習者撰寫，無論其經驗水平或背景如何。

## 如何使用本文檔

您可以從這裡開始，像閱讀書籍一樣線性瀏覽；或直接查閱特定章節。若已熟悉 React？可跳過[該章節](intro-react)或作為簡要複習。

## 前置知識

使用 React Native 需掌握 JavaScript 基礎知識。若需入門或複習，可透過 [Mozilla 開發者網絡](https://developer.mozilla.org/en-US/docs/Web/JavaScript)系統學習或[快速回顧](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)。

> 儘管我們盡量避免預設讀者具備 React、Android 或 iOS 開發經驗，但這些知識對 React Native 開發者極具價值。適當時，我們會提供深入資源連結。

## 互動式範例

本篇導覽提供可直接在瀏覽器中運行的互動範例，如下所示：

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

上方是 Snack Player——由 Expo 開發的便捷工具，用於嵌入和運行 React Native 專案，並展示其在 Android 和 iOS 平台的渲染效果。程式碼可即時編輯，您可直接在瀏覽器中操作。試著將「Try editing me!」文字改為「Hello, world!」吧！

> 若需設定本地開發環境，可[遵循本地環境設定指南](environment-setup)，並將程式碼範例貼至您的 `App.js` 檔案中。（網頁開發者可能已具備行動瀏覽器測試環境！）

## 函式元件與類別元件

React 中可使用類別或函式定義元件。最初僅類別元件能持有狀態，但自 React Hooks API 推出後，函式元件亦可管理狀態及其他特性。

[Hooks 於 React Native 0.59 版本引入](/blog/2019/03/12/releasing-react-native-059)。由於 Hooks 是未來主流的元件編寫方式，本文檔範例均採用函式元件。必要時，我們會透過切換按鈕展示類別元件寫法：

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Hello%20World%20Function%20Component
import React from 'react';
import {Text, View} from 'react-native';

const HelloWorldApp = () => {
  return (
    <View
      style={{
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
      }}>
      <Text>Hello, world!</Text>
    </View>
  );
};

export default HelloWorldApp;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Hello%20World%20Class%20Component
import React, {Component} from 'react';
import {Text, View} from 'react-native';

class HelloWorldApp extends Component {
  render() {
    return (
      <View
        style={{
          flex: 1,
          justifyContent: 'center',
          alignItems: 'center',
        }}>
        <Text>Hello, world!</Text>
      </View>
    );
  }
}

export default HelloWorldApp;
```

</TabItem>
</Tabs>

更多類別元件範例可查閱[歷史版本文件](/versions)。

## 開發者須知

React Native 學習者可能具備網頁、Android、iOS 等不同技術背景。我們盡量兼顧各類開發者需求，必要時會提供平台專屬說明如下：

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

## 格式規範

選單路徑以粗體標示，並用「>」符號表示子選單層級。範例：**Android Studio > Preferences**

---

了解本文檔結構後，接下來將深入 React Native 的核心基礎：[原生元件](intro-react-native-components.md)。