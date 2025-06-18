---
id: pressable
title: Pressable
---

Pressable 是一個核心元件包裝器，能偵測其定義的子元件上各種按壓互動的階段。

```tsx
<Pressable onPress={onPressFunction}>
  <Text>I'm pressable!</Text>
</Pressable>
```

## 運作原理

在由 `Pressable` 包裝的元素上：

- 當按壓被啟動時，會呼叫 [`onPressIn`](#onpressin)。
- 當按壓手勢被停用時，會呼叫 [`onPressOut`](#onpressout)。

在按下 [`onPressIn`](#onpressin) 後，會發生以下兩種情況之一：

1. 使用者移開手指，觸發 [`onPressOut`](#onpressout) 接著是 [`onPress`](#onpress)。
2. 如果使用者在移開手指前按住超過 500 毫秒，則會觸發 [`onLongPress`](#onlongpress)。（當他們移開手指時，[`onPressOut`](#onpressout) 仍會觸發。）

<img src="/docs/assets/d_pressable_pressing.svg" width="1000" alt="Diagram of the onPress events in sequence." />

手指並非最精確的工具，使用者常會意外啟動錯誤元素或錯過啟動區域。為此，`Pressable` 提供可選的 `HitRect`，可用來定義觸控在包裝元素外多遠的距離仍能註冊。按壓可以在 `HitRect` 內的任何位置開始。

`PressRect` 允許按壓移動超出元素及其 `HitRect`，同時保持啟動狀態並符合「按壓」資格——想像您按住按鈕時慢慢滑開手指的情況。

> 觸控區域永遠不會超出父視圖邊界，且若觸控同時命中兩個重疊視圖，兄弟視圖的 Z-index 永遠優先。

<figure>
  <img src="/docs/assets/d_pressable_anatomy.svg" width="1000" alt="Diagram of HitRect and PressRect and how they work." />
  <figcaption>
    You can set <code>HitRect</code> with <code>hitSlop</code> and set <code>PressRect</code> with <code>pressRetentionOffset</code>.
  </figcaption>
</figure>

> `Pressable` 使用 React Native 的 `Pressability` API。有關 Pressability 的狀態機流程及其運作方式的更多資訊，請查看 [Pressability](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Pressability/Pressability.js#L350) 的實作。

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

若為 true，按壓時不會播放 Android 系統音效。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

### `android_ripple` <div class="label android">Android</div>

啟用 Android 漣漪效果並配置其屬性。

| Type                                   |
| -------------------------------------- |
| [RippleConfig](pressable#rippleconfig) |

### `children`

可以是子元件，或是一個接收布林值（反映元件當前是否被按壓）的函式。

| Type                     |
| ------------------------ |
| [React Node](react-node) |

### `unstable_pressDelay`

按下後等待呼叫 `onPressIn` 的持續時間（以毫秒為單位）。

| Type   |
| ------ |
| number |

### `delayLongPress`

從 `onPressIn` 開始到呼叫 `onLongPress` 的持續時間（以毫秒為單位）。

| Type   | Default |
| ------ | ------- |
| number | `500`   |

### `disabled`

是否停用按壓行為。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

### `hitSlop`

設定在元素外可偵測按壓的額外距離。

| Type                   |
| ---------------------- |
| [Rect](rect) or number |

### `onHoverIn`

當懸停被啟動以提供視覺回饋時呼叫。

| Type                                                                                                      |
| --------------------------------------------------------------------------------------------------------- |
| `md ({ nativeEvent: [MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent) }) => void` |

### `onHoverOut`

當懸停被停用以取消視覺回饋時呼叫。

| Type                                                                                                      |
| --------------------------------------------------------------------------------------------------------- |
| `md ({ nativeEvent: [MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent) }) => void` |

### `onLongPress`

若 `onPressIn` 觸發後持續超過 500 毫秒，則會呼叫此函式。可透過 [`delayLongPress`](#delaylongpress) 自訂此時間閾值。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPress`

在 `onPressOut` 之後觸發。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPressIn`

當觸碰動作開始時立即觸發，先於 `onPressOut` 和 `onPress`。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPressOut`

當觸碰動作結束時觸發。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `pressRetentionOffset`

在 `onPressOut` 觸發前，可被視為按壓動作的觸碰區域延伸範圍（超出元件邊界的額外距離）。

| Type                   | Default                                      |
| ---------------------- | -------------------------------------------- |
| [Rect](rect) or number | `{bottom: 30, left: 20, right: 20, top: 20}` |

### `style`

可接受一般樣式物件，或接收元件當前按壓狀態（布林值）並回傳樣式物件的函式。

| Type                                                                                            |
| ----------------------------------------------------------------------------------------------- |
| [View Style](view-style-props) or `md ({ pressed: boolean }) => [View Style](view-style-props)` |

### `testOnly_pressed`

僅用於文件說明或測試情境（例如快照測試）。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

## 型別定義

### RippleConfig

用於設定 `android_ripple` 屬性的波紋效果參數。

| Type   |
| ------ |
| object |

**屬性：**

| Name       | Type            | Required | Description                                                                                                                                                                                                                                                  |
| ---------- | --------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| color      | [color](colors) | No       | Defines the color of the ripple effect.                                                                                                                                                                                                                      |
| borderless | boolean         | No       | Defines if ripple effect should not include border.                                                                                                                                                                                                          |
| radius     | number          | No       | Defines the radius of the ripple effect.                                                                                                                                                                                                                     |
| foreground | boolean         | No       | Set to true to add the ripple effect to the foreground of the view, instead of the background. This is useful if one of your child views has a background of its own, or you're e.g. displaying images, and you don't want the ripple to be covered by them. |