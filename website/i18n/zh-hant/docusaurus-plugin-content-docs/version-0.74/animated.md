---
id: animated
title: Animated
---

`Animated` 函式庫專為打造流暢、強大且易於建置維護的動畫而設計。其核心在於宣告式的輸入輸出關係、可配置的轉換過程，以及透過 `start`/`stop` 方法控制基於時間軸的動畫執行。

建立動畫的核心流程是：創建一個 `Animated.Value`，將其綁定至動畫元件的樣式屬性，再透過 `Animated.timing()` 驅動數值更新。

> 請勿直接修改動畫數值。可使用 [`useRef` Hook](https://reactjs.org/docs/hooks-reference.html#useref) 回傳可變的 ref 物件，該物件的 `current` 屬性將在元件生命週期中持續保持初始值。

## 範例

以下範例展示了一個 `View` 元件如何根據動畫數值 `fadeAnim` 實現淡入淡出效果：

```SnackPlayer name=Animated&supportedPlatforms=ios,android
import React from 'react';
import {
  Animated,
  Text,
  View,
  StyleSheet,
  Button,
  SafeAreaView,
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

更多實際應用範例請參閱 [動畫指南](animations#animated-api)。

## 概覽

`Animated` 支援兩種數值類型：

- [`Animated.Value()`](animated#value) 用於單一數值
- [`Animated.ValueXY()`](animated#valuexy) 用於向量

`Animated.Value` 可綁定至樣式屬性或其他參數，並支援插值計算。單一 `Animated.Value` 可驅動多個屬性。

### 動畫配置

`Animated` 提供三種動畫類型，每種類型對應特定的動畫曲線，控制數值從初始值變化至最終值的過程：

- [`Animated.decay()`](animated#decay) 以初始速度開始並逐漸減速至停止
- [`Animated.spring()`](animated#spring) 提供基礎彈簧物理模型
- [`Animated.timing()`](animated#timing) 使用[緩動函式](easing)實現時間軸動畫

多數情況下建議使用 `timing()`。預設採用對稱的 easeInOut 曲線，模擬物體加速至全速後再減速停止的過程。

### 動畫操作

透過呼叫動畫實例的 `start()` 啟動動畫，該方法接受一個完成回調函式。若動畫正常完成，回調將收到 `{finished: true}`；若因中途呼叫 `stop()` 中斷（例如被手勢或其他動畫打斷），則回傳 `{finished: false}`。

```tsx
Animated.timing({}).start(({finished}) => {
  /* completion callback */
});
```

### 使用原生驅動

啟用原生驅動後，動畫參數會在開始前預先傳送至原生端，使動畫能在 UI 執行緒運行，避免每幀都需跨越橋接器。動畫啟動後，即便 JavaScript 執行緒阻塞也不影響動畫表現。

在動畫配置中加入 `useNativeDriver: true` 即可啟用。詳見[動畫指南](animations#using-the-native-driver)。

### 可動畫化元件

僅特定元件支援動畫效果。這些特殊元件會將動畫數值綁定至屬性，並針對性地進行原生端更新，避免每幀都觸發 React 渲染與協調過程，同時在卸載時自動清理資源。

- 使用 [`createAnimatedComponent()`](animated#createanimatedcomponent) 可將元件轉換為可動畫化元件

`Animated` 內建以下透過封裝器實現的可動畫化元件：

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
- [`Animated.sequence()`](animated#sequence) 按順序啟動動畫，等待前一個完成後才開始下一個。
- [`Animated.stagger()`](animated#stagger) 按順序且並行啟動動畫，但具有連續延遲。

動畫也可以透過將一個動畫的 `toValue` 設為另一個 `Animated.Value` 來串聯。請參閱動畫指南中的[追蹤動態值](animations#tracking-dynamic-values)。

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

- [`interpolate()`](animated#interpolate)

有關插值的更多資訊，請參閱[動畫](animations#interpolation)指南。

### 處理手勢和其他事件

手勢（如平移或滾動）和其他事件可以使用 `Animated.event()` 直接映射到動畫值。這是透過結構化映射語法完成的，以便可以從複雜的事件對象中提取值。第一層是一個陣列，允許跨多個參數進行映射，該陣列包含嵌套對象。

- [`Animated.event()`](animated#event)

例如，在處理水平滾動手勢時，您可以執行以下操作，將 `event.nativeEvent.contentOffset.x` 映射到 `scrollX`（一個 `Animated.Value`）：

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

根據衰減係數，將值從初始速度動畫化為零。

配置是一個可能具有以下選項的對象：

- `velocity`: 初始速度。必填。
- `deceleration`: 衰減率。預設 0.997。
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「交互句柄」。預設 true。
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
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「交互句柄」。預設 true。
- `useNativeDriver`: 為 true 時使用原生驅動。必填。

---

### `spring()`

```tsx
static spring(value, config): CompositeAnimation;
```

根據[阻尼諧振子模型](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)動態計算數值變化，實現彈簧動畫效果。該方法會追蹤速度狀態以在`toValue`更新時產生流暢運動，並支持動畫鏈式調用。

配置參數為物件結構，可包含以下選項：

注意：bounciness/speed、tension/friction 或 stiffness/damping/mass 三組參數只能選擇其中一組定義：

friction/tension 或 bounciness/speed 參數組與 [`Facebook Pop`](https://github.com/facebook/pop)、[Rebound](https://github.com/facebookarchive/rebound) 及 [Origami](https://origami.design/) 的彈簧模型匹配。

- `friction`: 控制「彈性」/過衝幅度。預設值 7。
- `tension`: 控制動畫速度。預設值 40。
- `speed`: 控制動畫速度。預設值 12。
- `bounciness`: 控制彈性強度。預設值 8。

若指定 stiffness/damping/mass 參數組，`Animated.spring` 將採用基於[阻尼諧振子運動方程](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)的解析彈簧模型。此模式能更精確模擬彈簧物理特性，其行為與 iOS 的 CASpringAnimation 實現高度相似。

- `stiffness`: 彈簧剛度係數。預設值 100。
- `damping`: 定義因摩擦力導致的運動衰減程度。預設值 10。
- `mass`: 連接在彈簧末端的物體質量。預設值 1。

其他配置選項如下：

- `velocity`: 彈簧末端物體的初始速度。預設值 0（物體靜止）。
- `overshootClamping`: 布林值，決定是否限制彈簧過衝行為。預設 false。
- `restDisplacementThreshold`: 靜止位移閾值，低於此值視為靜止狀態。預設 0.001。
- `restSpeedThreshold`: 靜止速度閾值（像素/秒），低於此值視為靜止。預設 0.001。
- `delay`: 動畫延遲開始時間（毫秒）。預設 0。
- `isInteraction`: 是否在 `InteractionManager` 建立互動句柄。預設 true。
- `useNativeDriver`: 為 true 時啟用原生驅動。必填參數。

---

### `add()`

```tsx
static add(a: Animated, b: Animated): AnimatedAddition;
```

建立新的動畫數值，由兩個動畫數值相加而成。

---

### `subtract()`

```tsx
static subtract(a: Animated, b: Animated): AnimatedSubtraction;
```

建立新的動畫數值，由第一個動畫數值減去第二個動畫數值得出。

---

### `divide()`

```tsx
static divide(a: Animated, b: Animated): AnimatedDivision;
```

建立新的動畫數值，由第一個動畫數值除以第二個動畫數值得出。

---

### `multiply()`

```tsx
static multiply(a: Animated, b: Animated): AnimatedMultiplication;
```

建立新的動畫數值，由兩個動畫數值相乘而成。

---

### `modulo()`

```tsx
static modulo(a: Animated, modulus: number): AnimatedModulo;
```

建立新的動畫數值，為指定動畫數值的非負模數。

---

### `diffClamp()`

```tsx
static diffClamp(a: Animated, min: number, max: number): AnimatedDiffClamp;
```

建立一個新的動畫值，該值被限制在兩個值之間。它使用最後一個值的差異，因此即使該值遠離界限，當值開始再次接近時，它也會開始變化（`value = clamp(value + diff, min, max)`）。

這在滾動事件中非常有用，例如在向上滾動時顯示導航欄，向下滾動時隱藏它。

---

### `delay()`

```tsx
static delay(time: number): CompositeAnimation;
```

在給定的延遲後開始動畫。

---

### `sequence()`

```tsx
static sequence(animations: CompositeAnimation[]): CompositeAnimation;
```

按順序開始一系列動畫，等待每個動畫完成後再開始下一個。如果當前運行的動畫被停止，則不會開始後續的動畫。

---

### `parallel()`

```tsx
static parallel(
  animations: CompositeAnimation[],
  config?: ParallelConfig
): CompositeAnimation;
```

同時開始一系列動畫。默認情況下，如果其中一個動畫被停止，所有動畫都會被停止。你可以通過`stopTogether`標誌來覆蓋此行為。

---

### `stagger()`

```tsx
static stagger(
  time: number,
  animations: CompositeAnimation[]
): CompositeAnimation;
```

一系列動畫可以並行運行（重疊），但會以連續的延遲按順序開始。適合製作拖尾效果。

---

### `loop()`

```tsx
static loop(
  animation: CompositeAnimation[],
  config?: LoopAnimationConfig
): CompositeAnimation;
```

連續循環播放給定的動畫，每次到達結束時重置並從頭開始。如果子動畫設置為`useNativeDriver: true`，則不會阻塞JS線程。此外，循環可以防止`VirtualizedList`類組件在動畫運行時渲染更多行。你可以在子動畫配置中傳遞`isInteraction: false`來解決這個問題。

配置是一個對象，可能包含以下選項：

- `iterations`: 動畫應該循環的次數。默認為`-1`（無限）。

---

### `event()`

```tsx
static event(
  argMapping: Mapping[],
  config?: EventConfig
): (...args: any[]) => void;
```

接受一個映射數組，並從每個參數中提取相應的值，然後在映射的輸出上調用`setValue`。例如：

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
- `useNativeDriver`: 為true時使用原生驅動。必需。

---

### `forkEvent()`

```jsx
static forkEvent(event: AnimatedEvent, listener: Function): AnimatedEvent;
```

用於監聽通過props傳遞的動畫事件的高級命令式API。它允許向現有的`AnimatedEvent`添加一個新的javascript監聽器。如果`animatedEvent`是一個javascript監聽器，它會將兩個監聽器合併為一個；如果`animatedEvent`為null/undefined，則會直接分配javascript監聽器。盡可能直接使用值。

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

通過在動畫上調用start()來開始動畫。start()接受一個完成回調，該回調將在動畫完成時或在動畫完成之前調用stop()時被調用。

**參數：**

| Name     | Type                                    | Required | Description                                                                                                                                                     |
| -------- | --------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| callback | `(result: {finished: boolean}) => void` | No       | Function that will be called after the animation finished running normally or when the animation is done because stop() was called on it before it could finish |

帶回調的開始示例：

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

停止所有正在執行的動畫。

---

### `reset()`

```tsx
static reset();
```

停止所有正在執行的動畫並將值重置為原始狀態。

## 屬性

### `Value`

驅動動畫的標準值類別。通常在類別元件中初始化為 `useAnimatedValue(0)` 或 `new Animated.Value(0);`。

您可以在專屬的 [頁面](animatedvalue) 上閱讀更多關於 `Animated.Value` API 的資訊。

---

### `ValueXY`

用於驅動 2D 動畫（例如平移手勢）的 2D 值類別。

您可以在專屬的 [頁面](animatedvaluexy) 上閱讀更多關於 `Animated.ValueXY` API 的資訊。

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

將動畫值附加到視圖事件的命令式 API。如果可能，建議使用 `Animated.event` 並設置 `useNativeDriver: true`。