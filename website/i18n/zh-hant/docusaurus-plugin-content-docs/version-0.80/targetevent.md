---
id: targetevent
title: TargetEvent Object Type
---

`TargetEvent` 物件會在焦點變更時（例如 [TextInput](textinput) 元件的 `onFocus` 或 `onBlur` 事件）作為回調結果返回。

## 範例

```
{
    target: 1127
}
```

## 鍵值與數值

### `target`

接收 TargetEvent 的元素的節點 ID。

| Type                        | Optional |
| --------------------------- | -------- |
| number, `null`, `undefined` | No       |

## 使用元件

- [`TextInput`](textinput)
- [`TouchableWithoutFeedback`](touchablewithoutfeedback)