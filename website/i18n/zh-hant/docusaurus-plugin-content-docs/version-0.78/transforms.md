---
id: transforms
title: Transforms
---

變換(Transforms)是能幫助您使用2D或3D轉換來修改元件外觀和位置的樣式屬性。但需注意，當套用變換後，元件周圍的佈局會保持原狀，這可能導致變換後的元件與相鄰元件重疊。您可以透過為變換元件添加邊距(margin)、調整相鄰元件或設置容器內邊距(padding)來避免此類重疊問題。

## 範例

```SnackPlayer name=Transforms%20Example
import React from 'react';
import {ScrollView, StyleSheet, Text, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <ScrollView contentContainerStyle={styles.scrollContentContainer}>
        <View style={styles.box}>
          <Text style={styles.text}>Original Object</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{scale: 2}],
            },
          ]}>
          <Text style={styles.text}>Scale by 2</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{scaleX: 2}],
            },
          ]}>
          <Text style={styles.text}>ScaleX by 2</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{scaleY: 2}],
            },
          ]}>
          <Text style={styles.text}>ScaleY by 2</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{rotate: '45deg'}],
            },
          ]}>
          <Text style={styles.text}>Rotate by 45 deg</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{rotateX: '45deg'}, {rotateZ: '45deg'}],
            },
          ]}>
          <Text style={styles.text}>Rotate X&Z by 45 deg</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{rotateY: '45deg'}, {rotateZ: '45deg'}],
            },
          ]}>
          <Text style={styles.text}>Rotate Y&Z by 45 deg</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{skewX: '45deg'}],
            },
          ]}>
          <Text style={styles.text}>SkewX by 45 deg</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{skewY: '45deg'}],
            },
          ]}>
          <Text style={styles.text}>SkewY by 45 deg</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{skewX: '30deg'}, {skewY: '30deg'}],
            },
          ]}>
          <Text style={styles.text}>Skew X&Y by 30 deg</Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{translateX: -50}],
            },
          ]}>
          <Text style={styles.text}>TranslateX by -50 </Text>
        </View>

        <View
          style={[
            styles.box,
            {
              transform: [{translateY: 50}],
            },
          ]}>
          <Text style={styles.text}>TranslateY by 50 </Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  scrollContentContainer: {
    alignItems: 'center',
    paddingBottom: 60,
  },
  box: {
    height: 100,
    width: 100,
    borderRadius: 5,
    marginVertical: 40,
    backgroundColor: '#61dafb',
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    fontSize: 14,
    fontWeight: 'bold',
    margin: 8,
    color: '#000',
    textAlign: 'center',
  },
});

export default App;
```

---

# 參考文獻

## 變換(Transform)

`transform`可接受轉換物件陣列或以空格分隔的字串值。每個物件以要變換的屬性作為鍵(key)，並以變換值作為值(value)。物件不應合併使用，每個物件應只包含一組鍵/值對。

旋轉(rotate)變換需使用字串來表示角度(deg)或弧度(rad)。例如：

The rotate transformations require a string so that the transform may be expressed in degrees (deg) or radians (rad). For example:

```js
{
  transform: [{rotateX: '45deg'}, {rotateZ: '0.785398rad'}],
}
```

同樣效果也可透過空格分隔的字串實現：

```js
{
  transform: 'rotateX(45deg) rotateZ(0.785398rad)',
}
```

傾斜(skew)變換需使用字串來表示角度(deg)。例如：

```js
{
  transform: [{skewX: '45deg'}],
}
```

| Type                                                                                                                                                                                                                                                                                                          | Required |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| array of objects: `{matrix: number[]}`, `{perspective: number}`, `{rotate: string}`, `{rotateX: string}`, `{rotateY: string}`, `{rotateZ: string}`, `{scale: number}`, `{scaleX: number}`, `{scaleY: number}`, `{translateX: number}`, `{translateY: number}`, `{skewX: string}`, `{skewY: string}` or string | No       |

---

### `decomposedMatrix`, `rotation`, `scaleX`, `scaleY`, `transformMatrix`, `translateX`, `translateY`

> **已棄用。** 請改用 [`transform`](transforms#transform) 屬性。

## 變換原點(Transform Origin)

`transformOrigin`屬性設定視圖變換的基準點。變換原點是變換操作所圍繞的定位點，預設為`center`。

# 範例

```SnackPlayer name=TransformOrigin%20Example&supportedPlatforms=ios,android
import React, {useEffect} from 'react';
import {
  Animated,
  View,
  StyleSheet,
  Easing,
  useAnimatedValue,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const rotateAnim = useAnimatedValue(0);

  useEffect(() => {
    Animated.loop(
      Animated.timing(rotateAnim, {
        toValue: 1,
        duration: 5000,
        easing: Easing.linear,
        useNativeDriver: true,
      }),
    ).start();
  }, [rotateAnim]);

  const spin = rotateAnim.interpolate({
    inputRange: [0, 1],
    outputRange: ['0deg', '360deg'],
  });

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <View style={styles.transformOriginWrapper}>
          <Animated.View
            style={[
              styles.transformOriginView,
              {
                transform: [{rotate: spin}],
              },
            ]}
          />
        </View>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  transformOriginWrapper: {
    borderWidth: 1,
    borderColor: 'rgba(0, 0, 0, 0.5)',
  },
  transformOriginView: {
    backgroundColor: 'pink',
    width: 100,
    height: 100,
    transformOrigin: 'top',
  },
});

export default App;
```

### 數值格式

變換原點支援`px`、`percentage`單位及關鍵字`top`、`left`、`right`、`bottom`、`center`。

該屬性可使用一至三個值來指定偏移量：

#### 單值語法：

#### One-value syntax:

- 值必須為`px`、`percentage`或關鍵字`left`、`center`、`right`、`top`、`bottom`之一。

```js
{
  transformOrigin: '20px',
  transformOrigin: 'bottom',
}
```

#### 雙值語法：

- 首值(x軸偏移)須為`px`、`percentage`或關鍵字`left`、`center`、`right`
- 次值(y軸偏移)須為`px`、`percentage`或關鍵字`top`、`center`、`bottom`

```js
{
  transformOrigin: '10px 2px',
  transformOrigin: 'left top',
  transformOrigin: 'top right',
}
```

#### 三值語法：

- 前兩值規範同雙值語法
- 第三值(z軸偏移)必須為`px`單位，恆代表Z軸偏移量

```js
{
  transformOrigin: '2px 30% 10px',
  transformOrigin: 'right bottom 20px',
}
```

#### 陣列語法

`transformOrigin`亦支援陣列語法，便於與Animated API配合使用。此方式避免字串解析，執行效率更佳。

```js
{
  // Using numeric values
  transformOrigin: [10, 30, 40],
  // Mixing numeric and percentage values
  transformOrigin: [10, '20%', 0],
}
```

更多詳細資訊可參考MDN的[變換原點指南](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-origin)。