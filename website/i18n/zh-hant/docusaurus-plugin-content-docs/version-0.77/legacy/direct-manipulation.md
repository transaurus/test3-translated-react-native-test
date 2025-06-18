---
id: direct-manipulation
title: Direct Manipulation
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

有時需要直接修改元件而不透過 state/props 觸發整個子樹重新渲染。例如在瀏覽器中使用 React 時，有時需直接操作 DOM 節點，移動應用中的視圖亦是如此。`setNativeProps` 在 React Native 中等同於直接設置 DOM 節點的屬性。

:::caution
僅在頻繁重新渲染導致效能瓶頸時使用 `setNativeProps`！

直接操作不應作為常用工具。通常僅在創建連續動畫以避免渲染元件層級和協調大量視圖的開銷時使用。
`setNativeProps` 是命令式的，它將狀態儲存在原生層（DOM、UIView 等）而非 React 元件中，這會使程式碼更難維護。

使用前，請先嘗試用 `setState` 和 [`shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#shouldcomponentupdate-in-action) 解決問題。
:::

## 搭配 TouchableOpacity 使用 setNativeProps

[TouchableOpacity](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/Touchable/TouchableOpacity.js) 內部使用 `setNativeProps` 來更新子元件的不透明度：

```tsx
const viewRef = useRef<View>();
const setOpacityTo = useCallback(value => {
  // Redacted: animation related code
  viewRef.current.setNativeProps({
    opacity: value,
  });
}, []);
```

這使我們能編寫以下程式碼，並確保子元件會響應點擊更新不透明度，而無需子元件知曉此邏輯或修改其實作：

```tsx
<TouchableOpacity onPress={handlePress}>
  <View>
    <Text>Press me!</Text>
  </View>
</TouchableOpacity>
```

假設 `setNativeProps` 不可用。我們可能透過將不透明度值儲存在 state 中，並在 `onPress` 觸發時更新該值來實現：

```tsx
const [buttonOpacity, setButtonOpacity] = useState(1);
return (
  <TouchableOpacity
    onPressIn={() => setButtonOpacity(0.5)}
    onPressOut={() => setButtonOpacity(1)}>
    <View style={{opacity: buttonOpacity}}>
      <Text>Press me!</Text>
    </View>
  </TouchableOpacity>
);
```

與原始範例相比，這種方式計算量較大——即使視圖及其子元件的其他屬性未改變，React 仍需在每次不透明度變化時重新渲染元件層級。通常這種開銷無需擔心，但在執行連續動畫和響應手勢時，謹慎優化元件可提高動畫的流暢度。

若查看 [NativeMethodsMixin](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Renderer/implementations/ReactNativeRenderer-prod.js) 中 `setNativeProps` 的實作，會發現它實際是 `RCTUIManager.updateView` 的封裝——這與重新渲染觸發的函數調用完全相同（參見 [ReactNativeBaseComponent 中的 receiveComponent](https://github.com/facebook/react-native/blob/fb2ec1ea47c53c2e7b873acb1cb46192ac74274e/Libraries/Renderer/oss/ReactNativeRenderer-prod.js#L5793-L5813)）。

## 複合元件與 setNativeProps

複合元件沒有對應的原生視圖，因此無法對其調用 `setNativeProps`。參考以下範例：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=setNativeProps%20with%20Composite%20Components&ext=js
import React from 'react';
import {Text, TouchableOpacity, View} from 'react-native';

const MyButton = props => (
  <View style={{marginTop: 50}}>
    <Text>{props.label}</Text>
  </View>
);

const App = () => (
  <TouchableOpacity>
    <MyButton label="Press me!" />
  </TouchableOpacity>
);

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=setNativeProps%20with%20Composite%20Components&ext=tsx
import React from 'react';
import {Text, TouchableOpacity, View} from 'react-native';

const MyButton = (props: {label: string}) => (
  <View style={{marginTop: 50}}>
    <Text>{props.label}</Text>
  </View>
);

const App = () => (
  <TouchableOpacity>
    <MyButton label="Press me!" />
  </TouchableOpacity>
);

export default App;
```

</TabItem>
</Tabs>

運行時會立即看到錯誤：`Touchable 子元件必須是原生元件或將 setNativeProps 轉發給原生元件`。這是因為 `MyButton` 並非直接由可設置不透明度的原生視圖支持。可以這樣理解：若用 `createReactClass` 定義元件，你不會期望直接設置其 style prop 能生效——除非包裝了原生元件，否則需將 style prop 傳遞給子元件。同理，我們需將 `setNativeProps` 轉發給原生支持的子元件。

#### 將 setNativeProps 轉發給子元件

由於 `setNativeProps` 方法存在於任何 `View` 元件的 ref 上，只需將自訂元件的 ref 轉發給其渲染的某個 `<View />` 元件即可。這意味著對自訂元件調用 `setNativeProps` 的效果等同於直接對包裝的 `View` 元件調用該方法。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Forwarding%20setNativeProps&ext=js
import React from 'react';
import {Text, TouchableOpacity, View} from 'react-native';

const MyButton = React.forwardRef((props, ref) => (
  <View {...props} ref={ref} style={{marginTop: 50}}>
    <Text>{props.label}</Text>
  </View>
));

const App = () => (
  <TouchableOpacity>
    <MyButton label="Press me!" />
  </TouchableOpacity>
);

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Forwarding%20setNativeProps&ext=tsx
import React from 'react';
import {Text, TouchableOpacity, View} from 'react-native';

const MyButton = React.forwardRef<View, {label: string}>((props, ref) => (
  <View {...props} ref={ref} style={{marginTop: 50}}>
    <Text>{props.label}</Text>
  </View>
));

const App = () => (
  <TouchableOpacity>
    <MyButton label="Press me!" />
  </TouchableOpacity>
);

export default App;
```

</TabItem>
</Tabs>

現在你可以在 `TouchableOpacity` 中使用 `MyButton` 了！

你可能注意到我們使用 `{...props}` 將所有屬性傳遞給子視圖。原因是 `TouchableOpacity` 實際上是一個複合元件，因此除了依賴子元件的 `setNativeProps` 外，還需要子元件處理觸控事件。為此，它會傳遞[各種屬性](view.md#onmoveshouldsetresponder)回調到 `TouchableOpacity` 元件。相比之下，`TouchableHighlight` 由原生視圖支持，僅需要我們實現 `setNativeProps`。

## 使用 setNativeProps 編輯 TextInput 值

另一個常見的 `setNativeProps` 使用情境是編輯 TextInput 的值。當 `bufferDelay` 較低且用戶輸入速度極快時，TextInput 的 `controlled` 屬性有時會丟失字符。部分開發者傾向完全跳過此屬性，改在必要時直接使用 `setNativeProps` 操作 TextInput 值。例如，以下代碼示範點擊按鈕時編輯輸入框：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Clear%20text&ext=js
import React from 'react';
import {useCallback, useRef} from 'react';
import {
  StyleSheet,
  TextInput,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';

const App = () => {
  const inputRef = useRef(null);
  const editText = useCallback(() => {
    inputRef.current.setNativeProps({text: 'Edited Text'});
  }, []);

  return (
    <View style={styles.container}>
      <TextInput ref={inputRef} style={styles.input} />
      <TouchableOpacity onPress={editText}>
        <Text>Edit text</Text>
      </TouchableOpacity>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  input: {
    height: 50,
    width: 200,
    marginHorizontal: 20,
    borderWidth: 1,
    borderColor: '#ccc',
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Clear%20text&ext=tsx
import React from 'react';
import {useCallback, useRef} from 'react';
import {
  StyleSheet,
  TextInput,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';

const App = () => {
  const inputRef = useRef<TextInput>(null);
  const editText = useCallback(() => {
    inputRef.current?.setNativeProps({text: 'Edited Text'});
  }, []);

  return (
    <View style={styles.container}>
      <TextInput ref={inputRef} style={styles.input} />
      <TouchableOpacity onPress={editText}>
        <Text>Edit text</Text>
      </TouchableOpacity>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  input: {
    height: 50,
    width: 200,
    marginHorizontal: 20,
    borderWidth: 1,
    borderColor: '#ccc',
  },
});

export default App;
```

</TabItem>
</Tabs>

可使用 [`clear`](../textinput#clear) 方法清除 `TextInput`，該方法採用相同方式清空當前輸入文字。

## 避免與渲染函數衝突

若更新一個同時由渲染函數管理的屬性，可能導致不可預測的錯誤，因為每當元件重新渲染且該屬性變化時，先前透過 `setNativeProps` 設定的值會被完全忽略並覆寫。

## setNativeProps 與 shouldComponentUpdate

透過[智能應用 `shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#avoid-reconciliation)，可避免協調未變更元件子樹的額外開銷，甚至可能使 `setState` 的性能足夠取代 `setNativeProps`。

## 其他原生方法

此處描述的方法適用於 React Native 提供的大多數預設元件。但需注意，這些方法_不適用_於非直接由原生視圖支持的複合元件，這通常包含你在應用中自訂的多數元件。

### measure(callback)

測量指定視圖在螢幕上的位置、寬度和高度，並透過異步回調返回數值。成功時回調參數包含：

- x
- y
- width
- height
- pageX
- pageY

注意這些測量值需等待原生端渲染完成後才能取得。若需盡快獲取測量值且不需要 `pageX` 和 `pageY`，建議改用 [`onLayout`](view.md#onlayout) 屬性。

此外，`measure()` 返回的寬高是元件在視窗中的尺寸。若需要元件的實際尺寸，建議改用 [`onLayout`](view.md#onlayout) 屬性。

### measureInWindow(callback)

測量指定視圖在視窗中的位置，並透過異步回調返回數值。若 React 根視圖嵌入其他原生視圖中，此方法將提供絕對座標。成功時回調參數包含：

- x
- y
- width
- height

### measureLayout(relativeToNativeComponentRef, onSuccess, onFail)

類似 `measure()`，但測量相對於祖先視圖（透過 `relativeToNativeComponentRef` 參照指定）的座標。返回的座標是相對於祖先視圖原點 `x`, `y` 的相對值。

:::note
此方法也可接受 `relativeToNativeNode` 處理程序（替代參照）呼叫，但此變體在新架構中已過時。
:::

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=measureLayout%20example&supportedPlatforms=android,ios&ext=js
import React, {useEffect, useRef, useState} from 'react';
import {Text, View, StyleSheet} from 'react-native';

const App = () => {
  const textContainerRef = useRef(null);
  const textRef = useRef(null);
  const [measure, setMeasure] = useState(null);

  useEffect(() => {
    if (textRef.current && textContainerRef.current) {
      textRef.current.measureLayout(
        textContainerRef.current,
        (left, top, width, height) => {
          setMeasure({left, top, width, height});
        },
      );
    }
  }, [measure]);

  return (
    <View style={styles.container}>
      <View ref={textContainerRef} style={styles.textContainer}>
        <Text ref={textRef}>Where am I? (relative to the text container)</Text>
      </View>
      <Text style={styles.measure}>{JSON.stringify(measure)}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  textContainer: {
    backgroundColor: '#61dafb',
    justifyContent: 'center',
    alignItems: 'center',
    padding: 12,
  },
  measure: {
    textAlign: 'center',
    padding: 12,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=measureLayout%20example&ext=tsx
import React, {useEffect, useRef, useState} from 'react';
import {Text, View, StyleSheet} from 'react-native';

type Measurements = {
  left: number;
  top: number;
  width: number;
  height: number;
};

const App = () => {
  const textContainerRef = useRef<View>(null);
  const textRef = useRef<Text>(null);
  const [measure, setMeasure] = useState<Measurements | null>(null);

  useEffect(() => {
    if (textRef.current && textContainerRef.current) {
      textRef.current?.measureLayout(
        textContainerRef.current,
        (left, top, width, height) => {
          setMeasure({left, top, width, height});
        },
        () => {
          console.error('measurement failed');
        },
      );
    }
  }, [measure]);

  return (
    <View style={styles.container}>
      <View ref={textContainerRef} style={styles.textContainer}>
        <Text ref={textRef}>Where am I? (relative to the text container)</Text>
      </View>
      <Text style={styles.measure}>{JSON.stringify(measure)}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  textContainer: {
    backgroundColor: '#61dafb',
    justifyContent: 'center',
    alignItems: 'center',
    padding: 12,
  },
  measure: {
    textAlign: 'center',
    padding: 12,
  },
});

export default App;
```

</TabItem>
</Tabs>

### focus()

請求將焦點設定於指定的輸入框或視圖上。觸發的具體行為會根據平台和視圖類型而有所不同。

### blur()

移除輸入框或視圖的焦點。此為 `focus()` 的反向操作。