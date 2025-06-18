---
id: layout-props
title: Layout Props
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

> 關於這些屬性的更詳細範例，請參閱 [Flexbox 佈局](flexbox) 頁面。

### 範例

以下範例展示不同屬性如何影響或塑造 React Native 的佈局。您可以嘗試在變更 `flexWrap` 屬性值的同時，從 UI 中添加或移除方塊。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=LayoutProps%20Example&ext=js
import React, {useState} from 'react';
import {Button, ScrollView, StyleSheet, Text, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [flexDirection, setFlexDirection] = useState(0);
  const [justifyContent, setJustifyContent] = useState(0);
  const [alignItems, setAlignItems] = useState(0);
  const [direction, setDirection] = useState(0);
  const [wrap, setWrap] = useState(0);

  const [squares, setSquares] = useState([<Square />, <Square />, <Square />]);

  const hookedStyles = {
    flexDirection: flexDirections[flexDirection],
    justifyContent: justifyContents[justifyContent],
    alignItems: alignItemsArr[alignItems],
    direction: directions[direction],
    flexWrap: wraps[wrap],
  };

  const changeSetting = (value, options, setterFunction) => {
    if (value === options.length - 1) {
      setterFunction(0);
      return;
    }
    setterFunction(value + 1);
  };

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <View style={[styles.container, styles.playingSpace, hookedStyles]}>
          {squares.map(elem => elem)}
        </View>
        <ScrollView style={styles.layoutContainer}>
          <View style={styles.controlSpace}>
            <View style={styles.buttonView}>
              <Button
                title="Change Flex Direction"
                onPress={() =>
                  changeSetting(flexDirection, flexDirections, setFlexDirection)
                }
              />
              <Text style={styles.text}>{flexDirections[flexDirection]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Justify Content"
                onPress={() =>
                  changeSetting(
                    justifyContent,
                    justifyContents,
                    setJustifyContent,
                  )
                }
              />
              <Text style={styles.text}>{justifyContents[justifyContent]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Align Items"
                onPress={() =>
                  changeSetting(alignItems, alignItemsArr, setAlignItems)
                }
              />
              <Text style={styles.text}>{alignItemsArr[alignItems]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Direction"
                onPress={() =>
                  changeSetting(direction, directions, setDirection)
                }
              />
              <Text style={styles.text}>{directions[direction]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Flex Wrap"
                onPress={() => changeSetting(wrap, wraps, setWrap)}
              />
              <Text style={styles.text}>{wraps[wrap]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Add Square"
                onPress={() => setSquares([...squares, <Square />])}
              />
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Delete Square"
                onPress={() =>
                  setSquares(squares.filter((v, i) => i !== squares.length - 1))
                }
              />
            </View>
          </View>
        </ScrollView>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const flexDirections = ['row', 'row-reverse', 'column', 'column-reverse'];
const justifyContents = [
  'flex-start',
  'flex-end',
  'center',
  'space-between',
  'space-around',
  'space-evenly',
];
const alignItemsArr = [
  'flex-start',
  'flex-end',
  'center',
  'stretch',
  'baseline',
];
const wraps = ['nowrap', 'wrap', 'wrap-reverse'];
const directions = ['inherit', 'ltr', 'rtl'];

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  layoutContainer: {
    flex: 0.5,
  },
  playingSpace: {
    backgroundColor: 'white',
    borderColor: 'blue',
    borderWidth: 3,
    overflow: 'hidden',
  },
  controlSpace: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  buttonView: {
    width: '50%',
    padding: 10,
  },
  text: {
    textAlign: 'center',
  },
});

const Square = () => (
  <View
    style={{
      width: 50,
      height: 50,
      backgroundColor: randomHexColor(),
    }}
  />
);

const randomHexColor = () => {
  return '#000000'.replace(/0/g, () => {
    return Math.round(Math.random() * 14).toString(16);
  });
};

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=LayoutProps%20Example&ext=tsx
import React, {useState} from 'react';
import {
  Button,
  ScrollView,
  StyleSheet,
  Text,
  View,
  FlexAlignType,
  FlexStyle,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [flexDirection, setFlexDirection] = useState(0);
  const [justifyContent, setJustifyContent] = useState(0);
  const [alignItems, setAlignItems] = useState(0);
  const [direction, setDirection] = useState(0);
  const [wrap, setWrap] = useState(0);

  const [squares, setSquares] = useState([<Square />, <Square />, <Square />]);

  const hookedStyles = {
    flexDirection: flexDirections[flexDirection],
    justifyContent: justifyContents[justifyContent],
    alignItems: alignItemsArr[alignItems],
    direction: directions[direction],
    flexWrap: wraps[wrap],
  } as FlexStyle;

  const changeSetting = (
    value: number,
    options: any[],
    setterFunction: (index: number) => void,
  ) => {
    if (value === options.length - 1) {
      setterFunction(0);
      return;
    }
    setterFunction(value + 1);
  };

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <View style={[styles.container, styles.playingSpace, hookedStyles]}>
          {squares.map(elem => elem)}
        </View>
        <ScrollView style={styles.layoutContainer}>
          <View style={styles.controlSpace}>
            <View style={styles.buttonView}>
              <Button
                title="Change Flex Direction"
                onPress={() =>
                  changeSetting(flexDirection, flexDirections, setFlexDirection)
                }
              />
              <Text style={styles.text}>{flexDirections[flexDirection]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Justify Content"
                onPress={() =>
                  changeSetting(
                    justifyContent,
                    justifyContents,
                    setJustifyContent,
                  )
                }
              />
              <Text style={styles.text}>{justifyContents[justifyContent]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Align Items"
                onPress={() =>
                  changeSetting(alignItems, alignItemsArr, setAlignItems)
                }
              />
              <Text style={styles.text}>{alignItemsArr[alignItems]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Direction"
                onPress={() =>
                  changeSetting(direction, directions, setDirection)
                }
              />
              <Text style={styles.text}>{directions[direction]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Change Flex Wrap"
                onPress={() => changeSetting(wrap, wraps, setWrap)}
              />
              <Text style={styles.text}>{wraps[wrap]}</Text>
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Add Square"
                onPress={() => setSquares([...squares, <Square />])}
              />
            </View>
            <View style={styles.buttonView}>
              <Button
                title="Delete Square"
                onPress={() =>
                  setSquares(squares.filter((v, i) => i !== squares.length - 1))
                }
              />
            </View>
          </View>
        </ScrollView>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const flexDirections = [
  'row',
  'row-reverse',
  'column',
  'column-reverse',
] as FlexStyle['flexDirection'][];
const justifyContents = [
  'flex-start',
  'flex-end',
  'center',
  'space-between',
  'space-around',
  'space-evenly',
] as FlexStyle['justifyContent'][];
const alignItemsArr = [
  'flex-start',
  'flex-end',
  'center',
  'stretch',
  'baseline',
] as FlexAlignType[];
const wraps = ['nowrap', 'wrap', 'wrap-reverse'];
const directions = ['inherit', 'ltr', 'rtl'];

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  layoutContainer: {
    flex: 0.5,
  },
  playingSpace: {
    backgroundColor: 'white',
    borderColor: 'blue',
    borderWidth: 3,
    overflow: 'hidden',
  },
  controlSpace: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  buttonView: {
    width: '50%',
    padding: 10,
  },
  text: {
    textAlign: 'center',
  },
});

const Square = () => (
  <View
    style={{
      width: 50,
      height: 50,
      backgroundColor: randomHexColor(),
    }}
  />
);

const randomHexColor = () => {
  return '#000000'.replace(/0/g, () => {
    return Math.round(Math.random() * 14).toString(16);
  });
};

export default App;
```

</TabItem>
</Tabs>

---

# 參考文獻

## 屬性

### `alignContent`

`alignContent` 控制行在交叉軸方向上的對齊方式，會覆蓋父元素的 `alignContent`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/align-content。

| Type                                                                                                 | Required |
| ---------------------------------------------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'stretch', 'space-between', 'space-around', 'space-evenly') | No       |

---

### `alignItems`

`alignItems` 在交叉軸方向上對齊子元素。例如，若子元素是垂直排列，`alignItems` 則控制它們的水平對齊方式。其功能類似 CSS 的 `align-items`（預設值：stretch）。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/align-items。

| Type                                                            | Required |
| --------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'stretch', 'baseline') | No       |

---

### `alignSelf`

`alignSelf` 控制單個子元素在交叉軸上的對齊方式，會覆蓋父元素的 `alignItems`。其功能類似 CSS 的 `align-self`（預設值：auto）。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/align-self。

| Type                                                                    | Required |
| ----------------------------------------------------------------------- | -------- |
| enum('auto', 'flex-start', 'flex-end', 'center', 'stretch', 'baseline') | No       |

---

### `aspectRatio`

長寬比控制節點未定義維度的尺寸。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio。

- 在設定了寬度/高度的節點上，長寬比控制未設定維度的尺寸
- 在設定了 flex basis 的節點上，長寬比控制未設定時交叉軸上的節點尺寸
- 在具有測量函式的節點上，長寬比的作用如同測量函式測量 flex basis
- 在具有 flex grow/shrink 的節點上，長寬比控制未設定時交叉軸上的節點尺寸
- 長寬比會考慮最小/最大尺寸限制

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `borderBottomWidth`

`borderBottomWidth` 功能類似 CSS 的 `border-bottom-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-bottom-width。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `borderEndWidth`

當方向為 `ltr` 時，`borderEndWidth` 等同於 `borderRightWidth`；當方向為 `rtl` 時，則等同於 `borderLeftWidth`。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `borderLeftWidth`

`borderLeftWidth` 功能類似 CSS 的 `border-left-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-left-width。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `borderRightWidth`

`borderRightWidth` 功能類似 CSS 的 `border-right-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-right-width。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `borderStartWidth`

當方向為 `ltr` 時，`borderStartWidth` 等同於 `borderLeftWidth`。當方向為 `rtl` 時，`borderStartWidth` 等同於 `borderRightWidth`。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `borderTopWidth`

`borderTopWidth` 的功能類似 CSS 中的 `border-top-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-top-width。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `borderWidth`

`borderWidth` 的功能類似 CSS 中的 `border-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-width。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `bottom`

`bottom` 表示以邏輯像素為單位偏移此元件底部邊緣的數值。

其功能類似 CSS 中的 `bottom`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/bottom 了解 `bottom` 如何影響佈局。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `boxSizing`

`boxSizing` 定義如何計算元件的各種尺寸屬性（`width`、`height`、`minWidth`、`minHeight` 等）。若設為 `border-box`，這些尺寸會套用至元件的邊框盒；若設為 `content-box`，則套用至內容盒。預設值為 `border-box`。如需深入了解此屬性的運作方式，可參考 [網頁文件](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)。

| Type                              | Required |
| --------------------------------- | -------- |
| enum('border-box', 'content-box') | No       |

---

### `columnGap`

`columnGap` 的功能類似 CSS 中的 `column-gap`。在 React Native 中僅支援像素單位。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/column-gap。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `direction`

`direction` 指定使用者介面的文字流向。預設值為 `inherit`，但根節點會根據當前地區設定值。詳見 https://www.yogalayout.dev/docs/styling/layout-direction。

| Type                          | Required | Platform |
| ----------------------------- | -------- | -------- |
| enum('inherit', 'ltr', 'rtl') | No       | iOS      |

---

### `display`

`display` 設定此元件的顯示類型。

其功能類似 CSS 中的 `display`，但僅支援 'flex'、'none' 和 'contents' 值。預設值為 `flex`。

| Type                             | Required |
| -------------------------------- | -------- |
| enum('none', 'flex', 'contents') | No       |

---

### `end`

當方向為 `ltr` 時，`end` 等同於 `right`；當方向為 `rtl` 時，`end` 等同於 `left`。

此樣式優先於 `left` 和 `right` 樣式。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `flex`

在 React Native 中，`flex` 的運作方式與 CSS 不同。`flex` 是一個數字而非字串，其行為遵循 [Yoga](https://github.com/facebook/yoga) 佈局引擎。

當 `flex` 為正數時，會使元件具有彈性，其尺寸將按比例對應於 flex 值。因此，`flex` 設為 2 的元件所佔空間將是 `flex` 設為 1 的元件的兩倍。`flex: <正數>` 等同於 `flexGrow: <正數>, flexShrink: 1, flexBasis: 0`。

當 `flex` 為 0 時，元件尺寸由 `width` 和 `height` 決定，且不具彈性。

當 `flex` 為 -1 時，元件通常會根據 `width` 和 `height` 調整尺寸。然而，若空間不足，元件會縮小至其 `minWidth` 和 `minHeight`。

`flexGrow`、`flexShrink` 和 `flexBasis` 的功能與 CSS 中相同。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `flexBasis`

`flexBasis` 是一種與軸向無關的方式，用於設定項目在主軸上的預設尺寸。設定子元件的 `flexBasis` 類似於在父容器為 `flexDirection: row` 時設定該子元件的 `width`，或在父容器為 `flexDirection: column` 時設定其 `height`。項目的 `flexBasis` 是該項目的預設尺寸，即在進行任何 `flexGrow` 和 `flexShrink` 計算之前的尺寸。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `flexDirection`

`flexDirection` 控制容器內子元件的排列方向。`row` 表示從左到右排列，`column` 表示從上到下排列，其他兩個值的功能也可依此類推。其功能類似於 CSS 中的 `flex-direction`，但預設值為 `column`。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/flex-direction。

| Type                                                   | Required |
| ------------------------------------------------------ | -------- |
| enum('row', 'row-reverse', 'column', 'column-reverse') | No       |

---

### `flexGrow`

`flexGrow` 描述容器內沿主軸的剩餘空間應如何分配給其子元件。在佈局子元件後，容器會根據子元件指定的 flex grow 值分配剩餘空間。

`flexGrow` 接受任何大於或等於 0 的浮點數，預設值為 0。容器會根據子元件的 `flexGrow` 值加權分配剩餘空間。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `flexShrink`

[`flexShrink`](layout-props#flexshrink) 描述當子元件在主軸上的總尺寸超出容器尺寸時，應如何縮小子元件。`flexShrink` 與 `flexGrow` 非常相似，可將溢出的尺寸視為負的剩餘空間來理解。這兩個屬性配合使用，可讓子元件根據需要伸縮。

`flexShrink` 接受任何大於或等於 0 的浮點數，預設值為 0。容器會根據子元件的 `flexShrink` 值加權縮小其尺寸。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `flexWrap`

`flexWrap` 控制子元件在到達彈性容器末端後是否換行。其功能類似於 CSS 中的 `flex-wrap`（預設值為 nowrap）。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/flex-wrap。注意，它不再與預設的 `alignItems: stretch` 一起作用，因此您可能需要改用 `alignItems: flex-start`（重大變更詳情：https://github.com/facebook/react-native/releases/tag/v0.28.0）。

| Type                                   | Required |
| -------------------------------------- | -------- |
| enum('wrap', 'nowrap', 'wrap-reverse') | No       |

---

### `gap`

`gap` 的功能與 CSS 中的 `gap` 相同。在 React Native 中僅支援像素單位。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/gap 以獲取更多資訊。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `height`

`height` 設定此元件的高度。

其功能類似於 CSS 中的 `height`，但在 React Native 中必須使用點數或百分比。不支援 em 等其他單位。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/height 以獲取更多資訊。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `justifyContent`

`justifyContent` 在主軸方向上對齊子元件。例如，若子元件是垂直排列，`justifyContent` 控制它們如何垂直對齊。其功能類似於 CSS 中的 `justify-content`（預設值：flex-start）。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/justify-content 以獲取更多資訊。

| Type                                                                                      | Required |
| ----------------------------------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'space-between', 'space-around', 'space-evenly') | No       |

---

### `left`

`left` 是以邏輯像素為單位，偏移此元件左邊緣的數值。

其功能類似於 CSS 中的 `left`，但在 React Native 中必須使用點數或百分比。不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/left 以了解 `left` 如何影響佈局。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `margin`

設定 `margin` 的效果等同於同時設定 `marginTop`、`marginLeft`、`marginBottom` 和 `marginRight`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin 以獲取更多資訊。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginBottom`

`marginBottom` 的功能類似於 CSS 中的 `margin-bottom`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-bottom 以獲取更多資訊。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginEnd`

當方向為 `ltr` 時，`marginEnd` 等同於 `marginRight`。當方向為 `rtl` 時，`marginEnd` 等同於 `marginLeft`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginHorizontal`

設定 `marginHorizontal` 的效果等同於同時設定 `marginLeft` 和 `marginRight`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginLeft`

`marginLeft` 的功能類似於 CSS 中的 `margin-left`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-left 以獲取更多資訊。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginRight`

`marginRight` 的功能類似於 CSS 中的 `margin-right`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-right 以獲取更多資訊。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginStart`

當方向為 `ltr` 時，`marginStart` 等同於 `marginLeft`。當方向為 `rtl` 時，`marginStart` 等同於 `marginRight`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginTop`

`marginTop` 的功能類似於 CSS 中的 `margin-top`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-top 以獲取更多資訊。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginVertical`

設定 `marginVertical` 的效果等同於同時設定 `marginTop` 和 `marginBottom`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `maxHeight`

`maxHeight` 是此元件的最大高度，以邏輯像素為單位。

其功能類似 CSS 中的 `max-height`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/max-height 獲取更多細節。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `maxWidth`

`maxWidth` 是此元件的最大寬度，以邏輯像素為單位。

其功能類似 CSS 中的 `max-width`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/max-width 獲取更多細節。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `minHeight`

`minHeight` 是此元件的最小高度，以邏輯像素為單位。

其功能類似 CSS 中的 `min-height`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/min-height 獲取更多細節。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `minWidth`

`minWidth` 是此元件的最小寬度，以邏輯像素為單位。

其功能類似 CSS 中的 `min-width`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/min-width 獲取更多細節。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `overflow`

`overflow` 控制子元件的測量與顯示方式。`overflow: hidden` 會裁切超出範圍的視圖，而 `overflow: scroll` 會讓視圖獨立於父元件主軸進行測量。其功能類似 CSS 中的 `overflow`（預設值為 visible）。詳見 https://developer.mozilla.org/en/docs/Web/CSS/overflow 獲取更多細節。

| Type                                | Required |
| ----------------------------------- | -------- |
| enum('visible', 'hidden', 'scroll') | No       |

---

### `padding`

設定 `padding` 的效果等同於同時設定 `paddingTop`、`paddingBottom`、`paddingLeft` 和 `paddingRight`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/padding 獲取更多細節。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingBottom`

`paddingBottom` 的功能類似 CSS 中的 `padding-bottom`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/padding-bottom 獲取更多細節。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingEnd`

當方向為 `ltr` 時，`paddingEnd` 等同於 `paddingRight`；當方向為 `rtl` 時，`paddingEnd` 則等同於 `paddingLeft`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingHorizontal`

設定 `paddingHorizontal` 的效果等同於同時設定 `paddingLeft` 和 `paddingRight`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingLeft`

`paddingLeft` 的作用類似 CSS 中的 `padding-left`。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/padding-left。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingRight`

`paddingRight` 的作用類似 CSS 中的 `padding-right`。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/padding-right。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingStart`

當方向為 `ltr` 時，`paddingStart` 等同於 `paddingLeft`。當方向為 `rtl` 時，`paddingStart` 等同於 `paddingRight`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingTop`

`paddingTop` 的作用類似 CSS 中的 `padding-top`。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/padding-top。

| Type            | Required |
| --------------- | -------- |
| number, ,string | No       |

---

### `paddingVertical`

設定 `paddingVertical` 等同於同時設定 `paddingTop` 和 `paddingBottom`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `position`

React Native 中的 `position` 與 [常規 CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/position) 類似，但預設情況下所有元素都設為 `relative`。

`relative` 會根據佈局的正常流來定位元素。偏移量（`top`、`bottom`、`left`、`right`）會相對於此佈局進行偏移。

`absolute` 會將元素從佈局的正常流中移除。偏移量會相對於其 [包含塊](./flexbox.md#the-containing-block) 進行偏移。

`static` 會根據佈局的正常流來定位元素。偏移量不會產生任何效果。
`static` 元素不會為絕對定位的後代元素形成包含塊。

更多資訊，請參閱 [使用 Flexbox 佈局的文件](./flexbox.md#position)。此外，[Yoga 文件](https://www.yogalayout.dev/docs/styling/position) 詳細說明了 `position` 在 React Native 和 CSS 之間的差異。

| Type                                   | Required |
| -------------------------------------- | -------- |
| enum('absolute', 'relative', 'static') | No       |

---

### `right`

`right` 是用於偏移此元件右邊緣的邏輯像素數。

其作用類似 CSS 中的 `right`，但在 React Native 中必須使用點數或百分比。不支援 em 等其他單位。

詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/right，了解 `right` 如何影響佈局。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `rowGap`

`rowGap` 的作用類似 CSS 中的 `row-gap`。在 React Native 中僅支援像素單位。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/row-gap。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `start`

當方向為 `ltr` 時，`start` 等同於 `left`。當方向為 `rtl` 時，`start` 等同於 `right`。

此樣式的優先級高於 `left`、`right` 和 `end` 樣式。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `top`

`top` 是用於偏移此元件頂部邊緣的邏輯像素數。

其功能類似於 CSS 中的 `top`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

有關 `top` 如何影響佈局的更多細節，請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/top。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `width`

`width` 用於設定此元件的寬度。

其功能類似於 CSS 中的 `width`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。更多細節請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/width。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `zIndex`

`zIndex` 控制哪些元件會顯示在其他元件之上。通常情況下不需要使用 `zIndex`，元件會依照文件樹中的順序渲染，後面的元件會覆蓋在前面的元件上。但在動畫或自訂模態介面等不希望遵循此行為的場景中，`zIndex` 可能會很有用。

其運作方式類似 CSS 的 `z-index` 屬性——數值較大的 `zIndex` 元件會渲染在上層。可將 z 軸方向想像為從手機指向您的眼球。更多細節請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/z-index。

在 iOS 上，要使 `zIndex` 正常運作，可能需要讓 `View` 元件彼此是兄弟節點。

| Type   | Required |
| ------ | -------- |
| number | No       |

---