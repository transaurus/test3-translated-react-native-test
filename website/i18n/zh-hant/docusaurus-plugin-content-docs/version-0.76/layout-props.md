---
id: layout-props
title: Layout Props
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

> 關於這些屬性的更詳細範例，請參閱 [Flexbox 佈局](flexbox) 頁面。

### 範例

以下範例展示不同屬性如何影響或塑造 React Native 的佈局。您可以嘗試在修改 `flexWrap` 屬性值的同時，從 UI 中添加或移除方塊。

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

# 參考

## 屬性

### `alignContent`

`alignContent` 控制行在交叉軸方向上的對齊方式，會覆蓋父元素的 `alignContent`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/align-content。

| Type                                                                                                 | Required |
| ---------------------------------------------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'stretch', 'space-between', 'space-around', 'space-evenly') | No       |

---

### `alignItems`

`alignItems` 在交叉軸方向上對齊子元素。例如，若子元素是垂直排列，`alignItems` 則控制它們的水平對齊方式。其功能類似 CSS 的 `align-items`（預設值為 stretch）。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/align-items。

| Type                                                            | Required |
| --------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'stretch', 'baseline') | No       |

---

### `alignSelf`

`alignSelf` 控制子元素在交叉軸方向上的對齊方式，會覆蓋父元素的 `alignItems`。其功能類似 CSS 的 `align-self`（預設值為 auto）。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/align-self。

| Type                                                                    | Required |
| ----------------------------------------------------------------------- | -------- |
| enum('auto', 'flex-start', 'flex-end', 'center', 'stretch', 'baseline') | No       |

---

### `aspectRatio`

長寬比控制節點未定義維度的大小。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio。

- 在設定了寬度/高度的節點上，長寬比控制未設定維度的大小
- 在設定了 flex basis 的節點上，若未設定交叉軸尺寸，長寬比會控制節點在交叉軸的大小
- 在具有測量函式的節點上，長寬比的作用如同測量函式測量 flex basis
- 在具有 flex grow/shrink 的節點上，若未設定交叉軸尺寸，長寬比會控制節點在交叉軸的大小
- 長寬比會考慮最小/最大尺寸限制

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `borderBottomWidth`

`borderBottomWidth` 的功能類似 CSS 的 `border-bottom-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-bottom-width。

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

`borderLeftWidth` 的功能類似 CSS 的 `border-left-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-left-width。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `borderRightWidth`

`borderRightWidth` 的功能類似 CSS 的 `border-right-width`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/border-right-width。

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

`bottom` 表示此元件底部邊緣偏移的邏輯像素數值。

其功能類似 CSS 中的 `bottom`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/bottom 瞭解 `bottom` 如何影響佈局。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `columnGap`

`columnGap` 的功能類似 CSS 中的 `column-gap`。React Native 僅支援像素單位。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/column-gap。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `direction`

`direction` 指定使用者介面的文字流向。預設值為 `inherit`，根節點會根據當前語言環境設定值。詳見 https://www.yogalayout.dev/docs/styling/layout-direction。

| Type                          | Required | Platform |
| ----------------------------- | -------- | -------- |
| enum('inherit', 'ltr', 'rtl') | No       | iOS      |

---

### `display`

`display` 設定此元件的顯示類型。

功能類似 CSS 中的 `display`，但僅支援 'flex' 和 'none'，預設值為 'flex'。

| Type                 | Required |
| -------------------- | -------- |
| enum('none', 'flex') | No       |

---

### `end`

當方向為 `ltr` 時，`end` 等同於 `right`。當方向為 `rtl` 時，`end` 等同於 `left`。

此樣式優先於 `left` 和 `right` 樣式。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `flex`

在 React Native 中，`flex` 的行為與 CSS 不同。`flex` 是數字而非字串，其運作基於 [Yoga](https://github.com/facebook/yoga) 佈局引擎。

當 `flex` 為正數時，會使元件具有彈性，其尺寸將按比例分配。例如 `flex` 設為 2 的元件所佔空間是設為 1 的兩倍。`flex: <正數>` 等同於 `flexGrow: <正數>, flexShrink: 1, flexBasis: 0`。

當 `flex` 為 0 時，元件尺寸由 `width` 和 `height` 決定，且不具彈性。

當 `flex` 為 -1 時，元件通常按 `width` 和 `height` 設定尺寸。但若空間不足，元件會縮小至 `minWidth` 和 `minHeight`。

`flexGrow`、`flexShrink` 和 `flexBasis` 的行為與 CSS 相同。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `flexBasis`

`flexBasis` 是一種與軸向無關的方式，用於設定項目在主軸上的預設大小。設定子項目的 `flexBasis` 類似於在父容器為 `flexDirection: row` 時設定該子項目的 `width`，或在父容器為 `flexDirection: column` 時設定子項目的 `height`。項目的 `flexBasis` 是該項目的預設大小，即在任何 `flexGrow` 和 `flexShrink` 計算之前的大小。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `flexDirection`

`flexDirection` 控制容器中子項目的排列方向。`row` 表示從左到右排列，`column` 表示從上到下排列，其他兩個方向也可依此類推。它的功能類似於 CSS 中的 `flex-direction`，但預設值為 `column`。更多詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/flex-direction。

| Type                                                   | Required |
| ------------------------------------------------------ | -------- |
| enum('row', 'row-reverse', 'column', 'column-reverse') | No       |

---

### `flexGrow`

`flexGrow` 描述了容器內剩餘空間應如何沿主軸分配給其子項目。在佈局子項目後，容器會根據子項目指定的 `flexGrow` 值分配剩餘空間。

`flexGrow` 接受任何大於或等於 0 的浮點數，預設值為 0。容器會根據子項目的 `flexGrow` 值加權分配剩餘空間。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `flexShrink`

[`flexShrink`](layout-props#flexshrink) 描述了當子項目的總大小在主軸上超出容器大小時，應如何沿主軸縮小子項目。`flexShrink` 與 `flexGrow` 非常相似，可以將其視為將任何溢出的空間視為負的剩餘空間。這兩個屬性可以很好地配合使用，讓子項目根據需要增長或縮小。

`flexShrink` 接受任何大於或等於 0 的浮點數，預設值為 0。容器會根據子項目的 `flexShrink` 值加權縮小子項目。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `flexWrap`

`flexWrap` 控制子項目在到達彈性容器末端後是否可以換行。它的功能類似於 CSS 中的 `flex-wrap`（預設值為 `nowrap`）。更多詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/flex-wrap。請注意，它不再與 `alignItems: stretch`（預設值）一起使用，因此您可能需要使用 `alignItems: flex-start` 等（重大變更詳情：https://github.com/facebook/react-native/releases/tag/v0.28.0）。

| Type                                   | Required |
| -------------------------------------- | -------- |
| enum('wrap', 'nowrap', 'wrap-reverse') | No       |

---

### `gap`

`gap` 的功能類似於 CSS 中的 `gap`。在 React Native 中僅支援像素單位。更多詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/gap。

| Type   | Required |
| ------ | -------- |
| number | No       |

---

### `height`

`height` 設定此元件的高度。

它的功能類似於 CSS 中的 `height`，但在 React Native 中必須使用點數或百分比。不支援 em 等其他單位。更多詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/height。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `justifyContent`

`justifyContent` 用於在主軸方向上對齊子元素。例如，若子元素是垂直排列，`justifyContent` 會控制它們如何垂直對齊。其功能類似 CSS 中的 `justify-content`（預設值為 flex-start）。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/justify-content 以獲取更多資訊。

| Type                                                                                      | Required |
| ----------------------------------------------------------------------------------------- | -------- |
| enum('flex-start', 'flex-end', 'center', 'space-between', 'space-around', 'space-evenly') | No       |

---

### `left`

`left` 表示此元件左邊緣偏移的邏輯像素數值。

其功能類似 CSS 中的 `left`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

關於 `left` 如何影響佈局的詳細說明，請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/left。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `margin`

設定 `margin` 的效果等同於同時設定 `marginTop`、`marginLeft`、`marginBottom` 和 `marginRight`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginBottom`

`marginBottom` 的功能類似 CSS 中的 `margin-bottom`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-bottom。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginEnd`

當方向為 `ltr` 時，`marginEnd` 等同於 `marginRight`；當方向為 `rtl` 時，`marginEnd` 則等同於 `marginLeft`。

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

`marginLeft` 的功能類似 CSS 中的 `margin-left`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-left。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginRight`

`marginRight` 的功能類似 CSS 中的 `margin-right`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-right。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginStart`

當方向為 `ltr` 時，`marginStart` 等同於 `marginLeft`；當方向為 `rtl` 時，`marginStart` 則等同於 `marginRight`。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `marginTop`

`marginTop` 的功能類似 CSS 中的 `margin-top`。詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/margin-top。

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

`maxHeight` 表示此元件的最大高度（以邏輯像素為單位）。

其功能類似 CSS 中的 `max-height`，但在 React Native 中必須使用點數或百分比單位，不支援 em 等其他單位。

詳見 https://developer.mozilla.org/en-US/docs/Web/CSS/max-height 以獲取更多資訊。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `maxWidth`

`maxWidth` is the maximum width for this component, in logical pixels.

It works similarly to `max-width` in CSS, but in React Native you must use points or percentages. Ems and other units are not supported.

See https://developer.mozilla.org/en-US/docs/Web/CSS/max-width for more details.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `minHeight`

`minHeight` is the minimum height for this component, in logical pixels.

It works similarly to `min-height` in CSS, but in React Native you must use points or percentages. Ems and other units are not supported.

See https://developer.mozilla.org/en-US/docs/Web/CSS/min-height for more details.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `minWidth`

`minWidth` is the minimum width for this component, in logical pixels.

It works similarly to `min-width` in CSS, but in React Native you must use points or percentages. Ems and other units are not supported.

See https://developer.mozilla.org/en-US/docs/Web/CSS/min-width for more details.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `overflow`

`overflow` controls how children are measured and displayed. `overflow: hidden` causes views to be clipped while `overflow: scroll` causes views to be measured independently of their parents' main axis. It works like `overflow` in CSS (default: visible). See https://developer.mozilla.org/en/docs/Web/CSS/overflow for more details.

| Type                                | Required |
| ----------------------------------- | -------- |
| enum('visible', 'hidden', 'scroll') | No       |

---

### `padding`

Setting `padding` has the same effect as setting each of `paddingTop`, `paddingBottom`, `paddingLeft`, and `paddingRight`. See https://developer.mozilla.org/en-US/docs/Web/CSS/padding for more details.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingBottom`

`paddingBottom` works like `padding-bottom` in CSS. See https://developer.mozilla.org/en-US/docs/Web/CSS/padding-bottom for more details.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingEnd`

When direction is `ltr`, `paddingEnd` is equivalent to `paddingRight`. When direction is `rtl`, `paddingEnd` is equivalent to `paddingLeft`.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingHorizontal`

Setting `paddingHorizontal` is like setting both of `paddingLeft` and `paddingRight`.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingLeft`

`paddingLeft` works like `padding-left` in CSS. See https://developer.mozilla.org/en-US/docs/Web/CSS/padding-left for more details.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingRight`

`paddingRight` works like `padding-right` in CSS. See https://developer.mozilla.org/en-US/docs/Web/CSS/padding-right for more details.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingStart`

When direction is `ltr`, `paddingStart` is equivalent to `paddingLeft`. When direction is `rtl`, `paddingStart` is equivalent to `paddingRight`.

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `paddingTop`

`paddingTop` 的功能類似於 CSS 中的 `padding-top`。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/padding-top。

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

`relative` 會根據佈局的正常流來定位元素。偏移量（`top`、`bottom`、`left`、`right`）會相對於此佈局進行調整。

`absolute` 會將元素從佈局的正常流中移除。偏移量會相對於其 [包含塊](./flexbox.md#the-containing-block) 進行調整。

`static` 會根據佈局的正常流來定位元素。偏移量不會產生任何效果。`static` 元素不會為絕對定位的後代元素形成包含塊。

更多資訊請參閱 [Flexbox 佈局文件](./flexbox.md#position)。此外，[Yoga 文件](https://www.yogalayout.dev/docs/styling/position) 詳細說明了 `position` 在 React Native 和 CSS 之間的差異。

| Type                                   | Required |
| -------------------------------------- | -------- |
| enum('absolute', 'relative', 'static') | No       |

---

### `right`

`right` 是以邏輯像素為單位，偏移此元件右邊緣的數值。

其功能類似於 CSS 中的 `right`，但在 React Native 中必須使用點數或百分比。不支援 em 等其他單位。

詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/right，了解 `right` 如何影響佈局。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `rowGap`

`rowGap` 的功能類似於 CSS 中的 `row-gap`。在 React Native 中僅支援像素單位。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/row-gap。

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

`top` 是以邏輯像素為單位，偏移此元件頂邊緣的數值。

其功能類似於 CSS 中的 `top`，但在 React Native 中必須使用點數或百分比。不支援 em 等其他單位。

詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/top，了解 `top` 如何影響佈局。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `width`

`width` 設定此元件的寬度。

其功能類似於 CSS 中的 `width`，但在 React Native 中必須使用點數或百分比。不支援 em 等其他單位。詳情請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/width。

| Type           | Required |
| -------------- | -------- |
| number, string | No       |

---

### `zIndex`

`zIndex` 控制哪些元件會顯示在其他元件之上。通常情況下，您不需要使用 `zIndex`。元件會依照其在文件樹中的順序進行渲染，因此後面的元件會覆蓋在前面的元件之上。`zIndex` 在您有動畫或自定義模態介面且不希望這種行為時可能會有用。

它的運作方式類似於 CSS 的 `z-index` 屬性——`zIndex` 值較大的元件會渲染在上層。可以將 z 方向想像成從手機指向您的眼球。更多詳細資訊請參閱 https://developer.mozilla.org/en-US/docs/Web/CSS/z-index。

在 iOS 上，`zIndex` 可能需要 `View` 元件彼此為兄弟節點才能如預期般運作。

| Type   | Required |
| ------ | -------- |
| number | No       |

---