---
id: direct-manipulation
title: Direct Manipulation
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

有時需要直接修改元件而不透過狀態/屬性觸發整個子樹重新渲染。例如在瀏覽器中使用React時，有時需直接操作DOM節點，移動應用中的視圖亦是如此。`setNativeProps` 就是React Native中直接設定DOM節點屬性的等效方法。

:::caution
請在頻繁重新渲染導致效能瓶頸時使用 `setNativeProps`！

直接操作不應作為常用工具。通常僅在建立連續動畫以避免元件層級渲染和大量視圖協調的開銷時使用。
`setNativeProps` 是命令式的，它將狀態儲存在原生層（DOM、UIView等）而非React元件中，這會使程式碼更難維護。

使用前請先嘗試用 `setState` 和 [`shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#shouldcomponentupdate-in-action) 解決問題。
:::

## 在TouchableOpacity中使用setNativeProps

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

這讓我們能編寫以下程式碼，並確知子元件會響應點擊更新不透明度，而無需子元件知曉此邏輯或修改其實作：

```tsx
<TouchableOpacity onPress={handlePress}>
  <View>
    <Text>Press me!</Text>
  </View>
</TouchableOpacity>
```

假設 `setNativeProps` 不可用。我們可能將不透明度值儲存在狀態中，並在 `onPress` 觸發時更新：

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

相較原始範例，此方法計算量更大——即使視圖及其子元件的其他屬性未改變，React仍需在每次不透明度變化時重新渲染元件層級。通常這開銷無關緊要，但在執行連續動畫和處理手勢時，謹慎優化元件能提升動畫精確度。

若查看 [NativeMethodsMixin](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Renderer/implementations/ReactNativeRenderer-prod.js) 中 `setNativeProps` 的實作，會發現它其實是 `RCTUIManager.updateView` 的封裝——這與重新渲染產生的函數呼叫完全相同（參見 [ReactNativeBaseComponent中的receiveComponent](https://github.com/facebook/react-native/blob/fb2ec1ea47c53c2e7b873acb1cb46192ac74274e/Libraries/Renderer/oss/ReactNativeRenderer-prod.js#L5793-L5813)）。

## 複合元件與setNativeProps

複合元件沒有對應的原生視圖，因此無法對其呼叫 `setNativeProps`。參考以下範例：

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

執行時會立即看到錯誤：「Touchable子元件必須是原生元件或將setNativeProps轉發給原生元件」。這是因為 `MyButton` 並非直接由可設定不透明度的原生視圖支持。可以這樣理解：如果用 `createReactClass` 定義元件，你不會期望直接對其設定樣式屬性就能生效——除非包裝了原生元件，否則需要將樣式屬性傳遞給子元件。同理，我們需要將 `setNativeProps` 轉發給原生支持的子元件。

#### 將setNativeProps轉發給子元件

由於 `setNativeProps` 方法存在於任何 `View` 元件的ref上，只需將自訂元件的ref轉發給其渲染的某個 `<View />` 元件即可。這意味著對自訂元件呼叫 `setNativeProps` 的效果等同於直接對包裝的 `View` 元件呼叫該方法。

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

現在您可以在 `TouchableOpacity` 中使用 `MyButton` 了！

您可能注意到我們使用 `{...props}` 將所有屬性傳遞給子視圖。這是因為 `TouchableOpacity` 實際上是一個複合元件，除了依賴子元件的 `setNativeProps` 外，還需要子元件處理觸控操作。為此，它會傳遞[多種屬性](view.md#onmoveshouldsetresponder)來回調 `TouchableOpacity` 元件。相比之下，`TouchableHighlight` 由原生視圖支持，僅需要我們實現 `setNativeProps`。

## 使用 setNativeProps 編輯 TextInput 值

`setNativeProps` 另一個常見用途是編輯 TextInput 的值。當 `bufferDelay` 較低且用戶輸入速度非常快時，TextInput 的 `controlled` 屬性有時會丟失字符。一些開發者更傾向完全跳過此屬性，轉而在必要時使用 `setNativeProps` 直接操作 TextInput 的值。例如，以下代碼示範了點擊按鈕時編輯輸入框：

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

您可以使用 [`clear`](textinput#clear) 方法清除 `TextInput`，該方法採用相同方式清除當前輸入文本。

## 避免與渲染函數衝突

如果您更新了一個同時由渲染函數管理的屬性，可能會導致不可預測且混亂的錯誤，因為每當元件重新渲染且該屬性發生變化時，之前通過 `setNativeProps` 設置的值將被完全忽略並覆蓋。

## setNativeProps 與 shouldComponentUpdate

通過[智能應用 `shouldComponentUpdate`](https://reactjs.org/docs/optimizing-performance.html#avoid-reconciliation)，您可以避免在協調未變更的子元件樹時產生不必要的開銷，從而達到足以使用 `setState` 而非 `setNativeProps` 的性能水平。

## 其他原生方法

這裡描述的方法在 React Native 提供的大多數默認元件上都可用。但請注意，這些方法_不適用_於非直接由原生視圖支持的複合元件，這通常包括您在應用中自定義的大多數元件。

### measure(callback)

確定給定視圖在屏幕上的位置、寬度和高度，並通過異步回調返回這些值。如果成功，回調將被調用並傳入以下參數：

- x
- y
- width
- height
- pageX
- pageY

請注意，這些測量值在原生端渲染完成後才會可用。如果您需要盡快獲取測量值且不需要 `pageX` 和 `pageY`，可以考慮使用 [`onLayout`](view.md#onlayout) 屬性替代。

此外，`measure()` 返回的寬度和高度是元件在視口中的寬高。如果您需要元件的實際尺寸，建議改用 [`onLayout`](view.md#onlayout) 屬性。

### measureInWindow(callback)

確定給定視圖在窗口中的位置，並通過異步回調返回這些值。如果 React 根視圖嵌入到另一個原生視圖中，此方法將提供絕對座標。如果成功，回調將被調用並傳入以下參數：

- x
- y
- width
- height

### measureLayout(relativeToNativeComponentRef, onSuccess, onFail)

與 `measure()` 類似，但測量的是相對於祖先視圖的視圖位置，該祖先視圖由 `relativeToNativeComponentRef` 引用指定。這意味著返回的座標是相對於祖先視圖原點 `x`、`y` 的。

:::note
此方法也可以使用 `relativeToNativeNode` 處理程序（而非引用）調用，但此變體已被棄用。
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

請求對指定的輸入框或視圖進行聚焦。觸發的具體行為將取決於平台和視圖類型。

### blur()

移除輸入框或視圖的聚焦狀態。此為`focus()`的反向操作。