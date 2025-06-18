---
id: tutorial
title: Learn the Basics
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

React Native 類似 React，但它使用原生元件而非網頁元件作為建構基礎。因此要理解 React Native 應用的基本結構，您需要掌握一些 React 核心概念，如 JSX、元件、`state` 和 `props`。即使您已熟悉 React，仍需學習 React Native 特有的知識，例如原生元件。本教程適合所有讀者，無論您是否有 React 經驗。

讓我們開始吧。

## Hello World

遵循程式設計的古老傳統，我們首先要建立一個只顯示「Hello, world!」的應用程式。範例如下：

```SnackPlayer name=Hello%20World
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

若您感到好奇，可以直接在網頁模擬器中試玩這段範例程式碼。也可以將其貼到本機的 `App.js` 檔案中來建立實際應用。

## 這段程式碼做了什麼？

1. 首先需要導入 `React` 才能使用 `JSX`，這些語法之後會被轉換為各平台的原生元件
2. 第二行從 `react-native` 導入 `Text` 和 `View` 元件

接著定義的 `HelloWorldApp` 函式是一個[函式型元件](https://reactjs.org/docs/components-and-props.html#function-and-class-components)，其行為與網頁版 React 完全相同。該函式返回帶有樣式的 `View` 元件及其子元件 `Text`。

`Text` 元件用於渲染文字，而 `View` 元件渲染容器。這個容器應用了多種樣式，讓我們分析每個樣式的作用。

第一個樣式是 `flex: 1`，[`flex`](layout-props#flex) 屬性決定子元件如何沿主軸「填滿」可用空間。由於只有單一容器，它會佔滿父元件的所有空間。在此例中它是唯一元件，因此會佔據整個螢幕空間。

接下來的樣式是 [`justifyContent`](layout-props#justifycontent): "center"，這會將容器子元件沿主軸居中對齊。最後的 [`alignItems`](layout-props#alignitems): "center" 則讓子元件沿交叉軸居中對齊。

其中某些語法可能看起來不像 JavaScript。別擔心，_這是未來趨勢_。

首先，ES2015（又稱 ES6）是 JavaScript 的改進功能集，現已成為官方標準，但尚未被所有瀏覽器支援，因此在網頁開發中還不普及。React Native 內建支援 ES2015，所以您可以放心使用這些功能。上例中的 `import`、`export`、`const` 和 `from` 都是 ES2015 特性。如果您不熟悉 ES2015，可以透過閱讀本教程的範例程式碼來學習。[這個頁面](https://babeljs.io/learn-es2015/)提供了 ES2015 功能的完整概述。

程式碼中另一個特殊之處是 `<View><Text>Hello world!</Text></View>`。這是 JSX 語法——允許在 JavaScript 中嵌入 XML 的語法。多數框架使用專屬模板語言來在標記語言中嵌入程式碼，而 React 反其道而行。JSX 讓您能在程式碼中編寫標記語言，看起來類似網頁的 HTML，但您使用的是 React 元件而非 `<div>` 或 `<span>` 等網頁元素。此處的 `<Text>` 是[核心元件](intro-react-native-components)用於顯示文字，`View` 則類似 `<div>` 或 `<span>`。

## 元件

這段程式碼定義了一個新的「元件」`HelloWorldApp`。當你開發 React Native 應用程式時，會經常創建新元件。螢幕上看到的任何內容都是某種元件。

## Props 屬性

大多數元件在創建時可透過不同參數進行定制，這些創建參數稱為 props（屬性）。

自定義元件同樣能使用 `props`。這讓你可以創建單一元件並在應用多處使用，每處只需微調不同屬性。在函式元件中透過 `props.YOUR_PROP_NAME` 存取，類別元件則用 `this.props.YOUR_PROP_NAME`。範例如下：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Hello%20Props&ext=js
import React from 'react';
import {Text, View, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  center: {
    alignItems: 'center',
  },
});

const Greeting = props => {
  return (
    <View style={styles.center}>
      <Text>Hello {props.name}!</Text>
    </View>
  );
};

const LotsOfGreetings = () => {
  return (
    <View style={[styles.center, {top: 50}]}>
      <Greeting name="Rexxar" />
      <Greeting name="Jaina" />
      <Greeting name="Valeera" />
    </View>
  );
};

export default LotsOfGreetings;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Hello%20Props&ext=tsx
import React from 'react';
import {Text, View, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  center: {
    alignItems: 'center',
  },
});

type GreetingProps = {
  name: string;
};

const Greeting = (props: GreetingProps) => {
  return (
    <View style={styles.center}>
      <Text>Hello {props.name}!</Text>
    </View>
  );
};

const LotsOfGreetings = () => {
  return (
    <View style={[styles.center, {top: 50}]}>
      <Greeting name="Rexxar" />
      <Greeting name="Jaina" />
      <Greeting name="Valeera" />
    </View>
  );
};

export default LotsOfGreetings;
```

</TabItem>
</Tabs>

透過 `name` 屬性定制 `Greeting` 元件，讓我們能重複使用該元件來顯示不同問候語。此範例同時展示如何在 JSX 中使用 `Greeting` 元件，這正是 React 的強大之處。

此處另一個新概念是 [`View`](view.md) 元件。[`View`](view.md) 作為其他元件的容器，能有效控制樣式與佈局。

結合 `props` 與基礎的 [`Text`](text.md)、[`Image`](image.md)、[`View`](view.md) 元件，你已能建構各種靜態頁面。若想讓應用動態變化，請繼續學習 [State 狀態](#state)。

## State 狀態

與[唯讀的 props](https://reactjs.org/docs/components-and-props.html#props-are-read-only) 不同，`state` 允許 React 元件因應使用者操作、網路回應等情況動態改變輸出內容。

#### React 中 state 與 props 的差異為何？

在 React 元件中，props 是從父元件傳遞給子元件的變數；而 state 同樣是變數，關鍵差異在於 state 由元件自身初始化並管理，不需透過參數傳遞。

#### React 與 React Native 處理 state 的方式有差異嗎？

<div className="two-columns">

```tsx
// ReactJS Counter Example using Hooks!

import React, {useState} from 'react';



const App = () => {
  const [count, setCount] = useState(0);

  return (
    <div className="container">
      <p>You clicked {count} times</p>
      <button
        onClick={() => setCount(count + 1)}>
        Click me!
      </button>
    </div>
  );
};


// CSS
.container {
  display: flex;
  justify-content: center;
  align-items: center;
}

```

```tsx
// React Native Counter Example using Hooks!

import React, {useState} from 'react';
import {View, Text, Button, StyleSheet} from 'react-native';

const App = () => {
  const [count, setCount] = useState(0);

  return (
    <View style={styles.container}>
      <Text>You clicked {count} times</Text>
      <Button
        onPress={() => setCount(count + 1)}
        title="Click me!"
      />
    </View>
  );
};

// React Native Styles
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```

</div>

如上所述，[React](https://reactjs.org/docs/state-and-lifecycle.html) 與 React Native 在處理 `state` 上完全一致。無論是類別元件或使用 [hooks](https://reactjs.org/docs/hooks-intro.html) 的函式元件，都能運用元件狀態！

以下範例將用類別元件實現相同的計數器功能：

```SnackPlayer name=Hello%20Classes
import React, {Component} from 'react';
import {StyleSheet, TouchableOpacity, Text, View} from 'react-native';

class App extends Component {
  state = {
    count: 0,
  };

  onPress = () => {
    this.setState({
      count: this.state.count + 1,
    });
  };

  render() {
    return (
      <View style={styles.container}>
        <TouchableOpacity style={styles.button} onPress={this.onPress}>
          <Text>Click me</Text>
        </TouchableOpacity>
        <View>
          <Text>You clicked {this.state.count} times</Text>
        </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  button: {
    alignItems: 'center',
    backgroundColor: '#DDDDDD',
    padding: 10,
    marginBottom: 10,
  },
});

export default App;
```