---
id: animatedvalue
title: Animated.Value
---

驅動動畫的標準值。一個 `Animated.Value` 可以同步驅動多個屬性，但一次只能由一種機制驅動。使用新機制（例如啟動新動畫或調用 `setValue`）將停止先前的任何機制。

通常在類組件中使用 `useAnimatedValue(0);` 或 `new Animated.Value(0);` 初始化。

---

# 參考

## 方法

### `setValue()`

```tsx
setValue(value: number);
```

直接設定值。這將停止在該值上運行的任何動畫並更新所有綁定的屬性。

**參數：**

| Name  | Type   | Required | Description |
| ----- | ------ | -------- | ----------- |
| value | number | Yes      | Value       |

---

### `setOffset()`

```tsx
setOffset(offset: number);
```

設定一個偏移量，該偏移量會應用於無論是通過 `setValue`、動畫還是 `Animated.event` 設定的值之上。對於補償像平移手勢的開始這樣的情況很有用。

**參數：**

| Name   | Type   | Required | Description  |
| ------ | ------ | -------- | ------------ |
| offset | number | Yes      | Offset value |

---

### `flattenOffset()`

```tsx
flattenOffset();
```

將偏移量值合併到基礎值中並將偏移量重置為零。值的最終輸出保持不變。

---

### `extractOffset()`

```tsx
extractOffset();
```

將偏移量值設定為基礎值，並將基礎值重置為零。值的最終輸出保持不變。

---

### `addListener()`

```tsx
addListener(callback: (state: {value: number}) => void): string;
```

向值添加異步監聽器，以便您可以觀察來自動畫的更新。這很有用，因為無法同步讀取值，因為它可能是由原生驅動的。

返回一個字符串，作為監聽器的標識符。

**參數：**

| Name     | Type     | Required | Description                                                                                 |
| -------- | -------- | -------- | ------------------------------------------------------------------------------------------- |
| callback | function | Yes      | The callback function which will receive an object with a `value` key set to the new value. |

---

### `removeListener()`

```tsx
removeListener(id: string);
```

取消註冊監聽器。`id` 參數應與先前由 `addListener()` 返回的標識符匹配。

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

停止任何正在運行的動畫或跟踪。`callback` 會在停止動畫後以最終值調用，這對於更新狀態以匹配動畫位置與佈局很有用。

**參數：**

| Name     | Type     | Required | Description                                   |
| -------- | -------- | -------- | --------------------------------------------- |
| callback | function | No       | A function that will receive the final value. |

---

### `resetAnimation()`

```tsx
resetAnimation(callback?: (value: number) => void);
```

停止任何動畫並將值重置為其原始值。

**參數：**

| Name     | Type     | Required | Description                                      |
| -------- | -------- | -------- | ------------------------------------------------ |
| callback | function | No       | A function that will receive the original value. |

---

### `interpolate()`

```tsx
interpolate(config: InterpolationConfigType);
```

在更新屬性之前對值進行插值，例如將 0-1 映射到 0-10。

參見 `AnimatedInterpolation.js`

**參數：**

| Name   | Type   | Required | Description |
| ------ | ------ | -------- | ----------- |
| config | object | Yes      | See below.  |

`config` 物件由以下鍵值組成：

- `inputRange`: 數字陣列
- `outputRange`: 數字或字串陣列
- `easing` (選填): 接收輸入數字並回傳數字的函式
- `extrapolate` (選填): 字串，例如 'extend'、'identity' 或 'clamp'
- `extrapolateLeft` (選填): 字串，例如 'extend'、'identity' 或 'clamp'
- `extrapolateRight` (選填): 字串，例如 'extend'、'identity' 或 'clamp'

---

### `animate()`

```tsx
animate(animation, callback);
```

通常僅在內部使用，但可被自訂的 Animation 類別調用。

**參數：**

| Name      | Type      | Required | Description         |
| --------- | --------- | -------- | ------------------- |
| animation | Animation | Yes      | See `Animation.js`. |
| callback  | function  | Yes      | Callback function.  |