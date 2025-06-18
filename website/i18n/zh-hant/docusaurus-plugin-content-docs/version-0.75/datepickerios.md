---
id: datepickerios
title: '🚧 DatePickerIOS'
---

> **Removed.** Use one of the [community packages](https://reactnative.directory/?search=datepicker) instead.

Use `DatePickerIOS` to render a date/time picker (selector) on iOS. This is a controlled component, so you must hook in to the `onDateChange` callback and update the `date` prop in order for the component to update, otherwise the user's change will be reverted immediately to reflect `props.date` as the source of truth.

### Example

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

# Reference

## Props

Inherits [View Props](view.md#props).

### `date`

The currently selected date.

| Type | Required |
| ---- | -------- |
| Date | Yes      |

---

### `onChange`

Date change handler.

This is called when the user changes the date or time in the UI. The first and only argument is an Event. For getting the date the picker was changed to, use onDateChange instead.

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `onDateChange`

Date change handler.

This is called when the user changes the date or time in the UI. The first and only argument is a Date object representing the new date and time.

| Type     | Required |
| -------- | -------- |
| function | Yes      |

---

### `maximumDate`

Maximum date.

Restricts the range of possible date/time values.

| Type | Required |
| ---- | -------- |
| Date | No       |

Example with `maximumDate` set to December 31, 2017:

<center><img src="/docs/assets/DatePickerIOS/maximumDate.gif" width="360"></img></center>

---

### `minimumDate`

Minimum date.

Restricts the range of possible date/time values.

| Type | Required |
| ---- | -------- |
| Date | No       |

See [`maximumDate`](#maximumdate) for an example image.

---

### `minuteInterval`

The interval at which minutes can be selected.

| Type                                       | Required |
| ------------------------------------------ | -------- |
| enum(1, 2, 3, 4, 5, 6, 10, 12, 15, 20, 30) | No       |

Example with `minuteInterval` set to `10`:

<center><img src="/docs/assets/DatePickerIOS/minuteInterval.png" width="360"></img></center>

---

### `mode`

The date picker mode.

| Type                                          | Required |
| --------------------------------------------- | -------- |
| enum('date', 'time', 'datetime', 'countdown') | No       |

Example with `mode` set to `date`, `time`, and `datetime`: ![](/docs/assets/DatePickerIOS/mode.png)

---

### `locale`

The locale for the date picker. Value needs to be a [Locale ID](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/LanguageandLocaleIDs/LanguageandLocaleIDs.html).

| Type   | Required |
| ------ | -------- |
| String | No       |

---

### `timeZoneOffsetInMinutes`

Timezone offset in minutes.

By default, the date picker will use the device's timezone. With this parameter, it is possible to force a certain timezone offset. For instance, to show times in Pacific Standard Time, pass -7 \* 60.

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `initialDate`

提供一個初始值，當用戶開始選擇日期時會發生變化。這適用於您不想處理監聽事件和更新日期屬性來保持受控狀態同步的使用場景。受控狀態存在已知錯誤，可能導致其與原生狀態不同步。initialDate 屬性的目的是讓您能將原生狀態作為單一數據源。

| Type | Required |
| ---- | -------- |
| Date | No       |