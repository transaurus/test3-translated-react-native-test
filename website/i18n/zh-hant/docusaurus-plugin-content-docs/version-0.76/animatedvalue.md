---
id: animatedvalue
title: Animated.Value
---

驅動動畫的標準值。一個 `Animated.Value` 可以同步驅動多個屬性，但同一時間只能由一種機制驅動。使用新的機制（例如啟動新動畫或調用 `setValue`）將停止先前的所有機制。

通常在類組件中初始化為 `useAnimatedValue(0);` 或 `new Animated.Value(0);`。

---

# 參考文檔

## 方法

### `setValue()`

```tsx
setValue(value: number);
```

直接設定數值。這會停止該數值上運行的所有動畫，並更新所有綁定的屬性。

**參數：**

| Name  | Type   | Required | Description |
| ----- | ------ | -------- | ----------- |
| value | number | Yes      | Value       |

---

### `setOffset()`

```tsx
setOffset(offset: number);
```

設定一個偏移量，該偏移量會疊加在通過 `setValue`、動畫或 `Animated.event` 設定的數值之上。適用於補償平移手勢的起始點等情況。

**參數：**

| Name   | Type   | Required | Description  |
| ------ | ------ | -------- | ------------ |
| offset | number | Yes      | Offset value |

---

### `flattenOffset()`

```tsx
flattenOffset();
```

將偏移量合併到基礎值中，並將偏移量重置為零。數值的最終輸出保持不變。

---

### `extractOffset()`

```tsx
extractOffset();
```

將偏移量設為基礎值，並將基礎值重置為零。數值的最終輸出保持不變。

---

### `addListener()`

```tsx
addListener(callback: (state: {value: number}) => void): string;
```

為數值添加異步監聽器，以便觀察動畫的更新。由於數值可能由原生驅動，無法同步讀取，因此此方法非常有用。

返回一個字符串作為監聽器的標識符。

**參數：**

| Name     | Type     | Required | Description                                                                                 |
| -------- | -------- | -------- | ------------------------------------------------------------------------------------------- |
| callback | function | Yes      | The callback function which will receive an object with a `value` key set to the new value. |

---

### `removeListener()`

```tsx
removeListener(id: string);
```

取消註冊監聽器。`id` 參數應與先前 `addListener()` 返回的標識符匹配。

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
stopAnimation(callback?: (value: number) => void);
```

停止任何正在運行的動畫或追蹤。`callback` 會在停止動畫後以最終值調用，這對於將狀態更新至與動畫位置匹配的佈局非常有用。

**參數：**

| Name     | Type     | Required | Description                                   |
| -------- | -------- | -------- | --------------------------------------------- |
| callback | function | No       | A function that will receive the final value. |

---

### `resetAnimation()`

```tsx
resetAnimation(callback?: (value: number) => void);
```

停止任何動畫並將數值重置為原始值。

**參數：**

| Name     | Type     | Required | Description                                      |
| -------- | -------- | -------- | ------------------------------------------------ |
| callback | function | No       | A function that will receive the original value. |

---

### `interpolate()`

```tsx
interpolate(config: InterpolationConfigType);
```

在更新屬性之前對數值進行插值，例如將 0-1 映射到 0-10。

參見 `AnimatedInterpolation.js`

**參數：**

| Name   | Type   | Required | Description |
| ------ | ------ | -------- | ----------- |
| config | object | Yes      | See below.  |

The `config` object is composed of the following keys:

- `inputRange`: an array of numbers
- `outputRange`: an array of numbers or strings
- `easing` (optional): a function that returns a number, given an input number
- `extrapolate` (optional): a string such as 'extend', 'identity', or 'clamp'
- `extrapolateLeft` (optional): a string such as 'extend', 'identity', or 'clamp'
- `extrapolateRight` (optional): a string such as 'extend', 'identity', or 'clamp'

---

### `animate()`

```tsx
animate(animation, callback);
```

Typically only used internally, but could be used by a custom Animation class.

**Parameters:**

| Name      | Type      | Required | Description         |
| --------- | --------- | -------- | ------------------- |
| animation | Animation | Yes      | See `Animation.js`. |
| callback  | function  | Yes      | Callback function.  |