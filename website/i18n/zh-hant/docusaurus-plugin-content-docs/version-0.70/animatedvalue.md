---
id: animatedvalue
title: Animated.Value
---

驅動動畫的標準值。一個 `Animated.Value` 可以同步驅動多個屬性，但一次只能由一種機制驅動。使用新機制（例如啟動新動畫或調用 `setValue`）將停止先前的所有機制。

通常初始化為 `new Animated.Value(0);`

---

# 參考文檔

## 方法

### `setValue()`

```jsx
setValue(value);
```

直接設定數值。這將停止該值上運行的所有動畫，並更新所有綁定的屬性。

**參數：**

| Name  | Type   | Required | Description |
| ----- | ------ | -------- | ----------- |
| value | number | Yes      | Value       |

---

### `setOffset()`

```jsx
setOffset(offset);
```

設定一個偏移量，該偏移量會應用於無論是通過 `setValue`、動畫還是 `Animated.event` 設定的值之上。適用於補償像手勢開始時的偏移。

**參數：**

| Name   | Type   | Required | Description  |
| ------ | ------ | -------- | ------------ |
| offset | number | Yes      | Offset value |

---

### `flattenOffset()`

```jsx
flattenOffset();
```

將偏移量合併到基礎值中，並將偏移量重置為零。數值的最終輸出保持不變。

---

### `extractOffset()`

```jsx
extractOffset();
```

將偏移量設定為基礎值，並將基礎值重置為零。數值的最終輸出保持不變。

---

### `addListener()`

```jsx
addListener(callback);
```

為數值添加異步監聽器，以便觀察動畫的更新。這很有用，因為無法同步讀取數值，因為它可能由原生驅動。

返回一個字符串，作為監聽器的標識符。

**參數：**

| Name     | Type     | Required | Description                                                                                 |
| -------- | -------- | -------- | ------------------------------------------------------------------------------------------- |
| callback | function | Yes      | The callback function which will receive an object with a `value` key set to the new value. |

---

### `removeListener()`

```jsx
removeListener(id);
```

取消註冊監聽器。`id` 參數應與先前由 `addListener()` 返回的標識符匹配。

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

停止任何正在運行的動畫或追蹤。`callback` 會在停止動畫後以最終值調用，這對於將狀態更新為與佈局匹配的動畫位置很有用。

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

### `interpolate()`

```jsx
interpolate(config);
```

在更新屬性之前對數值進行插值，例如將 0-1 映射到 0-10。

參見 `AnimatedInterpolation.js`

**參數：**

| Name   | Type   | Required | Description |
| ------ | ------ | -------- | ----------- |
| config | object | Yes      | See below.  |

`config` 對象由以下鍵組成：

- `inputRange`: 數字陣列
- `outputRange`: 數字或字串陣列
- `easing` (選填): 接收輸入數字並返回數字的函式
- `extrapolate` (選填): 字串，如 'extend'、'identity' 或 'clamp'
- `extrapolateLeft` (選填): 字串，如 'extend'、'identity' 或 'clamp'
- `extrapolateRight` (選填): 字串，如 'extend'、'identity' 或 'clamp'

---

### `animate()`

```jsx
animate(animation, callback);
```

通常僅在內部使用，但可被自定義動畫類別調用。

**參數:**

| Name      | Type      | Required | Description         |
| --------- | --------- | -------- | ------------------- |
| animation | Animation | Yes      | See `Animation.js`. |
| callback  | function  | Yes      | Callback function.  |

---

### `stopTracking()`

```jsx
stopTracking();
```

通常僅在內部使用。

---

### `track()`

```jsx
track(tracking);
```

通常僅在內部使用。

**參數:**

| Name     | Type         | Required | Description           |
| -------- | ------------ | -------- | --------------------- |
| tracking | AnimatedNode | Yes      | See `AnimatedNode.js` |