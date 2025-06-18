---
id: slider
title: '🚧 Slider'
---

> **已棄用。** 請改用 [社群套件](https://reactnative.directory/?search=slider) 中的其中一個。

用於從數值範圍中選取單一值的元件。

---

# 參考文獻

## 屬性

繼承 [View 屬性](view.md#props)。

### `style`

用於設定 `Slider` 的樣式與佈局。詳見 `StyleSheet.js` 和 `ViewStylePropTypes.js`。

| Type       | Required |
| ---------- | -------- |
| View.style | No       |

---

### `disabled`

若為 true，使用者將無法移動滑桿。預設值為 false。

| Type | Required |
| ---- | -------- |
| bool | No       |

---

### `maximumValue`

滑桿的初始最大值。預設值為 1。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `minimumTrackTintColor`

用於按鈕左側軌道的顏色。會覆寫 iOS 上預設的藍色漸層圖片。

| Type               | Required |
| ------------------ | -------- |
| [color](colors.md) | No       |

---

### `minimumValue`

滑桿的初始最小值。預設值為 0。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `onSlidingComplete`

當使用者放開滑桿時呼叫的回呼函式，無論數值是否已變更。目前的數值會作為引數傳遞給回呼處理函式。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `onValueChange`

在使用者拖曳滑桿時持續呼叫的回呼函式。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `step`

滑桿的步進值。數值應介於 0 和 (maximumValue - minimumValue) 之間。預設值為 0。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `maximumTrackTintColor`

用於按鈕右側軌道的顏色。會覆寫 iOS 上預設的灰色漸層圖片。

| Type               | Required |
| ------------------ | -------- |
| [color](colors.md) | No       |

---

### `testID`

用於在 UI 自動化測試中定位此檢視。

| Type   | Required |
| ------ | -------- |
| string | No       |

---

### `value`

滑桿的初始值。數值應介於 minimumValue 和 maximumValue 之間，預設分別為 0 和 1。預設值為 0。

_這不是受控元件_，您不需要在拖曳期間更新數值。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `thumbTintColor`

用於在 iOS 上為預設拇指圖片著色的顏色，或在 Android 上為前景開關握把的顏色。

| Type               | Required |
| ------------------ | -------- |
| [color](colors.md) | No       |

---

### `maximumTrackImage`

指定最大軌道圖片。僅支援靜態圖片。圖片的最左側像素將被拉伸以填滿軌道。

| Type                   | Required | Platform |
| ---------------------- | -------- | -------- |
| Image.propTypes.source | No       | iOS      |

---

### `minimumTrackImage`

指定最小軌道的圖片。僅支援靜態圖片。圖片最右側的像素將被拉伸以填滿軌道。

| Type                   | Required | Platform |
| ---------------------- | -------- | -------- |
| Image.propTypes.source | No       | iOS      |

---

### `thumbImage`

設定滑塊的圖片。僅支援靜態圖片。

| Type                   | Required | Platform |
| ---------------------- | -------- | -------- |
| Image.propTypes.source | No       | iOS      |

---

### `trackImage`

為軌道指定單一圖片。僅支援靜態圖片。圖片的中心像素將被拉伸以填滿軌道。

| Type                   | Required | Platform |
| ---------------------- | -------- | -------- |
| Image.propTypes.source | No       | iOS      |