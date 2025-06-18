---
id: pressable
title: Pressable
---

Pressable 是一個核心組件包裝器，能夠檢測其定義的子元素上各種按壓互動的階段。

```tsx
<Pressable onPress={onPressFunction}>
  <Text>I'm pressable!</Text>
</Pressable>
```

## 運作原理

在被 `Pressable` 包裝的元素上：

- 當按壓被激活時，[`onPressIn`](#onpressin) 會被呼叫。
- 當按壓手勢被取消激活時，[`onPressOut`](#onpressout) 會被呼叫。

在按下 [`onPressIn`](#onpressin) 後，會發生以下兩種情況之一：

1. 使用者移開手指，觸發 [`onPressOut`](#onpressout)，接著是 [`onPress`](#onpress)。
2. 如果使用者在移開手指前按壓超過 500 毫秒，則會觸發 [`onLongPress`](#onlongpress)。（當他們移開手指時，[`onPressOut`](#onpressout) 仍會觸發。）

<img src="/docs/assets/d_pressable_pressing.svg" width="1000" alt="Diagram of the onPress events in sequence." />

手指並非最精確的工具，使用者經常會意外激活錯誤的元素或錯過激活區域。為此，`Pressable` 提供了一個可選的 `HitRect`，可用於定義觸控能夠在包裝元素多遠的距離外註冊。按壓可以在 `HitRect` 內的任何地方開始。

`PressRect` 允許按壓在保持激活狀態的同時，移動超出元素及其 `HitRect` 的範圍，並且仍然有資格成為「按壓」——想像一下你慢慢將手指從按下的按鈕上滑開的情景。

> 觸控區域永遠不會超出父視圖的邊界，並且如果觸控同時命中兩個重疊的視圖，兄弟視圖的 Z-index 總是優先。

<figure>
  <img src="/docs/assets/d_pressable_anatomy.svg" width="1000" alt="Diagram of HitRect and PressRect and how they work." />
  <figcaption>
    You can set <code>HitRect</code> with <code>hitSlop</code> and set <code>PressRect</code> with <code>pressRetentionOffset</code>.
  </figcaption>
</figure>

> `Pressable` 使用了 React Native 的 `Pressability` API。有關 Pressability 的狀態機流程及其運作方式的更多信息，請查看 [Pressability](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Pressability/Pressability.js#L350) 的實現。

## 範例

```SnackPlayer name=Pressable
import React, {useState} from 'react';
import {Pressable, StyleSheet, Text, View} from 'react-native';

const App = () => {
  const [timesPressed, setTimesPressed] = useState(0);

  let textLog = '';
  if (timesPressed > 1) {
    textLog = timesPressed + 'x onPress';
  } else if (timesPressed > 0) {
    textLog = 'onPress';
  }

  return (
    <View style={styles.container}>
      <Pressable
        onPress={() => {
          setTimesPressed(current => current + 1);
        }}
        style={({pressed}) => [
          {
            backgroundColor: pressed ? 'rgb(210, 230, 255)' : 'white',
          },
          styles.wrapperCustom,
        ]}>
        {({pressed}) => (
          <Text style={styles.text}>{pressed ? 'Pressed!' : 'Press Me'}</Text>
        )}
      </Pressable>
      <View style={styles.logBox}>
        <Text testID="pressable_press_console">{textLog}</Text>
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  text: {
    fontSize: 16,
  },
  wrapperCustom: {
    borderRadius: 8,
    padding: 6,
  },
  logBox: {
    padding: 20,
    margin: 10,
    borderWidth: StyleSheet.hairlineWidth,
    borderColor: '#f0f0f0',
    backgroundColor: '#f9f9f9',
  },
});

export default App;
```

## 屬性

### `android_disableSound` <div class="label android">Android</div>

如果為 true，則在按壓時不播放 Android 系統聲音。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

### `android_ripple` <div class="label android">Android</div>

啟用 Android 漣漪效果並配置其屬性。

| Type                                   |
| -------------------------------------- |
| [RippleConfig](pressable#rippleconfig) |

### `children`

可以是子元素，也可以是一個接收布爾值的函數，該布爾值反映組件當前是否被按壓。

| Type                     |
| ------------------------ |
| [React Node](react-node) |

### `unstable_pressDelay`

在呼叫 `onPressIn` 之前，按壓後等待的持續時間（以毫秒為單位）。

| Type   |
| ------ |
| number |

### `delayLongPress`

從 `onPressIn` 開始到呼叫 `onLongPress` 的持續時間（以毫秒為單位）。

| Type   | Default |
| ------ | ------- |
| number | `500`   |

### `disabled`

是否禁用按壓行為。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

### `hitSlop`

設置在元素外可以檢測到按壓的額外距離。

| Type                   |
| ---------------------- |
| [Rect](rect) or number |

### `onHoverIn`

當懸停被激活以提供視覺反饋時呼叫。

| Type                                                                                                      |
| --------------------------------------------------------------------------------------------------------- |
| `md ({ nativeEvent: [MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent) }) => void` |

### `onHoverOut`

當懸停被取消激活以撤銷視覺反饋時呼叫。

| Type                                                                                                      |
| --------------------------------------------------------------------------------------------------------- |
| `md ({ nativeEvent: [MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent) }) => void` |

### `onLongPress`

Called if the time after `onPressIn` lasts longer than 500 milliseconds. This time period can be customized with [`delayLongPress`](#delaylongpress).

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPress`

Called after `onPressOut`.

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPressIn`

Called immediately when a touch is engaged, before `onPressOut` and `onPress`.

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPressOut`

Called when a touch is released.

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `pressRetentionOffset`

Additional distance outside of this view in which a touch is considered a press before `onPressOut` is triggered.

| Type                   | Default                                      |
| ---------------------- | -------------------------------------------- |
| [Rect](rect) or number | `{bottom: 30, left: 20, right: 20, top: 20}` |

### `style`

Either view styles or a function that receives a boolean reflecting whether the component is currently pressed and returns view styles.

| Type                                                                                            |
| ----------------------------------------------------------------------------------------------- |
| [View Style](view-style-props) or `md ({ pressed: boolean }) => [View Style](view-style-props)` |

### `testOnly_pressed`

Used only for documentation or testing (e.g. snapshot testing).

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

## Type Definitions

### RippleConfig

Ripple effect configuration for the `android_ripple` property.

| Type   |
| ------ |
| object |

**Properties:**

| Name       | Type            | Required | Description                                                                                                                                                                                                                                                  |
| ---------- | --------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| color      | [color](colors) | No       | Defines the color of the ripple effect.                                                                                                                                                                                                                      |
| borderless | boolean         | No       | Defines if ripple effect should not include border.                                                                                                                                                                                                          |
| radius     | number          | No       | Defines the radius of the ripple effect.                                                                                                                                                                                                                     |
| foreground | boolean         | No       | Set to true to add the ripple effect to the foreground of the view, instead of the background. This is useful if one of your child views has a background of its own, or you're e.g. displaying images, and you don't want the ripple to be covered by them. |