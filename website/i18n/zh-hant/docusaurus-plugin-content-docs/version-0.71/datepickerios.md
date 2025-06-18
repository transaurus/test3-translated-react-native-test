---
id: datepickerios
title: '🚧 DatePickerIOS'
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

> **已棄用。** 請改用 [社群套件](https://reactnative.directory/?search=datepicker) 之一。

使用 `DatePickerIOS` 在 iOS 上渲染日期/時間選擇器（選擇器）。這是一個受控元件，因此您必須掛接到 `onDateChange` 回調並更新 `date` 屬性，以便元件更新，否則使用者的更改將立即還原以反映 `props.date` 作為真實來源。

### 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=DatePickerIOS&supportedPlatforms=ios
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

</TabItem>
<TabItem value="classical">

```SnackPlayer name=DatePickerIOS&supportedPlatforms=ios
import React, {Component} from 'react';
import {DatePickerIOS, View, StyleSheet} from 'react-native';

export default class App extends Component {
  state = {
    chosenDate: new Date(),
  };

  render() {
    return (
      <View style={styles.container}>
        <DatePickerIOS
          date={this.state.chosenDate}
          onDateChange={newDate => this.setState({chosenDate: newDate})}
        />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
});
```

</TabItem>
</Tabs>

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

日期更改處理程序。

當使用者在 UI 中更改日期或時間時調用此函數。第一個也是唯一的參數是一個事件。要獲取選擇器更改後的日期，請改用 onDateChange。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `onDateChange`

日期更改處理程序。

當使用者在 UI 中更改日期或時間時調用此函數。第一個也是唯一的參數是一個代表新日期和時間的 Date 物件。

| Type     | Required |
| -------- | -------- |
| function | Yes      |

---

### `maximumDate`

最大日期。

限制可能的日期/時間值的範圍。

| Type | Required |
| ---- | -------- |
| Date | No       |

將 `maximumDate` 設置為 2017 年 12 月 31 日的範例：

<center><img src="/docs/assets/DatePickerIOS/maximumDate.gif" width="360"></img></center>

---

### `minimumDate`

最小日期。

限制可能的日期/時間值的範圍。

| Type | Required |
| ---- | -------- |
| Date | No       |

請參閱 [`maximumDate`](#maximumdate) 的範例圖片。

---

### `minuteInterval`

可以選擇分鐘的間隔。

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

日期選擇器的地區設置。值需要是一個 [地區 ID](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/LanguageandLocaleIDs/LanguageandLocaleIDs.html)。

| Type   | Required |
| ------ | -------- |
| String | No       |

---

### `timeZoneOffsetInMinutes`

時區偏移（分鐘）。

默認情況下，日期選擇器將使用設備的時區。使用此參數，可以強制使用特定的時區偏移。例如，要顯示太平洋標準時間，請傳遞 -7 * 60。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `initialDate`

提供一個初始值，當用戶開始選擇日期時會發生變化。這適用於您不想處理監聽事件和更新日期屬性來保持受控狀態同步的使用場景。已知受控狀態存在一些錯誤，可能導致其與原生狀態不同步。initialDate 屬性旨在讓您以原生狀態作為單一數據源。

| Type | Required |
| ---- | -------- |
| Date | No       |