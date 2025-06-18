---
id: animations
title: Animations
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

動畫對於創造出色的使用者體驗至關重要。靜止的物體開始移動時需克服慣性，而運動中的物體具有動量，很少會立即停止。動畫能讓您在介面中傳遞符合物理規律的運動效果。

React Native 提供兩套互補的動畫系統：[`Animated`](animations#animated-api) 用於對特定值進行細粒度互動控制，[`LayoutAnimation`](animations#layoutanimation-api) 則用於全局佈局變換的動畫處理。

## `Animated` API

[`Animated`](animated) API 的設計旨在以高效能的方式簡潔表達各種動畫與互動模式。其核心是輸入輸出的聲明式關係，中間可配置轉換過程，並通過 `start`/`stop` 方法控制基於時間軸的動畫執行。

`Animated` 導出六種可動畫化元件類型：`View`、`Text`、`Image`、`ScrollView`、`FlatList` 和 `SectionList`，您也可以使用 `Animated.createAnimatedComponent()` 創建自訂元件。

例如，一個在掛載時淡入的容器視圖可能如下所示：

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer ext=js&supportedPlatforms=ios,android
import React, {useEffect, useAnimatedValue} from 'react';
import {Animated, Text, View} from 'react-native';

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

解析這段代碼：在 `FadeInView` 構造函數中，初始化了名為 `fadeAnim` 的 `Animated.Value` 作為 `state` 的一部分。`View` 的透明度屬性被映射到這個動畫值。底層運行時會提取數值並用於設置透明度。

元件掛載時，透明度設為 0。接著在 `fadeAnim` 動畫值上啟動緩動動畫，該動畫會在每一幀更新所有依賴映射（此例中僅透明度屬性），使數值從初始值漸變至最終值 1。

這種實現方式經過優化，比調用 `setState` 重新渲染更高效。由於整個配置是聲明式的，未來還能實現更多優化，例如序列化配置並在高優先級線程運行動畫。

### 配置動畫

動畫具有高度可配置性。自訂或預設的緩動函數、延遲、持續時間、衰減因子、彈簧常數等參數，均可根據動畫類型進行調整。

`Animated` 提供多種動畫類型，最常用的是 [`Animated.timing()`](animated#timing)。它支持使用預設緩動函數或自訂函數隨時間變化數值。緩動函數通常用於表現物體逐漸加速或減速的運動過程。

默認情況下，`timing` 採用 easeInOut 曲線，表現為先漸加速至全速，結束時漸減速至停止。您可通過 `easing` 參數指定其他緩動函數，同時支持自訂 `duration` 或設置動畫開始前的 `delay`。

例如要創建一個 2 秒長的動畫，讓物體在到達最終位置前輕微回彈：

```tsx
Animated.timing(this.state.xPosition, {
  toValue: 100,
  easing: Easing.back(),
  duration: 2000,
  useNativeDriver: true,
}).start();
```

查閱 `Animated` API 參考文檔的[配置動畫](animated#configuring-animations)章節，了解內建動畫支持的所有配置參數。

### 組合動畫

動畫可組合為順序或並行播放。順序動畫可在前一動畫結束後立即播放，或延遲指定時間啟動。`Animated` API 提供 `sequence()` 和 `delay()` 等方法，這些方法接收動畫數組並自動按需調用 `start()`/`stop()`。

例如以下動畫先滑行停止，然後並行執行回彈與旋轉：

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

如果一個動畫被停止或中斷，那麼群組中的所有其他動畫也會被停止。`Animated.parallel` 提供了一個 `stopTogether` 選項，可設置為 `false` 來禁用此行為。

您可以在 `Animated` API 參考文件的[組合動畫](animated#composing-animations)章節中找到完整的組合方法列表。

### 合併動畫值

您可以通過加法、乘法、除法或模運算來[合併兩個動畫值](animated#combining-animated-values)，從而創建一個新的動畫值。

在某些情況下，一個動畫值需要對另一個動畫值進行反向計算。例如反轉縮放比例（2倍 --> 0.5倍）：

```tsx
const a = new Animated.Value(1);
const b = Animated.divide(1, a);

Animated.spring(a, {
  toValue: 2,
  useNativeDriver: true,
}).start();
```

### 插值

每個屬性都可以先經過插值處理。插值將輸入範圍映射到輸出範圍，通常使用線性插值，但也支援緩動函數。默認情況下，它會外推超出給定範圍的曲線，但您也可以設置使其限制輸出值。

將 0-1 範圍轉換為 0-100 範圍的基本映射如下：

```tsx
value.interpolate({
  inputRange: [0, 1],
  outputRange: [0, 100],
});
```

例如，您可能希望將 `Animated.Value` 視為從 0 到 1，但將位置從 150px 動畫到 0px，並將透明度從 0 動畫到 1。可以通過修改上述範例中的 `style` 來實現：

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

[`interpolate()`](animated#interpolate) 還支援多個範圍段，這對於定義死區和其他技巧非常有用。例如，要在 -300 處建立一個反向關係，在 -100 處回到 0，在 0 處回到 1，然後在 100 處回到 0，之後的所有值保持為 0 的死區，可以這樣做：

```tsx
value.interpolate({
  inputRange: [-300, -100, 0, 100, 101],
  outputRange: [300, 0, 1, 0, 0],
});
```

其映射關係如下：

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

`interpolate()` 還支援映射到字符串，允許您動畫顏色以及帶單位的值。例如，如果要動畫旋轉，可以這樣做：

```tsx
value.interpolate({
  inputRange: [0, 360],
  outputRange: ['0deg', '360deg'],
});
```

`interpolate()` 還支援任意緩動函數，其中許多已在 [`Easing`](easing) 模組中實現。`interpolate()` 還可配置外推 `outputRange` 的行為。您可以通過設置 `extrapolate`、`extrapolateLeft` 或 `extrapolateRight` 選項來配置外推。默認值為 `extend`，但您可以使用 `clamp` 來防止輸出值超出 `outputRange`。

### 追蹤動態值

動畫值還可以通過將動畫的 `toValue` 設置為另一個動畫值而非普通數字來追蹤其他值。例如，Android 版 Messenger 使用的「聊天頭像」動畫可以通過固定在另一個動畫值上的 `spring()` 來實現，或者使用 `duration` 為 0 的 `timing()` 來實現嚴格的追蹤。它們還可以與插值組合使用：

```tsx
Animated.spring(follower, {toValue: leader}).start();
Animated.timing(opacity, {
  toValue: pan.x.interpolate({
    inputRange: [0, 300],
    outputRange: [1, 0],
    useNativeDriver: true,
  }),
}).start();
```

`leader` 和 `follower` 動畫值將使用 `Animated.ValueXY()` 實現。`ValueXY` 是處理 2D 交互（如平移或拖拽）的便捷方式。它是一個基本包裝器，包含兩個 `Animated.Value` 實例和一些調用它們的輔助函數，使 `ValueXY` 在許多情況下可以作為 `Value` 的替代品。它允許我們在上面的範例中同時追蹤 x 和 y 值。

### 追蹤手勢

手勢（如平移或滾動）和其他事件可以使用 [`Animated.event`](animated#event) 直接映射到動畫值。這是通過結構化映射語法實現的，以便可以從複雜的事件對象中提取值。第一層是一個數組，允許跨多個參數進行映射，該數組包含嵌套的對象。

例如，當處理水平滾動手勢時，您可以透過以下方式將 `event.nativeEvent.contentOffset.x` 映射到 `scrollX`（一個 `Animated.Value`）：

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

以下範例實現了一個水平滾動的輪播，其中滾動位置指示器使用 `ScrollView` 中的 `Animated.event` 進行動畫處理：

#### 使用動畫事件的 ScrollView 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Animated&supportedPlatforms=ios,android
import React from 'react';
import {
  SafeAreaView,
  ScrollView,
  Text,
  StyleSheet,
  View,
  ImageBackground,
  Animated,
  useWindowDimensions,
  useAnimatedValue,
} from 'react-native';

const images = new Array(6).fill(
  'https://images.unsplash.com/photo-1556740749-887f6717d7e4',
);

const App = () => {
  const scrollX = useAnimatedValue(0);

  const {width: windowWidth} = useWindowDimensions();

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
                  x: scrollX,
                },
              },
            },
          ])}
          scrollEventThrottle={1}>
          {images.map((image, imageIndex) => {
            return (
              <View style={{width: windowWidth, height: 250}} key={imageIndex}>
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

</TabItem>
<TabItem value="classical">

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Animated&supportedPlatforms=ios,android&ext=js
import React, {Component} from 'react';
import {
  SafeAreaView,
  ScrollView,
  Text,
  StyleSheet,
  View,
  ImageBackground,
  Animated,
  Dimensions,
} from 'react-native';

const images = new Array(6).fill(
  'https://images.unsplash.com/photo-1556740749-887f6717d7e4',
);

const windowDimensions = Dimensions.get('window');

export default class App extends Component {
  scrollX = new Animated.Value(0);

  state = {
    dimensions: {
      window: windowDimensions,
    },
  };

  onDimensionsChange = ({window}) => {
    this.setState({dimensions: {window}});
  };

  componentDidMount() {
    this.dimensionsSubscription = Dimensions.addEventListener(
      'change',
      this.onDimensionsChange,
    );
  }

  componentWillUnmount() {
    this.dimensionsSubscription.remove();
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
                    x: this.scrollX,
                  },
                },
              },
            ])}
            scrollEventThrottle={1}>
            {images.map((image, imageIndex) => {
              return (
                <View
                  style={{
                    width: windowWidth,
                    height: 250,
                  }}
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
              const width = this.scrollX.interpolate({
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
    );
  }
}

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
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Animated&supportedPlatforms=ios,android&ext=tsx
import React, {Component} from 'react';
import {
  SafeAreaView,
  ScrollView,
  Text,
  StyleSheet,
  View,
  ImageBackground,
  Animated,
  Dimensions,
} from 'react-native';
import type {ScaledSize, EmitterSubscription} from 'react-native';

const images = new Array(6).fill(
  'https://images.unsplash.com/photo-1556740749-887f6717d7e4',
);

const windowDimensions = Dimensions.get('window');

export default class App extends Component {
  dimensionsSubscription?: EmitterSubscription;
  scrollX = new Animated.Value(0);

  state = {
    dimensions: {
      window: windowDimensions,
    },
  };

  onDimensionsChange = ({window}: {window: ScaledSize}) => {
    this.setState({dimensions: {window}});
  };

  componentDidMount() {
    this.dimensionsSubscription = Dimensions.addEventListener(
      'change',
      this.onDimensionsChange,
    );
  }

  componentWillUnmount() {
    this.dimensionsSubscription?.remove();
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
                    x: this.scrollX,
                  },
                },
              },
            ])}
            scrollEventThrottle={1}>
            {images.map((image, imageIndex) => {
              return (
                <View
                  style={{
                    width: windowWidth,
                    height: 250,
                  }}
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
              const width = this.scrollX.interpolate({
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
    );
  }
}

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
```

</TabItem>
</Tabs>

</TabItem>
</Tabs>

當使用 `PanResponder` 時，您可以使用以下代碼從 `gestureState.dx` 和 `gestureState.dy` 中提取 x 和 y 位置。我們在陣列的第一個位置使用 `null`，因為我們只對傳遞給 `PanResponder` 處理器的第二個參數 `gestureState` 感興趣。

```tsx
onPanResponderMove={Animated.event(
  [null, // ignore the native event
  // extract dx and dy from gestureState
  // like 'pan.x = gestureState.dx, pan.y = gestureState.dy'
  {dx: pan.x, dy: pan.y}
])}
```

#### 使用動畫事件的 PanResponder 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

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

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Animated
import React, {Component} from 'react';
import {Animated, View, StyleSheet, PanResponder, Text} from 'react-native';

export default class App extends Component {
  pan = new Animated.ValueXY();
  panResponder = PanResponder.create({
    onMoveShouldSetPanResponder: () => true,
    onPanResponderMove: Animated.event([
      null,
      {dx: this.pan.x, dy: this.pan.y},
    ]),
    onPanResponderRelease: () => {
      Animated.spring(this.pan, {
        toValue: {x: 0, y: 0},
        useNativeDriver: true,
      }).start();
    },
  });

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.titleText}>Drag & Release this box!</Text>
        <Animated.View
          style={{
            transform: [{translateX: this.pan.x}, {translateY: this.pan.y}],
          }}
          {...this.panResponder.panHandlers}>
          <View style={styles.box} />
        </Animated.View>
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
```

</TabItem>
</Tabs>

### 響應當前的動畫值

您可能會注意到，在動畫過程中沒有明確的方法來讀取當前值。這是因為由於優化，該值可能僅在原生運行時中已知。如果您需要根據當前值運行 JavaScript，有兩種方法：

- `spring.stopAnimation(callback)` 會停止動畫並使用最終值調用 `callback`。這在製作手勢過渡時非常有用。
- `spring.addListener(callback)` 會在動畫運行時異步調用 `callback`，提供最近的值。這對於觸發狀態變化非常有用，例如當用戶將一個泡泡拖得更近時將其吸附到新選項，因為這些較大的狀態變化對幾幀的延遲不太敏感，而像平移這樣的連續手勢需要以 60 fps 運行。

`Animated` 設計為完全可序列化，以便動畫可以以高性能的方式運行，獨立於正常的 JavaScript 事件循環。這會影響 API，因此當您發現某些操作比完全同步的系統更棘手時，請記住這一點。可以查看 `Animated.Value.addListener` 作為解決這些限制的一種方法，但請謹慎使用，因為它可能會在未來影響性能。

### 使用原生驅動

`Animated` API 設計為可序列化。通過使用[原生驅動](/blog/2017/02/14/using-native-driver-for-animated)，我們在開始動畫之前將所有關於動畫的內容發送到原生端，允許原生代碼在 UI 線程上執行動畫，而無需在每一幀都通過橋接。一旦動畫開始，JS 線程可以被阻塞而不會影響動畫。

為普通動畫使用原生驅動可以通過在開始動畫時在動畫配置中設置 `useNativeDriver: true` 來實現。沒有 `useNativeDriver` 屬性的動畫將默認為 false（出於遺留原因），但會發出警告（在 TypeScript 中會出現類型檢查錯誤）。

```tsx
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // <-- Set this to true
}).start();
```

動畫值僅與一個驅動程序兼容，因此如果您在啟動某個值的動畫時使用原生驅動，請確保該值的每個動畫也都使用原生驅動。

原生驅動也可以與 `Animated.event` 一起使用。這對於跟隨滾動位置的動畫特別有用，因為如果沒有原生驅動，由於 React Native 的異步特性，動畫總是會比手勢慢一幀。

```tsx
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
    {useNativeDriver: true}, // <-- Set this to true
  )}>
  {content}
</Animated.ScrollView>
```

您可以通過運行 [RNTester 應用](https://github.com/facebook/react-native/blob/0.71-stable/packages/rn-tester/)，然後加載原生動畫範例來查看原生驅動的實際效果。您也可以查看[源代碼](https://github.com/facebook/react-native/blob/0.71-stable/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js)以了解這些範例是如何製作的。

#### 注意事項

並非所有使用 `Animated` 的功能目前都受到原生驅動程式的支援。主要的限制在於你只能動畫化非佈局屬性：例如 `transform` 和 `opacity` 會起作用，但 Flexbox 和位置屬性則不會。當使用 `Animated.event` 時，它僅適用於直接事件而不支援冒泡事件。這意味著它無法與 `PanResponder` 一起使用，但可以與 `ScrollView#onScroll` 等事件配合運作。

當動畫正在執行時，它可能會阻止 `VirtualizedList` 元件渲染更多行。如果你需要在用戶滾動列表時執行長時間或循環動畫，可以在動畫配置中使用 `isInteraction: false` 來避免此問題。

### 注意事項

使用如 `rotateY`、`rotateX` 等變形樣式時，請確保同時設置 `perspective` 變形樣式。目前某些動畫若未設置此屬性，在 Android 上可能無法正常渲染。範例如下。

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

RNTester 應用程式提供了多種使用 `Animated` 的範例：

- [AnimatedGratuitousApp](https://github.com/facebook/react-native/tree/0.71-stable/packages/rn-tester/js/examples/AnimatedGratuitousApp)
- [NativeAnimationsExample](https://github.com/facebook/react-native/blob/0.71-stable/packages/rn-tester/js/examples/NativeAnimation/NativeAnimationsExample.js)

## `LayoutAnimation` API

`LayoutAnimation` 允許你全局配置將在下一個渲染/佈局周期中用於所有視圖的「創建」和「更新」動畫。這對於進行 Flexbox 佈局更新非常有用，無需費心測量或計算特定屬性來直接動畫化它們，尤其當佈局變更可能影響上層元件時（例如「查看更多」展開同時增加父元件大小並下推下方行），否則需要元件間明確協調才能同步動畫化所有元件。

請注意，儘管 `LayoutAnimation` 功能強大且相當實用，但它提供的控制力遠低於 `Animated` 和其他動畫函式庫，因此若無法透過 `LayoutAnimation` 實現需求，可能需要改用其他方法。

請注意，在 **Android** 上要使此功能正常運作，需透過 `UIManager` 設置以下標誌：

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

此範例使用了預設值，你可以根據需求自訂動畫，詳見 [LayoutAnimation.js](https://github.com/facebook/react-native/blob/0.71-stable/Libraries/LayoutAnimation/LayoutAnimation.js) 以獲取更多資訊。

## 補充說明

### `requestAnimationFrame`

`requestAnimationFrame` 是來自瀏覽器的 polyfill，你可能對它很熟悉。它接受一個函式作為唯一參數，並在下一次重繪前調用該函式。這是所有基於 JavaScript 的動畫 API 的基礎建構塊。通常你不需要手動調用它——動畫 API 會為你管理幀更新。

### `setNativeProps`

如[直接操作章節](direct-manipulation)所述，`setNativeProps` 允許我們直接修改原生支援元件（實際由原生視圖支持的元件，而非複合元件）的屬性，而無需透過 `setState` 重新渲染元件層級結構。

我們可以在 Rebound 範例中使用此方法來更新縮放比例——這對於更新深度嵌套且未經 `shouldComponentUpdate` 優化的元件可能有所助益。

如果您發現動畫出現掉幀（低於每秒60幀）的情況，可以考慮使用 `setNativeProps` 或 `shouldComponentUpdate` 來優化。或者，您也可以選擇[透過 useNativeDriver 選項](/blog/2017/02/14/using-native-driver-for-animated)在UI執行緒而非JavaScript執行緒上執行動畫。此外，建議使用 [InteractionManager](interactionmanager) 將計算密集型工作延遲到動畫完成後再處理。您可以使用應用內開發者選單中的「FPS監測」工具來監控幀率。