---
id: layoutanimation
title: LayoutAnimation
---

當下一次佈局發生時，自動將視圖動畫過渡到新位置。

常見的使用方式是在函數式組件中更新狀態鉤子前調用此API，或在類組件中調用`setState`前使用。

請注意，在**Android**上要使此功能正常工作，需通過`UIManager`設置以下標誌：

```js
if (Platform.OS === 'android') {
  if (UIManager.setLayoutAnimationEnabledExperimental) {
    UIManager.setLayoutAnimationEnabledExperimental(true);
  }
}
```

## 範例

```SnackPlayer name=LayoutAnimation&supportedPlatforms=android,ios
import React, {useState} from 'react';
import {
  LayoutAnimation,
  Platform,
  StyleSheet,
  Text,
  TouchableOpacity,
  UIManager,
  View,
} from 'react-native';

if (
  Platform.OS === 'android' &&
  UIManager.setLayoutAnimationEnabledExperimental
) {
  UIManager.setLayoutAnimationEnabledExperimental(true);
}
const App = () => {
  const [expanded, setExpanded] = useState(false);

  return (
    <View style={style.container}>
      <TouchableOpacity
        onPress={() => {
          LayoutAnimation.configureNext(LayoutAnimation.Presets.spring);
          setExpanded(!expanded);
        }}>
        <Text>Press me to {expanded ? 'collapse' : 'expand'}!</Text>
      </TouchableOpacity>
      {expanded && (
        <View style={style.tile}>
          <Text>I disappear sometimes!</Text>
        </View>
      )}
    </View>
  );
};

const style = StyleSheet.create({
  tile: {
    backgroundColor: 'lightgrey',
    borderWidth: 0.5,
    borderColor: '#d6d7da',
  },
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    overflow: 'hidden',
  },
});

export default App;
```

---

# 參考文獻

## 方法

### `configureNext()`

```tsx
static configureNext(
  config: LayoutAnimationConfig,
  onAnimationDidEnd?: () => void,
  onAnimationDidFail?: () => void,
);
```

安排在下次佈局時執行動畫。

#### 參數：

| Name               | Type     | Required | Description                         |
| ------------------ | -------- | -------- | ----------------------------------- |
| config             | object   | Yes      | See config description below.       |
| onAnimationDidEnd  | function | No       | Called when the animation finished. |
| onAnimationDidFail | function | No       | Called when the animation failed.   |

`config`參數是一個包含以下鍵的對象。[`create`](layoutanimation.md#create)會返回一個有效的`config`對象，且所有[`Presets`](layoutanimation.md#presets)對象也可作為`config`傳入。

- `duration` 以毫秒為單位
- `create`，用於動畫新視圖的可選配置
- `update`，用於動畫已更新視圖的可選配置
- `delete`，用於動畫移除視圖的可選配置

傳遞給`create`、`update`或`delete`的配置包含以下鍵：

- `type`，使用的[動畫類型](layoutanimation.md#types)
- `property`，要動畫的[佈局屬性](layoutanimation.md#properties)（可選，但建議用於`create`和`delete`）
- `springDamping`（數字，可選且僅用於`type: Type.spring`）
- `initialVelocity`（數字，可選）
- `delay`（數字，可選）
- `duration`（數字，可選）

---

### `create()`

```tsx
static create(duration, type, creationProp)
```

輔助函數，創建一個對象（包含`create`、`update`和`delete`字段）以傳入[`configureNext`](layoutanimation.md#configurenext)。`type`參數是[動畫類型](layoutanimation.md#types)，`creationProp`參數是[佈局屬性](layoutanimation.md#properties)。

**範例：**

```SnackPlayer name=LayoutAnimation&supportedPlatforms=android,ios
import React, {useState} from 'react';
import {
  View,
  Platform,
  UIManager,
  LayoutAnimation,
  StyleSheet,
  Button,
} from 'react-native';

if (
  Platform.OS === 'android' &&
  UIManager.setLayoutAnimationEnabledExperimental
) {
  UIManager.setLayoutAnimationEnabledExperimental(true);
}

const App = () => {
  const [boxPosition, setBoxPosition] = useState('left');

  const toggleBox = () => {
    LayoutAnimation.configureNext({
      duration: 500,
      create: {type: 'linear', property: 'opacity'},
      update: {type: 'spring', springDamping: 0.4},
      delete: {type: 'linear', property: 'opacity'},
    });
    setBoxPosition(boxPosition === 'left' ? 'right' : 'left');
  };

  return (
    <View style={styles.container}>
      <View style={styles.buttonContainer}>
        <Button title="Toggle Layout" onPress={toggleBox} />
      </View>
      <View
        style={[styles.box, boxPosition === 'left' ? null : styles.moveRight]}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'flex-start',
    justifyContent: 'center',
  },
  box: {
    height: 100,
    width: 100,
    borderRadius: 5,
    margin: 8,
    backgroundColor: 'blue',
  },
  moveRight: {
    alignSelf: 'flex-end',
    height: 200,
    width: 200,
  },
  buttonContainer: {
    alignSelf: 'center',
  },
});

export default App;
```

## 屬性

### 類型

動畫類型的枚舉，用於[`create`](layoutanimation.md#create)方法，或[`configureNext`](layoutanimation.md#configurenext)的`create`/`update`/`delete`配置中。（使用範例：`LayoutAnimation.Types.easeIn`）

| Types         |
| ------------- |
| spring        |
| linear        |
| easeInEaseOut |
| easeIn        |
| easeOut       |
| keyboard      |

---

### 屬性

佈局屬性的枚舉，用於動畫，可在[`create`](layoutanimation.md#create)方法或[`configureNext`](layoutanimation.md#configurenext)的`create`/`update`/`delete`配置中使用。（使用範例：`LayoutAnimation.Properties.opacity`）

| Properties |
| ---------- |
| opacity    |
| scaleX     |
| scaleY     |
| scaleXY    |

---

### 預設配置

一組預定義的動畫配置，用於傳入[`configureNext`](layoutanimation.md#configurenext)。

| Presets       | Value                                                                                                                                                          |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| easeInEaseOut | `create(300, 'easeInEaseOut', 'opacity')`                                                                                                                      |
| linear        | `create(500, 'linear', 'opacity')`                                                                                                                             |
| spring        | `{duration: 700, create: {type: 'linear', property: 'opacity'}, update: {type: 'spring', springDamping: 0.4}, delete: {type: 'linear', property: 'opacity'} }` |

---

### `easeInEaseOut`

使用`Presets.easeInEaseOut`調用`configureNext()`。

---

### `linear`

使用`Presets.linear`調用`configureNext()`。

---

### `spring`

使用 `Presets.spring` 呼叫 `configureNext()`。

**範例:**

```SnackPlayer name=LayoutAnimation&supportedPlatforms=android,ios
import React, {useState} from 'react';
import {
  View,
  Platform,
  UIManager,
  LayoutAnimation,
  StyleSheet,
  Button,
} from 'react-native';

if (
  Platform.OS === 'android' &&
  UIManager.setLayoutAnimationEnabledExperimental
) {
  UIManager.setLayoutAnimationEnabledExperimental(true);
}

const App = () => {
  const [firstBoxPosition, setFirstBoxPosition] = useState('left');
  const [secondBoxPosition, setSecondBoxPosition] = useState('left');
  const [thirdBoxPosition, setThirdBoxPosition] = useState('left');

  const toggleFirstBox = () => {
    LayoutAnimation.configureNext(LayoutAnimation.Presets.easeInEaseOut);
    setFirstBoxPosition(firstBoxPosition === 'left' ? 'right' : 'left');
  };

  const toggleSecondBox = () => {
    LayoutAnimation.configureNext(LayoutAnimation.Presets.linear);
    setSecondBoxPosition(secondBoxPosition === 'left' ? 'right' : 'left');
  };

  const toggleThirdBox = () => {
    LayoutAnimation.configureNext(LayoutAnimation.Presets.spring);
    setThirdBoxPosition(thirdBoxPosition === 'left' ? 'right' : 'left');
  };

  return (
    <View style={styles.container}>
      <View style={styles.buttonContainer}>
        <Button title="EaseInEaseOut" onPress={toggleFirstBox} />
      </View>
      <View
        style={[
          styles.box,
          firstBoxPosition === 'left' ? null : styles.moveRight,
        ]}
      />
      <View style={styles.buttonContainer}>
        <Button title="Linear" onPress={toggleSecondBox} />
      </View>
      <View
        style={[
          styles.box,
          secondBoxPosition === 'left' ? null : styles.moveRight,
        ]}
      />
      <View style={styles.buttonContainer}>
        <Button title="Spring" onPress={toggleThirdBox} />
      </View>
      <View
        style={[
          styles.box,
          thirdBoxPosition === 'left' ? null : styles.moveRight,
        ]}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'flex-start',
    justifyContent: 'center',
  },
  box: {
    height: 100,
    width: 100,
    borderRadius: 5,
    margin: 8,
    backgroundColor: 'blue',
  },
  moveRight: {
    alignSelf: 'flex-end',
  },
  buttonContainer: {
    alignSelf: 'center',
  },
});

export default App;
```