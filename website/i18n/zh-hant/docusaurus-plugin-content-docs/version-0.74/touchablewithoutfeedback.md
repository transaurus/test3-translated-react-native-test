---
id: touchablewithoutfeedback
title: TouchableWithoutFeedback
---

> If you're looking for a more extensive and future-proof way to handle touch-based input, check out the [Pressable](pressable.md) API.

Do not use unless you have a very good reason. All elements that respond to press should have a visual feedback when touched.

`TouchableWithoutFeedback` supports only one child. If you wish to have several child components, wrap them in a View. Importantly, `TouchableWithoutFeedback` works by cloning its child and applying responder props to it. It is therefore required that any intermediary components pass through those props to the underlying React Native component.

## Usage Pattern

```tsx
function MyComponent(props: MyComponentProps) {
  return (
    <View {...props} style={{flex: 1, backgroundColor: '#fff'}}>
      <Text>My Component</Text>
    </View>
  );
}

<TouchableWithoutFeedback onPress={() => alert('Pressed!')}>
  <MyComponent />
</TouchableWithoutFeedback>;
```

## Example

```SnackPlayer name=TouchableWithoutFeedback
import React, {useState} from 'react';
import {StyleSheet, TouchableWithoutFeedback, Text, View} from 'react-native';

const TouchableWithoutFeedbackExample = () => {
  const [count, setCount] = useState(0);

  const onPress = () => {
    setCount(count + 1);
  };

  return (
    <View style={styles.container}>
      <View style={styles.countContainer}>
        <Text style={styles.countText}>Count: {count}</Text>
      </View>
      <TouchableWithoutFeedback onPress={onPress}>
        <View style={styles.button}>
          <Text>Touch Here</Text>
        </View>
      </TouchableWithoutFeedback>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    paddingHorizontal: 10,
  },
  button: {
    alignItems: 'center',
    backgroundColor: '#DDDDDD',
    padding: 10,
  },
  countContainer: {
    alignItems: 'center',
    padding: 10,
  },
  countText: {
    color: '#FF00FF',
  },
});

export default TouchableWithoutFeedbackExample;
```

---

# Reference

## Props

### `accessibilityIgnoresInvertColors`

| Type    |
| ------- |
| Boolean |

---

### `accessible`

When `true`, indicates that the view is an accessibility element. By default, all the touchable elements are accessible.

| Type |
| ---- |
| bool |

---

### `accessibilityLabel`

Overrides the text that's read by the screen reader when the user interacts with the element. By default, the label is constructed by traversing all the children and accumulating all the `Text` nodes separated by space.

| Type   |
| ------ |
| string |

---

### `accessibilityLanguage` <div class="label ios">iOS</div>

A value indicating which language should be used by the screen reader when the user interacts with the element. It should follow the [BCP 47 specification](https://www.rfc-editor.org/info/bcp47).

See the [iOS `accessibilityLanguage` doc](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage) for more information.

| Type   |
| ------ |
| string |

---

### `accessibilityHint`

An accessibility hint helps users understand what will happen when they perform an action on the accessibility element when that result is not clear from the accessibility label.

| Type   |
| ------ |
| string |

---

### `accessibilityRole`

`accessibilityRole` communicates the purpose of a component to the user of an assistive technology.

`accessibilityRole` can be one of the following:

- `'none'` - Used when the element has no role.
- `'button'` - Used when the element should be treated as a button.
- `'link'` - Used when the element should be treated as a link.
- `'search'` - Used when the text field element should also be treated as a search field.
- `'image'` - Used when the element should be treated as an image. Can be combined with button or link, for example.
- `'keyboardkey'` - Used when the element acts as a keyboard key.
- `'text'` - Used when the element should be treated as static text that cannot change.
- `'adjustable'` - Used when an element can be "adjusted" (e.g. a slider).
- `'imagebutton'` - Used when the element should be treated as a button and is also an image.
- `'header'` - Used when an element acts as a header for a content section (e.g. the title of a navigation bar).
- `'summary'` - Used when an element can be used to provide a quick summary of current conditions in the app when the app first launches.
- `'alert'` - Used when an element contains important text to be presented to the user.
- `'checkbox'` - Used when an element represents a checkbox which can be checked, unchecked, or have mixed checked state.
- `'combobox'` - Used when an element represents a combo box, which allows the user to select among several choices.
- `'menu'` - Used when the component is a menu of choices.
- `'menubar'` - Used when a component is a container of multiple menus.
- `'menuitem'` - Used to represent an item within a menu.
- `'progressbar'` - Used to represent a component which indicates progress of a task.
- `'radio'` - Used to represent a radio button.
- `'radiogroup'` - Used to represent a group of radio buttons.
- `'scrollbar'` - Used to represent a scroll bar.
- `'spinbutton'` - Used to represent a button which opens a list of choices.
- `'switch'` - Used to represent a switch which can be turned on and off.
- `'tab'` - Used to represent a tab.
- `'tablist'` - Used to represent a list of tabs.
- `'timer'` - Used to represent a timer.
- `'toolbar'` - Used to represent a tool bar (a container of action buttons or components).

| Type   |
| ------ |
| string |

---

### `accessibilityState`

Describes the current state of a component to the user of an assistive technology.

See the [Accessibility guide](accessibility.md#accessibilitystate-ios-android) for more information.

| Type                                                                                             |
| ------------------------------------------------------------------------------------------------ |
| object: `{disabled: bool, selected: bool, checked: bool or 'mixed', busy: bool, expanded: bool}` |

---

### `accessibilityActions`

Accessibility actions allow an assistive technology to programmatically invoke the actions of a component. The `accessibilityActions` property should contain a list of action objects. Each action object should contain the field name and label.

See the [Accessibility guide](accessibility.md#accessibility-actions) for more information.

| Type  |
| ----- |
| array |

---

### `aria-busy`

Indicates an element is being modified and that assistive technologies may want to wait until the changes are complete before informing the user about the update.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-checked`

Indicates the state of a checkable element. This field can either take a boolean or the "mixed" string to represent mixed checkboxes.

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

---

### `aria-disabled`

Indicates that the element is perceivable but disabled, so it is not editable or otherwise operable.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-expanded`

Indicates whether an expandable element is currently expanded or collapsed.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-hidden`

表示此無障礙元素內包含的子元素是否隱藏。

例如，在包含兄弟視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-label`

定義一個字串值，用於標記互動式元素。

| Type   |
| ------ |
| string |

---

### `aria-live` <div class="label android">Android</div>

表示元素將會更新，並描述用戶代理、輔助技術和用戶可以從即時區域獲得的更新類型。

- **off** 無障礙服務不應宣佈此視圖的變更。
- **polite** 無障礙服務應宣佈此視圖的變更。
- **assertive** 無障礙服務應中斷正在進行的語音，立即宣佈此視圖的變更。

| Type                                     | Default |
| ---------------------------------------- | ------- |
| enum(`'assertive'`, `'off'`, `'polite'`) | `'off'` |

---

### `aria-modal` <div class="label ios">iOS</div>

布林值，表示 VoiceOver 是否應忽略接收者兄弟視圖中的元素。優先於 [`accessibilityViewIsModal`](#accessibilityviewismodal-ios) 屬性。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-selected`

表示可選元素當前是否被選中。

| Type    |
| ------- |
| boolean |

### `onAccessibilityAction`

當用戶執行無障礙操作時調用。此函數的唯一參數是一個包含要執行操作名稱的事件。

詳見[無障礙指南](accessibility.md#accessibility-actions)以獲取更多資訊。

| Type     |
| -------- |
| function |

---

### `accessibilityValue`

表示組件的當前值。可以是組件值的文字描述，或是基於範圍的組件（如滑塊和進度條）的範圍資訊（最小值、當前值和最大值）。

詳見[無障礙指南](accessibility.md#accessibilityvalue-ios-android)以獲取更多資訊。

| Type                                                            |
| --------------------------------------------------------------- |
| object: `{min: number, max: number, now: number, text: string}` |

---

### `aria-valuemax`

表示基於範圍的組件（如滑塊和進度條）的最大值。優先於 `accessibilityValue` 屬性中的 `max` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuemin`

表示基於範圍的組件（如滑塊和進度條）的最小值。優先於 `accessibilityValue` 屬性中的 `min` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuenow`

表示基於範圍的組件（如滑塊和進度條）的當前值。優先於 `accessibilityValue` 屬性中的 `now` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuetext`

表示組件的文字描述。優先於 `accessibilityValue` 屬性中的 `text` 值。

| Type   |
| ------ |
| string |

---

### `delayLongPress`

從 `onPressIn` 開始到呼叫 `onLongPress` 的持續時間（以毫秒為單位）。

| Type   |
| ------ |
| number |

---

### `delayPressIn`

從觸碰開始到呼叫 `onPressIn` 的持續時間（以毫秒為單位）。

| Type   |
| ------ |
| number |

---

### `delayPressOut`

從觸碰釋放到呼叫 `onPressOut` 的持續時間（以毫秒為單位）。

| Type   |
| ------ |
| number |

---

### `disabled`

若為 true，則停用此元件的所有互動。

| Type |
| ---- |
| bool |

---

### `hitSlop`

定義觸碰可以從按鈕多遠的距離開始。當移出按鈕時，此值會加到 `pressRetentionOffset` 上。

> 觸碰區域永遠不會超出父視圖邊界，且如果觸碰同時命中兩個重疊的視圖，兄弟視圖的 Z-index 永遠具有優先權。

| Type                   |
| ---------------------- |
| [Rect](rect) or number |

### `id`

用於從原生程式碼定位此視圖。優先於 `nativeID` 屬性。

| Type   |
| ------ |
| string |

---

### `onBlur`

當項目失去焦點時呼叫。

| Type     |
| -------- |
| function |

---

### `onFocus`

當項目獲得焦點時呼叫。

| Type     |
| -------- |
| function |

---

### `onLayout`

在掛載時和佈局變更時呼叫。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onLongPress`

若 `onPressIn` 後的時間超過 370 毫秒則呼叫。此時間可透過 [`delayLongPress`](#delaylongpress) 自訂。

| Type     |
| -------- |
| function |

---

### `onPress`

當觸碰釋放時呼叫，但若已取消（例如被搶佔 responder lock 的滾動）則不呼叫。第一個函數參數是 [PressEvent](pressevent) 形式的事件。

| Type     |
| -------- |
| function |

---

### `onPressIn`

在可觸碰元件被按下時立即呼叫，甚至在 onPress 之前呼叫。這在發送網路請求時很有用。第一個函數參數是 [PressEvent](pressevent) 形式的事件。

| Type     |
| -------- |
| function |

---

### `onPressOut`

在觸碰釋放時立即呼叫，甚至在 onPress 之前呼叫。第一個函數參數是 [PressEvent](pressevent) 形式的事件。

| Type     |
| -------- |
| function |

---

### `pressRetentionOffset`

當滾動視圖被停用時，此值定義觸碰可以移出按鈕多遠的距離才會停用按鈕。一旦停用，嘗試移回按鈕會看到按鈕再次被啟用！在滾動視圖停用時來回移動多次。請確保傳入常數以減少記憶體配置。

| Type                   |
| ---------------------- |
| [Rect](rect) or number |

---

### `nativeID`

| Type   |
| ------ |
| string |

---

### `testID`

用於在端到端測試中定位此視圖。

| Type   |
| ------ |
| string |

---

### `touchSoundDisabled` <div class="label android">Android</div>

若為 true，觸摸時不會播放系統聲音。

| Type    |
| ------- |
| Boolean |