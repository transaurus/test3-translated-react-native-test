---
id: view-style-props
title: View Style Props
---

### 範例

```SnackPlayer name=ViewStyleProps
import React from "react";
import { View, StyleSheet } from "react-native";

const ViewStyleProps = () => {
    return (
      <View style={styles.container}>
        <View style={styles.top} />
        <View style={styles.middle} />
        <View style={styles.bottom} />
      </View>
    );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "space-between",
    backgroundColor: "#fff",
    padding: 20,
    margin: 10,
  },
  top: {
    flex: 0.3,
    backgroundColor: "grey",
    borderWidth: 5,
    borderTopLeftRadius: 20,
    borderTopRightRadius: 20,
  },
  middle: {
    flex: 0.3,
    backgroundColor: "beige",
    borderWidth: 5,
  },
  bottom: {
    flex: 0.3,
    backgroundColor: "pink",
    borderWidth: 5,
    borderBottomLeftRadius: 20,
    borderBottomRightRadius: 20,
  },
});

export default ViewStyleProps;
```

# 參考文獻

## 屬性

### `backfaceVisibility`

| Type                          |
| ----------------------------- |
| enum(`'visible'`, `'hidden'`) |

---

### `backgroundColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderBottomColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderBottomEndRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomLeftRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomRightRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomStartRadius`

| Type   |
| ------ |
| number |

---

### `borderBottomWidth`

| Type   |
| ------ |
| number |

---

### `borderColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderEndColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderLeftColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderLeftWidth`

| Type   |
| ------ |
| number |

---

### `borderRadius`

如果圓角邊框不可見，請嘗試同時套用 `overflow: 'hidden'`。

| Type   |
| ------ |
| number |

---

### `borderRightColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderRightWidth`

| Type   |
| ------ |
| number |

---

### `borderStartColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderStyle`

| Type                                    |
| --------------------------------------- |
| enum(`'solid'`, `'dotted'`, `'dashed'`) |

---

### `borderTopColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `borderTopEndRadius`

| Type   |
| ------ |
| number |

---

### `borderTopLeftRadius`

| Type   |
| ------ |
| number |

---

### `borderTopRightRadius`

| Type   |
| ------ |
| number |

---

### `borderTopStartRadius`

| Type   |
| ------ |
| number |

---

### `borderTopWidth`

| Type   |
| ------ |
| number |

---

### `borderWidth`

| Type   |
| ------ |
| number |

---

### `elevation` <div class="label android">Android</div>

設定視圖的高度（elevation），使用 Android 底層的 [elevation API](https://developer.android.com/training/material/shadows-clipping.html#Elevation)。這會為項目添加陰影效果，並影響重疊視圖的 z 軸順序。僅支援 Android 5.0 及以上版本，對較早版本無效。

| Type   |
| ------ |
| number |

---

### `opacity`

| Type   |
| ------ |
| number |