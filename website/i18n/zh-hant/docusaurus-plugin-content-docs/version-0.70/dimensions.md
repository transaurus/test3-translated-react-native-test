---
id: dimensions
title: Dimensions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

> 對於 React 元件來說，[`useWindowDimensions`](usewindowdimensions) 是推薦使用的 API。與 `Dimensions` 不同，它會隨著視窗尺寸的變化而更新。這與 React 的設計模式完美契合。

```jsx
import {Dimensions} from 'react-native';
```

您可以使用以下程式碼取得應用程式視窗的寬度和高度：

```jsx
const windowWidth = Dimensions.get('window').width;
const windowHeight = Dimensions.get('window').height;
```

> 雖然尺寸資訊可以立即取得，但它們可能會發生變化（例如由於裝置旋轉、折疊式裝置等），因此任何依賴這些常數的渲染邏輯或樣式，都應該在每次渲染時呼叫此函數，而不是快取值（例如使用行內樣式而非在 `StyleSheet` 中設定值）。

如果您針對的是折疊式裝置或可以改變螢幕尺寸或應用視窗大小的裝置，您可以使用 Dimensions 模組中的事件監聽器，如下例所示。

## 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Dimensions
import React, { useState, useEffect } from "react";
import { View, StyleSheet, Text, Dimensions } from "react-native";

const window = Dimensions.get("window");
const screen = Dimensions.get("screen");

const App = () => {
  const [dimensions, setDimensions] = useState({ window, screen });

  useEffect(() => {
    const subscription = Dimensions.addEventListener(
      "change",
      ({ window, screen }) => {
        setDimensions({ window, screen });
      }
    );
    return () => subscription?.remove();
  });

  return (
    <View style={styles.container}>
      <Text style={styles.header}>Window Dimensions</Text>
      {Object.entries(dimensions.window).map(([key, value]) => (
        <Text>{key} - {value}</Text>
      ))}
      <Text style={styles.header}>Screen Dimensions</Text>
      {Object.entries(dimensions.screen).map(([key, value]) => (
        <Text>{key} - {value}</Text>
      ))}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center"
  },
  header: {
    fontSize: 16,
    marginVertical: 10
  }
});

export default App;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Dimensions
import React, { Component } from "react";
import { View, StyleSheet, Text, Dimensions } from "react-native";

const window = Dimensions.get("window");
const screen = Dimensions.get("screen");

class App extends Component {
  state = {
    dimensions: {
      window,
      screen
    }
  };

  onChange = ({ window, screen }) => {
    this.setState({ dimensions: { window, screen } });
  };

  componentDidMount() {
    this.dimensionsSubscription = Dimensions.addEventListener("change", this.onChange);
  }

  componentWillUnmount() {
    this.dimensionsSubscription?.remove();
  }

  render() {
    const { dimensions: { window, screen } } = this.state;

    return (
      <View style={styles.container}>
        <Text style={styles.header}>Window Dimensions</Text>
        {Object.entries(window).map(([key, value]) => (
          <Text>{key} - {value}</Text>
        ))}
        <Text style={styles.header}>Screen Dimensions</Text>
        {Object.entries(screen).map(([key, value]) => (
          <Text>{key} - {value}</Text>
        ))}
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center"
  },
  header: {
    fontSize: 16,
    marginVertical: 10
  }
});

export default App;
```

</TabItem>
</Tabs>

# 參考

## 方法

### `addEventListener()`

```jsx
static addEventListener(type, handler)
```

新增事件處理器。支援的事件：

- `change`：當 `Dimensions` 物件中的屬性發生變化時觸發。事件處理器的參數是一個 [`DimensionsValue`](#dimensionsvalue) 類型的物件。

---

### `get()`

```jsx
static get(dim)
```

初始尺寸在 `runApplication` 被呼叫之前就已設定，因此它們應該在其他 require 執行之前就可取得，但可能會在之後更新。

範例：`const {height, width} = Dimensions.get('window');`

**參數：**

| Name                                                               | Type   | Description                                                                       |
| ------------------------------------------------------------------ | ------ | --------------------------------------------------------------------------------- |
| dim <div className="label basic required two-lines">Required</div> | string | Name of dimension as defined when calling `set`. Returns value for the dimension. |

> 在 Android 上，`window` 的尺寸將排除被 `狀態列`（如果不透明）和 `底部導覽列` 使用的空間。

---

### `removeEventListener()`

```jsx
static removeEventListener(type, handler)
```

> **已棄用。** 請使用 [`addEventListener()`](#addeventlistener) 返回的事件訂閱上的 `remove()` 方法。

---

### `set()`

```jsx
static set(dims)
```

此方法應僅由原生程式碼透過發送 `didUpdateDimensions` 事件來呼叫。

**參數：**

| Name                                                      | Type   | Description                               |
| --------------------------------------------------------- | ------ | ----------------------------------------- |
| dims <div className="label basic required">Required</div> | object | String-keyed object of dimensions to set. |

---

## 類型定義

### DimensionsValue

**屬性：**

| Name   | Type                                        | Description                             |
| ------ | ------------------------------------------- | --------------------------------------- |
| window | [DisplayMetrics](dimensions#displaymetrics) | Size of the visible Application window. |
| screen | [DisplayMetrics](dimensions#displaymetrics) | Size of the device's screen.            |

### DisplayMetrics

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