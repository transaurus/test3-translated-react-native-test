---
id: usewindowdimensions
title: useWindowDimensions
---

```tsx
import {useWindowDimensions} from 'react-native';
```

`useWindowDimensions` 會在螢幕尺寸或字體縮放比例變更時自動更新所有數值。您可以透過以下方式取得應用程式視窗的寬度和高度：

```tsx
const {height, width} = useWindowDimensions();
```

## 範例

```SnackPlayer name=useWindowDimensions&supportedPlatforms=ios,android
import React from 'react';
import {StyleSheet, Text, useWindowDimensions} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const {height, width, scale, fontScale} = useWindowDimensions();
  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text style={styles.header}>Window Dimension Data</Text>
        <Text>Height: {height}</Text>
        <Text>Width: {width}</Text>
        <Text>Font scale: {fontScale}</Text>
        <Text>Pixel ratio: {scale}</Text>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  header: {
    fontSize: 20,
    marginBottom: 12,
  },
});

export default App;
```

## 屬性

### `fontScale`

```tsx
useWindowDimensions().fontScale;
```

當前使用的字體縮放比例。部分作業系統允許使用者調整字體大小以提升閱讀舒適度，此屬性可讓您獲知當前生效的縮放值。

---

### `height`

```tsx
useWindowDimensions().height;
```

應用程式佔用的視窗或螢幕高度（像素單位）。

---

### `scale`

```tsx
useWindowDimensions().scale;
```

應用程式運行裝置的像素密度比例，可能值為：

- `1`：表示1點等於1像素（通常為96 PPI/DPI，部分平台為76）
- `2` 或 `3`：表示Retina或高DPI顯示器

---

### `width`

```tsx
useWindowDimensions().width;
```

應用程式佔用的視窗或螢幕寬度（像素單位）。