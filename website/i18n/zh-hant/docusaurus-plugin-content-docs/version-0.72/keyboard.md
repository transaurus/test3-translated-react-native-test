---
id: keyboard
title: Keyboard
---

`Keyboard` 模組用於控制鍵盤事件。

### 使用方法

Keyboard 模組允許您監聽原生事件並作出反應，同時也能對鍵盤進行操作，例如關閉鍵盤。

```SnackPlayer name=Keyboard%20Example&supportedPlatforms=ios,android
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

---

# 參考文檔

## 方法

### `addListener()`

```tsx
static addListener: (
  eventType: KeyboardEventName,
  listener: KeyboardEventListener,
) => EmitterSubscription;
```

`addListener` 函數將 JavaScript 函數與指定的原生鍵盤通知事件建立連接。

該函數會返回監聽器的引用。

**參數：**

| Name                                                                     | Type     | Description                                                                    |
| ------------------------------------------------------------------------ | -------- | ------------------------------------------------------------------------------ |
| eventName <div className="label basic two-lines required">Required</div> | string   | The string that identifies the event you're listening for. See the list below. |
| callback <div className="label basic two-lines required">Required</div>  | function | The function to be called when the event fires                                 |

**`eventName`**

可選值包括：

- `keyboardWillShow`
- `keyboardDidShow`
- `keyboardWillHide`
- `keyboardDidHide`
- `keyboardWillChangeFrame`
- `keyboardDidChangeFrame`

> 注意：Android 平臺僅支持 `keyboardDidShow` 和 `keyboardDidHide` 事件。若在 Android 10 及以下版本中將活動的 `android:windowSoftInputMode` 設為 `adjustNothing`，這些事件將不會觸發。

---

### `dismiss()`

```tsx
static dismiss();
```

關閉當前鍵盤並移除焦點。

---

### `scheduleLayoutAnimation`

```tsx
static scheduleLayoutAnimation(event: KeyboardEvent);
```

用於同步 TextInput（或其他鍵盤附屬視圖）的尺寸或位置變化與鍵盤移動。

---

### `isVisible()`

```tsx
static isVisible(): boolean;
```

獲取鍵盤最後已知的可見狀態。

---

### `metrics()`

```tsx
static metrics(): KeyboardMetrics | undefined;
```

返回當前可見軟鍵盤的度量信息。