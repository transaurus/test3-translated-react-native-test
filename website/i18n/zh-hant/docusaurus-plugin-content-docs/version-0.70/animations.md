---
id: animations
title: Animations
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

動畫對於打造優秀的使用者體驗至關重要。靜止的物體開始移動時需克服慣性，而運動中的物體具有動量，很少會立即停止。動畫能讓您在介面中傳遞符合物理規律的運動效果。

React Native 提供兩套互補的動畫系統：[`Animated`](animations#animated-api) 用於對特定值進行細粒度互動控制，[`LayoutAnimation`](animations#layoutanimation-api) 則用於全局佈局變換的動畫處理。

## `Animated` API

[`Animated`](animated) API 的設計旨在以高效能的方式簡潔表達各種動畫與互動模式。該 API 專注於輸入與輸出間的宣告式關係，中間可配置轉換過程，並透過 `start`/`stop` 方法控制基於時間軸的動畫執行。

`Animated` 導出六種可動畫化元件類型：`View`、`Text`、`Image`、`ScrollView`、`FlatList` 和 `SectionList`，您也可以使用 `Animated.createAnimatedComponent()` 創建自訂元件。

例如，一個在掛載時淡入的容器視圖可能如下所示：

```SnackPlayer
import React, { useRef, useEffect } from 'react';
import { Animated, Text, View } from 'react-native';

const FadeInView = (props) => {
  const fadeAnim = useRef(new Animated.Value(0)).current  // Initial value for opacity: 0

  useEffect(() => {
    Animated.timing(
      fadeAnim,
      {
        toValue: 1,
        duration: 10000,
      }
    ).start();
  }, [fadeAnim])

  return (
    <Animated.View                 // Special animatable View
      style={{
        ...props.style,
        opacity: fadeAnim,         // Bind opacity to animated value
      }}
    >
      {props.children}
    </Animated.View>
  );
}

// You can then use your `FadeInView` in place of a `View` in your components:
export default () => {
  return (
    <View style={{flex: 1, alignItems: 'center', justifyContent: 'center'}}>
      <FadeInView style={{width: 250, height: 50, backgroundColor: 'powderblue'}}>
        <Text style={{fontSize: 28, textAlign: 'center', margin: 10}}>Fading in</Text>
      </FadeInView>
    </View>
  )
}
```

讓我們解析這段程式碼。在 `FadeInView` 建構函式中，初始化了名為 `fadeAnim` 的 `Animated.Value` 並存入 `state`。`View` 的透明度屬性被映射到這個動畫值。在底層，數值會被提取並用於設置透明度。

當元件掛載時，透明度設為 0。接著在 `fadeAnim` 動畫值上啟動緩動動畫，該值會在每幀更新時從初始值漸變到最終值 1，同時更新所有依賴映射（此例中僅透明度屬性）。

這種實現方式經過優化，比呼叫 `setState` 重新渲染更高效。由於整個配置是宣告式的，未來還能實現將配置序列化並在高優先級線程運行動畫的進一步優化。

### 配置動畫

動畫具有高度可配置性。自訂與預設的緩動函數、延遲、持續時間、衰減因子、彈簧常數等參數，均可根據動畫類型進行調整。

`Animated` 提供多種動畫類型，最常用的是 [`Animated.timing()`](animated#timing)。它支援使用預定義緩動函數或自訂函數隨時間變化改變數值。緩動函數通常用於表現物體逐漸加速或減速的動畫效果。

預設情況下，`timing` 會使用 easeInOut 曲線，表現為逐漸加速至全速後再逐漸減速停止。您可透過傳遞 `easing` 參數指定其他緩動函數。同時支援設定自訂 `duration` 或動畫開始前的 `delay` 延遲。

例如，若要創建一個時長 2 秒的動畫，讓物體在到達最終位置前先輕微後退：

```jsx
Animated.timing(this.state.xPosition, {
  toValue: 100,
  easing: Easing.back(),
  duration: 2000,
}).start();
```

請參閱 `Animated` API 參考文件的[配置動畫](animated#configuring-animations)章節，了解內建動畫支援的所有配置參數。

### 組合動畫

動畫可組合並以序列或並行方式播放。序列動畫可在前一動畫結束後立即播放，或延遲指定時間啟動。`Animated` API 提供如 `sequence()` 和 `delay()` 等方法，這些方法接收動畫陣列並自動按需呼叫 `start()`/`stop()`。

例如，以下動畫先滑行至停止，然後並行執行彈回與旋轉效果：

```jsx
Animated.sequence([
  // decay, then spring to start and twirl
  Animated.decay(position, {
    // coast to a stop
    velocity: {x: gestureState.vx, y: gestureState.vy}, // velocity from gesture release
    deceleration: 0.997,
  }),
  Animated.parallel([
    // after decay, in parallel:
    Animated.spring(position, {
      toValue: {x: 0, y: 0}, // return to start
    }),
    Animated.timing(twirl, {
      // and twirl
      toValue: 360,
    }),
  ]),
]).start(); // start the sequence group
```

若某個動畫被停止或中斷，群組中其他動畫也會同步停止。可將 `Animated.parallel` 的 `stopTogether` 選項設為 `false` 來禁用此行為。

您可以在 `Animated` API 參考文件的[組合動畫](animated#composing-animations)章節中找到完整的組合方法清單。

### 合併動畫數值

您可以透過加法、乘法、除法或模運算來[合併兩個動畫數值](animated#combining-animated-values)，以產生新的動畫數值。

某些情況下需要反轉動畫數值進行計算，例如縮放反轉（2倍 → 0.5倍）：

```jsx
const a = new Animated.Value(1);
const b = Animated.divide(1, a);

Animated.spring(a, {
  toValue: 2,
}).start();
```

### 插值運算

每個屬性都可以先進行插值運算。插值將輸入範圍映射到輸出範圍，通常使用線性插值，但也支援緩動函數。預設情況下會外推曲線超出給定範圍，但您也可以設定輸出值箝位。

將 0-1 範圍轉換為 0-100 範圍的基本映射如下：

```jsx
value.interpolate({
  inputRange: [0, 1],
  outputRange: [0, 100],
});
```

例如，您可能希望將 `Animated.Value` 視為從 0 到 1，但實際動畫效果是位置從 150px 移動到 0px，透明度從 0 變化到 1。可以透過修改上述範例中的 `style` 來實現：

```jsx
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

[`interpolate()`](animated#interpolate) 還支援多範圍分段，這對於定義死區和其他技巧非常有用。例如要建立 -300 處的反向關係，在 -100 處歸零，0 處回升到 1，100 處再次降為零，之後進入保持為零的死區：

```jsx
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

`interpolate()` 也支援字串映射，可實現顏色和單位數值的動畫。例如要實現旋轉動畫：

```jsx
value.interpolate({
  inputRange: [0, 360],
  outputRange: ['0deg', '360deg'],
});
```

`interpolate()` 支援任意緩動函數，許多已實現在 [`Easing`](easing) 模組中。還可配置外推行為，透過設定 `extrapolate`、`extrapolateLeft` 或 `extrapolateRight` 選項。預設值為 `extend`，也可使用 `clamp` 防止輸出值超出 `outputRange`。

### 追蹤動態數值

動畫數值還能透過將動畫的 `toValue` 設為另一個動畫數值（而非純數字）來追蹤其他值。例如 Android 版 Messenger 使用的「聊天頭像」動畫，可用 `spring()` 固定於另一動畫值實現，或用 `duration` 為 0 的 `timing()` 實現嚴格追蹤。還能與插值結合：

```jsx
Animated.spring(follower, {toValue: leader}).start();
Animated.timing(opacity, {
  toValue: pan.x.interpolate({
    inputRange: [0, 300],
    outputRange: [1, 0],
  }),
}).start();
```

`leader` 和 `follower` 動畫數值會使用 `Animated.ValueXY()` 實現。`ValueXY` 是處理二維互動（如平移或拖曳）的便利工具，它封裝了兩個 `Animated.Value` 實例和相關輔助函數，在多數情況下可直接替代 `Value`。上例中可同時追蹤 x 和 y 值。

### 追蹤手勢

透過 [`Animated.event`](animated#event) 可將平移、滾動等手勢及其他事件直接映射到動畫數值。採用結構化映射語法，能從複雜事件物件提取值。第一層是跨多參數的陣列，內含嵌套物件。

例如處理水平滾動手勢時，可將 `event.nativeEvent.contentOffset.x` 映射到 `scrollX`（`Animated.Value`）：

```jsx
 onScroll={Animated.event(
   // scrollX = e.nativeEvent.contentOffset.x
   [{ nativeEvent: {
        contentOffset: {
          x: scrollX
        }
      }
    }]
 )}
```

以下範例實作了一個水平滾動的輪播組件，其中滾動位置指示器是透過在 `ScrollView` 中使用的 `Animated.event` 來實現動畫效果

#### 使用 Animated Event 的 ScrollView 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Animated&supportedPlatforms=ios,android
import React, { useRef } from "react";
import {
  SafeAreaView,
  ScrollView,
  Text,
  StyleSheet,
  View,
  ImageBackground,
  Animated,
  useWindowDimensions
} from "react-native";

const images = new Array(6).fill('https://images.unsplash.com/photo-1556740749-887f6717d7e4');

const App = () => {
  const scrollX = useRef(new Animated.Value(0)).current;

  const { width: windowWidth } = useWindowDimensions();

  return (
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
                  x: scrollX
                }
              }
            }
          ])}
          scrollEventThrottle={1}
        >
          {images.map((image, imageIndex) => {
            return (
              <View
                style={{ width: windowWidth, height: 250 }}
                key={imageIndex}
              >
                <ImageBackground source={{ uri: image }} style={styles.card}>
                  <View style={styles.textContainer}>
                    <Text style={styles.infoText}>
                      {"Image - " + imageIndex}
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
                windowWidth * (imageIndex + 1)
              ],
              outputRange: [8, 16, 8],
              extrapolate: "clamp"
            });
            return (
              <Animated.View
                key={imageIndex}
                style={[styles.normalDot, { width }]}
              />
            );
          })}
        </View>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center"
  },
  scrollContainer: {
    height: 300,
    alignItems: "center",
    justifyContent: "center"
  },
  card: {
    flex: 1,
    marginVertical: 4,
    marginHorizontal: 16,
    borderRadius: 5,
    overflow: "hidden",
    alignItems: "center",
    justifyContent: "center"
  },
  textContainer: {
    backgroundColor: "rgba(0,0,0, 0.7)",
    paddingHorizontal: 24,
    paddingVertical: 8,
    borderRadius: 5
  },
  infoText: {
    color: "white",
    fontSize: 16,
    fontWeight: "bold"
  },
  normalDot: {
    height: 8,
    width: 8,
    borderRadius: 4,
    backgroundColor: "silver",
    marginHorizontal: 4
  },
  indicatorContainer: {
    flexDirection: "row",
    alignItems: "center",
    justifyContent: "center"
  }
});

export default App;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Animated&supportedPlatforms=ios,android
import React, { Component } from "react";
import {
  SafeAreaView,
  ScrollView,
  Text,
  StyleSheet,
  View,
  ImageBackground,
  Animated,
  Dimensions
} from "react-native";

const images = new Array(6).fill('https://images.unsplash.com/photo-1556740749-887f6717d7e4');

const window = Dimensions.get("window");

export default class App extends Component {
  scrollX = new Animated.Value(0);

  state = {
    dimensions: {
      window
    }
  };

  onDimensionsChange = ({ window }) => {
    this.setState({ dimensions: { window } });
  };

  componentDidMount() {
    Dimensions.addEventListener("change", this.onDimensionsChange);
  }

  componentWillUnmount() {
    Dimensions.removeEventListener("change", this.onDimensionsChange);
  }

  render() {
    const windowWidth = this.state.dimensions.window.width;

    return (
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
                    x: this.scrollX
                  }
                }
              }
            ])}
            scrollEventThrottle={1}
          >
            {images.map((image, imageIndex) => {
              return (
                <View
                  style={{
                    width: windowWidth,
                    height: 250
                  }}
                  key={imageIndex}
                >
                  <ImageBackground source={{ uri: image }} style={styles.card}>
                    <View style={styles.textContainer}>
                      <Text style={styles.infoText}>
                        {"Image - " + imageIndex}
                      </Text>
                    </View>
                  </ImageBackground>
                </View>
              );
            })}
          </ScrollView>
          <View style={styles.indicatorContainer}>
            {images.map((image, imageIndex) => {
              const width = this.scrollX.interpolate({
                inputRange: [
                  windowWidth * (imageIndex - 1),
                  windowWidth * imageIndex,
                  windowWidth * (imageIndex + 1)
                ],
                outputRange: [8, 16, 8],
                extrapolate: "clamp"
              });
              return (
                <Animated.View
                  key={imageIndex}
                  style={[styles.normalDot, { width }]}
                />
              );
            })}
          </View>
        </View>
      </SafeAreaView>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center"
  },
  scrollContainer: {
    height: 300,
    alignItems: "center",
    justifyContent: "center"
  },
  card: {
    flex: 1,
    marginVertical: 4,
    marginHorizontal: 16,
    borderRadius: 5,
    overflow: "hidden",
    alignItems: "center",
    justifyContent: "center"
  },
  textContainer: {
    backgroundColor: "rgba(0,0,0, 0.7)",
    paddingHorizontal: 24,
    paddingVertical: 8,
    borderRadius: 5
  },
  infoText: {
    color: "white",
    fontSize: 16,
    fontWeight: "bold"
  },
  normalDot: {
    height: 8,
    width: 8,
    borderRadius: 4,
    backgroundColor: "silver",
    marginHorizontal: 4
  },
  indicatorContainer: {
    flexDirection: "row",
    alignItems: "center",
    justifyContent: "center"
  }
});
```

</TabItem>
</Tabs>

當使用 `PanResponder` 時，您可以使用以下程式碼從 `gestureState.dx` 和 `gestureState.dy` 中提取 x 和 y 座標。我們在陣列的第一個位置使用 `null`，因為我們只對傳遞給 `PanResponder` 處理器的第二個參數感興趣，也就是 `gestureState`。

```jsx
onPanResponderMove={Animated.event(
  [null, // ignore the native event
  // extract dx and dy from gestureState
  // like 'pan.x = gestureState.dx, pan.y = gestureState.dy'
  {dx: pan.x, dy: pan.y}
])}
```

#### 使用 Animated Event 的 PanResponder 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Animated
import React, { useRef } from "react";
import { Animated, View, StyleSheet, PanResponder, Text } from "react-native";

const App = () => {
  const pan = useRef(new Animated.ValueXY()).current;
  const panResponder = useRef(
    PanResponder.create({
      onMoveShouldSetPanResponder: () => true,
      onPanResponderMove: Animated.event([
        null,
        { dx: pan.x, dy: pan.y }
      ]),
      onPanResponderRelease: () => {
        Animated.spring(pan, { toValue: { x: 0, y: 0 } }).start();
      }
    })
  ).current;

  return (
    <View style={styles.container}>
      <Text style={styles.titleText}>Drag & Release this box!</Text>
      <Animated.View
        style={{
          transform: [{ translateX: pan.x }, { translateY: pan.y }]
        }}
        {...panResponder.panHandlers}
      >
        <View style={styles.box} />
      </Animated.View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center"
  },
  titleText: {
    fontSize: 14,
    lineHeight: 24,
    fontWeight: "bold"
  },
  box: {
    height: 150,
    width: 150,
    backgroundColor: "blue",
    borderRadius: 5
  }
});

export default App;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Animated
import React, { Component } from "react";
import { Animated, View, StyleSheet, PanResponder, Text } from "react-native";

export default class App extends Component {
  pan = new Animated.ValueXY();
  panResponder = PanResponder.create({
    onMoveShouldSetPanResponder: () => true,
    onPanResponderMove: Animated.event([
      null,
      { dx: this.pan.x, dy: this.pan.y }
    ]),
    onPanResponderRelease: () => {
      Animated.spring(this.pan, { toValue: { x: 0, y: 0 } }).start();
    }
  });

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.titleText}>Drag & Release this box!</Text>
        <Animated.View
          style={{
            transform: [{ translateX: this.pan.x }, { translateY: this.pan.y }]
          }}
          {...this.panResponder.panHandlers}
        >
          <View style={styles.box} />
        </Animated.View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center"
  },
  titleText: {
    fontSize: 14,
    lineHeight: 24,
    fontWeight: "bold"
  },
  box: {
    height: 150,
    width: 150,
    backgroundColor: "blue",
    borderRadius: 5
  }
});
```

</TabItem>
</Tabs>

### 響應當前的動畫值

您可能會注意到，在動畫過程中沒有明確的方法可以讀取當前值。這是因為由於優化的原因，該值可能只在原生運行時中才能得知。如果您需要根據當前值執行 JavaScript，有兩種方法：

- `spring.stopAnimation(callback)` 會停止動畫並使用最終值調用 `callback`。這在製作手勢過渡時非常有用。
- `spring.addListener(callback)` 會在動畫運行時異步調用 `callback`，提供最近的值。這對於觸發狀態變化非常有用，例如當用戶將一個泡泡拖得更近時將其吸附到新選項上，因為這些較大的狀態變化對幾幀的延遲不太敏感，而像平移這樣的連續手勢需要以 60 fps 運行。

`Animated` 被設計為完全可序列化，以便動畫可以以高性能的方式運行，獨立於正常的 JavaScript 事件循環。這確實會影響 API，所以當您發現某些操作比完全同步的系統更棘手時，請記住這一點。可以查看 `Animated.Value.addListener` 作為解決這些限制的一種方法，但請謹慎使用，因為它可能會在未來影響性能。

### 使用原生驅動

`Animated` API 被設計為可序列化。通過使用 [原生驅動](/blog/2017/02/14/using-native-driver-for-animated)，我們在開始動畫之前將所有關於動畫的信息發送到原生端，允許原生代碼在 UI 線程上執行動畫，而無需在每一幀都通過橋接。一旦動畫開始，JS 線程可以被阻塞而不會影響動畫。

為普通動畫使用原生驅動很簡單。您可以在啟動動畫時在動畫配置中添加 `useNativeDriver: true`。

```jsx
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // <-- Add this
}).start();
```

動畫值僅與一個驅動兼容，因此如果您在啟動動畫時使用原生驅動，請確保對該值的每個動畫也都使用原生驅動。

原生驅動也可以與 `Animated.event` 一起使用。這對於跟隨滾動位置的動畫特別有用，因為如果沒有原生驅動，由於 React Native 的異步特性，動畫總是會比手勢慢一幀。

```jsx
<Animated.ScrollView // <-- Use the Animated ScrollView wrapper
  scrollEventThrottle={1} // <-- Use 1 here to make sure no events are ever missed
  onScroll={Animated.event(
    [
      {
        nativeEvent: {
          contentOffset: {y: this.state.animatedValue},
        },
      },
    ],
    {useNativeDriver: true}, // <-- Add this
  )}>
  {content}
</Animated.ScrollView>
```

您可以通過運行 [RNTester 應用](https://github.com/facebook/react-native/blob/0.70-stable/packages/rn-tester/) 並加載原生動畫範例來查看原生驅動的實際效果。您也可以查看 [源代碼](https://github.com/facebook/react-native/blob/0.70-stable/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js) 來了解這些範例是如何製作的。

#### 注意事項

並非所有您可以使用 `Animated` 做的事情目前都受到原生驅動的支持。主要的限制是您只能動畫非佈局屬性：像 `transform` 和 `opacity` 這樣的屬性會起作用，但 Flexbox 和位置屬性則不會。當使用 `Animated.event` 時，它僅適用於直接事件而不適用於冒泡事件。這意味著它不適用於 `PanResponder`，但適用於像 `ScrollView#onScroll` 這樣的東西。

當動畫正在執行時，可能會阻止 `VirtualizedList` 元件渲染更多列。若您需要在用戶滾動列表時執行長時間或循環動畫，可以在動畫配置中使用 `isInteraction: false` 來避免此問題。

### 注意事項

使用如 `rotateY`、`rotateX` 等變形樣式時，請確保同時設定 `perspective` 變形樣式。目前某些動畫若缺少此設定，在 Android 上可能無法正常渲染。範例如下。

```jsx
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

RNTester 應用程式包含多種 `Animated` 的使用範例：

- [AnimatedGratuitousApp](https://github.com/facebook/react-native/tree/0.70-stable/packages/rn-tester/js/examples/AnimatedGratuitousApp)
- [NativeAnimationsExample](https://github.com/facebook/react-native/blob/0.70-stable/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js)

## `LayoutAnimation` API

`LayoutAnimation` 可讓您全域配置「建立」和「更新」動畫，這些動畫將在下一個渲染/佈局週期中用於所有視圖。這對於執行 Flexbox 佈局更新非常有用，無需費心測量或計算特定屬性來直接動畫化，尤其當佈局變更可能影響上層元件時（例如「查看更多」展開同時增加父元件尺寸並下推下方列），否則需要元件間明確協調才能同步動畫化。

請注意，儘管 `LayoutAnimation` 功能強大且相當實用，但它的控制力遠低於 `Animated` 和其他動畫函式庫，若無法用 `LayoutAnimation` 達成需求，您可能需要改用其他方法。

請注意，在 **Android** 上使用此功能需透過 `UIManager` 設定以下標記：

```jsx
UIManager.setLayoutAnimationEnabledExperimental &&
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

const { UIManager } = NativeModules;

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
    this.setState({w: this.state.w + 15, h: this.state.h + 15})
  }

  render() {
    return (
      <View style={styles.container}>
        <View style={[styles.box, {width: this.state.w, height: this.state.h}]} />
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

此範例使用預設值，您可根據需求自訂動畫，詳見 [LayoutAnimation.js](https://github.com/facebook/react-native/blob/0.70-stable/Libraries/LayoutAnimation/LayoutAnimation.js)。

## 補充說明

### `requestAnimationFrame`

`requestAnimationFrame` 是來自瀏覽器的 polyfill，您可能已熟悉其用法。它接受一個函式作為唯一參數，並在下一次重繪前呼叫該函式。這是所有基於 JavaScript 動畫 API 的基礎建構模組。通常您不需要直接呼叫它——動畫 API 會為您管理影格更新。

### `setNativeProps`

如[直接操作章節](direct-manipulation)所述，`setNativeProps` 讓我們能直接修改原生支援元件（實際由原生視圖支援的元件，而非複合元件）的屬性，無需透過 `setState` 重新渲染元件階層。

我們可在 Rebound 範例中使用此方法更新縮放比例——當更新的元件層級很深且未用 `shouldComponentUpdate` 優化時，這會特別有用。

若發現動畫影格率下降（低於每秒 60 影格），可嘗試使用 `setNativeProps` 或 `shouldComponentUpdate` 進行優化。或改用 [useNativeDriver 選項](/blog/2017/02/14/using-native-driver-for-animated) 在 UI 執行緒而非 JavaScript 執行緒執行動畫。您也可透過 [InteractionManager](interactionmanager) 將計算密集型工作延後至動畫完成後執行。可使用應用內開發者選單的「FPS 監測」工具監控影格率。