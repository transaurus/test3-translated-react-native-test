---
id: datepickerios
title: '🚧 DatePickerIOS'
---

> **已移除。** 請改用 [社群套件](https://reactnative.directory/?search=datepicker) 之一。

使用 `DatePickerIOS` 在 iOS 上渲染日期/時間選擇器（選擇器）。這是一個受控元件，因此您必須掛接到 `onDateChange` 回調並更新 `date` 屬性，以便元件更新，否則使用者的變更將立即還原以反映 `props.date` 作為真實來源。

### 範例

```SnackPlayer name=DatePickerIOS&supportedPlatforms=ios&disableLinting=true
import React, {useState} from 'react';
import {DatePickerIOS, View, StyleSheet} from 'react-native';

const App = () => {
  const [chosenDate, setChosenDate] = useState(new Date());

  return (
    <View style={styles.container}>
      <DatePickerIOS date={chosenDate} onDateChange={setChosenDate} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
});

export default App;
```

---

# 參考

## 屬性

繼承 [View 屬性](view.md#props)。

### `date`

當前選定的日期。

| Type | Required |
| ---- | -------- |
| Date | Yes      |

---

### `onChange`

日期變更處理器。

當使用者在 UI 中更改日期或時間時調用此函數。第一個也是唯一的參數是一個事件。要獲取選擇器更改後的日期，請改用 onDateChange。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `onDateChange`

日期變更處理器。

當使用者在 UI 中更改日期或時間時調用此函數。第一個也是唯一的參數是一個代表新日期和時間的 Date 物件。

| Type     | Required |
| -------- | -------- |
| function | Yes      |

---

### `maximumDate`

最大日期。

限制可能的日期/時間值範圍。

| Type | Required |
| ---- | -------- |
| Date | No       |

將 `maximumDate` 設置為 2017 年 12 月 31 日的範例：

<center><img src="/docs/assets/DatePickerIOS/maximumDate.gif" width="360"></img></center>

---

### `minimumDate`

最小日期。

限制可能的日期/時間值範圍。

| Type | Required |
| ---- | -------- |
| Date | No       |

請參閱 [`maximumDate`](#maximumdate) 的範例圖片。

---

### `minuteInterval`

可選擇分鐘的間隔。

| Type                                       | Required |
| ------------------------------------------ | -------- |
| enum(1, 2, 3, 4, 5, 6, 10, 12, 15, 20, 30) | No       |

將 `minuteInterval` 設置為 `10` 的範例：

<center><img src="/docs/assets/DatePickerIOS/minuteInterval.png" width="360"></img></center>

---

### `mode`

日期選擇器模式。

| Type                                          | Required |
| --------------------------------------------- | -------- |
| enum('date', 'time', 'datetime', 'countdown') | No       |

將 `mode` 設置為 `date`、`time` 和 `datetime` 的範例：![](/docs/assets/DatePickerIOS/mode.png)

---

### `locale`

日期選擇器的地區設置。值需要是 [地區 ID](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/LanguageandLocaleIDs/LanguageandLocaleIDs.html)。

| Type   | Required |
| ------ | -------- |
| String | No       |

---

### `timeZoneOffsetInMinutes`

時區偏移量（分鐘）。

預設情況下，日期選擇器將使用設備的時區。使用此參數，可以強制使用特定的時區偏移量。例如，要顯示太平洋標準時間，請傳遞 -7 * 60。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `initialDate`

Provides an initial value that will change when the user starts selecting a date. It is useful for use-cases where you do not want to deal with listening to events and updating the date prop to keep the controlled state in sync. The controlled state has known bugs which causes it to go out of sync with native. The initialDate prop is intended to allow you to have native be source of truth.

| Type | Required |
| ---- | -------- |
| Date | No       |