---
id: easing
title: Easing
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`Easing` 模組實現了常見的緩動函數。該模組被 [`Animated.timing()`](animated.md#timing) 用於在動畫中傳達物理上可信的運動。

您可以在 http://easings.net/ 找到一些常見緩動函數的可視化效果。

### 預定義動畫

`Easing` 模組通過以下方法提供了幾種預定義的動畫：

- [`back`](easing.md#back) 提供基本動畫效果，物件會先稍微後退再向前移動
- [`bounce`](easing.md#bounce) 提供彈跳動畫效果
- [`ease`](easing.md#ease) 提供基本慣性動畫效果
- [`elastic`](easing.md#elastic) 提供基本彈簧交互效果

### 標準函數

提供了三種標準緩動函數：

- [`linear`](easing.md#linear)
- [`quad`](easing.md#quad)
- [`cubic`](easing.md#cubic)

[`poly`](easing.md#poly) 函數可用於實現四次方、五次方及其他更高次方的函數。

### 附加函數

以下方法提供了額外的數學函數：

- [`bezier`](easing.md#bezier) 提供三次貝茲曲線
- [`circle`](easing.md#circle) 提供圓形函數
- [`sin`](easing.md#sin) 提供正弦函數
- [`exp`](easing.md#exp) 提供指數函數

以下輔助函數用於修改其他緩動函數。

- [`in`](easing.md#in) 正向運行緩動函數
- [`inOut`](easing.md#inout) 使任何緩動函數對稱
- [`out`](easing.md#out) 反向運行緩動函數

## 範例

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Easing%20Demo&ext=js&supportedPlatforms=ios,android
import React from 'react';
import {
  Animated,
  Easing,
  SectionList,
  StatusBar,
  StyleSheet,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';

const App = () => {
  let opacity = new Animated.Value(0);

  const animate = easing => {
    opacity.setValue(0);
    Animated.timing(opacity, {
      toValue: 1,
      duration: 1200,
      easing,
      useNativeDriver: true,
    }).start();
  };

  const size = opacity.interpolate({
    inputRange: [0, 1],
    outputRange: [0, 80],
  });

  const animatedStyles = [
    styles.box,
    {
      opacity,
      width: size,
      height: size,
    },
  ];

  return (
    <View style={styles.container}>
      <StatusBar hidden={true} />
      <Text style={styles.title}>Press rows below to preview the Easing!</Text>
      <View style={styles.boxContainer}>
        <Animated.View style={animatedStyles} />
      </View>
      <SectionList
        style={styles.list}
        sections={SECTIONS}
        keyExtractor={item => item.title}
        renderItem={({item}) => (
          <TouchableOpacity
            onPress={() => animate(item.easing)}
            style={styles.listRow}>
            <Text>{item.title}</Text>
          </TouchableOpacity>
        )}
        renderSectionHeader={({section: {title}}) => (
          <Text style={styles.listHeader}>{title}</Text>
        )}
      />
    </View>
  );
};

const SECTIONS = [
  {
    title: 'Predefined animations',
    data: [
      {title: 'Bounce', easing: Easing.bounce},
      {title: 'Ease', easing: Easing.ease},
      {title: 'Elastic', easing: Easing.elastic(4)},
    ],
  },
  {
    title: 'Standard functions',
    data: [
      {title: 'Linear', easing: Easing.linear},
      {title: 'Quad', easing: Easing.quad},
      {title: 'Cubic', easing: Easing.cubic},
    ],
  },
  {
    title: 'Additional functions',
    data: [
      {
        title: 'Bezier',
        easing: Easing.bezier(0, 2, 1, -1),
      },
      {title: 'Circle', easing: Easing.circle},
      {title: 'Sin', easing: Easing.sin},
      {title: 'Exp', easing: Easing.exp},
    ],
  },
  {
    title: 'Combinations',
    data: [
      {
        title: 'In + Bounce',
        easing: Easing.in(Easing.bounce),
      },
      {
        title: 'Out + Exp',
        easing: Easing.out(Easing.exp),
      },
      {
        title: 'InOut + Elastic',
        easing: Easing.inOut(Easing.elastic(1)),
      },
    ],
  },
];

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#20232a',
  },
  title: {
    marginTop: 10,
    textAlign: 'center',
    color: '#61dafb',
  },
  boxContainer: {
    height: 160,
    alignItems: 'center',
  },
  box: {
    marginTop: 32,
    borderRadius: 4,
    backgroundColor: '#61dafb',
  },
  list: {
    backgroundColor: '#fff',
  },
  listHeader: {
    paddingHorizontal: 8,
    paddingVertical: 4,
    backgroundColor: '#f4f4f4',
    color: '#999',
    fontSize: 12,
    textTransform: 'uppercase',
  },
  listRow: {
    padding: 8,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Easing%20Demo&ext=tsx
import React from 'react';
import {
  Animated,
  Easing,
  SectionList,
  StatusBar,
  StyleSheet,
  Text,
  TouchableOpacity,
  View,
} from 'react-native';
import type {EasingFunction} from 'react-native';

const App = () => {
  let opacity = new Animated.Value(0);

  const animate = (easing: EasingFunction) => {
    opacity.setValue(0);
    Animated.timing(opacity, {
      toValue: 1,
      duration: 1200,
      easing,
      useNativeDriver: true,
    }).start();
  };

  const size = opacity.interpolate({
    inputRange: [0, 1],
    outputRange: [0, 80],
  });

  const animatedStyles = [
    styles.box,
    {
      opacity,
      width: size,
      height: size,
    },
  ];

  return (
    <View style={styles.container}>
      <StatusBar hidden={true} />
      <Text style={styles.title}>Press rows below to preview the Easing!</Text>
      <View style={styles.boxContainer}>
        <Animated.View style={animatedStyles} />
      </View>
      <SectionList
        style={styles.list}
        sections={SECTIONS}
        keyExtractor={item => item.title}
        renderItem={({item}) => (
          <TouchableOpacity
            onPress={() => animate(item.easing)}
            style={styles.listRow}>
            <Text>{item.title}</Text>
          </TouchableOpacity>
        )}
        renderSectionHeader={({section: {title}}) => (
          <Text style={styles.listHeader}>{title}</Text>
        )}
      />
    </View>
  );
};

const SECTIONS = [
  {
    title: 'Predefined animations',
    data: [
      {title: 'Bounce', easing: Easing.bounce},
      {title: 'Ease', easing: Easing.ease},
      {title: 'Elastic', easing: Easing.elastic(4)},
    ],
  },
  {
    title: 'Standard functions',
    data: [
      {title: 'Linear', easing: Easing.linear},
      {title: 'Quad', easing: Easing.quad},
      {title: 'Cubic', easing: Easing.cubic},
    ],
  },
  {
    title: 'Additional functions',
    data: [
      {
        title: 'Bezier',
        easing: Easing.bezier(0, 2, 1, -1),
      },
      {title: 'Circle', easing: Easing.circle},
      {title: 'Sin', easing: Easing.sin},
      {title: 'Exp', easing: Easing.exp},
    ],
  },
  {
    title: 'Combinations',
    data: [
      {
        title: 'In + Bounce',
        easing: Easing.in(Easing.bounce),
      },
      {
        title: 'Out + Exp',
        easing: Easing.out(Easing.exp),
      },
      {
        title: 'InOut + Elastic',
        easing: Easing.inOut(Easing.elastic(1)),
      },
    ],
  },
];

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#20232a',
  },
  title: {
    marginTop: 10,
    textAlign: 'center',
    color: '#61dafb',
  },
  boxContainer: {
    height: 160,
    alignItems: 'center',
  },
  box: {
    marginTop: 32,
    borderRadius: 4,
    backgroundColor: '#61dafb',
  },
  list: {
    backgroundColor: '#fff',
  },
  listHeader: {
    paddingHorizontal: 8,
    paddingVertical: 4,
    backgroundColor: '#f4f4f4',
    color: '#999',
    fontSize: 12,
    textTransform: 'uppercase',
  },
  listRow: {
    padding: 8,
  },
});

export default App;
```

</TabItem>
</Tabs>

---

# 參考

## 方法

### `step0()`

```tsx
static step0(n: number);
```

階梯函數，對於任何正值的 `n` 都返回 1。

---

### `step1()`

```tsx
static step1(n: number);
```

階梯函數，當 `n` 大於或等於 1 時返回 1。

---

### `linear()`

```tsx
static linear(t: number);
```

線性函數，`f(t) = t`。位置與經過時間呈一對一關係。

http://cubic-bezier.com/#0,0,1,1

---

### `ease()`

```tsx
static ease(t: number);
```

基本慣性交互效果，類似物體緩慢加速至全速。

http://cubic-bezier.com/#.42,0,1,1

---

### `quad()`

```tsx
static quad(t: number);
```

二次函數，`f(t) = t * t`。位置等於經過時間的平方。

http://easings.net/#easeInQuad

---

### `cubic()`

```tsx
static cubic(t: number);
```

三次函數，`f(t) = t * t * t`。位置等於經過時間的立方。

http://easings.net/#easeInCubic

---

### `poly()`

```tsx
static poly(n: number);
```

冪次函數。位置等於經過時間的N次方。

n = 4: http://easings.net/#easeInQuart n = 5: http://easings.net/#easeInQuint

---

### `sin()`

```tsx
static sin(t: number);
```

正弦函數。

http://easings.net/#easeInSine

---

### `circle()`

```tsx
static circle(t: number);
```

圓形函數。

http://easings.net/#easeInCirc

---

### `exp()`

```tsx
static exp(t: number);
```

指數函數。

http://easings.net/#easeInExpo

---

### `elastic()`

```tsx
static elastic(bounciness: number);
```

基本彈性互動效果，類似彈簧來回振盪。

預設彈性係數為1，會稍微過衝一次。係數為0時完全不過衝，係數N>1時會過衝約N次。

http://easings.net/#easeInElastic

---

### `back()`

```tsx
static back(s)
```

與`Animated.parallel()`配合使用，可創建動畫開始時物件略微向後退的基本效果。

---

### `bounce()`

```tsx
static bounce(t: number);
```

提供基本彈跳效果。

http://easings.net/#easeInBounce

---

### `bezier()`

```tsx
static bezier(x1: number, y1: number, x2: number, y2: number);
```

提供三次貝茲曲線，等效於CSS Transitions的`transition-timing-function`。

可視化三次貝茲曲線的實用工具：http://cubic-bezier.com/

---

### `in()`

```tsx
static in(easing: number);
```

正向執行緩動函數。

---

### `out()`

```tsx
static out(easing: number);
```

反向執行緩動函數。

---

### `inOut()`

```tsx
static inOut(easing: number);
```

使任何緩動函數對稱執行。緩動函數會在前半段時間正向運行，後半段時間反向運行。