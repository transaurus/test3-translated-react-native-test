---
id: pressable
title: Pressable
---

Pressable 是一個核心元件包裝器，能夠偵測其定義的子元件上各種按壓互動的階段。

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

1. 使用者移開手指，觸發 [`onPressOut`](#onpressout)，接著觸發 [`onPress`](#onpress)。
2. 如果使用者在移開手指前按壓超過 500 毫秒，則會觸發 [`onLongPress`](#onlongpress)。（當他們移開手指時，[`onPressOut`](#onpressout) 仍會觸發。）

<img src="/docs/assets/d_pressable_pressing.svg" width="1000" alt="Diagram of the onPress events in sequence." />

手指並非最精確的工具，使用者常會意外啟動錯誤的元素或錯過啟動區域。為此，`Pressable` 提供了一個可選的 `HitRect`，可用於定義觸控能夠在距離包裝元素多遠的位置註冊。按壓可以在 `HitRect` 內的任何位置開始。

`PressRect` 允許按壓在維持啟動狀態的同時，移動超出元素及其 `HitRect` 的範圍，並仍有資格成為「按壓」——想像一下當你按壓一個按鈕時，手指慢慢滑開的情況。

> 觸控區域永遠不會超出父視圖的邊界，且如果觸控同時命中兩個重疊的視圖，兄弟視圖的 Z-index 永遠具有優先權。

<figure>
  <img src="/docs/assets/d_pressable_anatomy.svg" width="1000" alt="Diagram of HitRect and PressRect and how they work." />
  <figcaption>
    You can set <code>HitRect</code> with <code>hitSlop</code> and set <code>PressRect</code> with <code>pressRetentionOffset</code>.
  </figcaption>
</figure>

> `Pressable` 使用了 React Native 的 `Pressability` API。如需更多關於 Pressability 狀態機流程及其運作方式的資訊，請查看 [Pressability](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Pressability/Pressability.js#L350) 的實作。

## 範例

```SnackPlayer name=Pressable
import React, {useState} from 'react';
import {Pressable, StyleSheet, Text, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [timesPressed, setTimesPressed] = useState(0);

  let textLog = '';
  if (timesPressed > 1) {
    textLog = timesPressed + 'x onPress';
  } else if (timesPressed > 0) {
    textLog = 'onPress';
  }

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
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
      </SafeAreaView>
    </SafeAreaProvider>
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

若為 true，則在按壓時不會播放 Android 系統音效。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

### `android_ripple` <div class="label android">Android</div>

啟用 Android 漣漪效果並配置其屬性。

| Type                                   |
| -------------------------------------- |
| [RippleConfig](pressable#rippleconfig) |

### `children`

可以是子元件，或是一個接收布林值的函數，該值反映元件當前是否被按壓。

| Type                     |
| ------------------------ |
| [React Node](react-node) |

### `unstable_pressDelay`

在呼叫 `onPressIn` 之前，等待按壓的持續時間（以毫秒為單位）。

| Type   |
| ------ |
| number |

### `delayLongPress`

從 `onPressIn` 到呼叫 `onLongPress` 的持續時間（以毫秒為單位）。

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

當懸停被停用以撤銷視覺回饋時呼叫。

| Type                                                                                                      |
| --------------------------------------------------------------------------------------------------------- |
| `md ({ nativeEvent: [MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent) }) => void` |

### `onLongPress`

當 `onPressIn` 觸發後持續超過 500 毫秒時呼叫。此時間間隔可透過 [`delayLongPress`](#delaylongpress) 自訂。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPress`

在 `onPressOut` 之後呼叫。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPressIn`

當觸碰開始時立即呼叫，會在 `onPressOut` 和 `onPress` 之前觸發。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `onPressOut`

當觸碰結束時呼叫。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

### `pressRetentionOffset`

在此元件外部額外設定的距離範圍內，觸碰仍會被視為按壓行為，直到觸發 `onPressOut`。

| Type                   | Default                                      |
| ---------------------- | -------------------------------------------- |
| [Rect](rect) or number | `{bottom: 30, left: 20, right: 20, top: 20}` |

### `style`

可以是視圖樣式，或是一個接收布林值（反映元件當前是否被按壓）並返回視圖樣式的函式。

| Type                                                                                            |
| ----------------------------------------------------------------------------------------------- |
| [View Style](view-style-props) or `md ({ pressed: boolean }) => [View Style](view-style-props)` |

### `testOnly_pressed`

僅用於文件記錄或測試（例如快照測試）。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

## 類型定義

### RippleConfig

用於 `android_ripple` 屬性的漣漪效果設定。

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