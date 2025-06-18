---
id: shadow-props
title: Shadow Props
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Shadow%20Props&supportedPlatforms=ios&ext=js&dependencies=@react-native-community/slider
import React, {useState} from 'react';
import {Text, View, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';
import Slider from '@react-native-community/slider';

const ShadowPropSlider = ({label, value, ...props}) => {
  return (
    <>
      <Text>
        {label} ({value.toFixed(2)})
      </Text>
      <Slider step={1} value={value} {...props} />
    </>
  );
};

const App = () => {
  const [shadowOffsetWidth, setShadowOffsetWidth] = useState(0);
  const [shadowOffsetHeight, setShadowOffsetHeight] = useState(0);
  const [shadowRadius, setShadowRadius] = useState(0);
  const [shadowOpacity, setShadowOpacity] = useState(0.1);

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <View
          style={[
            styles.square,
            {
              shadowOffset: {
                width: shadowOffsetWidth,
                height: -shadowOffsetHeight,
              },
              shadowOpacity,
              shadowRadius,
            },
          ]}
        />
        <View style={styles.controls}>
          <ShadowPropSlider
            label="shadowOffset - X"
            minimumValue={-50}
            maximumValue={50}
            value={shadowOffsetWidth}
            onValueChange={setShadowOffsetWidth}
          />
          <ShadowPropSlider
            label="shadowOffset - Y"
            minimumValue={-50}
            maximumValue={50}
            value={shadowOffsetHeight}
            onValueChange={setShadowOffsetHeight}
          />
          <ShadowPropSlider
            label="shadowRadius"
            minimumValue={0}
            maximumValue={100}
            value={shadowRadius}
            onValueChange={setShadowRadius}
          />
          <ShadowPropSlider
            label="shadowOpacity"
            minimumValue={0}
            maximumValue={1}
            step={0.05}
            value={shadowOpacity}
            onValueChange={val => setShadowOpacity(val)}
          />
        </View>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'space-around',
    backgroundColor: '#ecf0f1',
    padding: 8,
  },
  square: {
    alignSelf: 'center',
    backgroundColor: 'white',
    borderRadius: 4,
    height: 150,
    shadowColor: 'black',
    width: 150,
  },
  controls: {
    paddingHorizontal: 12,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Shadow%20Props&supportedPlatforms=ios&ext=tsx&dependencies=@react-native-community/slider
import React, {useState} from 'react';
import {Text, View, StyleSheet} from 'react-native';
import Slider, {SliderProps} from '@react-native-community/slider';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

type ShadowPropSliderProps = SliderProps & {
  label: string;
};

const ShadowPropSlider = ({label, value, ...props}: ShadowPropSliderProps) => {
  return (
    <>
      <Text>
        {label} ({value?.toFixed(2)})
      </Text>
      <Slider step={1} value={value} {...props} />
    </>
  );
};

const App = () => {
  const [shadowOffsetWidth, setShadowOffsetWidth] = useState(0);
  const [shadowOffsetHeight, setShadowOffsetHeight] = useState(0);
  const [shadowRadius, setShadowRadius] = useState(0);
  const [shadowOpacity, setShadowOpacity] = useState(0.1);

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <View
          style={[
            styles.square,
            {
              shadowOffset: {
                width: shadowOffsetWidth,
                height: -shadowOffsetHeight,
              },
              shadowOpacity,
              shadowRadius,
            },
          ]}
        />
        <View style={styles.controls}>
          <ShadowPropSlider
            label="shadowOffset - X"
            minimumValue={-50}
            maximumValue={50}
            value={shadowOffsetWidth}
            onValueChange={setShadowOffsetWidth}
          />
          <ShadowPropSlider
            label="shadowOffset - Y"
            minimumValue={-50}
            maximumValue={50}
            value={shadowOffsetHeight}
            onValueChange={setShadowOffsetHeight}
          />
          <ShadowPropSlider
            label="shadowRadius"
            minimumValue={0}
            maximumValue={100}
            value={shadowRadius}
            onValueChange={setShadowRadius}
          />
          <ShadowPropSlider
            label="shadowOpacity"
            minimumValue={0}
            maximumValue={1}
            step={0.05}
            value={shadowOpacity}
            onValueChange={val => setShadowOpacity(val)}
          />
        </View>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'space-around',
    backgroundColor: '#ecf0f1',
    padding: 8,
  },
  square: {
    alignSelf: 'center',
    backgroundColor: 'white',
    borderRadius: 4,
    height: 150,
    shadowColor: 'black',
    width: 150,
  },
  controls: {
    paddingHorizontal: 12,
  },
});

export default App;
```

</TabItem>
</Tabs>

---

# 參考文獻

React Native 提供三組陰影 API：

- `boxShadow`：一個 View 樣式屬性，符合 [同名網頁樣式屬性](https://developer.mozilla.org/en-US/docs/Web/CSS/box-shadow) 的規範。
- `dropShadow`：作為 [`filter`](./view-style-props#filter) View 樣式屬性的一部分提供的特定濾鏡函數。
- 各種 `shadow` 屬性（`shadowColor`、`shadowOffset`、`shadowOpacity`、`shadowRadius`）：這些直接映射到平台級 API 暴露的原生對應項。

`dropShadow` 和 `boxShadow` 的區別如下：

- `dropShadow` 是 `filter` 的一部分，而 `boxShadow` 是一個獨立的樣式屬性。
- `dropShadow` 是一個 alpha 遮罩，因此只有具有正 alpha 值的像素才會「投射」陰影。`boxShadow` 則會圍繞元素的邊框盒投射陰影，無論其內容如何（除非是內陰影）。
- `dropShadow` 僅在 Android 上可用，`boxShadow` 在 iOS 和 Android 上均可使用。
- `dropShadow` 不能像 `boxShadow` 那樣設置為內陰影。
- `dropShadow` 沒有像 `boxShadow` 那樣的 `spreadDistance` 參數。

`boxShadow` 和 `dropShadow` 通常比 `shadow` 屬性更強大。然而，`shadow` 屬性映射到原生平台級 API，因此如果你只需要一個簡單的陰影，建議使用這些屬性。請注意，只有 `shadowColor` 在 Android 和 iOS 上均可使用，其他 `shadow` 屬性僅在 iOS 上有效。

## 屬性

### `boxShadow`

請參閱 [View 樣式屬性](./view-style-props#boxshadow) 以獲取文檔。

### `dropShadow` <div class="label android">Android</div>

請參閱 [View 樣式屬性](./view-style-props#filter) 以獲取文檔。

### `shadowColor`

設置陰影顏色。

此屬性僅在 Android API 28 及以上版本中有效。對於較低 Android API 的類似功能，請使用 [`elevation` 屬性](view-style-props#elevation-android)。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `shadowOffset` <div class="label ios">iOS</div>

設置陰影偏移量。

| Type                                     |
| ---------------------------------------- |
| object: `{width: number,height: number}` |

---

### `shadowOpacity` <div class="label ios">iOS</div>

設置陰影不透明度（乘以顏色的 alpha 分量）。

| Type   |
| ------ |
| number |

---

### `shadowRadius` <div class="label ios">iOS</div>

設置陰影模糊半徑。

| Type   |
| ------ |
| number |