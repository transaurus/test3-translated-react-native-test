---
id: dimensions
title: Dimensions
---

> 對於 React 元件來說，[`useWindowDimensions`](usewindowdimensions) 是更推薦使用的 API。與 `Dimensions` 不同，它會隨著視窗尺寸的變化而更新。這與 React 的設計模式非常契合。

```tsx
import {Dimensions} from 'react-native';
```

你可以使用以下程式碼取得應用程式視窗的寬度和高度：

```tsx
const windowWidth = Dimensions.get('window').width;
const windowHeight = Dimensions.get('window').height;
```

> 雖然尺寸資訊可以立即取得，但它們可能會發生變化（例如由於裝置旋轉、摺疊裝置等），因此任何依賴這些常數的渲染邏輯或樣式都應該在每次渲染時呼叫此函數，而不是快取值（例如使用內聯樣式而非在 `StyleSheet` 中設定值）。

如果你的目標是摺疊裝置或可以改變螢幕尺寸或應用視窗大小的裝置，你可以使用 Dimensions 模組中提供的事件監聽器，如下例所示。

## 範例

```SnackPlayer name=Dimensions%20Example
import React, {useState, useEffect} from 'react';
import {StyleSheet, Text, Dimensions} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

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
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
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
      </SafeAreaView>
    </SafeAreaProvider>
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

# 參考

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

新增一個事件處理器。支援的事件：

- `change`：當 `Dimensions` 物件中的屬性發生變化時觸發。事件處理器的參數是一個 [`DimensionsValue`](#dimensionsvalue) 類型的物件。

---

### `get()`

```tsx
static get(dim: 'window' | 'screen'): ScaledSize;
```

初始尺寸在 `runApplication` 被呼叫之前就已設定，因此它們應該在其他任何 require 執行之前就可取得，但可能會在之後更新。

範例：`const {height, width} = Dimensions.get('window');`

**參數：**

| Name                                                               | Type   | Description                                                                       |
| ------------------------------------------------------------------ | ------ | --------------------------------------------------------------------------------- |
| dim <div className="label basic required two-lines">Required</div> | string | Name of dimension as defined when calling `set`. Returns value for the dimension. |

> 在 Android 上，`window` 的尺寸將排除被 `狀態列`（如果不透明）和 `底部導覽列` 使用的空間

---

## 類型定義

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