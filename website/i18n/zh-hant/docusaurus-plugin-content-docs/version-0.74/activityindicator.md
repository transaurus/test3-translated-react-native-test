---
id: activityindicator
title: ActivityIndicator
---

顯示圓形載入指示器。

## 範例

```SnackPlayer name=ActivityIndicator%20Example
import React from 'react';
import {ActivityIndicator, StyleSheet, View} from 'react-native';

const App = () => (
  <View style={[styles.container, styles.horizontal]}>
    <ActivityIndicator />
    <ActivityIndicator size="large" />
    <ActivityIndicator size="small" color="#0000ff" />
    <ActivityIndicator size="large" color="#00ff00" />
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  horizontal: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    padding: 10,
  },
});

export default App;
```

# 參考

## 屬性

### [View 屬性](view#props)

繼承 [View 屬性](view#props)。

---

### `animating`

是否顯示指示器（`true`）或隱藏它（`false`）。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `color`

指示器的前景顏色。

| Type            | Default                                                                                                                                                                                     |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [color](colors) | `null` (system accent default color)<div class="label android">Android</div><hr/><ins style={{background: '#999'}} className="color-box" />`'#999999'` <div className="label ios">iOS</div> |

---

### `hidesWhenStopped` <div class="label ios">iOS</div>

當未動畫時是否隱藏指示器。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `size`

指示器的大小。

| Type                                                                           | Default   |
| ------------------------------------------------------------------------------ | --------- |
| enum(`'small'`, `'large'`)<hr/>number <div class="label android">Android</div> | `'small'` |