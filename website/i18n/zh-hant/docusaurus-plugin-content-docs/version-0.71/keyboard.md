---
id: keyboard
title: Keyboard
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`Keyboard` 模組用於控制鍵盤事件。

### 使用方法

Keyboard 模組允許您監聽原生事件並作出反應，同時也能對鍵盤進行操作，例如關閉鍵盤。

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Keyboard%20Function%20Component%20Example&supportedPlatforms=ios,android
import React, {useState, useEffect} from 'react';
import {Keyboard, Text, TextInput, StyleSheet, View} from 'react-native';

const Example = () => {
  const [keyboardStatus, setKeyboardStatus] = useState('');

  useEffect(() => {
    const showSubscription = Keyboard.addListener('keyboardDidShow', () => {
      setKeyboardStatus('Keyboard Shown');
    });
    const hideSubscription = Keyboard.addListener('keyboardDidHide', () => {
      setKeyboardStatus('Keyboard Hidden');
    });

    return () => {
      showSubscription.remove();
      hideSubscription.remove();
    };
  }, []);

  return (
    <View style={style.container}>
      <TextInput
        style={style.input}
        placeholder="Click here…"
        onSubmitEditing={Keyboard.dismiss}
      />
      <Text style={style.status}>{keyboardStatus}</Text>
    </View>
  );
};

const style = StyleSheet.create({
  container: {
    flex: 1,
    padding: 36,
  },
  input: {
    padding: 10,
    borderWidth: 0.5,
    borderRadius: 4,
  },
  status: {
    padding: 10,
    textAlign: 'center',
  },
});

export default Example;
```

</TabItem>
<TabItem value="classical">

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Keyboard%20Class%20Component%20Example&supportedPlatforms=ios,android&ext=js
import React, {Component} from 'react';
import {Keyboard, Text, TextInput, StyleSheet, View} from 'react-native';

class Example extends Component {
  state = {
    keyboardStatus: '',
  };

  componentDidMount() {
    this.keyboardDidShowSubscription = Keyboard.addListener(
      'keyboardDidShow',
      () => {
        this.setState({keyboardStatus: 'Keyboard Shown'});
      },
    );
    this.keyboardDidHideSubscription = Keyboard.addListener(
      'keyboardDidHide',
      () => {
        this.setState({keyboardStatus: 'Keyboard Hidden'});
      },
    );
  }

  componentWillUnmount() {
    this.keyboardDidShowSubscription.remove();
    this.keyboardDidHideSubscription.remove();
  }

  render() {
    return (
      <View style={style.container}>
        <TextInput
          style={style.input}
          placeholder="Click here…"
          onSubmitEditing={Keyboard.dismiss}
        />
        <Text style={style.status}>{this.state.keyboardStatus}</Text>
      </View>
    );
  }
}

const style = StyleSheet.create({
  container: {
    flex: 1,
    padding: 36,
  },
  input: {
    padding: 10,
    borderWidth: 0.5,
    borderRadius: 4,
  },
  status: {
    padding: 10,
    textAlign: 'center',
  },
});

export default Example;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Keyboard%20Class%20Component%20Example&supportedPlatforms=ios,android&ext=tsx
import React, {Component} from 'react';
import {Keyboard, Text, TextInput, StyleSheet, View} from 'react-native';
import type {EmitterSubscription} from 'react-native';

class Example extends Component {
  keyboardDidShowSubscription?: EmitterSubscription;
  keyboardDidHideSubscription?: EmitterSubscription;

  state = {
    keyboardStatus: undefined,
  };

  componentDidMount() {
    this.keyboardDidShowSubscription = Keyboard.addListener(
      'keyboardDidShow',
      () => {
        this.setState({keyboardStatus: 'Keyboard Shown'});
      },
    );
    this.keyboardDidHideSubscription = Keyboard.addListener(
      'keyboardDidHide',
      () => {
        this.setState({keyboardStatus: 'Keyboard Hidden'});
      },
    );
  }

  componentWillUnmount() {
    this.keyboardDidShowSubscription?.remove();
    this.keyboardDidHideSubscription?.remove();
  }

  render() {
    return (
      <View style={style.container}>
        <TextInput
          style={style.input}
          placeholder="Click here…"
          onSubmitEditing={Keyboard.dismiss}
        />
        <Text style={style.status}>{this.state.keyboardStatus}</Text>
      </View>
    );
  }
}

const style = StyleSheet.create({
  container: {
    flex: 1,
    padding: 36,
  },
  input: {
    padding: 10,
    borderWidth: 0.5,
    borderRadius: 4,
  },
  status: {
    padding: 10,
    textAlign: 'center',
  },
});

export default Example;
```

</TabItem>
</Tabs>

</TabItem>
</Tabs>

---

# 參考文獻

## 方法

### `addListener()`

```tsx
static addListener: (
  eventType: KeyboardEventName,
  listener: KeyboardEventListener,
) => EmitterSubscription;
```

`addListener` 函數將 JavaScript 函數連接到指定的原生鍵盤通知事件。

此函數會返回監聽器的引用。

**參數：**

| Name                                                                     | Type     | Description                                                                    |
| ------------------------------------------------------------------------ | -------- | ------------------------------------------------------------------------------ |
| eventName <div className="label basic two-lines required">Required</div> | string   | The string that identifies the event you're listening for. See the list below. |
| callback <div className="label basic two-lines required">Required</div>  | function | The function to be called when the event fires                                 |

**`eventName`**

可以是以下任一事件：

- `keyboardWillShow`
- `keyboardDidShow`
- `keyboardWillHide`
- `keyboardDidHide`
- `keyboardWillChangeFrame`
- `keyboardDidChangeFrame`

> 注意：在 Android 上僅支援 `keyboardDidShow` 和 `keyboardDidHide` 事件。若您的 activity 將 `android:windowSoftInputMode` 設為 `adjustNothing`，則 Android 10 及以下版本不會觸發這些事件。

---

### `dismiss()`

```tsx
static dismiss();
```

關閉當前活動的鍵盤並移除焦點。

---

### `scheduleLayoutAnimation`

```tsx
static scheduleLayoutAnimation(event: KeyboardEvent);
```

用於同步 TextInput（或其他鍵盤附屬視圖）的大小或位置變化與鍵盤移動。

---

### `isVisible()`

```tsx
static isVisible(): boolean;
```

鍵盤最後已知的可見狀態。

---

### `metrics()`

```tsx
static metrics(): KeyboardMetrics | undefined;
```

返回當前可見軟鍵盤的度量數據。