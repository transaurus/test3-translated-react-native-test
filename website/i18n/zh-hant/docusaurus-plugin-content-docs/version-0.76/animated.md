---
id: animated
title: Animated
---

`Animated` 函式庫專為打造流暢、強大且易於建置維護的動畫而設計。其核心聚焦於輸入與輸出間的宣告式關聯、可配置的轉換過程，以及透過 `start`/`stop` 方法控制基於時間軸的動畫執行。

建立動畫的核心流程為：創建一個 `Animated.Value`，將其綁定至動畫元件的多個樣式屬性，再透過 `Animated.timing()` 驅動動畫更新。

> 請勿直接修改動畫值。您可使用 [`useRef` Hook](https://reactjs.org/docs/hooks-reference.html#useref) 回傳可變的 ref 物件，該物件的 `current` 屬性將在元件生命週期中持續保持初始化的參數值。

## 範例

以下範例展示一個 `View` 元件如何依據動畫值 `fadeAnim` 實現淡入淡出效果：

```SnackPlayer name=Animated%20Example&supportedPlatforms=ios,android
import React from 'react';
import {Animated, Text, View, StyleSheet, Button, useAnimatedValue} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

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

參閱 [動畫指南](animations#animated-api) 以獲取更多 `Animated` 實際應用範例。

## 概覽

`Animated` 支援兩種數值型態：

- [`Animated.Value()`](animated#value) 用於單一數值
- [`Animated.ValueXY()`](animated#valuexy) 用於向量

`Animated.Value` 可綁定至樣式屬性或其他參數，並支援插值計算。單一 `Animated.Value` 能驅動任意數量的屬性。

### 動畫配置

`Animated` 提供三種動畫類型，每種類型皆提供特定動畫曲線來控制數值從初始值變化至最終值的過程：

- [`Animated.decay()`](animated#decay) 以初始速度開始並逐漸減速至停止
- [`Animated.spring()`](animated#spring) 提供基礎彈簧物理模型
- [`Animated.timing()`](animated#timing) 透過[緩動函式](easing)隨時間變化數值

多數情況下建議使用 `timing()`。預設採用對稱的 easeInOut 曲線，模擬物體加速至全速後再減速停止的過程。

### 動畫操作

透過呼叫動畫實例的 `start()` 啟動動畫，該方法接受一個在動畫完成時執行的回調函式。若動畫正常完成，回調將收到 `{finished: true}`；若因呼叫 `stop()` 中斷（例如被手勢或其他動畫打斷），則回傳 `{finished: false}`。

```tsx
Animated.timing({}).start(({finished}) => {
  /* completion callback */
});
```

### 使用原生驅動

啟用原生驅動後，系統會在動畫開始前將所有參數傳送至原生端，使動畫能在 UI 執行緒運行，避免每幀都需跨越橋接器。動畫啟動後，即使 JavaScript 執行緒阻塞亦不影響動畫表現。

透過在動畫配置中設定 `useNativeDriver: true` 啟用此功能，詳見[動畫指南](animations#using-the-native-driver)。

### 可動畫化元件

僅有特殊處理的可動畫化元件能綁定動畫值。這些元件會將動畫值綁定至屬性，並透過針對性的原生更新避免每幀重新渲染的成本，同時自動處理卸載時的清理工作。

- 使用 [`createAnimatedComponent()`](animated#createanimatedcomponent) 可將普通元件轉化為可動畫化元件。

`Animated` 內建以下透過該封裝器處理的可動畫化元件：

- `Animated.Image`
- `Animated.ScrollView`
- `Animated.Text`
- `Animated.View`
- `Animated.FlatList`
- `Animated.SectionList`

### 動畫組合

動畫也可以透過組合函數以複雜的方式結合：

- [`Animated.delay()`](animated#delay) 在指定延遲後開始動畫。
- [`Animated.parallel()`](animated#parallel) 同時啟動多個動畫。
- [`Animated.sequence()`](animated#sequence) 按順序啟動動畫，等待每個動畫完成後再開始下一個。
- [`Animated.stagger()`](animated#stagger) 按順序並行啟動動畫，但具有連續的延遲。

動畫也可以透過將一個動畫的 `toValue` 設為另一個 `Animated.Value` 來鏈接在一起。詳見動畫指南中的[追蹤動態值](animations#tracking-dynamic-values)。

預設情況下，如果一個動畫被停止或中斷，則群組中的所有其他動畫也會停止。

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

手勢（如平移或滾動）和其他事件可以使用 `Animated.event()` 直接映射到動畫值。這是透過結構化的映射語法實現的，以便可以從複雜的事件對象中提取值。第一層是一個數組，允許跨多個參數進行映射，該數組包含嵌套的對象。

- [`Animated.event()`](animated#event)

例如，在處理水平滾動手勢時，您可以將 `event.nativeEvent.contentOffset.x` 映射到 `scrollX`（一個 `Animated.Value`）：

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

當給定的值是 ValueXY 而不是 Value 時，每個配置選項可以是向量形式 `{x: ..., y: ...}` 而不是標量。

### `decay()`

```tsx
static decay(value, config): CompositeAnimation;
```

根據衰減係數，從初始速度到零動畫化一個值。

配置是一個可能具有以下選項的對象：

- `velocity`: 初始速度。必填。
- `deceleration`: 衰減率。預設 0.997。
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「互動句柄」。預設為 true。
- `useNativeDriver`: 為 true 時使用原生驅動。必填。

---

### `timing()`

```tsx
static timing(value, config): CompositeAnimation;
```

沿著定時緩動曲線動畫化一個值。[`Easing`](easing) 模組有許多預定義的曲線，或者您可以使用自己的函數。

配置是一個可能具有以下選項的對象：

- `duration`: 動畫長度（毫秒）。預設 500。
- `easing`: 定義曲線的緩動函數。預設為 `Easing.inOut(Easing.ease)`。
- `delay`: 在延遲後開始動畫（毫秒）。預設 0。
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「互動句柄」。預設為 true。
- `useNativeDriver`: 為 true 時使用原生驅動。必填。

---

### `spring()`

```tsx
static spring(value, config): CompositeAnimation;
```

根據[阻尼諧振子模型](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)以解析彈簧動畫驅動數值變化。該動畫會追蹤速度狀態以在`toValue`更新時產生流暢運動效果，並可進行鏈式組合。

配置參數為包含以下選項的物件：

注意：只能選擇定義以下任一組參數，不可同時定義多組：

friction/tension或bounciness/speed參數組與[Facebook Pop](https://github.com/facebook/pop)、[Rebound](https://github.com/facebookarchive/rebound)及[Origami](https://origami.design/)的彈簧模型相匹配。

- `friction`：控制「彈性」/過衝現象。預設值7。
- `tension`：控制動畫速度。預設值40。
- `speed`：控制動畫速度。預設值12。
- `bounciness`：控制彈性程度。預設值8。

指定stiffness/damping/mass參數時，`Animated.spring`將採用基於[阻尼諧振子運動方程](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)的解析彈簧模型。此行為更精確地模擬彈簧動力學背後的物理原理，並高度還原iOS中CASpringAnimation的實現方式。

- `stiffness`：彈簧剛度係數。預設值100。
- `damping`：定義因摩擦力導致的彈簧運動衰減程度。預設值10。
- `mass`：連接在彈簧末端的物體質量。預設值1。

其他配置選項如下：

- `velocity`：連接彈簧物體的初始速度。預設0（物體處於靜止狀態）。
- `overshootClamping`：布林值，決定彈簧是否應箝位而不反彈。預設false。
- `restDisplacementThreshold`：彈簧被視為靜止的位移閾值。預設0.001。
- `restSpeedThreshold`：彈簧被視為靜止的速度閾值（像素/秒）。預設0.001。
- `delay`：延遲啟動動畫的時間（毫秒）。預設0。
- `isInteraction`：是否在`InteractionManager`上建立「互動句柄」。預設true。
- `useNativeDriver`：為true時使用原生驅動。必須設定。

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

建立取提供Animated值非負模數的新Animated值。

---

### `diffClamp()`

```tsx
static diffClamp(a: Animated, min: number, max: number): AnimatedDiffClamp;
```

Create a new Animated value that is limited between 2 values. It uses the difference between the last value so even if the value is far from the bounds it will start changing when the value starts getting closer again. (`value = clamp(value + diff, min, max)`).

This is useful with scroll events, for example, to show the navbar when scrolling up and to hide it when scrolling down.

---

### `delay()`

```tsx
static delay(time: number): CompositeAnimation;
```

Starts an animation after the given delay.

---

### `sequence()`

```tsx
static sequence(animations: CompositeAnimation[]): CompositeAnimation;
```

Starts an array of animations in order, waiting for each to complete before starting the next. If the current running animation is stopped, no following animations will be started.

---

### `parallel()`

```tsx
static parallel(
  animations: CompositeAnimation[],
  config?: ParallelConfig
): CompositeAnimation;
```

Starts an array of animations all at the same time. By default, if one of the animations is stopped, they will all be stopped. You can override this with the `stopTogether` flag.

---

### `stagger()`

```tsx
static stagger(
  time: number,
  animations: CompositeAnimation[]
): CompositeAnimation;
```

Array of animations may run in parallel (overlap), but are started in sequence with successive delays. Nice for doing trailing effects.

---

### `loop()`

```tsx
static loop(
  animation: CompositeAnimation[],
  config?: LoopAnimationConfig
): CompositeAnimation;
```

Loops a given animation continuously, so that each time it reaches the end, it resets and begins again from the start. Will loop without blocking the JS thread if the child animation is set to `useNativeDriver: true`. In addition, loops can prevent `VirtualizedList`-based components from rendering more rows while the animation is running. You can pass `isInteraction: false` in the child animation config to fix this.

Config is an object that may have the following options:

- `iterations`: Number of times the animation should loop. Default `-1` (infinite).

---

### `event()`

```tsx
static event(
  argMapping: Mapping[],
  config?: EventConfig
): (...args: any[]) => void;
```

Takes an array of mappings and extracts values from each arg accordingly, then calls `setValue` on the mapped outputs. e.g.

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

Config is an object that may have the following options:

- `listener`: Optional async listener.
- `useNativeDriver`: Uses the native driver when true. Required.

---

### `forkEvent()`

```jsx
static forkEvent(event: AnimatedEvent, listener: Function): AnimatedEvent;
```

Advanced imperative API for snooping on animated events that are passed in through props. It permits to add a new javascript listener to an existing `AnimatedEvent`. If `animatedEvent` is a javascript listener, it will merge the 2 listeners into a single one, and if `animatedEvent` is null/undefined, it will assign the javascript listener directly. Use values directly where possible.

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

Animations are started by calling start() on your animation. start() takes a completion callback that will be called when the animation is done or when the animation is done because stop() was called on it before it could finish.

**Parameters:**

| Name     | Type                                    | Required | Description                                                                                                                                                     |
| -------- | --------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| callback | `(result: {finished: boolean}) => void` | No       | Function that will be called after the animation finished running normally or when the animation is done because stop() was called on it before it could finish |

Start example with callback:

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

停止任何正在執行的動畫。

---

### `reset()`

```tsx
static reset();
```

停止任何正在執行的動畫並將值重置為原始狀態。

## 屬性

### `Value`

用於驅動動畫的標準值類別。通常在類別元件中初始化為 `useAnimatedValue(0);` 或 `new Animated.Value(0);`。

您可以在單獨的[頁面](animatedvalue)上閱讀更多關於 `Animated.Value` API 的資訊。

---

### `ValueXY`

用於驅動 2D 動畫（例如平移手勢）的 2D 值類別。

您可以在單獨的[頁面](animatedvaluexy)上閱讀更多關於 `Animated.ValueXY` API 的資訊。

---

### `Interpolation`

導出以在流程中使用 Interpolation 類型。

---

### `Node`

導出以便於類型檢查。所有動畫值均派生自此類別。

---

### `createAnimatedComponent`

使任何 React 元件可動畫化。用於創建 `Animated.View` 等。

---

### `attachNativeEvent`

將動畫值附加到視圖上的事件的命令式 API。如果可能，建議使用 `Animated.event` 並設置 `useNativeDriver: true`。