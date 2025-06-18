---
id: animated
title: Animated
---

`Animated` 函式庫專為打造流暢、強大且易於建置維護的動畫而設計。它聚焦於輸入與輸出間的宣告式關係，可配置的中間轉換過程，以及透過 `start`/`stop` 方法來控制基於時間軸的動畫執行。

建立動畫的核心流程是：創建一個 `Animated.Value`，將其綁定至動畫元件的一個或多個樣式屬性，然後透過 `Animated.timing()` 驅動動畫更新。

> 請勿直接修改動畫值。您可以使用 [`useRef` Hook](https://reactjs.org/docs/hooks-reference.html#useref) 返回一個可變的 ref 物件，該物件的 `current` 屬性會被初始化為傳入參數，並在元件生命週期內持續存在。

## 範例

以下範例包含一個 `View`，其透明度會根據動畫值 `fadeAnim` 進行淡入淡出效果：

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

請參閱 [動畫指南](animations#animated-api) 以查看更多實際運用 `Animated` 的範例。

## 概覽

`Animated` 提供兩種數值類型可供使用：

- [`Animated.Value()`](animated#value) 用於單一數值
- [`Animated.ValueXY()`](animated#valuexy) 用於向量

`Animated.Value` 可綁定至樣式屬性或其他屬性，並可進行插值計算。單一 `Animated.Value` 能驅動任意數量的屬性。

### 設定動畫

`Animated` 提供三種動畫類型，每種類型都有特定的動畫曲線，控制數值如何從初始值變化至最終值：

- [`Animated.decay()`](animated#decay) 以初始速度開始並逐漸減速至停止
- [`Animated.spring()`](animated#spring) 提供基礎彈簧物理模型
- [`Animated.timing()`](animated#timing) 使用[緩動函式](easing)隨時間變化動畫數值

多數情況下會使用 `timing()`。預設採用對稱的 easeInOut 曲線，模擬物體逐漸加速至全速後再減速停止的過程。

### 動畫操作

透過呼叫動畫的 `start()` 方法啟動動畫。`start()` 接受一個完成回調函式，當動畫結束時會被呼叫。若動畫正常完成，回調函式會收到 `{finished: true}`；若因中途呼叫 `stop()` 而中止（例如被手勢或其他動畫打斷），則會收到 `{finished: false}`。

```tsx
Animated.timing({}).start(({finished}) => {
  /* completion callback */
});
```

### 使用原生驅動

啟用原生驅動後，系統會在動畫開始前將所有動畫參數傳送至原生端，讓原生代碼能在 UI 線程執行動畫，無需每幀都透過橋接通訊。動畫啟動後，即使 JS 線程阻塞也不會影響動畫運行。

在動畫配置中指定 `useNativeDriver: true` 即可啟用原生驅動。詳見[動畫指南](animations#using-the-native-driver)。

### 可動畫化元件

只有可動畫化元件才能應用動畫效果。這些特殊元件會將動畫值綁定至屬性，並透過針對性的原生更新來避免每幀都觸發 React 渲染與協調過程的開銷，同時會在卸載時自動處理清理工作確保安全性。

- 使用 [`createAnimatedComponent()`](animated#createanimatedcomponent) 可將元件轉為可動畫化。

`Animated` 導出以下透過上述封裝器處理的可動畫化元件：

- `Animated.Image`
- `Animated.ScrollView`
- `Animated.Text`
- `Animated.View`
- `Animated.FlatList`
- `Animated.SectionList`

### 動畫組合

動畫也可以透過組合函數以複雜的方式結合：

- [`Animated.delay()`](animated#delay) 在指定延遲後開始動畫。
- [`Animated.parallel()`](animated#parallel) 同時開始多個動畫。
- [`Animated.sequence()`](animated#sequence) 按順序開始動畫，等待每個動畫完成後才開始下一個。
- [`Animated.stagger()`](animated#stagger) 按順序並行開始動畫，但具有連續的延遲。

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

`interpolate()` 函數允許將輸入範圍映射到不同的輸出範圍。預設情況下，它會將曲線外推到給定的範圍之外，但您也可以讓它限制輸出值。它預設使用線性插值，但也支援緩動函數。

- [`interpolate()`](animated#interpolate)

有關插值的更多資訊，請參閱[動畫](animations#interpolation)指南。

### 處理手勢和其他事件

手勢（如平移或滾動）和其他事件可以使用 `Animated.event()` 直接映射到動畫值。這是透過結構化映射語法完成的，以便可以從複雜的事件對象中提取值。第一層是一個數組，允許跨多個參數進行映射，該數組包含嵌套的對象。

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

- `velocity`: 初始速度。必需。
- `deceleration`: 衰減率。預設 0.997。
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「互動句柄」。預設 true。
- `useNativeDriver`: 為 true 時使用原生驅動。必需。

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
- `isInteraction`: 此動畫是否在 `InteractionManager` 上創建「互動句柄」。預設 true。
- `useNativeDriver`: 為 true 時使用原生驅動。必需。

---

### `spring()`

```tsx
static spring(value, config): CompositeAnimation;
```

根據[阻尼諧振子](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)的解析彈簧模型動畫化數值。追蹤速度狀態以在`toValue`更新時創建流暢運動，並可串聯使用。

配置為一個物件，可能包含以下選項。

注意您只能定義以下其中一組參數，不可同時定義多組：

摩擦/張力或彈性/速度選項與[`Facebook Pop`](https://github.com/facebook/pop)、[Rebound](https://github.com/facebookarchive/rebound)和[Origami](https://origami.design/)中的彈簧模型匹配。

- `friction`: 控制「彈性」/過衝。預設值7。
- `tension`: 控制速度。預設值40。
- `speed`: 控制動畫速度。預設值12。
- `bounciness`: 控制彈性。預設值8。

指定剛度/阻尼/質量作為參數會使`Animated.spring`使用基於[阻尼諧振子](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator)運動方程的解析彈簧模型。此行為更精確且忠實於彈簧動力學背後的物理原理，並密切模仿iOS中CASpringAnimation的實現。

- `stiffness`: 彈簧剛度係數。預設值100。
- `damping`: 定義彈簧運動應如何因摩擦力而衰減。預設值10。
- `mass`: 附著在彈簧末端的物體質量。預設值1。

其他配置選項如下：

- `velocity`: 附著在彈簧上物體的初始速度。預設0（物體靜止）。
- `overshootClamping`: 布林值，指示彈簧是否應夾緊且不反彈。預設false。
- `restDisplacementThreshold`: 靜止位移閾值，低於此值彈簧應視為靜止。預設0.001。
- `restSpeedThreshold`: 彈簧應視為靜止的速度，單位為像素/秒。預設0.001。
- `delay`: 延遲後開始動畫（毫秒）。預設0。
- `isInteraction`: 此動畫是否在`InteractionManager`上創建「交互句柄」。預設true。
- `useNativeDriver`: 為true時使用原生驅動。必需。

---

### `add()`

```tsx
static add(a: Animated, b: Animated): AnimatedAddition;
```

創建一個由兩個動畫值相加組成的新動畫值。

---

### `subtract()`

```tsx
static subtract(a: Animated, b: Animated): AnimatedSubtraction;
```

創建一個由第一個動畫值減去第二個動畫值組成的新動畫值。

---

### `divide()`

```tsx
static divide(a: Animated, b: Animated): AnimatedDivision;
```

創建一個由第一個動畫值除以第二個動畫值組成的新動畫值。

---

### `multiply()`

```tsx
static multiply(a: Animated, b: Animated): AnimatedMultiplication;
```

創建一個由兩個動畫值相乘組成的新動畫值。

---

### `modulo()`

```tsx
static modulo(a: Animated, modulus: number): AnimatedModulo;
```

創建一個新動畫值，該值是所提供動畫值的（非負）模數。

---

### `diffClamp()`

```tsx
static diffClamp(a: Animated, min: number, max: number): AnimatedDiffClamp;
```

創建一個新的動畫值，該值被限制在兩個值之間。它使用最後一個值的差異，因此即使該值遠離邊界，當值開始再次接近時，它也會開始變化（`value = clamp(value + diff, min, max)`）。

這在滾動事件中非常有用，例如，在向上滾動時顯示導航欄，向下滾動時隱藏它。

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

按順序啟動一系列動畫，等待每個動畫完成後再開始下一個。如果當前運行的動畫被停止，後續的動畫將不會啟動。

---

### `parallel()`

```tsx
static parallel(
  animations: CompositeAnimation[],
  config?: ParallelConfig
): CompositeAnimation;
```

同時啟動一系列動畫。默認情況下，如果其中一個動畫被停止，所有動畫都會被停止。你可以通過 `stopTogether` 標誌來覆蓋此行為。

---

### `stagger()`

```tsx
static stagger(
  time: number,
  animations: CompositeAnimation[]
): CompositeAnimation;
```

一系列動畫可以並行運行（重疊），但會按順序以連續的延遲啟動。非常適合製作拖尾效果。

---

### `loop()`

```tsx
static loop(
  animation: CompositeAnimation[],
  config?: LoopAnimationConfig
): CompositeAnimation;
```

循環播放給定的動畫，每次到達結束時重置並從頭開始。如果子動畫設置為 `useNativeDriver: true`，則不會阻塞 JS 線程。此外，循環可能會阻止基於 `VirtualizedList` 的組件在動畫運行時渲染更多行。你可以在子動畫配置中傳遞 `isInteraction: false` 來解決這個問題。

配置是一個可能包含以下選項的對象：

- `iterations`: 動畫應該循環的次數。默認為 `-1`（無限）。

---

### `event()`

```tsx
static event(
  argMapping: Mapping[],
  config?: EventConfig
): (...args: any[]) => void;
```

接受一個映射數組，並從每個參數中提取相應的值，然後在映射的輸出上調用 `setValue`。例如：

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

配置是一個可能包含以下選項的對象：

- `listener`: 可選的異步監聽器。
- `useNativeDriver`: 為 true 時使用原生驅動。必需。

---

### `forkEvent()`

```jsx
static forkEvent(event: AnimatedEvent, listener: Function): AnimatedEvent;
```

用於監聽通過 props 傳遞的動畫事件的高級命令式 API。它允許向現有的 `AnimatedEvent` 添加新的 JavaScript 監聽器。如果 `animatedEvent` 是一個 JavaScript 監聽器，它會將兩個監聽器合併為一個；如果 `animatedEvent` 為 null/undefined，則會直接分配 JavaScript 監聽器。盡可能直接使用值。

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

通過在動畫上調用 start() 來啟動動畫。start() 接受一個完成回調，該回調將在動畫完成時或在動畫完成之前調用 stop() 時被調用。

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

將動畫值附加到視圖事件的命令式 API。如果可能，建議使用帶有 `useNativeDriver: true` 的 `Animated.event`。