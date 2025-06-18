---
id: animatedvaluexy
title: Animated.ValueXY
---

用於驅動2D動畫（如平移手勢）的2D數值。API幾乎與標準[`Animated.Value`](animatedvalue)相同，但採用多路復用技術。底層包含兩個常規的`Animated.Value`。

## 範例

```SnackPlayer name=Animated.ValueXY%20Example
import React, {useRef} from 'react';
import {Animated, PanResponder, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

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
        {toValue: {x: 0, y: 0}, useNativeDriver: true}, // Back to zero
      ).start();
    },
  });

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Animated.View
          {...panResponder.panHandlers}
          style={[pan.getLayout(), styles.box]}
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  box: {
    backgroundColor: '#61dafb',
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

```tsx
setValue(value: {x: number; y: number});
```

直接設定數值。這會停止所有正在運行的動畫，並更新所有綁定的屬性。

**參數：**

| Name  | Type                     | Required | Description |
| ----- | ------------------------ | -------- | ----------- |
| value | `{x: number; y: number}` | Yes      | Value       |

---

### `setOffset()`

```tsx
setOffset(offset: {x: number; y: number});
```

設定一個偏移量，該偏移量會疊加到無論通過`setValue`、動畫或`Animated.event`設定的數值上。適用於補償平移手勢起始點等情況。

**參數：**

| Name   | Type                     | Required | Description  |
| ------ | ------------------------ | -------- | ------------ |
| offset | `{x: number; y: number}` | Yes      | Offset value |

---

### `flattenOffset()`

```tsx
flattenOffset();
```

將偏移量合併到基礎值中，並將偏移量重置為零。最終輸出的數值保持不變。

---

### `extractOffset()`

```tsx
extractOffset();
```

將基礎值設為偏移量，並將基礎值重置為零。最終輸出的數值保持不變。

---

### `addListener()`

```tsx
addListener(callback: (value: {x: number; y: number}) => void);
```

為數值添加異步監聽器，以便觀察動畫的更新。由於數值可能由原生驅動而無法同步讀取，此方法非常實用。

返回一個作為監聽器標識符的字串。

**參數：**

| Name     | Type     | Required | Description                                                                                 |
| -------- | -------- | -------- | ------------------------------------------------------------------------------------------- |
| callback | function | Yes      | The callback function which will receive an object with a `value` key set to the new value. |

---

### `removeListener()`

```tsx
removeListener(id: string);
```

取消註冊監聽器。`id`參數需與先前`addListener()`返回的標識符匹配。

**參數：**

| Name | Type   | Required | Description                        |
| ---- | ------ | -------- | ---------------------------------- |
| id   | string | Yes      | Id for the listener being removed. |

---

### `removeAllListeners()`

```tsx
removeAllListeners();
```

移除所有已註冊的監聽器。

---

### `stopAnimation()`

```tsx
stopAnimation(callback?: (value: {x: number; y: number}) => void);
```

停止任何正在運行的動畫或追蹤。`callback`會在動畫停止後以最終值調用，便於將狀態更新至與動畫位置匹配的佈局。

**參數：**

| Name     | Type     | Required | Description                                   |
| -------- | -------- | -------- | --------------------------------------------- |
| callback | function | No       | A function that will receive the final value. |

---

### `resetAnimation()`

```tsx
resetAnimation(callback?: (value: {x: number; y: number}) => void);
```

停止任何動畫並將數值重置為原始狀態。

**參數：**

| Name     | Type     | Required | Description                                      |
| -------- | -------- | -------- | ------------------------------------------------ |
| callback | function | No       | A function that will receive the original value. |

---

### `getLayout()`

```tsx
getLayout(): {left: Animated.Value, top: Animated.Value};
```

將`{x, y}`轉換為樣式中可用的`{left, top}`，例如：

```tsx
style={this.state.anim.getLayout()}
```

---

### `getTranslateTransform()`

```tsx
getTranslateTransform(): [
  {translateX: Animated.Value},
  {translateY: Animated.Value},
];
```

將`{x, y}`轉換為可用的平移變換，例如：

```tsx
style={{
  transform: this.state.anim.getTranslateTransform()
}}
```