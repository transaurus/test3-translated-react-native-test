---
id: intro-react
title: React Fundamentals
description: To understand React Native fully, you need a solid foundation in React. This short introduction to React can help you get started or get refreshed.
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

React Native 基於 [React](https://react.dev/) 運行，這是一個使用 JavaScript 構建用戶界面的熱門開源庫。要充分掌握 React Native，理解 React 本身至關重要。本節內容可作為入門指南或複習課程。

我們將涵蓋 React 的核心概念：

- 組件 (components)
- JSX 語法
- 屬性 (props)
- 狀態 (state)

若需深入學習，建議查閱 [React 官方文檔](https://react.dev/learn)。

## 第一個組件

本篇 React 入門指南將以貓咪為例：這些友好親切的生物需要名字和工作的咖啡館。以下是你的第一個 Cat 組件：

```SnackPlayer name=Your%20Cat
import React from 'react';
import {Text} from 'react-native';

const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};

export default Cat;
```

具體操作如下：要定義 `Cat` 組件，首先使用 JavaScript 的 [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 語法導入 React 和 React Native 的 [`Text`](/docs/next/text) 核心組件：

```tsx
import React from 'react';
import {Text} from 'react-native';
```

組件以函數形式開始：

```tsx
const Cat = () => {};
```

可將組件視為藍圖。函數組件返回的內容會被渲染為 **React 元素**，這些元素用於描述屏幕上顯示的內容。

此處 `Cat` 組件將渲染一個 `<Text>` 元素：

```tsx
const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};
```

可以使用 JavaScript 的 [`export default`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) 導出函數組件，以便在整個應用中使用：

```tsx
const Cat = () => {
  return <Text>Hello, I am your cat!</Text>;
};

export default Cat;
```

> 這是導出組件的多種方式之一。此類導出方式與 Snack Player 兼容良好。但根據應用文件結構的不同，可能需要採用其他約定方式。[JavaScript 導入導出速查表](https://medium.com/dailyjs/javascript-module-cheatsheet-7bd474f1d829)可提供幫助。

請仔細觀察這個 `return` 語句。`<Text>Hello, I am your cat!</Text>` 使用了某種簡化元素書寫的 JavaScript 語法：JSX。

## JSX 語法

React 和 React Native 使用 **JSX**，這種語法允許在 JavaScript 中直接編寫元素：`<Text>Hello, I am your cat!</Text>`。React 文檔提供 [完整的 JSX 指南](https://react.dev/learn/writing-markup-with-jsx)可供深入學習。由於 JSX 本質是 JavaScript，因此可以在其中使用變量。此處聲明了貓咪名字 `name`，並用花括號將其嵌入 `<Text>`。

```SnackPlayer name=Curly%20Braces
import React from 'react';
import {Text} from 'react-native';

const Cat = () => {
  const name = 'Maru';
  return <Text>Hello, I am {name}!</Text>;
};

export default Cat;
```

花括號內可包含任何 JavaScript 表達式，包括函數調用如 `{getFullName("Rum", "Tum", "Tugger")}`：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Curly%20Braces&ext=js
import React from 'react';
import {Text} from 'react-native';

const getFullName = (firstName, secondName, thirdName) => {
  return firstName + ' ' + secondName + ' ' + thirdName;
};

const Cat = () => {
  return <Text>Hello, I am {getFullName('Rum', 'Tum', 'Tugger')}!</Text>;
};

export default Cat;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Curly%20Braces&ext=tsx
import React from 'react';
import {Text} from 'react-native';

const getFullName = (
  firstName: string,
  secondName: string,
  thirdName: string,
) => {
  return firstName + ' ' + secondName + ' ' + thirdName;
};

const Cat = () => {
  return <Text>Hello, I am {getFullName('Rum', 'Tum', 'Tugger')}!</Text>;
};

export default Cat;
```

</TabItem>
</Tabs>

可將花括號視為通往 JSX 中 JavaScript 功能的傳送門！

> 由於 JSX 包含在 React 庫中，若文件頂部沒有 `import React from 'react'` 導入語句，JSX 將無法工作！

## 自定義組件

你已接觸過 [React Native 核心組件](intro-react-native-components)。React 允許將這些組件相互嵌套以創建新組件，這種可嵌套、可複用的組件正是 React 範式的核心。

例如，可以將 [`Text`](text) 和 [`TextInput`](textinput) 嵌套在 [`View`](view) 組件內，React Native 會將它們一併渲染：

```SnackPlayer name=Custom%20Components
import React from 'react';
import {Text, TextInput, View} from 'react-native';

const Cat = () => {
  return (
    <View>
      <Text>Hello, I am...</Text>
      <TextInput
        style={{
          height: 40,
          borderColor: 'gray',
          borderWidth: 1,
        }}
        defaultValue="Name me!"
      />
    </View>
  );
};

export default Cat;
```

#### 開發者須知

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["android", "web"])}>

<TabItem value="web">

> If you’re familiar with web development, `<View>` and `<Text>` might remind you of HTML! You can think of them as the `<div>` and `<p>` tags of application development.

</TabItem>
<TabItem value="android">

> On Android, you usually put your views inside `LinearLayout`, `FrameLayout`, `RelativeLayout`, etc. to define how the view’s children will be arranged on the screen. In React Native, `View` uses Flexbox for its children’s layout. You can learn more in [our guide to layout with Flexbox](flexbox).

</TabItem>
</Tabs>

你可以透過使用 `<Cat>` 來多次渲染這個元件，且無需重複程式碼：

```SnackPlayer name=Multiple%20Components
import React from 'react';
import {Text, View} from 'react-native';

const Cat = () => {
  return (
    <View>
      <Text>I am also a cat!</Text>
    </View>
  );
};

const Cafe = () => {
  return (
    <View>
      <Text>Welcome!</Text>
      <Cat />
      <Cat />
      <Cat />
    </View>
  );
};

export default Cafe;
```

任何渲染其他元件的元件都稱為**父元件**。在此範例中，`Cafe` 是父元件，而每個 `Cat` 則是**子元件**。

你可以在咖啡館中放入任意數量的貓咪。每個 `<Cat>` 都會渲染一個獨特的元素——你可以透過 props 來客製化它們。

## Props

**Props** 是「屬性」(properties) 的簡稱。Props 讓你能夠客製化 React 元件。例如，這裡你為每個 `<Cat>` 傳遞不同的 `name`，讓 `Cat` 渲染時使用：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Multiple%20Props&ext=js
import React from 'react';
import {Text, View} from 'react-native';

const Cat = props => {
  return (
    <View>
      <Text>Hello, I am {props.name}!</Text>
    </View>
  );
};

const Cafe = () => {
  return (
    <View>
      <Cat name="Maru" />
      <Cat name="Jellylorum" />
      <Cat name="Spot" />
    </View>
  );
};

export default Cafe;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Multiple%20Props&ext=tsx
import React from 'react';
import {Text, View} from 'react-native';

type CatProps = {
  name: string;
};

const Cat = (props: CatProps) => {
  return (
    <View>
      <Text>Hello, I am {props.name}!</Text>
    </View>
  );
};

const Cafe = () => {
  return (
    <View>
      <Cat name="Maru" />
      <Cat name="Jellylorum" />
      <Cat name="Spot" />
    </View>
  );
};

export default Cafe;
```

</TabItem>
</Tabs>

React Native 的大多數核心元件也可以透過 props 來客製化。舉例來說，使用 [`Image`](image) 時，你可以傳遞一個名為 [`source`](image#source) 的 prop 來定義它顯示的圖片：

```SnackPlayer name=Props
import React from 'react';
import {Text, View, Image} from 'react-native';

const CatApp = () => {
  return (
    <View>
      <Image
        source={{
          uri: 'https://reactnative.dev/docs/assets/p_cat1.png',
        }}
        style={{width: 200, height: 200}}
      />
      <Text>Hello, I am your cat!</Text>
    </View>
  );
};

export default CatApp;
```

`Image` 有[許多不同的 props](image#props)，包括 [`style`](image#style)，它接受一個包含設計和佈局相關屬性值對的 JS 物件。

> 注意 `style` 的寬度和高度周圍的雙層大括號 `{{ }}`。在 JSX 中，JavaScript 值是用 `{}` 來引用的。這在傳遞非字串的 props 時非常方便，例如陣列或數字：`<Cat food={["魚", "乾糧"]} age={2} />`。然而，JS 物件**同樣**使用大括號表示：`{width: 200, height: 200}`。因此，要在 JSX 中傳遞一個 JS 物件，你必須用**另一對**大括號將其包裹起來：`{{width: 200, height: 200}}`

你可以透過 props 和核心元件 [`Text`](text)、[`Image`](image) 和 [`View`](view) 來建構許多東西！但要建立互動式的內容，你需要狀態。

## State

你可以將 props 視為用來配置元件渲染方式的參數，而**state**則像是元件的個人資料儲存空間。state 非常適合用來處理隨時間變化的資料或來自使用者互動的資料。state 賦予了你的元件記憶能力！

> 一般來說，當元件渲染時，使用 props 來配置它。使用 state 來追蹤任何你預期會隨時間變化的元件資料。

以下範例發生在一家貓咪咖啡館中，兩隻飢餓的貓咪正等待被餵食。牠們的飢餓狀態（我們預期會隨時間變化，與牠們的名字不同）被儲存為 state。要餵食貓咪，請按下牠們的按鈕——這將更新牠們的 state。

你可以透過呼叫 [React 的 `useState` Hook](https://react.dev/learn/state-a-components-memory) 來為元件添加 state。Hook 是一種函數，讓你能夠「鉤入」React 的功能。例如，`useState` 是一個 Hook，讓你能夠為函數元件添加 state。你可以在 [React 文件](https://react.dev/reference/react) 中了解更多關於其他種類的 Hook。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=State&ext=js
import React, {useState} from 'react';
import {Button, Text, View} from 'react-native';

const Cat = props => {
  const [isHungry, setIsHungry] = useState(true);

  return (
    <View>
      <Text>
        I am {props.name}, and I am {isHungry ? 'hungry' : 'full'}!
      </Text>
      <Button
        onPress={() => {
          setIsHungry(false);
        }}
        disabled={!isHungry}
        title={isHungry ? 'Give me some food, please!' : 'Thank you!'}
      />
    </View>
  );
};

const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};

export default Cafe;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=State&ext=tsx
import React, {useState} from 'react';
import {Button, Text, View} from 'react-native';

type CatProps = {
  name: string;
};

const Cat = (props: CatProps) => {
  const [isHungry, setIsHungry] = useState(true);

  return (
    <View>
      <Text>
        I am {props.name}, and I am {isHungry ? 'hungry' : 'full'}!
      </Text>
      <Button
        onPress={() => {
          setIsHungry(false);
        }}
        disabled={!isHungry}
        title={isHungry ? 'Give me some food, please!' : 'Thank you!'}
      />
    </View>
  );
};

const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};

export default Cafe;
```

</TabItem>
</Tabs>

首先，你需要從 React 中導入 `useState`：

```tsx
import React, {useState} from 'react';
```

然後，在元件的函數內部呼叫 `useState` 來宣告元件的 state。在此範例中，`useState` 創建了一個 `isHungry` 狀態變數：

```tsx
const Cat = (props: CatProps) => {
  const [isHungry, setIsHungry] = useState(true);
  // ...
};
```

> 你可以使用 `useState` 來追蹤任何類型的資料：字串、數字、布林值、陣列、物件。例如，你可以用 `const [timesPetted, setTimesPetted] = useState(0)` 來追蹤貓咪被撫摸的次數！

呼叫 `useState` 會做兩件事：

- 它創建一個具有初始值的「狀態變數」——在此範例中，狀態變數是 `isHungry`，其初始值為 `true`
- 它創建一個用來設定該狀態變數值的函數——`setIsHungry`

It doesn’t matter what names you use. But it can be handy to think of the pattern as `[<getter>, <setter>] = useState(<initialValue>)`.

Next you add the [`Button`](button) Core Component and give it an `onPress` prop:

```tsx
<Button
  onPress={() => {
    setIsHungry(false);
  }}
  //..
/>
```

Now, when someone presses the button, `onPress` will fire, calling the `setIsHungry(false)`. This sets the state variable `isHungry` to `false`. When `isHungry` is false, the `Button`’s `disabled` prop is set to `true` and its `title` also changes:

```tsx
<Button
  //..
  disabled={!isHungry}
  title={isHungry ? 'Give me some food, please!' : 'Thank you!'}
/>
```

> You might’ve noticed that although `isHungry` is a [const](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/const), it is seemingly reassignable! What is happening is when a state-setting function like `setIsHungry` is called, its component will re-render. In this case the `Cat` function will run again—and this time, `useState` will give us the next value of `isHungry`.

Finally, put your cats inside a `Cafe` component:

```tsx
const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};
```

> See the `<>` and `</>` above? These bits of JSX are [fragments](https://react.dev/reference/react/Fragment). Adjacent JSX elements must be wrapped in an enclosing tag. Fragments let you do that without nesting an extra, unnecessary wrapping element like `View`.

---

Now that you’ve covered both React and React Native’s Core Components, let’s dive deeper on some of these core components by looking at [handling `<TextInput>`](handling-text-input).