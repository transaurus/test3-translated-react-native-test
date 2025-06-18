---
id: animated
title: Animated
---

The `Animated` library is designed to make animations fluid, powerful, and painless to build and maintain. `Animated` focuses on declarative relationships between inputs and outputs, configurable transforms in between, and `start`/`stop` methods to control time-based animation execution.

The core workflow for creating an animation is to create an `Animated.Value`, hook it up to one or more style attributes of an animated component, and then drive updates via animations using `Animated.timing()`.

> Don't modify the animated value directly. You can use the [`useRef` Hook](https://reactjs.org/docs/hooks-reference.html#useref) to return a mutable ref object. This ref object's `current` property is initialized as the given argument and persists throughout the component lifecycle.

## Example

The following example contains a `View` which will fade in and fade out based on the animated value `fadeAnim`

```SnackPlayer name=Animated%20Example&supportedPlatforms=ios,android
import React from 'react';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';
import {
  Animated,
  Text,
  View,
  StyleSheet,
  Button,
  useAnimatedValue,
} from 'react-native';

const App = () => {
  // fadeAnim will be used as the value for opacity. Initial Value: 0
  const fadeAnim = useAnimatedValue(0);

  const fadeIn = () => {
    // Will change fadeAnim value to 1 in 5 seconds
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 5000,
      useNativeDriver: true,
    }).start();
  };

  const fadeOut = () => {
    // Will change fadeAnim value to 0 in 3 seconds
    Animated.timing(fadeAnim, {
      toValue: 0,
      duration: 3000,
      useNativeDriver: true,
    }).start();
  };

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Animated.View
          style={[
            styles.fadingContainer,
            {
              // Bind opacity to animated value
              opacity: fadeAnim,
            },
          ]}>
          <Text style={styles.fadingText}>Fading View!</Text>
        </Animated.View>
        <View style={styles.buttonRow}>
          <Button title="Fade In View" onPress={fadeIn} />
          <Button title="Fade Out View" onPress={fadeOut} />
        </View>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  fadingContainer: {
    padding: 20,
    backgroundColor: 'powderblue',
  },
  fadingText: {
    fontSize: 28,
  },
  buttonRow: {
    flexBasis: 100,
    justifyContent: 'space-evenly',
    marginVertical: 16,
  },
});

export default App;
```

Refer to the [Animations](animations#animated-api) guide to see additional examples of `Animated` in action.

## Overview

There are two value types you can use with `Animated`:

- [`Animated.Value()`](animated#value) for single values
- [`Animated.ValueXY()`](animated#valuexy) for vectors

`Animated.Value` can bind to style properties or other props, and can be interpolated as well. A single `Animated.Value` can drive any number of properties.

### Configuring animations

`Animated` provides three types of animation types. Each animation type provides a particular animation curve that controls how your values animate from their initial value to the final value:

- [`Animated.decay()`](animated#decay) starts with an initial velocity and gradually slows to a stop.
- [`Animated.spring()`](animated#spring) provides a basic spring physics model.
- [`Animated.timing()`](animated#timing) animates a value over time using [easing functions](easing).

In most cases, you will be using `timing()`. By default, it uses a symmetric easeInOut curve that conveys the gradual acceleration of an object to full speed and concludes by gradually decelerating to a stop.

### Working with animations

Animations are started by calling `start()` on your animation. `start()` takes a completion callback that will be called when the animation is done. If the animation finished running normally, the completion callback will be invoked with `{finished: true}`. If the animation is done because `stop()` was called on it before it could finish (e.g. because it was interrupted by a gesture or another animation), then it will receive `{finished: false}`.

```tsx
Animated.timing({}).start(({finished}) => {
  /* completion callback */
});
```

### Using the native driver

By using the native driver, we send everything about the animation to native before starting the animation, allowing native code to perform the animation on the UI thread without having to go through the bridge on every frame. Once the animation has started, the JS thread can be blocked without affecting the animation.

You can use the native driver by specifying `useNativeDriver: true` in your animation configuration. See the [Animations](animations#using-the-native-driver) guide to learn more.

### Animatable components

Only animatable components can be animated. These unique components do the magic of binding the animated values to the properties, and do targeted native updates to avoid the cost of the React render and reconciliation process on every frame. They also handle cleanup on unmount so they are safe by default.

- [`createAnimatedComponent()`](animated#createanimatedcomponent) can be used to make a component animatable.

`Animated` exports the following animatable components using the above wrapper:

- `Animated.Image`
- `Animated.ScrollView`
- `Animated.Text`
- `Animated.View`
- `Animated.FlatList`
- `Animated.SectionList`

### Composing animations

動畫也可以透過組合函數以複雜的方式結合：

- [`Animated.delay()`](animated#delay) 在指定延遲後開始動畫。
- [`Animated.parallel()`](animated#parallel) 同時啟動多個動畫。
- [`Animated.sequence()`](animated#sequence) 按順序啟動動畫，等待前一個完成後才開始下一個。
- [`Animated.stagger()`](animated#stagger) 按順序且並行啟動動畫，但會連續延遲。

動畫也可以透過將一個動畫的 `toValue` 設為另一個 `Animated.Value` 來串聯。請參閱動畫指南中的[追蹤動態值](animations#tracking-dynamic-values)。

預設情況下，如果一個動畫被停止或中斷，群組中的所有其他動畫也會停止。

### 結合動畫值

您可以透過加法、減法、乘法、除法或取模來結合兩個動畫值，以創建新的動畫值：

- [`Animated.add()`](animated#add)
- [`Animated.subtract()`](animated#subtract)
- [`Animated.divide()`](animated#divide)
- [`Animated.modulo()`](animated#modulo)
- [`Animated.multiply()`](animated#multiply)

### 插值

`interpolate()` 函數允許將輸入範圍映射到不同的輸出範圍。預設情況下，它會將曲線外推到給定範圍之外，但您也可以讓它限制輸出值。它預設使用線性插值，但也支援緩動函數。

- [`interpolate()`](animatedvalue#interpolate)

更多關於插值的資訊，請參閱[動畫](animations#interpolation)指南。

### 處理手勢和其他事件

手勢（如平移或滾動）和其他事件可以直接使用 `Animated.event()` 映射到動畫值。這是透過結構化映射語法完成的，以便可以從複雜的事件物件中提取值。第一層是一個陣列，允許跨多個參數進行映射，該陣列包含嵌套物件。

- [`Animated.event()`](animated#event)

例如，在處理水平滾動手勢時，您可以這樣做以將 `event.nativeEvent.contentOffset.x` 映射到 `scrollX`（一個 `Animated.Value`）：

```tsx
 onScroll={Animated.event(
   // scrollX = e.nativeEvent.contentOffset.x
   [{nativeEvent: {
        contentOffset: {
          x: scrollX
        }
      }
    }]
 )}
```

---

# 參考

## 方法

當給定的值是 ValueXY 而不是 Value 時，每個配置選項可以是 `{x: ..., y: ...}` 形式的向量，而不是標量。

### `decay()`

```tsx
static decay(value, config): CompositeAnimation;
```

根據衰減係數，從初始速度動畫化一個值到零。

配置是一個物件，可能包含以下選項：

- `velocity`: 初始速度。必填。
- `deceleration`: 衰減率。預設 0.997。
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「互動句柄」。預設 true。
- `useNativeDriver`: 為 true 時使用原生驅動。必填。

---

### `timing()`

```tsx
static timing(value, config): CompositeAnimation;
```

沿著定時緩動曲線動畫化一個值。[`Easing`](easing) 模組有許多預定義曲線，或者您可以使用自己的函數。

配置是一個物件，可能包含以下選項：

- `duration`: 動畫長度（毫秒）。預設 500。
- `easing`: 定義曲線的緩動函數。預設為 `Easing.inOut(Easing.ease)`。
- `delay`: 在延遲後開始動畫（毫秒）。預設 0。
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「互動句柄」。預設 true。
- `useNativeDriver`: 為 true 時使用原生驅動。必填。

---

### `spring()`

```tsx
static spring(value, config): CompositeAnimation;
```

根據[阻尼諧振子模型](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)實現的彈簧動畫效果。該方法會追蹤速度狀態以在`toValue`更新時產生流暢運動，並支持動畫串聯。

配置參數為一個物件，可包含以下選項：

注意：彈性/速度、張力/摩擦力、或剛度/阻尼/質量三組參數中只能選擇一組定義：

摩擦力/張力或彈性/速度參數與[Facebook Pop](https://github.com/facebook/pop)、[Rebound](https://github.com/facebookarchive/rebound)及[Origami](https://origami.design/)的彈簧模型匹配。

- `friction`: 控制「彈性」/過衝。預設值7。
- `tension`: 控制速度。預設值40。
- `speed`: 控制動畫速度。預設值12。
- `bounciness`: 控制彈性。預設值8。

使用剛度/阻尼/質量參數時，`Animated.spring`將採用基於[阻尼諧振子運動方程](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)的解析彈簧模型。此行為更精確地模擬了彈簧動力學的物理特性，並高度還原iOS中CASpringAnimation的實現。

- `stiffness`: 彈簧剛度係數。預設值100。
- `damping`: 定義因摩擦力導致的運動衰減程度。預設值10。
- `mass`: 連接在彈簧末端的物體質量。預設值1。

其他配置選項如下：

- `velocity`: 連接物體的初始速度。預設0（靜止狀態）。
- `overshootClamping`: 布林值，決定彈簧是否應箝位而不反彈。預設false。
- `restDisplacementThreshold`: 判定彈簧靜止的位移閾值。預設0.001。
- `restSpeedThreshold`: 判定彈簧靜止的速度閾值（像素/秒）。預設0.001。
- `delay`: 延遲啟動動畫（毫秒）。預設0。
- `isInteraction`: 是否在`InteractionManager`上建立「互動句柄」。預設true。
- `useNativeDriver`: 為true時使用原生驅動。必填。

---

### `add()`

```tsx
static add(a: Animated, b: Animated): AnimatedAddition;
```

建立由兩個Animated值相加組成的新Animated值。

---

### `subtract()`

```tsx
static subtract(a: Animated, b: Animated): AnimatedSubtraction;
```

建立由第一個Animated值減去第二個Animated值組成的新Animated值。

---

### `divide()`

```tsx
static divide(a: Animated, b: Animated): AnimatedDivision;
```

建立由第一個Animated值除以第二個Animated值組成的新Animated值。

---

### `multiply()`

```tsx
static multiply(a: Animated, b: Animated): AnimatedMultiplication;
```

建立由兩個Animated值相乘組成的新Animated值。

---

### `modulo()`

```tsx
static modulo(a: Animated, modulus: number): AnimatedModulo;
```

建立提供Animated值取模（非負）後的新Animated值。

---

### `diffClamp()`

```tsx
static diffClamp(a: Animated, min: number, max: number): AnimatedDiffClamp;
```

建立一個新的動畫值，其值被限制在兩個數值之間。它會根據前後值的差異來調整，因此即使當前值遠離邊界，只要值開始接近邊界時就會開始變化（計算方式為 `value = clamp(value + diff, min, max)`）。

這在處理滾動事件時特別有用，例如在向上滾動時顯示導覽列，向下滾動時隱藏它。

---

### `delay()`

```tsx
static delay(time: number): CompositeAnimation;
```

在指定的延遲時間後開始動畫。

---

### `sequence()`

```tsx
static sequence(animations: CompositeAnimation[]): CompositeAnimation;
```

按順序開始一系列動畫，等待每個動畫完成後才開始下一個。如果當前運行的動畫被停止，後續的動畫將不會啟動。

---

### `parallel()`

```tsx
static parallel(
  animations: CompositeAnimation[],
  config?: ParallelConfig
): CompositeAnimation;
```

同時開始一系列動畫。預設情況下，如果其中一個動畫被停止，所有動畫都會停止。可以通過 `stopTogether` 標誌來覆蓋此行為。

---

### `stagger()`

```tsx
static stagger(
  time: number,
  animations: CompositeAnimation[]
): CompositeAnimation;
```

一系列動畫可以並行運行（重疊），但會按順序以連續的延遲開始。適合用於實現拖尾效果。

---

### `loop()`

```tsx
static loop(
  animation: CompositeAnimation[],
  config?: LoopAnimationConfig
): CompositeAnimation;
```

循環播放指定的動畫，每次到達結尾時重置並從頭開始。如果子動畫設置為 `useNativeDriver: true`，則循環不會阻塞 JS 線程。此外，循環可能會阻止基於 `VirtualizedList` 的組件在動畫運行時渲染更多行。可以在子動畫配置中傳遞 `isInteraction: false` 來解決此問題。

配置是一個對象，可能包含以下選項：

- `iterations`: 動畫應該循環的次數。默認為 `-1`（無限循環）。

---

### `event()`

```tsx
static event(
  argMapping: Mapping[],
  config?: EventConfig
): (...args: any[]) => void;
```

接收一個映射數組，並從每個參數中提取相應的值，然後在映射的輸出上調用 `setValue`。例如：

```tsx
onScroll={Animated.event(
  [{nativeEvent: {contentOffset: {x: this._scrollX}}}],
  {listener: (event: ScrollEvent) => console.log(event)}, // Optional async listener
)}
 ...
onPanResponderMove: Animated.event(
  [
    null, // raw event arg ignored
    {dx: this._panX},
  ], // gestureState arg
  {
    listener: (
      event: GestureResponderEvent,
      gestureState: PanResponderGestureState
    ) => console.log(event, gestureState),
  } // Optional async listener
);
```

配置是一個對象，可能包含以下選項：

- `listener`: 可選的異步監聽器。
- `useNativeDriver`: 為 true 時使用原生驅動。必需。

---

### `forkEvent()`

```jsx
static forkEvent(event: AnimatedEvent, listener: Function): AnimatedEvent;
```

這是一個高階的命令式 API，用於監聽通過 props 傳入的動畫事件。它允許在現有的 `AnimatedEvent` 上添加一個新的 JavaScript 監聽器。如果 `animatedEvent` 是一個 JavaScript 監聽器，它會將兩個監聽器合併為一個；如果 `animatedEvent` 為 null/undefined，則會直接分配 JavaScript 監聽器。盡可能直接使用值。

---

### `unforkEvent()`

```jsx
static unforkEvent(event: AnimatedEvent, listener: Function);
```

---

### `start()`

```tsx
static start(callback?: (result: {finished: boolean}) => void);
```

動畫通過調用 start() 來啟動。start() 接受一個完成回調，該回調會在動畫完成時或在動畫完成前被 stop() 調用時執行。

**參數：**

| Name     | Type                                    | Required | Description                                                                                                                                                     |
| -------- | --------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| callback | `(result: {finished: boolean}) => void` | No       | Function that will be called after the animation finished running normally or when the animation is done because stop() was called on it before it could finish |

帶回調的啟動示例：

```tsx
Animated.timing({}).start(({finished}) => {
  /* completion callback */
});
```

---

### `stop()`

```tsx
static stop();
```

Stops any running animation.

---

### `reset()`

```tsx
static reset();
```

Stops any running animation and resets the value to its original.

## Properties

### `Value`

Standard value class for driving animations. Typically initialized with `useAnimatedValue(0);` or `new Animated.Value(0);` in class components.

You can read more about `Animated.Value` API on the separate [page](animatedvalue).

---

### `ValueXY`

2D value class for driving 2D animations, such as pan gestures.

You can read more about `Animated.ValueXY` API on the separate [page](animatedvaluexy).

---

### `Interpolation`

Exported to use the Interpolation type in flow.

---

### `Node`

Exported for ease of type checking. All animated values derive from this class.

---

### `createAnimatedComponent`

Make any React component Animatable. Used to create `Animated.View`, etc.

---

### `attachNativeEvent`

Imperative API to attach an animated value to an event on a view. Prefer using `Animated.event` with `useNativeDriver: true` if possible.