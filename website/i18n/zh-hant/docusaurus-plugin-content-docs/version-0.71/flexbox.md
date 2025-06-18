---
id: flexbox
title: Layout with Flexbox
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

元件可以透過 Flexbox 演算法來指定其子元素的佈局方式。Flexbox 的設計旨在為不同螢幕尺寸提供一致的佈局。

通常你會結合使用 `flexDirection`、`alignItems` 和 `justifyContent` 來達成正確的佈局。

:::caution
Flexbox 在 React Native 中的運作方式與在網頁 CSS 中相同，但有一些例外。
預設值有所不同：`flexDirection` 預設為 `column` 而非 `row`，`alignContent` 預設為 `flex-start` 而非 `stretch`，`flexShrink` 預設為 `0` 而非 `1`，且 `flex` 參數僅支援單一數字。
:::

## Flex 彈性佈局

[`flex`](layout-props#flex) 定義了子元素如何沿主軸「填充」可用空間。空間將根據每個元素的 flex 屬性進行分配。

在以下範例中，紅色、橙色和綠色視圖都是設有 `flex: 1` 的容器視圖的子元素。紅色視圖使用 `flex: 1`，橙色視圖使用 `flex: 2`，綠色視圖使用 `flex: 3`。**1+2+3 = 6**，這意味著紅色視圖將獲得 `1/6` 的空間，橙色視圖獲得 `2/6`，綠色視圖獲得 `3/6`。

```SnackPlayer name=Flex%20Example
import React from 'react';
import {StyleSheet, View} from 'react-native';

const Flex = () => {
  return (
    <View
      style={[
        styles.container,
        {
          // Try setting `flexDirection` to `"row"`.
          flexDirection: 'column',
        },
      ]}>
      <View style={{flex: 1, backgroundColor: 'red'}} />
      <View style={{flex: 2, backgroundColor: 'darkorange'}} />
      <View style={{flex: 3, backgroundColor: 'green'}} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
});

export default Flex;
```

## Flex Direction 主軸方向

[`flexDirection`](layout-props#flexdirection) 控制子節點的排列方向，這也被稱為主軸。交叉軸則是與主軸垂直的軸，或是換行排列的方向軸。

- `column`（**預設值**）從上到下排列子元素。如果啟用換行，則下一行將從頂部第一個項目的右側開始。
- `row` 從左到右排列子元素。如果啟用換行，則下一行將從左側第一個項目的下方開始。
- `column-reverse` 從下到上排列子元素。如果啟用換行，則下一行將從底部第一個項目的右側開始。
- `row-reverse` 從右到左排列子元素。如果啟用換行，則下一行將從右側第一個項目的下方開始。

你可以在此[了解更多](https://www.yogalayout.dev/docs/styling/flex-direction)。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Flex%20Direction&ext=js
import React, {useState} from 'react';
import {StyleSheet, Text, TouchableOpacity, View} from 'react-native';

const FlexDirectionBasics = () => {
  const [flexDirection, setflexDirection] = useState('column');

  return (
    <PreviewLayout
      label="flexDirection"
      values={['column', 'row', 'row-reverse', 'column-reverse']}
      selectedValue={flexDirection}
      setSelectedValue={setflexDirection}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default FlexDirectionBasics;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Flex%20Direction&ext=tsx
import React, {useState} from 'react';
import {StyleSheet, Text, TouchableOpacity, View} from 'react-native';
import type {PropsWithChildren} from 'react';

const FlexDirectionBasics = () => {
  const [flexDirection, setflexDirection] = useState('column');

  return (
    <PreviewLayout
      label="flexDirection"
      values={['column', 'row', 'row-reverse', 'column-reverse']}
      selectedValue={flexDirection}
      setSelectedValue={setflexDirection}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: string[];
  selectedValue: string;
  setSelectedValue: (value: string) => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default FlexDirectionBasics;
```

</TabItem>
</Tabs>

## Layout Direction 佈局方向

佈局 [`direction`](layout-props#direction) 指定了子元素和文字在層級結構中的排列方向。佈局方向也會影響 `start` 和 `end` 所指向的邊緣。預設情況下，React Native 使用 LTR（從左到右）佈局方向。在此模式下，`start` 指向左側，`end` 指向右側。

- `LTR`（**預設值**）文字和子元素從左到右排列。應用於元素起始位置的邊距和內距會作用於左側。
- `RTL` 文字和子元素從右到左排列。應用於元素起始位置的邊距和內距會作用於右側。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Flex%20Direction&ext=js
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';

const DirectionLayout = () => {
  const [direction, setDirection] = useState('ltr');

  return (
    <PreviewLayout
      label="direction"
      selectedValue={direction}
      values={['ltr', 'rtl']}
      setSelectedValue={setDirection}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default DirectionLayout;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Flex%20Direction&ext=tsx
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';
import type {PropsWithChildren} from 'react';

const DirectionLayout = () => {
  const [direction, setDirection] = useState('ltr');

  return (
    <PreviewLayout
      label="direction"
      selectedValue={direction}
      values={['ltr', 'rtl']}
      setSelectedValue={setDirection}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: string[];
  selectedValue: string;
  setSelectedValue: (value: string) => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default DirectionLayout;
```

</TabItem>
</Tabs>

## Justify Content 主軸對齊

[`justifyContent`](layout-props#justifycontent) 描述了如何在容器的主軸上對齊子元素。例如，你可以使用此屬性在 `flexDirection` 設為 `row` 的容器中水平居中子元素，或在 `flexDirection` 設為 `column` 的容器中垂直居中子元素。

- `flex-start`（**預設值**）將容器的子元素對齊至主軸起始處。

- `flex-end` 將容器的子元素對齊至主軸末端。

- `center` 將容器的子元素對齊至主軸中央。

- `space-between` 沿主軸均勻分配子元素間距，剩餘空間全部分佈於子元素之間。

- `space-around` 沿主軸均勻分配子元素間距，剩餘空間環繞子元素分佈。相較於 `space-between`，使用 `space-around` 會在第一個子元素前和最後一個子元素後也分配間距。

- `space-evenly` 沿主軸完全均勻分佈子元素。每對相鄰項目之間、主軸起始邊緣與第一個項目之間、主軸末端邊緣與最後一個項目之間的間距完全相等。

詳細說明可參閱[此文件](https://www.yogalayout.dev/docs/styling/justify-content)。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Justify%20Content&ext=js
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';

const JustifyContentBasics = () => {
  const [justifyContent, setJustifyContent] = useState('flex-start');

  return (
    <PreviewLayout
      label="justifyContent"
      selectedValue={justifyContent}
      values={[
        'flex-start',
        'flex-end',
        'center',
        'space-between',
        'space-around',
        'space-evenly',
      ]}
      setSelectedValue={setJustifyContent}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default JustifyContentBasics;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Justify%20Content&ext=tsx
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';
import type {PropsWithChildren} from 'react';

const JustifyContentBasics = () => {
  const [justifyContent, setJustifyContent] = useState('flex-start');

  return (
    <PreviewLayout
      label="justifyContent"
      selectedValue={justifyContent}
      values={[
        'flex-start',
        'flex-end',
        'center',
        'space-between',
        'space-around',
        'space-evenly',
      ]}
      setSelectedValue={setJustifyContent}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: string[];
  selectedValue: string;
  setSelectedValue: (value: string) => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default JustifyContentBasics;
```

</TabItem>
</Tabs>

## 對齊項目 (Align Items)

[`alignItems`](layout-props#alignitems) 定義子元素沿交叉軸的對齊方式，其作用類似於 `justifyContent`，但作用於交叉軸而非主軸。

- `stretch`（**預設值**）拉伸子元素高度以填滿容器交叉軸。

- `flex-start` 將子元素對齊至交叉軸起始處。

- `flex-end` 將子元素對齊至交叉軸末端。

- `center` 將子元素對齊至交叉軸中央。

- `baseline` 使子元素沿基準線對齊。可指定個別子元素作為父元素的基準參考。

:::info
要使 `stretch` 生效，子元素在次要軸向上不能有固定尺寸。下例中，除非移除子元素的 `width: 50` 設定，否則 `alignItems: stretch` 不會產生效果。
:::

詳細說明可參閱[此文件](https://www.yogalayout.dev/docs/styling/align-items-self)。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Align%20Items&ext=js
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';

const AlignItemsLayout = () => {
  const [alignItems, setAlignItems] = useState('stretch');

  return (
    <PreviewLayout
      label="alignItems"
      selectedValue={alignItems}
      values={['stretch', 'flex-start', 'flex-end', 'center', 'baseline']}
      setSelectedValue={setAlignItems}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View
        style={[
          styles.box,
          {
            backgroundColor: 'steelblue',
            width: 'auto',
            minWidth: 50,
          },
        ]}
      />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    minHeight: 200,
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default AlignItemsLayout;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Align%20Items&ext=tsx
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';
import type {PropsWithChildren} from 'react';

const AlignItemsLayout = () => {
  const [alignItems, setAlignItems] = useState('stretch');

  return (
    <PreviewLayout
      label="alignItems"
      selectedValue={alignItems}
      values={['stretch', 'flex-start', 'flex-end', 'center', 'baseline']}
      setSelectedValue={setAlignItems}>
      <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View
        style={[
          styles.box,
          {
            backgroundColor: 'steelblue',
            width: 'auto',
            minWidth: 50,
          },
        ]}
      />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: string[];
  selectedValue: string;
  setSelectedValue: (value: string) => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    minHeight: 200,
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default AlignItemsLayout;
```

</TabItem>
</Tabs>

## 自身對齊 (Align Self)

[`alignSelf`](layout-props#alignself) 的選項與效果同 `alignItems`，但作用於單個子元素，可覆寫父容器設定的 `alignItems` 值。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Align%20Self&ext=js
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';

const AlignSelfLayout = () => {
  const [alignSelf, setAlignSelf] = useState('stretch');

  return (
    <PreviewLayout
      label="alignSelf"
      selectedValue={alignSelf}
      values={['stretch', 'flex-start', 'flex-end', 'center', 'baseline']}
      setSelectedValue={setAlignSelf}>
      <View
        style={[
          styles.box,
          {
            alignSelf,
            width: 'auto',
            minWidth: 50,
            backgroundColor: 'powderblue',
          },
        ]}
      />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={styles.container}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    minHeight: 200,
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default AlignSelfLayout;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Align%20Self&ext=tsx
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';
import type {PropsWithChildren} from 'react';
import type {FlexAlignType} from 'react-native';

const AlignSelfLayout = () => {
  const [alignSelf, setAlignSelf] = useState<FlexAlignType>('stretch');

  return (
    <PreviewLayout
      label="alignSelf"
      selectedValue={alignSelf}
      values={['stretch', 'flex-start', 'flex-end', 'center', 'baseline']}
      setSelectedValue={setAlignSelf}>
      <View
        style={[
          styles.box,
          {
            alignSelf,
            width: 'auto',
            minWidth: 50,
            backgroundColor: 'powderblue',
          },
        ]}
      />
      <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: FlexAlignType[];
  selectedValue: string;
  setSelectedValue: (value: FlexAlignType) => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={styles.container}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    minHeight: 200,
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default AlignSelfLayout;
```

</TabItem>
</Tabs>

## 對齊內容 (Align Content)

[alignContent](layout-props#aligncontent) 定義多行內容沿交叉軸的分佈方式，僅在啟用 `flexWrap` 換行時生效。

- `flex-start`（**預設值**）將換行內容對齊至交叉軸起始處。

- `flex-end` 將換行內容對齊至交叉軸末端。

- `stretch`（網頁版 Yoga 的預設值）拉伸換行內容高度以填滿容器交叉軸。

- `center` 將換行內容對齊至交叉軸中央。

- `space-between` 沿交叉軸均勻分配行間距，剩餘空間全部分佈於行之間。

- `space-around` 沿交叉軸均勻分配行間距，剩餘空間環繞行分佈。相較於 `space-between`，使用 `space-around` 會在第一行前和最後一行後也分配間距。

詳細說明可參閱[此文件](https://www.yogalayout.dev/docs/styling/align-content)。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Align%20Content&ext=js
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';

const AlignContentLayout = () => {
  const [alignContent, setAlignContent] = useState('flex-start');

  return (
    <PreviewLayout
      label="alignContent"
      selectedValue={alignContent}
      values={[
        'flex-start',
        'flex-end',
        'stretch',
        'center',
        'space-between',
        'space-around',
      ]}
      setSelectedValue={setAlignContent}>
      <View style={[styles.box, {backgroundColor: 'orangered'}]} />
      <View style={[styles.box, {backgroundColor: 'orange'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumseagreen'}]} />
      <View style={[styles.box, {backgroundColor: 'deepskyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumturquoise'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumslateblue'}]} />
      <View style={[styles.box, {backgroundColor: 'purple'}]} />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexWrap: 'wrap',
    marginTop: 8,
    backgroundColor: 'aliceblue',
    maxHeight: 400,
  },
  box: {
    width: 50,
    height: 80,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default AlignContentLayout;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Align%20Content&ext=tsx
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';
import type {PropsWithChildren} from 'react';

const AlignContentLayout = () => {
  const [alignContent, setAlignContent] = useState('flex-start');

  return (
    <PreviewLayout
      label="alignContent"
      selectedValue={alignContent}
      values={[
        'flex-start',
        'flex-end',
        'stretch',
        'center',
        'space-between',
        'space-around',
      ]}
      setSelectedValue={setAlignContent}>
      <View style={[styles.box, {backgroundColor: 'orangered'}]} />
      <View style={[styles.box, {backgroundColor: 'orange'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumseagreen'}]} />
      <View style={[styles.box, {backgroundColor: 'deepskyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumturquoise'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumslateblue'}]} />
      <View style={[styles.box, {backgroundColor: 'purple'}]} />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: string[];
  selectedValue: string;
  setSelectedValue: (value: string) => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexWrap: 'wrap',
    marginTop: 8,
    backgroundColor: 'aliceblue',
    maxHeight: 400,
  },
  box: {
    width: 50,
    height: 80,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default AlignContentLayout;
```

</TabItem>
</Tabs>

## 彈性換行 (Flex Wrap)

[`flexWrap`](layout-props#flexwrap) 屬性設置在容器上，用於控制當子元素沿主軸溢出容器尺寸時的行為。默認情況下，子元素會被強制排列在單一行上（這可能會壓縮元素）。如果允許換行，則會根據需要將項目沿主軸換行成多行。

當換行時，可以使用 `alignContent` 來指定行在容器中的排列方式。了解更多[這裡](https://www.yogalayout.dev/docs/styling/flex-wrap)。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Flex%20Wrap&ext=js
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';

const FlexWrapLayout = () => {
  const [flexWrap, setFlexWrap] = useState('wrap');

  return (
    <PreviewLayout
      label="flexWrap"
      selectedValue={flexWrap}
      values={['wrap', 'nowrap']}
      setSelectedValue={setFlexWrap}>
      <View style={[styles.box, {backgroundColor: 'orangered'}]} />
      <View style={[styles.box, {backgroundColor: 'orange'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumseagreen'}]} />
      <View style={[styles.box, {backgroundColor: 'deepskyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumturquoise'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumslateblue'}]} />
      <View style={[styles.box, {backgroundColor: 'purple'}]} />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    maxHeight: 400,
  },
  box: {
    width: 50,
    height: 80,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default FlexWrapLayout;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Flex%20Wrap&ext=tsx
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';
import type {PropsWithChildren} from 'react';

const FlexWrapLayout = () => {
  const [flexWrap, setFlexWrap] = useState('wrap');

  return (
    <PreviewLayout
      label="flexWrap"
      selectedValue={flexWrap}
      values={['wrap', 'nowrap']}
      setSelectedValue={setFlexWrap}>
      <View style={[styles.box, {backgroundColor: 'orangered'}]} />
      <View style={[styles.box, {backgroundColor: 'orange'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumseagreen'}]} />
      <View style={[styles.box, {backgroundColor: 'deepskyblue'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumturquoise'}]} />
      <View style={[styles.box, {backgroundColor: 'mediumslateblue'}]} />
      <View style={[styles.box, {backgroundColor: 'purple'}]} />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: string[];
  selectedValue: string;
  setSelectedValue: (value: string) => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={[styles.container, {[label]: selectedValue}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    maxHeight: 400,
  },
  box: {
    width: 50,
    height: 80,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default FlexWrapLayout;
```

</TabItem>
</Tabs>

## Flex Basis、Grow 和 Shrink

- [`flexBasis`](layout-props#flexbasis) 是一種與軸無關的方式，用於提供項目沿主軸的默認大小。設置子元素的 `flexBasis` 類似於設置該子元素的 `width`（如果其父容器是 `flexDirection: row`）或 `height`（如果其父容器是 `flexDirection: column`）。`flexBasis` 是項目在進行任何 `flexGrow` 和 `flexShrink` 計算之前的默認大小。

- [`flexGrow`](layout-props#flexgrow) 描述容器內沿主軸的剩餘空間應如何在其子元素之間分配。在佈局其子元素後，容器將根據子元素指定的 `flexGrow` 值分配任何剩餘空間。

  `flexGrow` 接受任何大於或等於 0 的浮點值，默認值為 0。容器將根據子元素的 `flexGrow` 值加權分配剩餘空間。

- [`flexShrink`](layout-props#flexshrink) 描述當子元素的總大小沿主軸溢出容器尺寸時，如何沿主軸縮小子元素。`flexShrink` 與 `flexGrow` 非常相似，可以將其視為將任何溢出尺寸視為負剩餘空間的情況。這兩個屬性也可以很好地協同工作，允許子元素根據需要增長和縮小。

  `flexShrink` 接受任何大於或等於 0 的浮點值，默認值為 0（在 Web 上，默認值為 1）。容器將根據子元素的 `flexShrink` 值加權縮小子元素。

了解更多[這裡](https://www.yogalayout.dev/docs/styling/flex-basis-grow-shrink)。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Flex%20Basis%2C%20Grow%2C%20and%20Shrink&ext=js
import React, {useState} from 'react';
import {View, Text, TextInput, StyleSheet} from 'react-native';

const App = () => {
  const [powderblue, setPowderblue] = useState({
    flexGrow: 0,
    flexShrink: 1,
    flexBasis: 'auto',
  });
  const [skyblue, setSkyblue] = useState({
    flexGrow: 1,
    flexShrink: 0,
    flexBasis: 100,
  });
  const [steelblue, setSteelblue] = useState({
    flexGrow: 0,
    flexShrink: 1,
    flexBasis: 200,
  });
  return (
    <View style={styles.container}>
      <View
        style={[
          styles.container,
          {
            flexDirection: 'row',
            alignContent: 'space-between',
          },
        ]}>
        <BoxInfo color="powderblue" {...powderblue} setStyle={setPowderblue} />
        <BoxInfo color="skyblue" {...skyblue} setStyle={setSkyblue} />
        <BoxInfo color="steelblue" {...steelblue} setStyle={setSteelblue} />
      </View>
      <View style={styles.previewContainer}>
        <View
          style={[
            styles.box,
            {
              flexBasis: powderblue.flexBasis,
              flexGrow: powderblue.flexGrow,
              flexShrink: powderblue.flexShrink,
              backgroundColor: 'powderblue',
            },
          ]}
        />
        <View
          style={[
            styles.box,
            {
              flexBasis: skyblue.flexBasis,
              flexGrow: skyblue.flexGrow,
              flexShrink: skyblue.flexShrink,
              backgroundColor: 'skyblue',
            },
          ]}
        />
        <View
          style={[
            styles.box,
            {
              flexBasis: steelblue.flexBasis,
              flexGrow: steelblue.flexGrow,
              flexShrink: steelblue.flexShrink,
              backgroundColor: 'steelblue',
            },
          ]}
        />
      </View>
    </View>
  );
};

const BoxInfo = ({color, flexBasis, flexShrink, setStyle, flexGrow}) => (
  <View style={[styles.row, {flexDirection: 'column'}]}>
    <View
      style={[
        styles.boxLabel,
        {
          backgroundColor: color,
        },
      ]}>
      <Text
        style={{
          color: '#fff',
          fontWeight: '500',
          textAlign: 'center',
        }}>
        Box
      </Text>
    </View>
    <Text style={styles.label}>flexBasis</Text>
    <TextInput
      value={flexBasis}
      style={styles.input}
      onChangeText={fB =>
        setStyle(value => ({
          ...value,
          flexBasis: isNaN(parseInt(fB, 10)) ? 'auto' : parseInt(fB, 10),
        }))
      }
    />
    <Text style={styles.label}>flexShrink</Text>
    <TextInput
      value={flexShrink}
      style={styles.input}
      onChangeText={fS =>
        setStyle(value => ({
          ...value,
          flexShrink: isNaN(parseInt(fS, 10)) ? undefined : parseInt(fS, 10),
        }))
      }
    />
    <Text style={styles.label}>flexGrow</Text>
    <TextInput
      value={flexGrow}
      style={styles.input}
      onChangeText={fG =>
        setStyle(value => ({
          ...value,
          flexGrow: isNaN(parseInt(fG, 10)) ? undefined : parseInt(fG, 10),
        }))
      }
    />
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingHorizontal: 10,
  },
  box: {
    flex: 1,
    height: 50,
    width: 50,
  },
  boxLabel: {
    minWidth: 80,
    padding: 8,
    borderRadius: 4,
    marginTop: 8,
  },
  label: {
    marginTop: 6,
    fontSize: 16,
    fontWeight: '100',
  },
  previewContainer: {
    flex: 1,
    flexDirection: 'row',
    backgroundColor: 'aliceblue',
  },
  row: {
    flex: 1,
    flexDirection: 'row',
    flexWrap: 'wrap',
    alignItems: 'center',
    marginBottom: 10,
  },
  input: {
    borderBottomWidth: 1,
    paddingVertical: 3,
    width: 50,
    textAlign: 'center',
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Flex%20Basis%2C%20Grow%2C%20and%20Shrink&ext=tsx
import React, {useState} from 'react';
import {View, Text, TextInput, StyleSheet} from 'react-native';
import type {ViewStyle} from 'react-native';

const App = () => {
  const [powderblue, setPowderblue] = useState<ViewStyle>({
    flexGrow: 0,
    flexShrink: 1,
    flexBasis: 'auto',
  });
  const [skyblue, setSkyblue] = useState<ViewStyle>({
    flexGrow: 1,
    flexShrink: 0,
    flexBasis: 100,
  });
  const [steelblue, setSteelblue] = useState<ViewStyle>({
    flexGrow: 0,
    flexShrink: 1,
    flexBasis: 200,
  });
  return (
    <View style={styles.container}>
      <View
        style={[
          styles.container,
          {
            flexDirection: 'row',
            alignContent: 'space-between',
          },
        ]}>
        <BoxInfo color="powderblue" {...powderblue} setStyle={setPowderblue} />
        <BoxInfo color="skyblue" {...skyblue} setStyle={setSkyblue} />
        <BoxInfo color="steelblue" {...steelblue} setStyle={setSteelblue} />
      </View>
      <View style={styles.previewContainer}>
        <View
          style={[
            styles.box,
            {
              flexBasis: powderblue.flexBasis,
              flexGrow: powderblue.flexGrow,
              flexShrink: powderblue.flexShrink,
              backgroundColor: 'powderblue',
            },
          ]}
        />
        <View
          style={[
            styles.box,
            {
              flexBasis: skyblue.flexBasis,
              flexGrow: skyblue.flexGrow,
              flexShrink: skyblue.flexShrink,
              backgroundColor: 'skyblue',
            },
          ]}
        />
        <View
          style={[
            styles.box,
            {
              flexBasis: steelblue.flexBasis,
              flexGrow: steelblue.flexGrow,
              flexShrink: steelblue.flexShrink,
              backgroundColor: 'steelblue',
            },
          ]}
        />
      </View>
    </View>
  );
};

type BoxInfoProps = ViewStyle & {
  color: string;
  setStyle: React.Dispatch<React.SetStateAction<ViewStyle>>;
};

const BoxInfo = ({
  color,
  flexBasis,
  flexShrink,
  setStyle,
  flexGrow,
}: BoxInfoProps) => (
  <View style={[styles.row, {flexDirection: 'column'}]}>
    <View
      style={[
        styles.boxLabel,
        {
          backgroundColor: color,
        },
      ]}>
      <Text
        style={{
          color: '#fff',
          fontWeight: '500',
          textAlign: 'center',
        }}>
        Box
      </Text>
    </View>
    <Text style={styles.label}>flexBasis</Text>
    <TextInput
      value={String(flexBasis)}
      style={styles.input}
      onChangeText={fB =>
        setStyle(value => ({
          ...value,
          flexBasis: isNaN(parseInt(fB, 10)) ? 'auto' : parseInt(fB, 10),
        }))
      }
    />
    <Text style={styles.label}>flexShrink</Text>
    <TextInput
      value={String(flexShrink)}
      style={styles.input}
      onChangeText={fS =>
        setStyle(value => ({
          ...value,
          flexShrink: isNaN(parseInt(fS, 10)) ? undefined : parseInt(fS, 10),
        }))
      }
    />
    <Text style={styles.label}>flexGrow</Text>
    <TextInput
      value={String(flexGrow)}
      style={styles.input}
      onChangeText={fG =>
        setStyle(value => ({
          ...value,
          flexGrow: isNaN(parseInt(fG, 10)) ? undefined : parseInt(fG, 10),
        }))
      }
    />
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingHorizontal: 10,
  },
  box: {
    flex: 1,
    height: 50,
    width: 50,
  },
  boxLabel: {
    minWidth: 80,
    padding: 8,
    borderRadius: 4,
    marginTop: 8,
  },
  label: {
    marginTop: 6,
    fontSize: 16,
    fontWeight: '100',
  },
  previewContainer: {
    flex: 1,
    flexDirection: 'row',
    backgroundColor: 'aliceblue',
  },
  row: {
    flex: 1,
    flexDirection: 'row',
    flexWrap: 'wrap',
    alignItems: 'center',
    marginBottom: 10,
  },
  input: {
    borderBottomWidth: 1,
    paddingVertical: 3,
    width: 50,
    textAlign: 'center',
  },
});

export default App;
```

</TabItem>
</Tabs>

## 行間距、列間距和間距

- [`rowGap`](layout-props#rowgap) 設置元素行之間的間距（溝槽）大小。

- [`columnGap`](layout-props#columngap) 設置元素列之間的間距（溝槽）大小。

- [`gap`](layout-props#gap) 設置行和列之間的間距（溝槽）大小。它是 `rowGap` 和 `columnGap` 的簡寫形式。

您可以結合使用 `flexWrap`、`alignContent` 和 `gap` 來在項目之間添加一致的間距。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Row%20Gap%20and%20Column%20Gap&ext=js
import React, {useState} from 'react';
import {View, Text, StyleSheet, TextInput} from 'react-native';

const RowGapAndColumnGap = () => {
  const [rowGap, setRowGap] = useState(10);
  const [columnGap, setColumnGap] = useState(10);

  return (
    <PreviewLayout
      columnGap={columnGap}
      handleColumnGapChange={setColumnGap}
      rowGap={rowGap}
      handleRowGapChange={setRowGap}>
      <View style={[styles.box, styles.box1]} />
      <View style={[styles.box, styles.box2]} />
      <View style={[styles.box, styles.box3]} />
      <View style={[styles.box, styles.box4]} />
      <View style={[styles.box, styles.box5]} />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  children,
  handleColumnGapChange,
  handleRowGapChange,
  rowGap,
  columnGap,
}) => (
  <View style={styles.previewContainer}>
    <View style={styles.inputContainer}>
      <View style={styles.itemsCenter}>
        <Text>Row Gap</Text>
        <TextInput
          style={styles.input}
          value={rowGap}
          onChangeText={v => handleRowGapChange(Number(v))}
        />
      </View>
      <View style={styles.itemsCenter}>
        <Text>Column Gap</Text>
        <TextInput
          style={styles.input}
          value={columnGap}
          onChangeText={v => handleColumnGapChange(Number(v))}
        />
      </View>
    </View>
    <View style={[styles.container, {rowGap, columnGap}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  itemsCenter: {alignItems: 'center'},
  inputContainer: {
    gap: 4,
    flexDirection: 'row',
    justifyContent: 'space-around',
  },
  previewContainer: {padding: 10, flex: 1},
  input: {
    borderBottomWidth: 1,
    paddingVertical: 3,
    width: 50,
    textAlign: 'center',
  },
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    maxHeight: 400,
    flexWrap: 'wrap',
    alignContent: 'flex-start',
  },
  box: {
    width: 50,
    height: 80,
  },
  box1: {
    backgroundColor: 'orangered',
  },
  box2: {
    backgroundColor: 'orange',
  },
  box3: {
    backgroundColor: 'mediumseagreen',
  },
  box4: {
    backgroundColor: 'deepskyblue',
  },
  box5: {
    backgroundColor: 'mediumturquoise',
  },
});

export default RowGapAndColumnGap;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Row%20Gap%20and%20Column%20Gap&ext=tsx
import React, {useState} from 'react';
import {View, Text, StyleSheet, TextInput} from 'react-native';
import type {PropsWithChildren} from 'react';

const RowGapAndColumnGap = () => {
  const [rowGap, setRowGap] = useState(10);
  const [columnGap, setColumnGap] = useState(10);

  return (
    <PreviewLayout
      columnGap={columnGap}
      handleColumnGapChange={setColumnGap}
      rowGap={rowGap}
      handleRowGapChange={setRowGap}>
      <View style={[styles.box, styles.box1]} />
      <View style={[styles.box, styles.box2]} />
      <View style={[styles.box, styles.box3]} />
      <View style={[styles.box, styles.box4]} />
      <View style={[styles.box, styles.box5]} />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  columnGap: number;
  handleColumnGapChange: (gap: number) => void;
  rowGap: number;
  handleRowGapChange: (gap: number) => void;
}>;

const PreviewLayout = ({
  children,
  handleColumnGapChange,
  handleRowGapChange,
  rowGap,
  columnGap,
}: PreviewLayoutProps) => (
  <View style={styles.previewContainer}>
    <View style={styles.inputContainer}>
      <View style={styles.itemsCenter}>
        <Text>Row Gap</Text>
        <TextInput
          style={styles.input}
          value={String(rowGap)}
          onChangeText={v => handleRowGapChange(Number(v))}
        />
      </View>
      <View style={styles.itemsCenter}>
        <Text>Column Gap</Text>
        <TextInput
          style={styles.input}
          value={String(columnGap)}
          onChangeText={v => handleColumnGapChange(Number(v))}
        />
      </View>
    </View>
    <View style={[styles.container, {rowGap, columnGap}]}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  itemsCenter: {alignItems: 'center'},
  inputContainer: {
    gap: 4,
    flexDirection: 'row',
    justifyContent: 'space-around',
  },
  previewContainer: {padding: 10, flex: 1},
  input: {
    borderBottomWidth: 1,
    paddingVertical: 3,
    width: 50,
    textAlign: 'center',
  },
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    maxHeight: 400,
    flexWrap: 'wrap',
    alignContent: 'flex-start',
  },
  box: {
    width: 50,
    height: 80,
  },
  box1: {
    backgroundColor: 'orangered',
  },
  box2: {
    backgroundColor: 'orange',
  },
  box3: {
    backgroundColor: 'mediumseagreen',
  },
  box4: {
    backgroundColor: 'deepskyblue',
  },
  box5: {
    backgroundColor: 'mediumturquoise',
  },
});

export default RowGapAndColumnGap;
```

</TabItem>
</Tabs>

## 寬度和高度

`width` 屬性指定元素內容區域的寬度。類似地，`height` 屬性指定元素內容區域的高度。

`width` 和 `height` 可以接受以下值：

- `auto`（**默認值**）React Native 根據元素的內容（無論是其他子元素、文本還是圖像）計算其寬度/高度。

- `pixels` 以絕對像素定義寬度/高度。根據組件上設置的其他樣式，這可能不是節點的最終尺寸。

- `percentage` 以父元素寬度或高度的百分比定義寬度或高度。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Width%20and%20Height&ext=js
import React, {useState} from 'react';
import {
  View,
  SafeAreaView,
  TouchableOpacity,
  Text,
  StyleSheet,
} from 'react-native';

const WidthHeightBasics = () => {
  const [widthType, setWidthType] = useState('auto');
  const [heightType, setHeightType] = useState('auto');

  return (
    <PreviewLayout
      widthType={widthType}
      heightType={heightType}
      widthValues={['auto', 300, '80%']}
      heightValues={['auto', 200, '60%']}
      setWidthType={setWidthType}
      setHeightType={setHeightType}>
      <View
        style={{
          alignSelf: 'flex-start',
          backgroundColor: 'aliceblue',
          height: heightType,
          width: widthType,
          padding: 15,
        }}>
        <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
        <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
        <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
      </View>
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  children,
  widthType,
  heightType,
  widthValues,
  heightValues,
  setWidthType,
  setHeightType,
}) => (
  <SafeAreaView style={{flex: 1, padding: 10}}>
    <View style={styles.row}>
      <Text style={styles.label}>width </Text>
      {widthValues.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setWidthType(value)}
          style={[styles.button, widthType === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              widthType === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={styles.row}>
      <Text style={styles.label}>height </Text>
      {heightValues.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setHeightType(value)}
          style={[styles.button, heightType === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              heightType === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    {children}
  </SafeAreaView>
);

const styles = StyleSheet.create({
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    padding: 8,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginRight: 10,
    marginBottom: 10,
  },
  selected: {
    backgroundColor: 'coral',
    shadowOpacity: 0,
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default WidthHeightBasics;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Width%20and%20Height&ext=tsx
import React, {useState} from 'react';
import {
  View,
  SafeAreaView,
  TouchableOpacity,
  Text,
  StyleSheet,
} from 'react-native';
import type {PropsWithChildren} from 'react';

type Dimension = string | number;

const WidthHeightBasics = () => {
  const [widthType, setWidthType] = useState<Dimension>('auto');
  const [heightType, setHeightType] = useState<Dimension>('auto');

  return (
    <PreviewLayout
      widthType={widthType}
      heightType={heightType}
      widthValues={['auto', 300, '80%']}
      heightValues={['auto', 200, '60%']}
      setWidthType={setWidthType}
      setHeightType={setHeightType}>
      <View
        style={{
          alignSelf: 'flex-start',
          backgroundColor: 'aliceblue',
          height: heightType,
          width: widthType,
          padding: 15,
        }}>
        <View style={[styles.box, {backgroundColor: 'powderblue'}]} />
        <View style={[styles.box, {backgroundColor: 'skyblue'}]} />
        <View style={[styles.box, {backgroundColor: 'steelblue'}]} />
      </View>
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  widthType: Dimension;
  heightType: Dimension;
  widthValues: Dimension[];
  heightValues: Dimension[];
  setWidthType: (value: Dimension) => void;
  setHeightType: (value: Dimension) => void;
}>;

const PreviewLayout = ({
  children,
  widthType,
  heightType,
  widthValues,
  heightValues,
  setWidthType,
  setHeightType,
}: PreviewLayoutProps) => (
  <SafeAreaView style={{flex: 1, padding: 10}}>
    <View style={styles.row}>
      <Text style={styles.label}>width </Text>
      {widthValues.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setWidthType(value)}
          style={[styles.button, widthType === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              widthType === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={styles.row}>
      <Text style={styles.label}>height </Text>
      {heightValues.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setHeightType(value)}
          style={[styles.button, heightType === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              heightType === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    {children}
  </SafeAreaView>
);

const styles = StyleSheet.create({
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    padding: 8,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginRight: 10,
    marginBottom: 10,
  },
  selected: {
    backgroundColor: 'coral',
    shadowOpacity: 0,
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default WidthHeightBasics;
```

</TabItem>
</Tabs>

## 絕對與相對佈局

元素的 `position` 類型定義了其在父元素內的定位方式。

- `relative`（**預設值**）預設情況下，元素會以相對方式定位。這表示元素會根據佈局的正常流動進行定位，然後根據 `top`、`right`、`bottom` 和 `left` 的值相對於該位置進行偏移。此偏移不會影響任何兄弟元素或父元素的位置。

- `absolute` 當元素以絕對方式定位時，它不會參與正常的佈局流動。相反，它會獨立於其兄弟元素進行佈局。其位置是根據 `top`、`right`、`bottom` 和 `left` 的值來決定的。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Absolute%20%26%20Relative%20Layout&ext=js
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';

const PositionLayout = () => {
  const [position, setPosition] = useState('relative');

  return (
    <PreviewLayout
      label="position"
      selectedValue={position}
      values={['relative', 'absolute']}
      setSelectedValue={setPosition}>
      <View
        style={[
          styles.box,
          {
            top: 25,
            left: 25,
            position,
            backgroundColor: 'powderblue',
          },
        ]}
      />
      <View
        style={[
          styles.box,
          {
            top: 50,
            left: 50,
            position,
            backgroundColor: 'skyblue',
          },
        ]}
      />
      <View
        style={[
          styles.box,
          {
            top: 75,
            left: 75,
            position,
            backgroundColor: 'steelblue',
          },
        ]}
      />
    </PreviewLayout>
  );
};

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={styles.container}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    minHeight: 200,
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default PositionLayout;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Absolute%20%26%20Relative%20Layout&ext=tsx
import React, {useState} from 'react';
import {View, TouchableOpacity, Text, StyleSheet} from 'react-native';
import type {PropsWithChildren} from 'react';

const PositionLayout = () => {
  const [position, setPosition] = useState<'relative' | 'absolute'>('relative');

  return (
    <PreviewLayout
      label="position"
      selectedValue={position}
      values={['relative', 'absolute']}
      setSelectedValue={setPosition}>
      <View
        style={[
          styles.box,
          {
            top: 25,
            left: 25,
            position,
            backgroundColor: 'powderblue',
          },
        ]}
      />
      <View
        style={[
          styles.box,
          {
            top: 50,
            left: 50,
            position,
            backgroundColor: 'skyblue',
          },
        ]}
      />
      <View
        style={[
          styles.box,
          {
            top: 75,
            left: 75,
            position,
            backgroundColor: 'steelblue',
          },
        ]}
      />
    </PreviewLayout>
  );
};

type PreviewLayoutProps = PropsWithChildren<{
  label: string;
  values: Array<'relative' | 'absolute'>;
  selectedValue: string;
  setSelectedValue: (value: 'relative' | 'absolute') => void;
}>;

const PreviewLayout = ({
  label,
  children,
  values,
  selectedValue,
  setSelectedValue,
}: PreviewLayoutProps) => (
  <View style={{padding: 10, flex: 1}}>
    <Text style={styles.label}>{label}</Text>
    <View style={styles.row}>
      {values.map(value => (
        <TouchableOpacity
          key={value}
          onPress={() => setSelectedValue(value)}
          style={[styles.button, selectedValue === value && styles.selected]}>
          <Text
            style={[
              styles.buttonLabel,
              selectedValue === value && styles.selectedLabel,
            ]}>
            {value}
          </Text>
        </TouchableOpacity>
      ))}
    </View>
    <View style={styles.container}>{children}</View>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 8,
    backgroundColor: 'aliceblue',
    minHeight: 200,
  },
  box: {
    width: 50,
    height: 50,
  },
  row: {
    flexDirection: 'row',
    flexWrap: 'wrap',
  },
  button: {
    paddingHorizontal: 8,
    paddingVertical: 6,
    borderRadius: 4,
    backgroundColor: 'oldlace',
    alignSelf: 'flex-start',
    marginHorizontal: '1%',
    marginBottom: 6,
    minWidth: '48%',
    textAlign: 'center',
  },
  selected: {
    backgroundColor: 'coral',
    borderWidth: 0,
  },
  buttonLabel: {
    fontSize: 12,
    fontWeight: '500',
    color: 'coral',
  },
  selectedLabel: {
    color: 'white',
  },
  label: {
    textAlign: 'center',
    marginBottom: 10,
    fontSize: 24,
  },
});

export default PositionLayout;
```

</TabItem>
</Tabs>

## 深入探討

查看互動式的 [yoga playground](https://www.yogalayout.dev/playground)，你可以用它來更深入理解 flexbox。

我們已經涵蓋了基礎知識，但還有許多其他你可能需要用於佈局的樣式。控制佈局的完整屬性列表記錄在[這裡](./layout-props.md)。

此外，你也可以參考 [Wix Engineers](https://medium.com/wix-engineering/the-full-react-native-layout-cheat-sheet-a4147802405c) 提供的一些範例。