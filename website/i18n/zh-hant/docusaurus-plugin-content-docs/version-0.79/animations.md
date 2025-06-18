---
id: animations
title: Animations
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

動畫對於打造優質使用者體驗至關重要。靜止物體開始移動時需克服慣性，而運動中的物體具有動量，很少會立即停止。動畫能讓您在介面中傳遞符合物理規律的運動效果。

React Native 提供兩套互補的動畫系統：[`Animated`](animations#animated-api) 用於對特定值進行細粒度互動控制，[`LayoutAnimation`](animations#layoutanimation-api) 則用於全局佈局變換的動畫處理。

## `Animated` API

[`Animated`](animated) API 專為簡潔表達各種動畫與互動模式而設計，並保持高效性能。其核心在於輸入輸出的聲明式關係，中間可配置轉換過程，並通過 `start`/`stop` 方法控制基於時間的動畫執行。

`Animated` 導出六種可動畫化元件類型：`View`、`Text`、`Image`、`ScrollView`、`FlatList` 和 `SectionList`，您也可使用 `Animated.createAnimatedComponent()` 創建自定義元件。

例如，一個在掛載時淡入的容器視圖可能如下所示：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer ext=js&supportedPlatforms=ios,android
import React, {useEffect} from 'react';
import {Animated, Text, View, useAnimatedValue} from 'react-native';

const FadeInView = props => {
  const fadeAnim = useAnimatedValue(0); // Initial value for opacity: 0

  useEffect(() => {
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 10000,
      useNativeDriver: true,
    }).start();
  }, [fadeAnim]);

  return (
    <Animated.View // Special animatable View
      style={{
        ...props.style,
        opacity: fadeAnim, // Bind opacity to animated value
      }}>
      {props.children}
    </Animated.View>
  );
};

// You can then use your `FadeInView` in place of a `View` in your components:
export default () => {
  return (
    <View
      style={{
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
      }}>
      <FadeInView
        style={{
          width: 250,
          height: 50,
          backgroundColor: 'powderblue',
        }}>
        <Text style={{fontSize: 28, textAlign: 'center', margin: 10}}>
          Fading in
        </Text>
      </FadeInView>
    </View>
  );
};
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer ext=tsx
import React, {useEffect} from 'react';
import {Animated, Text, View, useAnimatedValue} from 'react-native';
import type {PropsWithChildren} from 'react';
import type {ViewStyle} from 'react-native';

type FadeInViewProps = PropsWithChildren<{style: ViewStyle}>;

const FadeInView: React.FC<FadeInViewProps> = props => {
  const fadeAnim = useAnimatedValue(0); // Initial value for opacity: 0

  useEffect(() => {
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 10000,
      useNativeDriver: true,
    }).start();
  }, [fadeAnim]);

  return (
    <Animated.View // Special animatable View
      style={{
        ...props.style,
        opacity: fadeAnim, // Bind opacity to animated value
      }}>
      {props.children}
    </Animated.View>
  );
};

// You can then use your `FadeInView` in place of a `View` in your components:
export default () => {
  return (
    <View
      style={{
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
      }}>
      <FadeInView
        style={{
          width: 250,
          height: 50,
          backgroundColor: 'powderblue',
        }}>
        <Text style={{fontSize: 28, textAlign: 'center', margin: 10}}>
          Fading in
        </Text>
      </FadeInView>
    </View>
  );
};
```

</TabItem>
</Tabs>

解析這段代碼：在 `FadeInView` 渲染方法中，通過 `useRef` 初始化名為 `fadeAnim` 的 `Animated.Value`。`View` 的透明度屬性映射至此動畫值，底層會提取數值來設置透明度。

組件掛載時，透明度設為 0。隨後在 `fadeAnim` 上啟動緩動動畫，該動畫值會逐幀更新所有依賴映射（本例僅透明度屬性），直到最終值達到 1。

此過程經過優化，比調用 `setState` 重新渲染更高效。由於整個配置是聲明式的，未來可實現更多優化，例如序列化配置並在高優先級線程運行動畫。

### 配置動畫

動畫具有高度可配置性。自定義或預定義的緩動函數、延遲、持續時間、衰減因子、彈簧常數等參數，均可根據動畫類型調整。

`Animated` 提供多種動畫類型，最常用的是 [`Animated.timing()`](animated#timing)。它支持使用預定義緩動函數或自定義函數隨時間變化數值。緩動函數通常用於表現物體逐漸加速或減速的效果。

默認情況下，`timing` 使用 easeInOut 曲線實現加速至全速後逐漸停止的效果。您可通過 `easing` 參數指定其他緩動函數，同時支持自定義 `duration` 或設置動畫開始前的 `delay`。

例如要創建一個 2 秒長的動畫，讓物體在到達最終位置前輕微後退：

```tsx
Animated.timing(this.state.xPosition, {
  toValue: 100,
  easing: Easing.back(),
  duration: 2000,
  useNativeDriver: true,
}).start();
```

查閱 `Animated` API 參考的[配置動畫](animated#configuring-animations)章節，了解內建動畫支持的所有配置參數。

### 組合動畫

動畫可組合並以序列或並行方式播放。序列動畫可在前一個動畫結束後立即播放，或延遲指定時間啟動。`Animated` API 提供 `sequence()` 和 `delay()` 等方法，這些方法接收動畫數組並自動按需調用 `start()`/`stop()`。

例如以下動畫先滑行停止，然後並行執行彈回與旋轉：

```tsx
Animated.sequence([
  // decay, then spring to start and twirl
  Animated.decay(position, {
    // coast to a stop
    velocity: {x: gestureState.vx, y: gestureState.vy}, // velocity from gesture release
    deceleration: 0.997,
    useNativeDriver: true,
  }),
  Animated.parallel([
    // after decay, in parallel:
    Animated.spring(position, {
      toValue: {x: 0, y: 0}, // return to start
      useNativeDriver: true,
    }),
    Animated.timing(twirl, {
      // and twirl
      toValue: 360,
      useNativeDriver: true,
    }),
  ]),
]).start(); // start the sequence group
```

若某個動畫被停止或中斷，同組內其他動畫也會連帶停止。`Animated.parallel`提供`stopTogether`選項，設為`false`可禁用此行為。

完整合成方法清單請參閱`Animated` API參考文件的[動畫合成](animated#composing-animations)章節。

### 合併動畫數值

可透過[加減乘除或取模運算](animated#combining-animated-values)將兩個動畫數值合併為新數值。

某些情況下需用反向計算，例如縮放比例反轉（2倍→0.5倍）：

```tsx
const a = new Animated.Value(1);
const b = Animated.divide(1, a);

Animated.spring(a, {
  toValue: 2,
  useNativeDriver: true,
}).start();
```

### 插值計算

每個屬性都可先進行插值計算。插值將輸入範圍映射到輸出範圍，預設採用線性插值但支援緩動函數。預設情況下會外推曲線超出給定範圍，也可設置為箝制輸出值。

將0-1範圍轉換為0-100範圍的基本映射範例：

```tsx
value.interpolate({
  inputRange: [0, 1],
  outputRange: [0, 100],
});
```

例如您可能希望`Animated.Value`從0變化到1，但實際要讓位置從150px移動到0px，同時透明度從0變為1。可透過修改樣式實現：

```tsx
  style={{
    opacity: this.state.fadeAnim, // Binds directly
    transform: [{
      translateY: this.state.fadeAnim.interpolate({
        inputRange: [0, 1],
        outputRange: [150, 0]  // 0 : 150, 0.5 : 75, 1 : 0
      }),
    }],
  }}
```

[`interpolate()`](animated#interpolate)支援多段範圍定義，適用於死區設定等進階技巧。例如要實現-300時為負值、-100到0時回升、0到100時再下降，超過100後保持死區零值的映射：

```tsx
value.interpolate({
  inputRange: [-300, -100, 0, 100, 101],
  outputRange: [300, 0, 1, 0, 0],
});
```

映射關係如下：

```
Input | Output
------|-------
  -400|    450
  -300|    300
  -200|    150
  -100|      0
   -50|    0.5
     0|      1
    50|    0.5
   100|      0
   101|      0
   200|      0
```

`interpolate()`亦支援字串映射，可實現顏色漸變與單位數值動畫。例如旋轉動畫可這樣實現：

```tsx
value.interpolate({
  inputRange: [0, 360],
  outputRange: ['0deg', '360deg'],
});
```

`interpolate()`支援任意緩動函數（[`Easing`](easing)模組已內建多種），並可透過`extrapolate`、`extrapolateLeft`或`extrapolateRight`配置外推行為。預設值為`extend`，使用`clamp`可限制輸出值不超出`outputRange`。

### 追蹤動態數值

動畫數值可透過將`toValue`設為另一個動畫數值（而非固定數字）來實現聯動效果。例如Android版Messenger的「聊天頭像」動畫，可用`spring()`綁定其他動畫值實現，或使用`duration`設為0的`timing()`進行剛性追蹤。這些數值還能與插值結合：

```tsx
Animated.spring(follower, {toValue: leader}).start();
Animated.timing(opacity, {
  toValue: pan.x.interpolate({
    inputRange: [0, 300],
    outputRange: [1, 0],
  }),
  useNativeDriver: true,
}).start();
```

範例中的`leader`與`follower`動畫值需使用`Animated.ValueXY()`實現。`ValueXY`是處理二維交互（如平移或拖拽）的實用工具，它封裝了兩個`Animated.Value`實例及相關輔助方法，在多數情況下可直接替代`Value`使用，能同時追蹤x與y軸數值。

### 追蹤手勢

透過[`Animated.event`](animated#event)可將平移/滾動手勢等事件直接映射到動畫數值。採用結構化映射語法，能從複雜事件對象提取數值。第一層為數組結構以支持多參數映射，數組內包含嵌套對象。

舉例來說，若要將水平滾動手勢的 `event.nativeEvent.contentOffset.x` 映射到 `scrollX`（一個 `Animated.Value`），可採用以下方式：

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

以下範例實現了一個水平滾動輪播，其中滾動位置指示器透過 `ScrollView` 中的 `Animated.event` 進行動畫處理：

#### 使用動畫事件的 ScrollView 範例

```SnackPlayer name=Animated&supportedPlatforms=ios,android
import React from 'react';
import {
  ScrollView,
  Text,
  StyleSheet,
  View,
  ImageBackground,
  Animated,
  useWindowDimensions,
  useAnimatedValue,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const images = new Array(6).fill(
  'https://images.unsplash.com/photo-1556740749-887f6717d7e4',
);

const App = () => {
  const scrollX = useAnimatedValue(0);

  const {width: windowWidth} = useWindowDimensions();

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <View style={styles.scrollContainer}>
          <ScrollView
            horizontal={true}
            pagingEnabled
            showsHorizontalScrollIndicator={false}
            onScroll={Animated.event([
              {
                nativeEvent: {
                  contentOffset: {
                    x: scrollX,
                  },
                },
              },
            ])}
            scrollEventThrottle={1}>
            {images.map((image, imageIndex) => {
              return (
                <View
                  style={{width: windowWidth, height: 250}}
                  key={imageIndex}>
                  <ImageBackground source={{uri: image}} style={styles.card}>
                    <View style={styles.textContainer}>
                      <Text style={styles.infoText}>
                        {'Image - ' + imageIndex}
                      </Text>
                    </View>
                  </ImageBackground>
                </View>
              );
            })}
          </ScrollView>
          <View style={styles.indicatorContainer}>
            {images.map((image, imageIndex) => {
              const width = scrollX.interpolate({
                inputRange: [
                  windowWidth * (imageIndex - 1),
                  windowWidth * imageIndex,
                  windowWidth * (imageIndex + 1),
                ],
                outputRange: [8, 16, 8],
                extrapolate: 'clamp',
              });
              return (
                <Animated.View
                  key={imageIndex}
                  style={[styles.normalDot, {width}]}
                />
              );
            })}
          </View>
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
  scrollContainer: {
    height: 300,
    alignItems: 'center',
    justifyContent: 'center',
  },
  card: {
    flex: 1,
    marginVertical: 4,
    marginHorizontal: 16,
    borderRadius: 5,
    overflow: 'hidden',
    alignItems: 'center',
    justifyContent: 'center',
  },
  textContainer: {
    backgroundColor: 'rgba(0,0,0, 0.7)',
    paddingHorizontal: 24,
    paddingVertical: 8,
    borderRadius: 5,
  },
  infoText: {
    color: 'white',
    fontSize: 16,
    fontWeight: 'bold',
  },
  normalDot: {
    height: 8,
    width: 8,
    borderRadius: 4,
    backgroundColor: 'silver',
    marginHorizontal: 4,
  },
  indicatorContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default App;
```

使用 `PanResponder` 時，可透過以下程式碼從 `gestureState.dx` 和 `gestureState.dy` 提取 x 和 y 座標。由於我們僅關注傳遞給 `PanResponder` 處理器的第二個參數（即 `gestureState`），因此陣列的第一個位置設為 `null`：

```tsx
onPanResponderMove={Animated.event(
  [null, // ignore the native event
  // extract dx and dy from gestureState
  // like 'pan.x = gestureState.dx, pan.y = gestureState.dy'
  {dx: pan.x, dy: pan.y}
])}
```

#### 使用動畫事件的 PanResponder 範例

```SnackPlayer name=Animated
import React, {useRef} from 'react';
import {Animated, View, StyleSheet, PanResponder, Text} from 'react-native';

const App = () => {
  const pan = useRef(new Animated.ValueXY()).current;
  const panResponder = useRef(
    PanResponder.create({
      onMoveShouldSetPanResponder: () => true,
      onPanResponderMove: Animated.event([null, {dx: pan.x, dy: pan.y}]),
      onPanResponderRelease: () => {
        Animated.spring(pan, {
          toValue: {x: 0, y: 0},
          useNativeDriver: true,
        }).start();
      },
    }),
  ).current;

  return (
    <View style={styles.container}>
      <Text style={styles.titleText}>Drag & Release this box!</Text>
      <Animated.View
        style={{
          transform: [{translateX: pan.x}, {translateY: pan.y}],
        }}
        {...panResponder.panHandlers}>
        <View style={styles.box} />
      </Animated.View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  titleText: {
    fontSize: 14,
    lineHeight: 24,
    fontWeight: 'bold',
  },
  box: {
    height: 150,
    width: 150,
    backgroundColor: 'blue',
    borderRadius: 5,
  },
});

export default App;
```

### 響應當前的動畫值

您可能會注意到，在動畫進行時無法直接讀取當前值。這是因為由於優化機制，該值可能僅在原生運行時中可知。若需根據當前值執行 JavaScript，有兩種解決方案：

- `spring.stopAnimation(callback)` 會停止動畫並以最終值觸發 `callback`。此方法適用於手勢轉換情境。
- `spring.addListener(callback)` 會在動畫運行時異步觸發 `callback`，提供最新值。此方法適用於觸發狀態變化，例如當用戶拖曳時將浮動控件吸附至新選項，因為這類較大的狀態變化對少數幀的延遲不如需以 60 fps 運行的連續手勢（如平移）敏感。

`Animated` 的設計具備完全可序列化特性，使動畫能獨立於常規 JavaScript 事件循環高效運行。這會影響 API 的設計方式，因此當某些操作比全同步系統更複雜時請理解此特性。可透過 `Animated.Value.addListener` 突破部分限制，但請謹慎使用，因其可能影響未來效能表現。

### 使用原生驅動

`Animated` API 設計為可序列化結構。透過[原生驅動](/blog/2017/02/14/using-native-driver-for-animated)，我們在動畫開始前將所有相關參數傳送至原生端，使原生代碼能在 UI 線程執行動畫，無需每幀都透過橋接通訊。動畫啟動後，即使 JavaScript 線程阻塞也不影響動畫運行。

為常規動畫啟用原生驅動時，只需在動畫配置中設定 `useNativeDriver: true`。未指定此屬性的動畫基於相容性考量會預設為 `false`，但會觸發警告（TypeScript 中會顯示類型檢查錯誤）：

```tsx
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // <-- Set this to true
}).start();
```

動畫值僅相容單一驅動模式，因此若對某值啟用原生驅動，請確保該值的所有動畫皆使用原生驅動。

原生驅動也可與 `Animated.event` 協同工作。這對於跟隨滾動位置的動畫特別有用，因為若無原生驅動，由於 React Native 的異步特性，動畫幀率將永遠落後於手勢操作。

```tsx
<Animated.ScrollView // <-- Use the Animated ScrollView wrapper
  onScroll={Animated.event(
    [
      {
        nativeEvent: {
          contentOffset: {y: this.state.animatedValue},
        },
      },
    ],
    {useNativeDriver: true}, // <-- Set this to true
  )}>
  {content}
</Animated.ScrollView>
```

您可透過運行 [RNTester 應用程式](https://github.com/facebook/react-native/blob/main/packages/rn-tester/)並載入「原生動畫範例」觀察原生驅動的實際效果。也可查閱[原始碼](https://github.com/facebook/react-native/blob/master/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js)了解這些範例的實現方式。

#### 注意事項

並非所有使用 `Animated` 的功能目前都受到原生驅動程式的支援。主要的限制是您只能動畫化非佈局屬性：例如 `transform` 和 `opacity` 會起作用，但 Flexbox 和位置屬性則不會。當使用 `Animated.event` 時，它僅適用於直接事件，而不適用於冒泡事件。這意味著它不適用於 `PanResponder`，但適用於像 `ScrollView#onScroll` 這樣的功能。

當動畫運行時，它可能會阻止 `VirtualizedList` 元件渲染更多行。如果您需要在用戶滾動列表時運行長時間或循環的動畫，您可以在動畫配置中使用 `isInteraction: false` 來避免這個問題。

### 注意事項

在使用諸如 `rotateY`、`rotateX` 等變換樣式時，請確保變換樣式 `perspective` 已設置。目前某些動畫在沒有它的情況下可能無法在 Android 上渲染。以下是一個例子。

```tsx
<Animated.View
  style={{
    transform: [
      {scale: this.state.scale},
      {rotateY: this.state.rotateY},
      {perspective: 1000}, // without this line this Animation will not render on Android while working fine on iOS
    ],
  }}
/>
```

### 其他範例

RNTester 應用程式中有多個使用 `Animated` 的範例：

- [AnimatedGratuitousApp](https://github.com/facebook/react-native/tree/main/packages/rn-tester/js/examples/AnimatedGratuitousApp)
- [NativeAnimationsExample](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js)

## `LayoutAnimation` API

`LayoutAnimation` 允許您全局配置 `create` 和 `update` 動畫，這些動畫將用於下一個渲染/佈局週期中的所有視圖。這對於在不需測量或計算特定屬性的情況下進行 Flexbox 佈局更新非常有用，尤其當佈局更改可能影響祖先視圖時，例如一個「查看更多」的擴展同時增加了父視圖的大小並向下推動了下面的行，這通常需要元件之間的明確協調才能同步動畫化它們。

請注意，儘管 `LayoutAnimation` 非常強大且相當有用，但它提供的控制比 `Animated` 和其他動畫庫少得多，因此如果您無法讓 `LayoutAnimation` 達到您的需求，可能需要使用其他方法。

請注意，為了讓這在 **Android** 上起作用，您需要通過 `UIManager` 設置以下標誌：

```tsx
UIManager.setLayoutAnimationEnabledExperimental(true);
```

```SnackPlayer name=LayoutAnimations&supportedPlatforms=ios,android
import React from 'react';
import {
  NativeModules,
  LayoutAnimation,
  Text,
  TouchableOpacity,
  StyleSheet,
  View,
} from 'react-native';

const {UIManager} = NativeModules;

UIManager.setLayoutAnimationEnabledExperimental &&
  UIManager.setLayoutAnimationEnabledExperimental(true);

export default class App extends React.Component {
  state = {
    w: 100,
    h: 100,
  };

  _onPress = () => {
    // Animate the update
    LayoutAnimation.spring();
    this.setState({w: this.state.w + 15, h: this.state.h + 15});
  };

  render() {
    return (
      <View style={styles.container}>
        <View
          style={[styles.box, {width: this.state.w, height: this.state.h}]}
        />
        <TouchableOpacity onPress={this._onPress}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>Press me!</Text>
          </View>
        </TouchableOpacity>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  box: {
    width: 200,
    height: 200,
    backgroundColor: 'red',
  },
  button: {
    backgroundColor: 'black',
    paddingHorizontal: 20,
    paddingVertical: 15,
    marginTop: 15,
  },
  buttonText: {
    color: '#fff',
    fontWeight: 'bold',
  },
});
```

這個例子使用了一個預設值，您可以根據需要自定義動畫，更多信息請參閱 [LayoutAnimation.js](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/LayoutAnimation/LayoutAnimation.js)。

## 其他注意事項

### `requestAnimationFrame`

`requestAnimationFrame` 是一個您可能熟悉的瀏覽器 polyfill。它接受一個函數作為其唯一參數，並在下一次重繪之前調用該函數。它是所有基於 JavaScript 的動畫 API 的基本構建塊。通常，您不需要自己調用它——動畫 API 會為您管理幀更新。

### `setNativeProps`

如[直接操作部分](legacy/direct-manipulation)所述，`setNativeProps` 允許我們直接修改原生支持的元件（實際由原生視圖支持的元件，與複合元件不同）的屬性，而無需 `setState` 和重新渲染元件層次結構。

我們可以在 Rebound 範例中使用它來更新縮放比例——如果我們正在更新的元件深度嵌套且未使用 `shouldComponentUpdate` 進行優化，這可能會有所幫助。

如果您發現動畫出現掉幀（低於每秒60幀）的情況，可以考慮使用 `setNativeProps` 或 `shouldComponentUpdate` 來優化。或者，您也可以選擇透過 [useNativeDriver 選項](/blog/2017/02/14/using-native-driver-for-animated) 將動畫運行在 UI 執行緒而非 JavaScript 執行緒上。此外，建議使用 [InteractionManager](interactionmanager) 將計算密集型工作延遲到動畫完成後執行。您可透過內建開發選單中的「FPS 監測工具」來監控幀率。