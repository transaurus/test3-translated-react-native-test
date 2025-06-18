---
id: touchableopacity
title: TouchableOpacity
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

> 若您需要更全面且具未來性的觸控輸入處理方案，請參閱 [Pressable](pressable.md) API。

用於使視圖正確響應觸控操作的封裝元件。按下時，被包裝視圖的不透明度會降低以產生變暗效果。

透明度控制是通過將子元件包裹在 `Animated.View` 中實現的，該動畫視圖會被添加到視圖層級結構。請注意這可能會影響佈局。

## 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=TouchableOpacity%20Function%20Component%20Example
import React, {useState} from 'react';
import {StyleSheet, Text, TouchableOpacity, View} from 'react-native';

const App = () => {
  const [count, setCount] = useState(0);
  const onPress = () => setCount(prevCount => prevCount + 1);

  return (
    <View style={styles.container}>
      <View style={styles.countContainer}>
        <Text>Count: {count}</Text>
      </View>
      <TouchableOpacity style={styles.button} onPress={onPress}>
        <Text>Press Here</Text>
      </TouchableOpacity>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    paddingHorizontal: 10,
  },
  button: {
    alignItems: 'center',
    backgroundColor: '#DDDDDD',
    padding: 10,
  },
  countContainer: {
    alignItems: 'center',
    padding: 10,
  },
});

export default App;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=TouchableOpacity%20Class%20Component%20Example
import React, {Component} from 'react';
import {StyleSheet, Text, TouchableOpacity, View} from 'react-native';

class App extends Component {
  state = {
    count: 0,
  };

  onPress = () => {
    this.setState({
      count: this.state.count + 1,
    });
  };

  render() {
    const {count} = this.state;
    return (
      <View style={styles.container}>
        <View style={styles.countContainer}>
          <Text>Count: {count}</Text>
        </View>
        <TouchableOpacity style={styles.button} onPress={this.onPress}>
          <Text>Press Here</Text>
        </TouchableOpacity>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    paddingHorizontal: 10,
  },
  button: {
    alignItems: 'center',
    backgroundColor: '#DDDDDD',
    padding: 10,
  },
  countContainer: {
    alignItems: 'center',
    padding: 10,
  },
});

export default App;
```

</TabItem>
</Tabs>

---

# 參考文獻

## 屬性

### [TouchableWithoutFeedback 屬性](touchablewithoutfeedback.md#props)

繼承 [TouchableWithoutFeedback 屬性](touchablewithoutfeedback.md#props)。

---

### `style`

| Type                           |
| ------------------------------ |
| [View.style](view-style-props) |

---

### `activeOpacity`

決定觸控激活時被包裝視圖的不透明度值。預設為 `0.2`。

| Type   |
| ------ |
| number |

---

### `tvParallaxProperties` <div class="label ios">IOS</div>

_(僅限 Apple TV)_ 用於控制 Apple TV 視差效果的物件屬性。

- `enabled`: 若為 `true` 則啟用視差效果。預設值 `true`。
- `shiftDistanceX`: 預設值 `2.0`。
- `shiftDistanceY`: 預設值 `2.0`。
- `tiltAngle`: 預設值 `0.05`。
- `magnification`: 預設值 `1.0`。
- `pressMagnification`: 預設值 `1.0`。
- `pressDuration`: 預設值 `0.3`。
- `pressDelay`: 預設值 `0.0`。

| Type   |
| ------ |
| object |

---

### `hasTVPreferredFocus` <div class="label ios">iOS</div>

_(僅限 Apple TV)_ TV 偏好焦點設定（詳見 View 元件文件）。

| Type |
| ---- |
| bool |

---

### `nextFocusDown` <div class="label android">Android</div>

TV 向下焦點導航設定（詳見 View 元件文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div>

TV 向前焦點導航設定（詳見 View 元件文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div>

TV 向左焦點導航設定（詳見 View 元件文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div>

TV 向右焦點導航設定（詳見 View 元件文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div>

TV 向上焦點導航設定（詳見 View 元件文件）。

| Type   |
| ------ |
| number |