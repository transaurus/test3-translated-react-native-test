---
id: touchablehighlight
title: TouchableHighlight
---

> 如果您正在尋找更全面且面向未來的觸控輸入處理方式，請參閱 [Pressable](pressable.md) API。

用於使視圖正確響應觸控操作的包裝元件。按下時，被包裝視圖的不透明度會降低，從而讓底層顏色透出，使視圖變暗或著色。

底層效果是通過將子元件包裹在一個新視圖中實現的，這可能會影響佈局，如果使用不當（例如未明確將被包裝視圖的backgroundColor設置為不透明顏色），有時會導致不必要的視覺瑕疵。

TouchableHighlight 必須有且只有一個子元件（不能為零或多個）。如果您需要多個子元件，請將它們包裹在一個 View 中。

```tsx
function MyComponent(props: MyComponentProps) {
  return (
    <View {...props} style={{flex: 1, backgroundColor: '#fff'}}>
      <Text>My Component</Text>
    </View>
  );
}

<TouchableHighlight
  activeOpacity={0.6}
  underlayColor="#DDDDDD"
  onPress={() => alert('Pressed!')}>
  <MyComponent />
</TouchableHighlight>;
```

## 範例

```SnackPlayer name=TouchableHighlight%20Example
import React, {useState} from 'react';
import {StyleSheet, Text, TouchableHighlight, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const TouchableHighlightExample = () => {
  const [count, setCount] = useState(0);
  const onPress = () => setCount(count + 1);

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <TouchableHighlight onPress={onPress}>
          <View style={styles.button}>
            <Text>Touch Here</Text>
          </View>
        </TouchableHighlight>
        <View style={styles.countContainer}>
          <Text style={styles.countText}>{count || null}</Text>
        </View>
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
  countText: {
    color: '#FF00FF',
  },
});

export default TouchableHighlightExample;
```

---

# 參考文獻

## 屬性

### [TouchableWithoutFeedback 屬性](touchablewithoutfeedback.md#props)

繼承 [TouchableWithoutFeedback 屬性](touchablewithoutfeedback.md#props)。

---

### `activeOpacity`

決定觸控激活時被包裝視圖的不透明度值。該值應介於 0 和 1 之間，預設為 0.85。需設置 `underlayColor` 方可生效。

| Type   |
| ------ |
| number |

---

### `onHideUnderlay`

當底層隱藏後立即調用。

| Type     |
| -------- |
| function |

---

### `onShowUnderlay`

當底層顯示後立即調用。

| Type     |
| -------- |
| function |

---

### `style`

| Type       |
| ---------- |
| View.style |

---

### `underlayColor`

觸控激活時會透出的底層顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `hasTVPreferredFocus` <div class="label ios">iOS</div>

_(僅限 Apple TV)_ TV 首選焦點（參見 View 元件的文檔）。

| Type |
| ---- |
| bool |

---

### `nextFocusDown` <div class="label android">Android</div>

TV 向下移動焦點（參見 View 元件的文檔）。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div>

TV 向前移動焦點（參見 View 元件的文檔）。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div>

TV 向左移動焦點（參見 View 元件的文檔）。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div>

TV 向右移動焦點（參見 View 元件的文檔）。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div>

TV 向上移動焦點（參見 View 元件的文檔）。

| Type   |
| ------ |
| number |

---

### `testOnly_pressed`

適用於快照測試的便捷屬性。

| Type |
| ---- |
| bool |