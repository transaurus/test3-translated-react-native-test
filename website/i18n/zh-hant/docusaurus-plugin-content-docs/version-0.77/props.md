---
id: props
title: Props
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

大多數元件在創建時可以通過不同參數進行自定義。這些創建參數稱為`props`（屬性的簡稱）。

例如，React Native 的基本元件之一是`Image`。創建圖片時，您可以使用名為`source`的 prop 來控制顯示的圖片。

```SnackPlayer name=Props
import React from 'react';
import {Image} from 'react-native';

const Bananas = () => {
  let pic = {
    uri: 'https://upload.wikimedia.org/wikipedia/commons/d/de/Bananavarieties.jpg',
  };
  return (
    <Image source={pic} style={{width: 193, height: 110, marginTop: 50}} />
  );
};

export default Bananas;
```

注意圍繞`{pic}`的大括號——這些將變量`pic`嵌入到 JSX 中。您可以在 JSX 的大括號內放入任何 JavaScript 表達式。

您自己的元件也可以使用`props`。這讓您可以創建一個在應用程式中多處使用的元件，通過在`render`函數中引用`props`來在每個地方稍微改變屬性。這裡有一個例子：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Props&ext=js
import React from 'react';
import {Text, View} from 'react-native';

const Greeting = props => {
  return (
    <View style={{alignItems: 'center'}}>
      <Text>Hello {props.name}!</Text>
    </View>
  );
};

const LotsOfGreetings = () => {
  return (
    <View style={{alignItems: 'center', top: 50}}>
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

```SnackPlayer name=Props&ext=tsx
import React from 'react';
import {Text, View} from 'react-native';

type GreetingProps = {
  name: string;
};

const Greeting = (props: GreetingProps) => {
  return (
    <View style={{alignItems: 'center'}}>
      <Text>Hello {props.name}!</Text>
    </View>
  );
};

const LotsOfGreetings = () => {
  return (
    <View style={{alignItems: 'center', top: 50}}>
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

使用`name`作為 prop 讓我們可以自定義`Greeting`元件，因此我們可以為每個問候語重用該元件。此示例還在 JSX 中使用`Greeting`元件，類似於[核心元件](intro-react-native-components)。能夠這樣做是 React 如此酷的原因——如果您發現自己希望有一組不同的 UI 原語來使用，您可以發明新的原語。

這裡的另一個新東西是[`View`](view.md)元件。[`View`](view.md)作為其他元件的容器非常有用，可以幫助控制樣式和佈局。

通過`props`和基本的[`Text`](text.md)、[`Image`](image.md)和[`View`](view.md)元件，您可以構建各種靜態屏幕。要學習如何讓您的應用程式隨時間變化，您需要[學習狀態](state.md)。