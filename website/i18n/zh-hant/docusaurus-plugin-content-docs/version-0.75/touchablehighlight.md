---
id: touchablehighlight
title: TouchableHighlight
---

> 如果您正在尋找更全面且具未來兼容性的觸控輸入處理方式，請參閱 [Pressable](pressable.md) API。

用於使視圖正確響應觸控操作的封裝元件。當按下時，被包裝視圖的不透明度會降低，從而讓底層顏色透出，使視圖變暗或著色。

底層效果是通過將子元件包裹在一個新的 View 中實現的，這可能會影響佈局，若使用不當有時會導致不必要的視覺瑕疵，例如若未明確將被包裹視圖的 backgroundColor 設置為不透明顏色時。

TouchableHighlight 必須有且只有一個子元件（不能為零或多個）。若需包含多個子元件，請將它們包裹在一個 View 中。

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

const TouchableHighlightExample = () => {
  const [count, setCount] = useState(0);
  const onPress = () => setCount(count + 1);

  return (
    <View style={styles.container}>
      <TouchableHighlight onPress={onPress}>
        <View style={styles.button}>
          <Text>Touch Here</Text>
        </View>
      </TouchableHighlight>
      <View style={styles.countContainer}>
        <Text style={styles.countText}>{count || null}</Text>
      </View>
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

決定觸控激活時被包裹視圖的不透明度值。該值應介於 0 和 1 之間，預設為 0.85。需設置 `underlayColor` 方可生效。

| Type   |
| ------ |
| number |

---

### `onHideUnderlay`

在底層效果隱藏後立即調用。

| Type     |
| -------- |
| function |

---

### `onShowUnderlay`

在底層效果顯示後立即調用。

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

_(僅限 Apple TV)_ TV 偏好焦點（參見 View 元件的文檔說明）。

| Type |
| ---- |
| bool |

---

### `nextFocusDown` <div class="label android">Android</div>

TV 向下移動焦點（參見 View 元件的文檔說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div>

TV 向前移動焦點（參見 View 元件的文檔說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div>

TV 向左移動焦點（參見 View 元件的文檔說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div>

TV 向右移動焦點（參見 View 元件的文檔說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div>

TV 向上移動焦點（參見 View 元件的文檔說明）。

| Type   |
| ------ |
| number |

---

### `testOnly_pressed`

Handy for snapshot tests.

| Type |
| ---- |
| bool |