---
id: touchablenativefeedback
title: TouchableNativeFeedback
---

> 若您需要更全面且具未來兼容性的觸控輸入處理方案，請參閱 [Pressable](pressable.md) API。

用於讓視圖正確響應觸控操作的封裝元件（僅限 Android）。在 Android 平台上，此元件會使用原生狀態繪製物件來顯示觸控反饋效果。

目前僅支援單一 View 實例作為子節點，其實作方式是將該 View 替換為另一個設定了附加屬性的 RCTView 節點實例。

可透過 `background` 屬性自訂原生反饋觸控元件的背景繪製物件。

## 範例

```SnackPlayer name=TouchableNativeFeedback%20Android%20Component%20Example&supportedPlatforms=android
import React, {useState} from 'react';
import {Text, View, StyleSheet, TouchableNativeFeedback} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [rippleColor, setRippleColor] = useState(randomHexColor());
  const [rippleOverflow, setRippleOverflow] = useState(false);
  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <TouchableNativeFeedback
          onPress={() => {
            setRippleColor(randomHexColor());
            setRippleOverflow(!rippleOverflow);
          }}
          background={TouchableNativeFeedback.Ripple(
            rippleColor,
            rippleOverflow,
          )}>
          <View style={styles.touchable}>
            <Text style={styles.text}>TouchableNativeFeedback</Text>
          </View>
        </TouchableNativeFeedback>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const randomHexColor = () => {
  return '#000000'.replace(/0/g, function () {
    return Math.round(Math.random() * 16).toString(16);
  });
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: 'black',
    paddingHorizontal: 20,
  },
  touchable: {
    flex: 0.33,
    justifyContent: 'center',
    backgroundColor: '#eeeeee',
    borderColor: 'black',
    borderWidth: 1,
  },
  text: {
    alignSelf: 'center',
  },
});

export default App;
```

---

# 參考資料

## 屬性

### [TouchableWithoutFeedback 屬性](touchablewithoutfeedback.md#props)

繼承自 [TouchableWithoutFeedback 屬性](touchablewithoutfeedback.md#props)。

---

### `background`

決定用於顯示反饋效果的背景繪製物件類型。接收一個包含 `type` 屬性的物件，並根據 `type` 攜帶額外資料。建議使用靜態方法來生成此字典。

| Type               |
| ------------------ |
| backgroundPropType |

---

### `useForeground`

設為 true 可將漣漪效果添加到視圖的前景而非背景。當子視圖本身具有背景，或您正在顯示圖片等內容且不希望漣漪效果被遮蓋時，此屬性特別有用。

請先檢查 TouchableNativeFeedback.canUseNativeForeground()，因為此功能僅在 Android 6.0 及以上版本可用。若在舊版系統嘗試使用，將會收到警告並自動回退至背景模式。

| Type |
| ---- |
| bool |

---

### `hasTVPreferredFocus` <div class="label android">Android</div>

電視首選聚焦（參閱 View 元件的文件說明）。

| Type |
| ---- |
| bool |

---

### `nextFocusDown` <div class="label android">Android</div>

電視向下聚焦目標（參閱 View 元件的文件說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div>

電視向前聚焦目標（參閱 View 元件的文件說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div>

電視向左聚焦目標（參閱 View 元件的文件說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div>

電視向右聚焦目標（參閱 View 元件的文件說明）。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div>

電視向上聚焦目標（參閱 View 元件的文件說明）。

| Type   |
| ------ |
| number |

## 方法

### `SelectableBackground()`

```tsx
static SelectableBackground(
  rippleRadius: number | null,
): ThemeAttributeBackgroundPropType;
```

建立一個代表 Android 主題預設可選元素背景的物件（`?android:attr/selectableItemBackground`）。`rippleRadius` 參數用於控制漣漪效果的半徑。

---

### `SelectableBackgroundBorderless()`

```tsx
static SelectableBackgroundBorderless(
  rippleRadius: number | null,
): ThemeAttributeBackgroundPropType;
```

建立一個物件，代表 Android 主題中無邊框可選元素的預設背景 (`?android:attr/selectableItemBackgroundBorderless`)。需 Android API 等級 21+ 以上支援。`rippleRadius` 參數控制漣漪效果的半徑。

---

### `Ripple()`

```tsx
static Ripple(
  color: ColorValue,
  borderless: boolean,
  rippleRadius?: number | null,
): RippleBackgroundPropType;
```

建立一個物件，代表以指定顏色（字串格式）繪製的漣漪效果。若 `borderless` 屬性設為 true，漣漪效果會渲染在視圖邊界之外（參見原生 actionbar 按鈕的行為範例）。此背景類型需 Android API 等級 21+ 以上支援。

**參數：**

| Name         | Type    | Required | Description                                 |
| ------------ | ------- | -------- | ------------------------------------------- |
| color        | string  | Yes      | The ripple color                            |
| borderless   | boolean | Yes      | If the ripple can render outside its bounds |
| rippleRadius | ?number | No       | controls the radius of the ripple effect    |

---

### `canUseNativeForeground()`

```tsx
static canUseNativeForeground(): boolean;
```