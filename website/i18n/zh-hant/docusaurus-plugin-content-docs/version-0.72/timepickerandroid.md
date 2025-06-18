---
id: timepickerandroid
title: '🚧 TimePickerAndroid'
---

> **已移除。** 請改用 [社群套件](https://reactnative.directory/?search=timepicker) 中的替代方案。

開啟標準的 Android 時間選擇器對話框。

### 範例

```jsx
try {
  const {action, hour, minute} = await TimePickerAndroid.open({
    hour: 14,
    minute: 0,
    is24Hour: false, // Will display '2 PM'
  });
  if (action !== TimePickerAndroid.dismissedAction) {
    // Selected hour (0-23), minute (0-59)
  }
} catch ({code, message}) {
  console.warn('Cannot open time picker', message);
}
```

---

# 參考文獻

## 方法

### `open()`

```jsx
static open(options)
```

開啟標準的 Android 時間選擇器對話框。

`options` 物件可用的鍵值包括：

- `hour` (0-23) - 顯示的小時，預設為當前時間
- `minute` (0-59) - 顯示的分鐘，預設為當前時間
- `is24Hour` (布林值) - 若為 `true`，選擇器使用 24 小時制。若為 `false`，選擇器顯示 AM/PM 選擇器。若未定義，則使用當前地區的預設值。
- `mode` (`enum('clock', 'spinner', 'default')`) - 設定時間選擇器模式
  - 'clock': 以時鐘模式顯示時間選擇器。
  - 'spinner': 以滾輪模式顯示時間選擇器。
  - 'default': 根據 Android 版本顯示預設的時間選擇器。

回傳一個 Promise，當使用者選擇時間時，將以包含 `action`、`hour` (0-23)、`minute` (0-59) 的物件呼叫。若使用者關閉對話框，Promise 仍會以 `action` 為 `TimePickerAndroid.dismissedAction` 且其他鍵值為未定義的狀態解析。**務必**在讀取值之前檢查 `action`。

---

### `timeSetAction()`

```jsx
static timeSetAction()
```

已選擇時間。

---

### `dismissedAction()`

```jsx
static dismissedAction()
```

對話框已關閉。