---
id: pressevent
title: PressEvent Object Type
---

`PressEvent` 物件會在使用者按壓互動（例如 [Button](button) 元件的 `onPress`）的回呼函式中被回傳。

## 範例

```js
{
    changedTouches: [PressEvent],
    identifier: 1,
    locationX: 8,
    locationY: 4.5,
    pageX: 24,
    pageY: 49.5,
    target: 1127,
    timestamp: 85131876.58868201,
    touches: []
}
```

## 鍵值與數值

### `changedTouches`

自上次事件後所有已變更的 PressEvent 陣列。

| Type                 | Optional |
| -------------------- | -------- |
| array of PressEvents | No       |

### `force` <div class="label ios">iOS</div>

3D Touch 按壓過程中施加的壓力值。回傳範圍從 `0.0` 到 `1.0` 的浮點數。

| Type   | Optional |
| ------ | -------- |
| number | Yes      |

### `identifier`

分配給該事件的唯一數字識別碼。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `locationX`

觸控起始點在可觸控區域內的 X 座標（相對於元素）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `locationY`

觸控起始點在可觸控區域內的 Y 座標（相對於元素）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `pageX`

觸控起始點在螢幕上的 X 座標（相對於根視圖）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `pageY`

觸控起始點在螢幕上的 Y 座標（相對於根視圖）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `target`

接收 PressEvent 的元素的節點 ID。

| Type                        | Optional |
| --------------------------- | -------- |
| number, `null`, `undefined` | No       |

### `timestamp`

PressEvent 發生時的時間戳記。數值以毫秒為單位。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `touches`

當前螢幕上所有 PressEvent 的陣列。

| Type                 | Optional |
| -------------------- | -------- |
| array of PressEvents | No       |

## 使用此物件的元件

- [`Button`](button)
- [`PanResponder`](panresponder)
- [`Pressable`](pressable)
- [`ScrollView`](scrollview)
- [`Text`](text)
- [`TextInput`](textinput)
- [`TouchableHighlight`](touchablenativefeedback)
- [`TouchableOpacity`](touchablewithoutfeedback)
- [`TouchableNativeFeedback`](touchablenativefeedback)
- [`TouchableWithoutFeedback`](touchablewithoutfeedback)
- [`View`](view)