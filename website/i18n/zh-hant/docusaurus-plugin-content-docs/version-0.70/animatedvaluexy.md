---
id: animatedvaluexy
title: Animated.ValueXY
---

用於驅動2D動畫（如平移手勢）的2D數值。其API幾乎與標準的[`Animated.Value`](animatedvalue)相同，但採用多路復用技術。底層包含兩個常規的`Animated.Value`。

## 範例

```SnackPlayer name=Animated.ValueXY
import React, { useRef } from "react";
import { Animated, PanResponder, StyleSheet, View } from "react-native";

const DraggableView = () => {
  const pan = useRef(new Animated.ValueXY()).current;

  const panResponder = PanResponder.create({
    onStartShouldSetPanResponder: () => true,
    onPanResponderMove: Animated.event([
      null,
      {
        dx: pan.x, // x,y are Animated.Value
        dy: pan.y,
      },
    ]),
    onPanResponderRelease: () => {
      Animated.spring(
        pan, // Auto-multiplexed
        { toValue: { x: 0, y: 0 } } // Back to zero
      ).start();
    },
  });

  return (
    <View style={styles.container}>
      <Animated.View
        {...panResponder.panHandlers}
        style={[pan.getLayout(), styles.box]}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },
  box: {
    backgroundColor: "#61dafb",
    width: 80,
    height: 80,
    borderRadius: 4,
  },
});

export default DraggableView;
```

---

# 參考文獻

## 方法

### `setValue()`

```jsx
setValue(value);
```

直接設定數值。這會停止所有正在運行的動畫並更新所有綁定的屬性。

**參數：**

| Name  | Type   | Required | Description |
| ----- | ------ | -------- | ----------- |
| value | number | Yes      | Value       |

---

### `setOffset()`

```jsx
setOffset(offset);
```

設定一個偏移量，該偏移量會疊加到任何通過`setValue`、動畫或`Animated.event`設定的數值上。適用於補償平移手勢起始點等情況。

**參數：**

| Name   | Type   | Required | Description  |
| ------ | ------ | -------- | ------------ |
| offset | number | Yes      | Offset value |

---

### `flattenOffset()`

```jsx
flattenOffset();
```

將偏移量合併到基礎值中並將偏移量重置為零。最終輸出的數值保持不變。

---

### `extractOffset()`

```jsx
extractOffset();
```

將基礎值設為偏移量，並將基礎值重置為零。最終輸出的數值保持不變。

---

### `addListener()`

```jsx
addListener(callback);
```

為數值添加異步監聽器以觀察動畫更新。由於數值可能由原生驅動而無法同步讀取，此方法非常實用。

返回一個作為監聽器標識符的字串。

**參數：**

| Name     | Type     | Required | Description                                                                                 |
| -------- | -------- | -------- | ------------------------------------------------------------------------------------------- |
| callback | function | Yes      | The callback function which will receive an object with a `value` key set to the new value. |

---

### `removeListener()`

```jsx
removeListener(id);
```

取消註冊監聽器。`id`參數需與`addListener()`先前返回的標識符匹配。

**參數：**

| Name | Type   | Required | Description                        |
| ---- | ------ | -------- | ---------------------------------- |
| id   | string | Yes      | Id for the listener being removed. |

---

### `removeAllListeners()`

```jsx
removeAllListeners();
```

移除所有已註冊的監聽器。

---

### `stopAnimation()`

```jsx
stopAnimation([callback]);
```

停止任何正在運行的動畫或追蹤。停止動畫後會以最終值調用`callback`，這對於將狀態更新至與佈局動畫位置同步非常有用。

**參數：**

| Name     | Type     | Required | Description                                   |
| -------- | -------- | -------- | --------------------------------------------- |
| callback | function | No       | A function that will receive the final value. |

---

### `resetAnimation()`

```jsx
resetAnimation([callback]);
```

停止任何動畫並將數值重置為原始值。

**參數：**

| Name     | Type     | Required | Description                                      |
| -------- | -------- | -------- | ------------------------------------------------ |
| callback | function | No       | A function that will receive the original value. |

---

### `getLayout()`

```jsx
getLayout();
```

將`{x, y}`轉換為可用於樣式的`{left, top}`，例如：

```jsx
style={this.state.anim.getLayout()}
```

---

### `getTranslateTransform()`

```jsx
getTranslateTransform();
```

將`{x, y}`轉換為可用的平移變換，例如：

```jsx
style={{
  transform: this.state.anim.getTranslateTransform()
}}
```