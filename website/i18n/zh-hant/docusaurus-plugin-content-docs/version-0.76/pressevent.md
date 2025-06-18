---
id: pressevent
title: PressEvent Object Type
---

`PressEvent` 物件會在使用者按壓互動的回調函數中返回，例如 [Button](button) 元件中的 `onPress`。

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

自上次事件以來所有已變更的 PressEvent 陣列。

| Type                 | Optional |
| -------------------- | -------- |
| array of PressEvents | No       |

### `force` <div class="label ios">iOS</div>

3D Touch 按壓過程中使用的力度大小。返回範圍從 `0.0` 到 `1.0` 的浮點數值。

| Type   | Optional |
| ------ | -------- |
| number | Yes      |

### `identifier`

分配給事件的唯一數字識別碼。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `locationX`

觸控起源在可觸控區域內的 X 座標（相對於元素）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `locationY`

觸控起源在可觸控區域內的 Y 座標（相對於元素）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `pageX`

觸控起源在螢幕上的 X 座標（相對於根視圖）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `pageY`

觸控起源在螢幕上的 Y 座標（相對於根視圖）。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `target`

接收 PressEvent 的元素的節點 ID。

| Type                        | Optional |
| --------------------------- | -------- |
| number, `null`, `undefined` | No       |

### `timestamp`

PressEvent 發生時的時間戳記值。數值以毫秒表示。

| Type   | Optional |
| ------ | -------- |
| number | No       |

### `touches`

螢幕上所有當前 PressEvent 的陣列。

| Type                 | Optional |
| -------------------- | -------- |
| array of PressEvents | No       |

## 使用於

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