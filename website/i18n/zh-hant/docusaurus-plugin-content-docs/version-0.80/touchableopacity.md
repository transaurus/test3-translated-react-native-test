---
id: touchableopacity
title: TouchableOpacity
---

> 如果您正在尋找更全面且面向未來的觸控輸入處理方式，請參閱 [Pressable](pressable.md) API。

一個用於讓視圖正確響應觸控操作的封裝元件。當按下時，被包裝視圖的不透明度會降低，使其變暗。

透明度是通過將子元件包裹在一個 `Animated.View` 中來控制的，該視圖會被添加到視圖層級結構中。請注意，這可能會影響佈局。

## 範例

```SnackPlayer name=TouchableOpacity%20Example
import React, {useState} from 'react';
import {StyleSheet, Text, TouchableOpacity, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [count, setCount] = useState(0);
  const onPress = () => setCount(prevCount => prevCount + 1);

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <View style={styles.countContainer}>
          <Text>Count: {count}</Text>
        </View>
        <TouchableOpacity style={styles.button} onPress={onPress}>
          <Text>Press Here</Text>
        </TouchableOpacity>
      </SafeAreaView>
    </SafeAreaProvider>
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

---

# 參考

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

決定觸控激活時被包裝視圖的不透明度。預設值為 `0.2`。

| Type   |
| ------ |
| number |

---

### `hasTVPreferredFocus` <div class="label ios">iOS</div>

_(僅限 Apple TV)_ TV 首選焦點（請參閱 View 元件的文件）。

| Type |
| ---- |
| bool |

---

### `nextFocusDown` <div class="label android">Android</div>

TV 下一個向下焦點（請參閱 View 元件的文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div>

TV 下一個向前焦點（請參閱 View 元件的文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div>

TV 下一個向左焦點（請參閱 View 元件的文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div>

TV 下一個向右焦點（請參閱 View 元件的文件）。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div>

TV 下一個向上焦點（請參閱 View 元件的文件）。

| Type   |
| ------ |
| number |