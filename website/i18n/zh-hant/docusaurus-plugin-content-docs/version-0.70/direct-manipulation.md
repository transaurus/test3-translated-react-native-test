---
id: direct-manipulation
title: Direct Manipulation
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

It is sometimes necessary to make changes directly to a component without using state/props to trigger a re-render of the entire subtree. When using React in the browser for example, you sometimes need to directly modify a DOM node, and the same is true for views in mobile apps. `setNativeProps` is the React Native equivalent to setting properties directly on a DOM node.

:::caution
Use `setNativeProps` when frequent re-rendering creates a performance bottleneck!

Direct manipulation will not be a tool that you reach for frequently. You will typically only be using it for creating continuous animations to avoid the overhead of rendering the component hierarchy and reconciling many views.
`setNativeProps` is imperative and stores state in the native layer (DOM, UIView, etc.) and not within your React components, which makes your code more difficult to reason about.

Before you use it, try to solve your problem with `setState` and [`shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#shouldcomponentupdate-in-action).
:::

## setNativeProps with TouchableOpacity

[TouchableOpacity](https://github.com/facebook/react-native/blob/0.70-stable/Libraries/Components/Touchable/TouchableOpacity.js) uses `setNativeProps` internally to update the opacity of its child component:

```jsx
const viewRef = useRef();
const setOpacityTo = useCallback(value => {
  // Redacted: animation related code
  viewRef.current.setNativeProps({
    opacity: value,
  });
}, []);
```

This allows us to write the following code and know that the child will have its opacity updated in response to taps, without the child having any knowledge of that fact or requiring any changes to its implementation:

```jsx
<TouchableOpacity onPress={handlePress}>
  <View>
    <Text>Press me!</Text>
  </View>
</TouchableOpacity>
```

Let's imagine that `setNativeProps` was not available. One way that we might implement it with that constraint is to store the opacity value in the state, then update that value whenever `onPress` is fired:

```jsx
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

This is computationally intensive compared to the original example - React needs to re-render the component hierarchy each time the opacity changes, even though other properties of the view and its children haven't changed. Usually this overhead isn't a concern but when performing continuous animations and responding to gestures, judiciously optimizing your components can improve your animations' fidelity.

If you look at the implementation of `setNativeProps` in [NativeMethodsMixin](https://github.com/facebook/react-native/blob/0.70-stable/Libraries/Renderer/implementations/ReactNativeRenderer-prod.js) you will notice that it is a wrapper around `RCTUIManager.updateView` - this is the exact same function call that results from re-rendering - see [receiveComponent in ReactNativeBaseComponent](https://github.com/facebook/react-native/blob/fb2ec1ea47c53c2e7b873acb1cb46192ac74274e/Libraries/Renderer/oss/ReactNativeRenderer-prod.js#L5793-L5813).

## Composite components and setNativeProps

Composite components are not backed by a native view, so you cannot call `setNativeProps` on them. Consider this example:

```SnackPlayer name=setNativeProps%20with%20Composite%20Components
import React from "react";
import { Text, TouchableOpacity, View } from "react-native";

const MyButton = (props) => (
  <View style={{ marginTop: 50 }}>
    <Text>{props.label}</Text>
  </View>
);

export default App = () => (
  <TouchableOpacity>
    <MyButton label="Press me!" />
  </TouchableOpacity>
);
```

If you run this you will immediately see this error: `Touchable child must either be native or forward setNativeProps to a native component`. This occurs because `MyButton` isn't directly backed by a native view whose opacity should be set. You can think about it like this: if you define a component with `createReactClass` you would not expect to be able to set a style prop on it and have that work - you would need to pass the style prop down to a child, unless you are wrapping a native component. Similarly, we are going to forward `setNativeProps` to a native-backed child component.

#### Forward setNativeProps to a child

Since the `setNativeProps` method exists on any ref to a `View` component, it is enough to forward a ref on your custom component to one of the `<View />` components that it renders. This means that a call to `setNativeProps` on the custom component will have the same effect as if you called `setNativeProps` on the wrapped `View` component itself.

```SnackPlayer name=Forwarding%20setNativeProps
import React from "react";
import { Text, TouchableOpacity, View } from "react-native";

const MyButton = React.forwardRef((props, ref) => (
  <View {...props} ref={ref} style={{ marginTop: 50 }}>
    <Text>{props.label}</Text>
  </View>
));

export default App = () => (
  <TouchableOpacity>
    <MyButton label="Press me!" />
  </TouchableOpacity>
);
```

現在您可以在 `TouchableOpacity` 中使用 `MyButton` 了！

您可能注意到我們使用 `{...props}` 將所有屬性傳遞給子視圖。這是因為 `TouchableOpacity` 實際上是一個複合元件，除了依賴子元件的 `setNativeProps` 外，還要求子元件處理觸控操作。為此，它會傳遞[多種屬性](view.md#onmoveshouldsetresponder)回調到 `TouchableOpacity` 元件。相比之下，`TouchableHighlight` 由原生視圖支持，僅需要我們實現 `setNativeProps`。

## 使用 setNativeProps 編輯 TextInput 值

`setNativeProps` 另一個常見用途是編輯 TextInput 的值。當 `bufferDelay` 較低且用戶輸入速度極快時，TextInput 的 `controlled` 屬性有時會丟失字符。部分開發者傾向完全跳過此屬性，轉而必要時直接使用 `setNativeProps` 操作 TextInput 值。例如，以下代碼示範點擊按鈕時編輯輸入框：

```SnackPlayer name=Clear%20text
import React from "react";
import { useCallback, useRef } from "react";
import { StyleSheet, TextInput, Text, TouchableOpacity, View } from "react-native";

const App = () => {
  const inputRef = useRef();
  const editText = useCallback(() => {
    inputRef.current.setNativeProps({ text: "Edited Text" });
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
    alignItems: "center",
    justifyContent: "center",
  },
  input: {
    height: 50,
    width: 200,
    marginHorizontal: 20,
    borderWidth: 1,
    borderColor: "#ccc",
  },
});

export default App;
```

您可以使用 [`clear`](textinput#clear) 方法清除 `TextInput`，該方法採用相同方式清除當前輸入文本。

## 避免與渲染函數衝突

若您更新的屬性同時由渲染函數管理，可能導致不可預測的混淆錯誤，因為元件重新渲染且該屬性變更時，先前透過 `setNativeProps` 設定的值將被完全忽略並覆寫。

## setNativeProps 與 shouldComponentUpdate

透過[智慧應用 `shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#avoid-reconciliation)，可避免協調未變更元件子樹的不必要開銷，甚至可能使 `setState` 的性能足以取代 `setNativeProps`。

## 其他原生方法

此處描述的方法適用於 React Native 提供的大多數預設元件。但請注意，這些方法_不適用_於非直接由原生視圖支持的複合元件，這通常包含您自訂應用中的多數元件。

### measure(callback)

測定指定視圖在螢幕上的位置、寬度和高度，並透過異步回調返回數值。成功時回調參數如下：

- x
- y
- width
- height
- pageX
- pageY

請注意這些測量值需待原生端渲染完成後才可用。若需盡快取得測量值且不需要 `pageX` 和 `pageY`，建議改用 [`onLayout`](view.md#onlayout) 屬性。

此外，`measure()` 返回的寬高是元件在視窗中的尺寸。若需元件的實際尺寸，建議改用 [`onLayout`](view.md#onlayout) 屬性。

### measureInWindow(callback)

測定指定視圖在視窗中的位置，並透過異步回調返回數值。若 React 根視圖嵌入其他原生視圖中，此方法將提供絕對座標。成功時回調參數如下：

- x
- y
- width
- height

### measureLayout(relativeToNativeComponentRef, onSuccess, onFail)

類似 `measure()`，但以 `relativeToNativeComponentRef` 參照的祖先視圖為基準進行測量。返回的座標相對於祖先視圖原點 `x`, `y`。

:::note
此方法亦可使用 `relativeToNativeNode` 處理程序（而非參照）調用，但此變體已棄用。
:::

```SnackPlayer name=measureLayout%20example&supportedPlatforms=android,ios
import React, { useEffect, useRef, useState } from "react";
import { Text, View, StyleSheet } from "react-native";

const App = () => {
  const textContainerRef = useRef(null);
  const textRef = useRef(null);
  const [measure, setMeasure] = useState(null);

  useEffect(() => {
    if (textRef.current && textContainerRef.current) {
      textRef.current.measureLayout(
        textContainerRef.current,
        (left, top, width, height) => {
          setMeasure({ left, top, width, height });
        }
      );
    }
  }, [measure]);

  return (
    <View style={styles.container}>
      <View
        ref={textContainerRef}
        style={styles.textContainer}
      >
        <Text ref={textRef}>
          Where am I? (relative to the text container)
        </Text>
      </View>
      <Text style={styles.measure}>
        {JSON.stringify(measure)}
      </Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
  },
  textContainer: {
    backgroundColor: "#61dafb",
    justifyContent: "center",
    alignItems: "center",
    padding: 12,
  },
  measure: {
    textAlign: "center",
    padding: 12,
  },
});

export default App;
```

### focus()

Requests focus for the given input or view. The exact behavior triggered will depend on the platform and type of view.

### blur()

Removes focus from an input or view. This is the opposite of `focus()`.