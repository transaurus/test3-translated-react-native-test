---
id: dimensions
title: Dimensions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

> [`useWindowDimensions`](usewindowdimensions) 是 React 元件的推薦 API。與 `Dimensions` 不同，它會隨著視窗尺寸變化自動更新，更符合 React 的設計模式。

```tsx
import {Dimensions} from 'react-native';
```

可透過以下程式碼取得應用程式視窗的寬度和高度：

```tsx
const windowWidth = Dimensions.get('window').width;
const windowHeight = Dimensions.get('window').height;
```

> 雖然尺寸數值能立即取得，但可能因裝置旋轉、摺疊裝置等因素變動。任何依賴這些常數的渲染邏輯或樣式，應在每次渲染時呼叫此函數（例如使用行內樣式），而非快取值（例如寫入 `StyleSheet`）。

若目標裝置為可摺疊裝置或螢幕/視窗尺寸可變的裝置，可使用 Dimensions 模組中的事件監聽器，如下例所示。

## 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Dimensions
import React, {useState, useEffect} from 'react';
import {View, StyleSheet, Text, Dimensions} from 'react-native';

const windowDimensions = Dimensions.get('window');
const screenDimensions = Dimensions.get('screen');

const App = () => {
  const [dimensions, setDimensions] = useState({
    window: windowDimensions,
    screen: screenDimensions,
  });

  useEffect(() => {
    const subscription = Dimensions.addEventListener(
      'change',
      ({window, screen}) => {
        setDimensions({window, screen});
      },
    );
    return () => subscription?.remove();
  });

  return (
    <View style={styles.container}>
      <Text style={styles.header}>Window Dimensions</Text>
      {Object.entries(dimensions.window).map(([key, value]) => (
        <Text>
          {key} - {value}
        </Text>
      ))}
      <Text style={styles.header}>Screen Dimensions</Text>
      {Object.entries(dimensions.screen).map(([key, value]) => (
        <Text>
          {key} - {value}
        </Text>
      ))}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  header: {
    fontSize: 16,
    marginVertical: 10,
  },
});

export default App;
```

</TabItem>
<TabItem value="classical">

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Dimensions&ext=js
import React, {Component} from 'react';
import {View, StyleSheet, Text, Dimensions} from 'react-native';

const windowDimensions = Dimensions.get('window');
const screenDimensions = Dimensions.get('screen');

class App extends Component {
  state = {
    dimensions: {
      window: windowDimensions,
      screen: screenDimensions,
    },
  };

  onChange = ({window, screen}) => {
    this.setState({dimensions: {window, screen}});
  };

  componentDidMount() {
    this.dimensionsSubscription = Dimensions.addEventListener(
      'change',
      this.onChange,
    );
  }

  componentWillUnmount() {
    this.dimensionsSubscription?.remove();
  }

  render() {
    const {
      dimensions: {window, screen},
    } = this.state;

    return (
      <View style={styles.container}>
        <Text style={styles.header}>Window Dimensions</Text>
        {Object.entries(window).map(([key, value]) => (
          <Text>
            {key} - {value}
          </Text>
        ))}
        <Text style={styles.header}>Screen Dimensions</Text>
        {Object.entries(screen).map(([key, value]) => (
          <Text>
            {key} - {value}
          </Text>
        ))}
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  header: {
    fontSize: 16,
    marginVertical: 10,
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Dimensions&ext=tsx
import React, {Component} from 'react';
import {View, StyleSheet, Text, Dimensions} from 'react-native';
import type {EmitterSubscription, ScaledSize} from 'react-native';

const windowDimensions = Dimensions.get('window');
const screenDimensions = Dimensions.get('screen');

class App extends Component {
  dimensionsSubscription?: EmitterSubscription;
  state = {
    dimensions: {
      window: windowDimensions,
      screen: screenDimensions,
    },
  };

  onChange = ({window, screen}: {window: ScaledSize; screen: ScaledSize}) => {
    this.setState({dimensions: {window, screen}});
  };

  componentDidMount() {
    this.dimensionsSubscription = Dimensions.addEventListener(
      'change',
      this.onChange,
    );
  }

  componentWillUnmount() {
    this.dimensionsSubscription?.remove();
  }

  render() {
    const {
      dimensions: {window, screen},
    } = this.state;

    return (
      <View style={styles.container}>
        <Text style={styles.header}>Window Dimensions</Text>
        {Object.entries(window).map(([key, value]) => (
          <Text>
            {key} - {value}
          </Text>
        ))}
        <Text style={styles.header}>Screen Dimensions</Text>
        {Object.entries(screen).map(([key, value]) => (
          <Text>
            {key} - {value}
          </Text>
        ))}
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  header: {
    fontSize: 16,
    marginVertical: 10,
  },
});

export default App;
```

</TabItem>
</Tabs>

</TabItem>
</Tabs>

# 參考文件

## 方法

### `addEventListener()`

```tsx
static addEventListener(
  type: 'change',
  handler: ({
    window,
    screen,
  }: DimensionsValue) => void,
): EmitterSubscription;
```

新增事件處理器。支援的事件類型：

- `change`: 當 `Dimensions` 物件中的屬性變更時觸發。事件處理器的參數為 [`DimensionsValue`](#dimensionsvalue) 型別物件。

---

### `get()`

```tsx
static get(dim: 'window' | 'screen'): ScaledSize;
```

初始尺寸在 `runApplication` 呼叫前就已設定，因此應在其他 require 執行前即可取得，但後續可能更新。

範例：`const {height, width} = Dimensions.get('window');`

**參數：**

| Name                                                               | Type   | Description                                                                       |
| ------------------------------------------------------------------ | ------ | --------------------------------------------------------------------------------- |
| dim <div className="label basic required two-lines">Required</div> | string | Name of dimension as defined when calling `set`. Returns value for the dimension. |

> 在 Android 上，`window` 維度會排除狀態列（非透明時）和底部導覽列佔用的空間

---

## 型別定義

### DimensionsValue

**屬性：**

| Name   | Type                                | Description                             |
| ------ | ----------------------------------- | --------------------------------------- |
| window | [ScaledSize](dimensions#scaledsize) | Size of the visible Application window. |
| screen | [ScaledSize](dimensions#scaledsize) | Size of the device's screen.            |

### ScaledSize

| Type   |
| ------ |
| object |

**屬性：**

| Name      | Type   |
| --------- | ------ |
| width     | number |
| height    | number |
| scale     | number |
| fontScale | number |