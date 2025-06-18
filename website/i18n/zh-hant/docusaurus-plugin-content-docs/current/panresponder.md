---
id: panresponder
title: PanResponder
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`PanResponder` 將多個觸控事件協調為單一手勢。它使單點觸控手勢對額外觸點具有韌性，並可用於識別基本的多點觸控手勢。

預設情況下，`PanResponder` 會持有 `InteractionManager` 的控制權，以阻止長時間運行的 JS 事件中斷活動中的手勢。

它為[手勢響應系統](gesture-responder-system.md)提供的響應處理程序提供了一個可預測的封裝。對於每個處理程序，它會提供一個新的 `gestureState` 對象以及原生事件對象：

```
onPanResponderMove: (event, gestureState) => {}
```

原生事件是一個合成觸控事件，形式為 [PressEvent](pressevent)。

`gestureState` 對象包含以下內容：

- `stateID` - gestureState 的 ID - 只要螢幕上至少有一個觸點就會持續存在
- `moveX` - 最近移動觸點的螢幕座標 X 值
- `moveY` - 最近移動觸點的螢幕座標 Y 值
- `x0` - 響應授予時的螢幕座標 X 值
- `y0` - 響應授予時的螢幕座標 Y 值
- `dx` - 觸控開始後手勢的累積水平距離
- `dy` - 觸控開始後手勢的累積垂直距離
- `vx` - 手勢的當前水平速度
- `vy` - 手勢的當前垂直速度
- `numberActiveTouches` - 當前螢幕上的觸點數量

## 使用模式

```tsx
const ExampleComponent = () => {
  const panResponder = React.useRef(
    PanResponder.create({
      // Ask to be the responder:
      onStartShouldSetPanResponder: (evt, gestureState) => true,
      onStartShouldSetPanResponderCapture: (evt, gestureState) =>
        true,
      onMoveShouldSetPanResponder: (evt, gestureState) => true,
      onMoveShouldSetPanResponderCapture: (evt, gestureState) =>
        true,

      onPanResponderGrant: (evt, gestureState) => {
        // The gesture has started. Show visual feedback so the user knows
        // what is happening!
        // gestureState.d{x,y} will be set to zero now
      },
      onPanResponderMove: (evt, gestureState) => {
        // The most recent move distance is gestureState.move{X,Y}
        // The accumulated gesture distance since becoming responder is
        // gestureState.d{x,y}
      },
      onPanResponderTerminationRequest: (evt, gestureState) =>
        true,
      onPanResponderRelease: (evt, gestureState) => {
        // The user has released all touches while this view is the
        // responder. This typically means a gesture has succeeded
      },
      onPanResponderTerminate: (evt, gestureState) => {
        // Another component has become the responder, so this gesture
        // should be cancelled
      },
      onShouldBlockNativeResponder: (evt, gestureState) => {
        // Returns whether this component should block native components from becoming the JS
        // responder. Returns true by default. Is currently only supported on android.
        return true;
      },
    }),
  ).current;

  return <View {...panResponder.panHandlers} />;
};
```

## 範例

`PanResponder` 與 `Animated` API 協同工作，幫助構建 UI 中的複雜手勢。以下範例包含一個可動畫的 `View` 組件，可以在螢幕上自由拖動：

```SnackPlayer name=PanResponder
import React, {useRef} from 'react';
import {Animated, View, StyleSheet, PanResponder, Text} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const pan = useRef(new Animated.ValueXY()).current;

  const panResponder = useRef(
    PanResponder.create({
      onMoveShouldSetPanResponder: () => true,
      onPanResponderMove: Animated.event([null, {dx: pan.x, dy: pan.y}]),
      onPanResponderRelease: () => {
        pan.extractOffset();
      },
    }),
  ).current;

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text style={styles.titleText}>Drag this box!</Text>
        <Animated.View
          style={{
            transform: [{translateX: pan.x}, {translateY: pan.y}],
          }}
          {...panResponder.panHandlers}>
          <View style={styles.box} />
        </Animated.View>
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

試試 [RNTester 中的 PanResponder 範例](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/PanResponder/PanResponderExample.js)。

---

# 參考文獻

## 方法

### `create()`

```tsx
static create(config: PanResponderCallbacks): PanResponderInstance;
```

**參數：**

| Name                                                        | Type   | Description |
| ----------------------------------------------------------- | ------ | ----------- |
| config <div className="label basic required">Required</div> | object | Refer below |

`config` 對象提供了所有響應回調的增強版本，不僅提供 [`PressEvent`](pressevent)，還提供 `PanResponder` 手勢狀態，方法是將典型 `onResponder*` 回調中的 `Responder` 替換為 `PanResponder`。例如，`config` 對象可能如下所示：

- `onMoveShouldSetPanResponder: (e, gestureState) => {...}`
- `onMoveShouldSetPanResponderCapture: (e, gestureState) => {...}`
- `onStartShouldSetPanResponder: (e, gestureState) => {...}`
- `onStartShouldSetPanResponderCapture: (e, gestureState) => {...}`
- `onPanResponderReject: (e, gestureState) => {...}`
- `onPanResponderGrant: (e, gestureState) => {...}`
- `onPanResponderStart: (e, gestureState) => {...}`
- `onPanResponderEnd: (e, gestureState) => {...}`
- `onPanResponderRelease: (e, gestureState) => {...}`
- `onPanResponderMove: (e, gestureState) => {...}`
- `onPanResponderTerminate: (e, gestureState) => {...}`
- `onPanResponderTerminationRequest: (e, gestureState) => {...}`
- `onShouldBlockNativeResponder: (e, gestureState) => {...}`

通常情況下，對於具有捕獲階段等效項的事件，我們會在捕獲階段更新一次 gestureState，並可在冒泡階段同樣使用它。

Be careful with `onStartShould*` callbacks. They only reflect updated `gestureState` for start/end events that bubble/capture to the Node. Once the node is the responder, you can rely on every start/end event being processed by the gesture and `gestureState` being updated accordingly. (numberActiveTouches) may not be totally accurate unless you are the responder.