---
id: text-style-props
title: Text Style Props
---

### 範例

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=TextStyleProps&supportedPlatforms=ios,android&ext=js&dependencies=@react-native-community/slider
import React, {useState} from 'react';
import {
  FlatList,
  Platform,
  ScrollView,
  StatusBar,
  StyleSheet,
  Switch,
  Text,
  TouchableWithoutFeedback,
  View,
} from 'react-native';
import Slider from '@react-native-community/slider';

const fontStyles = ['normal', 'italic'];
const fontVariants = [
  undefined,
  'small-caps',
  'oldstyle-nums',
  'lining-nums',
  'tabular-nums',
  'proportional-nums',
];
const fontWeights = [
  'normal',
  'bold',
  '100',
  '200',
  '300',
  '400',
  '500',
  '600',
  '700',
  '800',
  '900',
];
const textAlignments = ['auto', 'left', 'right', 'center', 'justify'];
const textDecorationLines = [
  'none',
  'underline',
  'line-through',
  'underline line-through',
];
const textDecorationStyles = ['solid', 'double', 'dotted', 'dashed'];
const textTransformations = ['none', 'uppercase', 'lowercase', 'capitalize'];
const textAlignmentsVertical = ['auto', 'top', 'bottom', 'center'];
const writingDirections = ['auto', 'ltr', 'rtl'];

const App = () => {
  const [fontSize, setFontSize] = useState(10);
  const [fontStyleIdx, setFontStyleIdx] = useState(0);
  const [fontWeightIdx, setFontWeightIdx] = useState(0);
  const [lineHeight, setLineHeight] = useState(10);
  const [textAlignIdx, setTextAlignIdx] = useState(0);
  const [textDecorationLineIdx, setTextDecorationLineIdx] = useState(0);
  const [includeFontPadding, setIncludeFontPadding] = useState(false);
  const [textVerticalAlignIdx, setTextVerticalAlignIdx] = useState(0);
  const [fontVariantIdx, setFontVariantIdx] = useState(0);
  const [letterSpacing, setLetterSpacing] = useState(0);
  const [textDecorationStyleIdx, setTextDecorationStyleIdx] = useState(0);
  const [textTransformIdx, setTextTransformIdx] = useState(0);
  const [writingDirectionIdx, setWritingDirectionIdx] = useState(0);
  const [textShadowRadius, setTextShadowRadius] = useState(0);
  const [textShadowOffset, setTextShadowOffset] = useState({
    height: 0,
    width: 0,
  });

  const [, ...validFontVariants] = fontVariants;

  return (
    <View style={styles.container}>
      <Text
        style={[
          styles.paragraph,
          {
            fontSize,
            fontStyle: fontStyles[fontStyleIdx],
            fontWeight: fontWeights[fontWeightIdx],
            lineHeight,
            textAlign: textAlignments[textAlignIdx],
            textDecorationLine: textDecorationLines[textDecorationLineIdx],
            textTransform: textTransformations[textTransformIdx],
            textAlignVertical: textAlignmentsVertical[textVerticalAlignIdx],
            fontVariant:
              fontVariantIdx === 0
                ? undefined
                : [validFontVariants[fontVariantIdx - 1]],
            letterSpacing,
            includeFontPadding,
            textDecorationStyle: textDecorationStyles[textDecorationStyleIdx],
            writingDirection: writingDirections[writingDirectionIdx],
            textShadowOffset,
            textShadowRadius,
          },
        ]}>
        Lorem Ipsum is simply dummy text of the printing and typesetting
        industry. 112 Likes
      </Text>
      <ScrollView>
        <View>
          <Text>Common platform properties</Text>
          <CustomSlider
            label="Text Shadow Offset - Height"
            value={textShadowOffset.height}
            minimumValue={-40}
            maximumValue={40}
            handleValueChange={val =>
              setTextShadowOffset(prev => ({...prev, height: val}))
            }
          />
          <CustomSlider
            label="Text Shadow Offset - Width"
            value={textShadowOffset.width}
            minimumValue={-40}
            maximumValue={40}
            handleValueChange={val =>
              setTextShadowOffset(prev => ({...prev, width: val}))
            }
          />
          <CustomSlider
            label="Font Size"
            value={fontSize}
            maximumValue={40}
            handleValueChange={setFontSize}
          />
          <CustomPicker
            label="Font Style"
            data={fontStyles}
            currentIndex={fontStyleIdx}
            onSelected={setFontStyleIdx}
          />
          <CustomPicker
            label="Font Weight"
            data={fontWeights}
            currentIndex={fontWeightIdx}
            onSelected={setFontWeightIdx}
          />
          <CustomSlider
            label="Line Height"
            value={lineHeight}
            minimumValue={10}
            maximumValue={50}
            handleValueChange={setLineHeight}
          />
          <CustomPicker
            label="Text Align"
            data={textAlignments}
            currentIndex={textAlignIdx}
            onSelected={setTextAlignIdx}
          />
          <CustomPicker
            label="Text Decoration Line"
            data={textDecorationLines}
            currentIndex={textDecorationLineIdx}
            onSelected={setTextDecorationLineIdx}
          />
          <CustomSlider
            label="Text Shadow Radius"
            value={textShadowRadius}
            handleValueChange={setTextShadowRadius}
          />
          <CustomPicker
            label="Font Variant"
            data={fontVariants}
            currentIndex={fontVariantIdx}
            onSelected={setFontVariantIdx}
          />
          <CustomSlider
            label="Letter Spacing"
            step={0.1}
            value={letterSpacing}
            handleValueChange={setLetterSpacing}
          />
          <CustomPicker
            label="Text Transform"
            data={textTransformations}
            currentIndex={textTransformIdx}
            onSelected={setTextTransformIdx}
          />
        </View>
        {Platform.OS === 'android' && (
          <View style={styles.platformContainer}>
            <Text style={styles.platformContainerTitle}>
              Android only properties
            </Text>
            <CustomPicker
              label="Text Vertical Align"
              data={textAlignmentsVertical}
              currentIndex={textVerticalAlignIdx}
              onSelected={setTextVerticalAlignIdx}
            />
            <CustomSwitch
              label="Include Font Padding"
              handleValueChange={setIncludeFontPadding}
              value={includeFontPadding}
            />
          </View>
        )}
        {Platform.OS === 'ios' && (
          <View style={styles.platformContainer}>
            <Text style={styles.platformContainerTitle}>
              iOS only properties
            </Text>
            <CustomPicker
              label="Text Decoration Style"
              data={textDecorationStyles}
              currentIndex={textDecorationStyleIdx}
              onSelected={setTextDecorationStyleIdx}
            />
            <CustomPicker
              label="Writing Direction"
              data={writingDirections}
              currentIndex={writingDirectionIdx}
              onSelected={setWritingDirectionIdx}
            />
          </View>
        )}
      </ScrollView>
    </View>
  );
};

const CustomSwitch = ({label, handleValueChange, value}) => {
  return (
    <>
      <Text style={styles.title}>{label}</Text>
      <View style={{alignItems: 'flex-start'}}>
        <Switch
          trackColor={{false: '#767577', true: '#DAA520'}}
          thumbColor={value ? '#DAA520' : '#f4f3f4'}
          onValueChange={handleValueChange}
          value={value}
        />
      </View>
    </>
  );
};

const CustomSlider = ({
  label,
  handleValueChange,
  step = 1,
  minimumValue = 0,
  maximumValue = 10,
  value,
}) => {
  return (
    <>
      {label && (
        <Text style={styles.title}>{`${label} (${value.toFixed(2)})`}</Text>
      )}
      <View style={styles.wrapperHorizontal}>
        <Slider
          thumbTintColor="#DAA520"
          minimumTrackTintColor="#DAA520"
          minimumValue={minimumValue}
          maximumValue={maximumValue}
          step={step}
          onValueChange={handleValueChange}
          value={value}
        />
      </View>
    </>
  );
};

const CustomPicker = ({label, data, currentIndex, onSelected}) => {
  return (
    <>
      <Text style={styles.title}>{label}</Text>
      <View style={styles.wrapperHorizontal}>
        <FlatList
          bounces
          horizontal
          data={data}
          keyExtractor={item => String(item)}
          renderItem={({item, index}) => {
            const selected = index === currentIndex;
            return (
              <TouchableWithoutFeedback onPress={() => onSelected(index)}>
                <View
                  style={[
                    styles.itemStyleHorizontal,
                    selected && styles.itemSelectedStyleHorizontal,
                  ]}>
                  <Text
                    style={{
                      textAlign: 'center',
                      color: selected ? 'black' : 'grey',
                      fontWeight: selected ? 'bold' : 'normal',
                    }}>
                    {item + ''}
                  </Text>
                </View>
              </TouchableWithoutFeedback>
            );
          }}
        />
      </View>
    </>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
    backgroundColor: '#ecf0f1',
    padding: 8,
  },
  paragraph: {
    color: 'black',
    textDecorationColor: 'yellow',
    textShadowColor: 'red',
    textShadowRadius: 1,
    margin: 24,
  },
  wrapperHorizontal: {
    height: 54,
    justifyContent: 'center',
    color: 'black',
    marginBottom: 12,
  },
  itemStyleHorizontal: {
    marginRight: 10,
    height: 50,
    padding: 8,
    borderWidth: 1,
    borderColor: 'grey',
    borderRadius: 25,
    textAlign: 'center',
    justifyContent: 'center',
  },
  itemSelectedStyleHorizontal: {
    borderWidth: 2,
    borderColor: '#DAA520',
  },
  platformContainer: {
    marginTop: 8,
    borderTopWidth: 1,
  },
  platformContainerTitle: {
    marginTop: 8,
  },
  title: {
    fontWeight: 'bold',
    marginVertical: 4,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=TextStyleProps&supportedPlatforms=ios,android&ext=tsx&dependencies=@react-native-community/slider
import React, {useState} from 'react';
import {
  FlatList,
  Platform,
  ScrollView,
  StatusBar,
  StyleSheet,
  Switch,
  Text,
  TouchableWithoutFeedback,
  View,
} from 'react-native';
import Slider from '@react-native-community/slider';

const fontStyles = ['normal', 'italic'] as const;
const fontVariants = [
  undefined,
  'small-caps',
  'oldstyle-nums',
  'lining-nums',
  'tabular-nums',
  'proportional-nums',
] as const;
const fontWeights = [
  'normal',
  'bold',
  '100',
  '200',
  '300',
  '400',
  '500',
  '600',
  '700',
  '800',
  '900',
] as const;
const textAlignments = ['auto', 'left', 'right', 'center', 'justify'] as const;
const textDecorationLines = [
  'none',
  'underline',
  'line-through',
  'underline line-through',
] as const;
const textDecorationStyles = ['solid', 'double', 'dotted', 'dashed'] as const;
const textTransformations = [
  'none',
  'uppercase',
  'lowercase',
  'capitalize',
] as const;
const textAlignmentsVertical = ['auto', 'top', 'bottom', 'center'] as const;
const writingDirections = ['auto', 'ltr', 'rtl'] as const;

const App = () => {
  const [fontSize, setFontSize] = useState(10);
  const [fontStyleIdx, setFontStyleIdx] = useState(0);
  const [fontWeightIdx, setFontWeightIdx] = useState(0);
  const [lineHeight, setLineHeight] = useState(10);
  const [textAlignIdx, setTextAlignIdx] = useState(0);
  const [textDecorationLineIdx, setTextDecorationLineIdx] = useState(0);
  const [includeFontPadding, setIncludeFontPadding] = useState(false);
  const [textVerticalAlignIdx, setTextVerticalAlignIdx] = useState(0);
  const [fontVariantIdx, setFontVariantIdx] = useState(0);
  const [letterSpacing, setLetterSpacing] = useState(0);
  const [textDecorationStyleIdx, setTextDecorationStyleIdx] = useState(0);
  const [textTransformIdx, setTextTransformIdx] = useState(0);
  const [writingDirectionIdx, setWritingDirectionIdx] = useState(0);
  const [textShadowRadius, setTextShadowRadius] = useState(0);
  const [textShadowOffset, setTextShadowOffset] = useState({
    height: 0,
    width: 0,
  });

  const [, ...validFontVariants] = fontVariants;

  return (
    <View style={styles.container}>
      <Text
        style={[
          styles.paragraph,
          {
            fontSize,
            fontStyle: fontStyles[fontStyleIdx],
            fontWeight: fontWeights[fontWeightIdx],
            lineHeight,
            textAlign: textAlignments[textAlignIdx],
            textDecorationLine: textDecorationLines[textDecorationLineIdx],
            textTransform: textTransformations[textTransformIdx],
            textAlignVertical: textAlignmentsVertical[textVerticalAlignIdx],
            fontVariant:
              fontVariantIdx === 0
                ? undefined
                : [validFontVariants[fontVariantIdx - 1]],
            letterSpacing,
            includeFontPadding,
            textDecorationStyle: textDecorationStyles[textDecorationStyleIdx],
            writingDirection: writingDirections[writingDirectionIdx],
            textShadowOffset,
            textShadowRadius,
          },
        ]}>
        Lorem Ipsum is simply dummy text of the printing and typesetting
        industry. 112 Likes
      </Text>
      <ScrollView>
        <View>
          <Text>Common platform properties</Text>
          <CustomSlider
            label="Text Shadow Offset - Height"
            value={textShadowOffset.height}
            minimumValue={-40}
            maximumValue={40}
            handleValueChange={(val: number) =>
              setTextShadowOffset(prev => ({...prev, height: val}))
            }
          />
          <CustomSlider
            label="Text Shadow Offset - Width"
            value={textShadowOffset.width}
            minimumValue={-40}
            maximumValue={40}
            handleValueChange={(val: number) =>
              setTextShadowOffset(prev => ({...prev, width: val}))
            }
          />
          <CustomSlider
            label="Font Size"
            value={fontSize}
            maximumValue={40}
            handleValueChange={setFontSize}
          />
          <CustomPicker
            label="Font Style"
            data={fontStyles}
            currentIndex={fontStyleIdx}
            onSelected={setFontStyleIdx}
          />
          <CustomPicker
            label="Font Weight"
            data={fontWeights}
            currentIndex={fontWeightIdx}
            onSelected={setFontWeightIdx}
          />
          <CustomSlider
            label="Line Height"
            value={lineHeight}
            minimumValue={10}
            maximumValue={50}
            handleValueChange={setLineHeight}
          />
          <CustomPicker
            label="Text Align"
            data={textAlignments}
            currentIndex={textAlignIdx}
            onSelected={setTextAlignIdx}
          />
          <CustomPicker
            label="Text Decoration Line"
            data={textDecorationLines}
            currentIndex={textDecorationLineIdx}
            onSelected={setTextDecorationLineIdx}
          />
          <CustomSlider
            label="Text Shadow Radius"
            value={textShadowRadius}
            handleValueChange={setTextShadowRadius}
          />
          <CustomPicker
            label="Font Variant"
            data={fontVariants}
            currentIndex={fontVariantIdx}
            onSelected={setFontVariantIdx}
          />
          <CustomSlider
            label="Letter Spacing"
            step={0.1}
            value={letterSpacing}
            handleValueChange={setLetterSpacing}
          />
          <CustomPicker
            label="Text Transform"
            data={textTransformations}
            currentIndex={textTransformIdx}
            onSelected={setTextTransformIdx}
          />
        </View>
        {Platform.OS === 'android' && (
          <View style={styles.platformContainer}>
            <Text style={styles.platformContainerTitle}>
              Android only properties
            </Text>
            <CustomPicker
              label="Text Vertical Align"
              data={textAlignmentsVertical}
              currentIndex={textVerticalAlignIdx}
              onSelected={setTextVerticalAlignIdx}
            />
            <CustomSwitch
              label="Include Font Padding"
              handleValueChange={setIncludeFontPadding}
              value={includeFontPadding}
            />
          </View>
        )}
        {Platform.OS === 'ios' && (
          <View style={styles.platformContainer}>
            <Text style={styles.platformContainerTitle}>
              iOS only properties
            </Text>
            <CustomPicker
              label="Text Decoration Style"
              data={textDecorationStyles}
              currentIndex={textDecorationStyleIdx}
              onSelected={setTextDecorationStyleIdx}
            />
            <CustomPicker
              label="Writing Direction"
              data={writingDirections}
              currentIndex={writingDirectionIdx}
              onSelected={setWritingDirectionIdx}
            />
          </View>
        )}
      </ScrollView>
    </View>
  );
};

type CustomSwitchProps = {
  label: string;
  handleValueChange: (value: boolean) => void;
  value: boolean;
};

const CustomSwitch = ({label, handleValueChange, value}: CustomSwitchProps) => {
  return (
    <>
      <Text style={styles.title}>{label}</Text>
      <View style={{alignItems: 'flex-start'}}>
        <Switch
          trackColor={{false: '#767577', true: '#DAA520'}}
          thumbColor={value ? '#DAA520' : '#f4f3f4'}
          onValueChange={handleValueChange}
          value={value}
        />
      </View>
    </>
  );
};

type CustomSliderProps = {
  label: string;
  handleValueChange: (value: number) => void;
  step?: number;
  minimumValue?: number;
  maximumValue?: number;
  value: number;
};

const CustomSlider = ({
  label,
  handleValueChange,
  step = 1,
  minimumValue = 0,
  maximumValue = 10,
  value,
}: CustomSliderProps) => {
  return (
    <>
      {label && (
        <Text style={styles.title}>{`${label} (${value.toFixed(2)})`}</Text>
      )}
      <View style={styles.wrapperHorizontal}>
        <Slider
          thumbTintColor="#DAA520"
          minimumTrackTintColor="#DAA520"
          minimumValue={minimumValue}
          maximumValue={maximumValue}
          step={step}
          onValueChange={handleValueChange}
          value={value}
        />
      </View>
    </>
  );
};

type CustomPickerProps = {
  label: string;
  data: ReadonlyArray<string | undefined>;
  currentIndex: number;
  onSelected: (index: number) => void;
};

const CustomPicker = ({
  label,
  data,
  currentIndex,
  onSelected,
}: CustomPickerProps) => {
  return (
    <>
      <Text style={styles.title}>{label}</Text>
      <View style={styles.wrapperHorizontal}>
        <FlatList
          bounces
          horizontal
          data={data}
          keyExtractor={item => String(item)}
          renderItem={({item, index}) => {
            const selected = index === currentIndex;
            return (
              <TouchableWithoutFeedback onPress={() => onSelected(index)}>
                <View
                  style={[
                    styles.itemStyleHorizontal,
                    selected && styles.itemSelectedStyleHorizontal,
                  ]}>
                  <Text
                    style={{
                      textAlign: 'center',
                      color: selected ? 'black' : 'grey',
                      fontWeight: selected ? 'bold' : 'normal',
                    }}>
                    {item + ''}
                  </Text>
                </View>
              </TouchableWithoutFeedback>
            );
          }}
        />
      </View>
    </>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
    backgroundColor: '#ecf0f1',
    padding: 8,
  },
  paragraph: {
    color: 'black',
    textDecorationColor: 'yellow',
    textShadowColor: 'red',
    textShadowRadius: 1,
    margin: 24,
  },
  wrapperHorizontal: {
    height: 54,
    justifyContent: 'center',
    color: 'black',
    marginBottom: 12,
  },
  itemStyleHorizontal: {
    marginRight: 10,
    height: 50,
    padding: 8,
    borderWidth: 1,
    borderColor: 'grey',
    borderRadius: 25,
    textAlign: 'center',
    justifyContent: 'center',
  },
  itemSelectedStyleHorizontal: {
    borderWidth: 2,
    borderColor: '#DAA520',
  },
  platformContainer: {
    marginTop: 8,
    borderTopWidth: 1,
  },
  platformContainerTitle: {
    marginTop: 8,
  },
  title: {
    fontWeight: 'bold',
    marginVertical: 4,
  },
});

export default App;
```

</TabItem>
</Tabs>

# 參考文獻

## 屬性

### `color`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `fontFamily`

| Type   |
| ------ |
| string |

---

### `fontSize`

| Type   |
| ------ |
| number |

---

### `fontStyle`

| Type                         |
| ---------------------------- |
| enum(`'normal'`, `'italic'`) |

---

### `fontWeight`

指定字體粗細。大多數字體支援 `'normal'` 和 `'bold'` 值。並非所有字體都具備每個數值對應的變體，此時會選擇最接近的數值。

| Type                                                                                                                  | Default    |
| --------------------------------------------------------------------------------------------------------------------- | ---------- |
| enum(`'normal'`, `'bold'`, `'100'`, `'200'`, `'300'`, `'400'`, `'500'`, `'600'`, `'700'`, `'800'`, `'900'`) or number | `'normal'` |

---

### `includeFontPadding` <div class="label android">Android</div>

設為 `false` 可移除為特定升部/降部預留的額外字體內邊距。某些字體使用此內邊距時，垂直置中的文字可能略微不對齊。為達最佳效果，建議同時將 `textAlignVertical` 設為 `center`。

| Type | Default |
| ---- | ------- |
| bool | `true`  |

---

### `fontVariant`

允許設定字體的所有變體形式。可透過枚舉陣列或空格分隔字串設定，例如 `'small-caps common-ligatures'`。

| Type                                                                                                                 | Default |
| -------------------------------------------------------------------------------------------------------------------- | ------- |
| array of enum(`'small-caps'`, `'oldstyle-nums'`, `'lining-nums'`, `'tabular-nums'`, `'proportional-nums'`) or string | `[]`    |

---

### `letterSpacing`

增減字符間距。預設無額外字距。

| Type   |
| ------ |
| number |

---

### `lineHeight`

控制文字元素內行間垂直間距的數值。指定連續文字行基線之間的距離。

| Type   |
| ------ |
| number |

---

### `textAlign`

指定文字對齊方式。Android 系統中，'justify' 值僅支援 Oreo (8.0) 或更高版本 (API level >= 26)。較低 Android 版本會自動降級為 `left`。

| Type                                                         | Default  |
| ------------------------------------------------------------ | -------- |
| enum(`'auto'`, `'left'`, `'right'`, `'center'`, `'justify'`) | `'auto'` |

---

### `textAlignVertical` <div class="label android">Android</div>

| Type                                            | Default  |
| ----------------------------------------------- | -------- |
| enum(`'auto'`, `'top'`, `'bottom'`, `'center'`) | `'auto'` |

---

### `textDecorationColor` <div class="label ios">iOS</div>

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `textDecorationLine`

| Type                                                                        | Default  |
| --------------------------------------------------------------------------- | -------- |
| enum(`'none'`, `'underline'`, `'line-through'`, `'underline line-through'`) | `'none'` |

---

### `textDecorationStyle` <div class="label ios">iOS</div>

| Type                                                | Default   |
| --------------------------------------------------- | --------- |
| enum(`'solid'`, `'double'`, `'dotted'`, `'dashed'`) | `'solid'` |

---

### `textShadowColor`

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `textShadowOffset`

| Type                                        |
| ------------------------------------------- |
| object: `{width?: number, height?: number}` |

---

### `textShadowRadius`

| Type   |
| ------ |
| number |

---

### `textTransform`

| Type                                                         | Default  |
| ------------------------------------------------------------ | -------- |
| enum(`'none'`, `'uppercase'`, `'lowercase'`, `'capitalize'`) | `'none'` |

---

### `verticalAlign` <div class="label android">Android</div>

| Type                                            | Default  |
| ----------------------------------------------- | -------- |
| enum(`'auto'`, `'top'`, `'bottom'`, `'middle'`) | `'auto'` |

---

### `writingDirection` <div class="label ios">iOS</div>

| Type                             | Default  |
| -------------------------------- | -------- |
| enum(`'auto'`, `'ltr'`, `'rtl'`) | `'auto'` |

---

### `userSelect`

允許使用者選取文字並使用原生複製貼上功能。此屬性優先於 `selectable` 屬性。

| Type                                                     | Default |
| -------------------------------------------------------- | ------- |
| enum(`'auto'`, `'text'`, `'none'`, `'contain'`, `'all'`) | `none`  |