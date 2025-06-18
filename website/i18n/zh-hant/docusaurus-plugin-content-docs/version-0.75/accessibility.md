---
id: accessibility
title: Accessibility
description: Create mobile apps accessible to assistive technology with React Native's suite of APIs designed to work with Android and iOS.
---

Both Android and iOS provide APIs for integrating apps with assistive technologies like the bundled screen readers VoiceOver (iOS) and TalkBack (Android). React Native has complementary APIs that let your app accommodate all users.

:::info
Android and iOS differ slightly in their approaches, and thus the React Native implementations may vary by platform.
:::

## Accessibility properties

### `accessible`

When `true`, indicates that the view is an accessibility element. When a view is an accessibility element, it groups its children into a single selectable component. By default, all touchable elements are accessible.

On Android, `accessible={true}` property for a react-native View will be translated into native `focusable={true}`.

```tsx
<View accessible={true}>
  <Text>text one</Text>
  <Text>text two</Text>
</View>
```

In the above example, accessibility focus is only available on the parent view with the `accessible` property, and not individually for 'text one' and 'text two'.

### `accessibilityLabel`

When a view is marked as accessible, it is a good practice to set an `accessibilityLabel` on the view, so that people who use VoiceOver or TalkBack know what element they have selected. A screen reader will verbalize this string when the associated element is selected.

To use, set the `accessibilityLabel` property to a custom string on your View, Text, or Touchable:

```tsx
<TouchableOpacity
  accessible={true}
  accessibilityLabel="Tap me!"
  onPress={onPress}>
  <View style={styles.button}>
    <Text style={styles.buttonText}>Press me!</Text>
  </View>
</TouchableOpacity>
```

In the above example, the `accessibilityLabel` on the TouchableOpacity element would default to "Press me!". The label is constructed by concatenating all Text node children separated by spaces.

### `accessibilityLabelledBy` <div class="label android">Android</div>

A reference to another element [nativeID](view.md#nativeid) used to build complex forms.
The value of `accessibilityLabelledBy` should match the `nativeID` of the related element:

```tsx
<View>
  <Text nativeID="formLabel">Label for Input Field</Text>
  <TextInput
    accessibilityLabel="input"
    accessibilityLabelledBy="formLabel"
  />
</View>
```

In the above example, the screen reader announces `Input, Edit Box for Label for Input Field` when focusing on the TextInput.

### `accessibilityHint`

An accessibility hint can be used to provide additional context to the user on the result of the action when it is not clear from the accessibility label alone.

Provide the `accessibilityHint` property a custom string on your View, Text, or Touchable:

```tsx
<TouchableOpacity
  accessible={true}
  accessibilityLabel="Go back"
  accessibilityHint="Navigates to the previous screen"
  onPress={onPress}>
  <View style={styles.button}>
    <Text style={styles.buttonText}>Back</Text>
  </View>
</TouchableOpacity>
```

<div class="label ios basic">iOS</div>

In the above example, VoiceOver will read the hint after the label, if the user has hints enabled in the device's VoiceOver settings. Read more about guidelines for `accessibilityHint` in the [iOS Developer Docs](https://developer.apple.com/documentation/objectivec/nsobject/1615093-accessibilityhint)

<div class="label android basic">Android</div>

In the above example, TalkBack will read the hint after the label. At this time, hints cannot be turned off on Android.

### `accessibilityLanguage` <div class="label ios">iOS</div>

By using the `accessibilityLanguage` property, the screen reader will understand which language to use while reading the element's **label**, **value**, and **hint**. The provided string value must follow the [BCP 47 specification](https://www.rfc-editor.org/info/bcp47).

```tsx
<View
  accessible={true}
  accessibilityLabel="Pizza"
  accessibilityLanguage="it-IT">
  <Text>🍕</Text>
</View>
```

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

Inverting screen colors is an accessibility feature available in iOS and iPadOS for people with color blindness, low vision, or vision impairment. If there's a view you don't want to invert when this setting is on, possibly a photo, set this property to `true`.

### `accessibilityLiveRegion` <div class="label android">Android</div>

當元件動態變化時，我們希望 TalkBack 能通知終端使用者。這可透過 `accessibilityLiveRegion` 屬性實現，該屬性可設為 `none`、`polite` 或 `assertive`：

- **none** 輔助服務不應宣讀此視圖的變更。
- **polite** 輔助服務應宣讀此視圖的變更。
- **assertive** 輔助服務應中斷當前語音以立即宣讀此視圖的變更。

```tsx
<TouchableWithoutFeedback onPress={addOne}>
  <View style={styles.embedded}>
    <Text>Click me</Text>
  </View>
</TouchableWithoutFeedback>
<Text accessibilityLiveRegion="polite">
  Clicked {count} times
</Text>
```

上例中的 `addOne` 方法會改變狀態變數 `count`。當 TouchableWithoutFeedback 被觸發時，由於 Text 視圖設有 `accessibilityLiveRegion="polite"` 屬性，TalkBack 會朗讀其中的文字。

### `accessibilityRole`

`accessibilityRole` 向輔助技術使用者傳達元件的用途。

`accessibilityRole` 可為以下值之一：

- **adjustable** 用於可「調整」的元素（如滑桿）。
- **alert** 用於包含需向使用者展示重要文字的元素。
- **button** 用於應被視為按鈕的元素。
- **checkbox** 用於代表可勾選、取消勾選或處於混合勾選狀態的核取方塊。
- **combobox** 用於代表可讓使用者從多個選項中選擇的下拉式方塊。
- **header** 用於作為內容區段標題的元素（如導覽列標題）。
- **image** 用於應被視為圖像的元素，可與按鈕或連結結合使用。
- **imagebutton** 用於應被視為按鈕且同時是圖像的元素。
- **keyboardkey** 用於作為鍵盤按鍵的元素。
- **link** 用於應被視為連結的元素。
- **menu** 用於作為選單的元件。
- **menubar** 用於作為多個選單容器的元件。
- **menuitem** 用於代表選單中的項目。
- **none** 用於沒有角色的元素。
- **progressbar** 用於代表顯示任務進度的元件。
- **radio** 用於代表單選按鈕。
- **radiogroup** 用於代表一組單選按鈕。
- **scrollbar** 用於代表捲軸。
- **search** 用於文字欄位元素同時應被視為搜尋欄位的情況。
- **spinbutton** 用於代表可開啟選項清單的按鈕。
- **summary** 用於應用程式首次啟動時可提供當前狀態快速摘要的元素。
- **switch** 用於代表可開啟/關閉的開關。
- **tab** 用於代表分頁標籤。
- **tablist** 用於代表分頁標籤清單。
- **text** 用於應被視為靜態不可變文字的元素。
- **timer** 用於代表計時器。
- **togglebutton** 用於代表切換按鈕，應搭配 accessibilityState 的 checked 屬性指示按鈕是否處於切換狀態。
- **toolbar** 用於代表工具列（動作按鈕或元件的容器）。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 搭配使用時代表網格，會為 Android 的 GridView 添加網格內/外宣告。

### `accessibilityState`

向輔助技術使用者描述元件當前狀態。

`accessibilityState` 是一個物件，包含以下欄位：

| Name     | Description                                                                                                                           | Type               | Required |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | -------- |
| disabled | Indicates whether the element is disabled or not.                                                                                     | boolean            | No       |
| selected | Indicates whether a selectable element is currently selected or not.                                                                  | boolean            | No       |
| checked  | Indicates the state of a checkable element. This field can either take a boolean or the "mixed" string to represent mixed checkboxes. | boolean or 'mixed' | No       |
| busy     | Indicates whether an element is currently busy or not.                                                                                | boolean            | No       |
| expanded | Indicates whether an expandable element is currently expanded or collapsed.                                                           | boolean            | No       |

使用時需將 `accessibilityState` 設為具有特定定義的物件。

### `accessibilityValue`

代表元件的當前值，可以是元件值的文字描述；對於範圍型元件（如滑桿和進度條），則包含範圍資訊（最小值、當前值和最大值）。

`accessibilityValue` 是一個物件，包含以下欄位：

| Name | Description                                                                                    | Type    | Required                  |
| ---- | ---------------------------------------------------------------------------------------------- | ------- | ------------------------- |
| min  | The minimum value of this component's range.                                                   | integer | Required if `now` is set. |
| max  | The maximum value of this component's range.                                                   | integer | Required if `now` is set. |
| now  | The current value of this component's range.                                                   | integer | No                        |
| text | A textual description of this component's value. Will override `min`, `now`, and `max` if set. | string  | No                        |

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

布林值，用於指示 VoiceOver 是否應忽略接收者同層級視圖中的元素。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityViewIsModal` 設為 `true` 會導致 VoiceOver 忽略視圖 `A` 中的元素。另一方面，如果視圖 `B` 包含子視圖 `C` 且您將視圖 `C` 的 `accessibilityViewIsModal` 設為 `true`，VoiceOver 則不會忽略視圖 `A` 中的元素。

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

布林值，用於指示此無障礙元素內包含的無障礙元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityElementsHidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。這類似於 Android 的屬性 `importantForAccessibility="no-hide-descendants"`。

### `aria-valuemax`

表示基於範圍的元件（如滑塊和進度條）的最大值。

### `aria-valuemin`

表示基於範圍的元件（如滑塊和進度條）的最小值。

### `aria-valuenow`

表示基於範圍的元件（如滑塊和進度條）的當前值。

### `aria-valuetext`

表示元件的文字描述。

### `aria-busy`

表示元素正在被修改，輔助技術可能需要等待變更完成後再通知使用者更新。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-checked`

表示可勾選元素的狀態。此欄位可以是布林值或字串 "mixed" 以表示混合勾選狀態的核取方塊。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

### `aria-disabled`

表示元素是可感知的但已禁用，因此不可編輯或操作。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-expanded`

表示可展開元素當前是展開還是折疊狀態。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-hidden`

表示此無障礙元素內包含的無障礙元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-label`

定義標記互動式元素的字串值。

| Type   |
| ------ |
| string |

### `aria-labelledby` <div class="label android">Android</div>

標識用於標記應用此屬性的元素的元素。`aria-labelledby` 的值應與相關元素的 [`nativeID`](view.md#nativeid) 匹配：

```tsx
<View>
  <Text nativeID="formLabel">Label for Input Field</Text>
  <TextInput aria-label="input" aria-labelledby="formLabel" />
</View>
```

| Type   |
| ------ |
| string |

### `aria-live` <div class="label android">Android</div>

表示元素將被更新，並描述使用者代理、輔助技術和使用者可以從即時區域預期的更新類型。

- **off** 無障礙服務不應宣讀此視圖的變更。
- **polite** 無障礙服務應宣讀此視圖的變更。
- **assertive** 無障礙服務應中斷當前語音，立即宣讀此視圖的變更。

| Type                                     | Default |
| ---------------------------------------- | ------- |
| enum(`'assertive'`, `'off'`, `'polite'`) | `'off'` |

---

### `aria-modal` <div class="label ios">iOS</div>

布林值，指示 VoiceOver 是否應忽略接收者同層級視圖中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-selected`

指示可選元素當前是否被選中。

| Type    |
| ------- |
| boolean |

### `importantForAccessibility` <div class="label android">Android</div>

當兩個具有相同父元素的 UI 組件重疊時，預設的無障礙焦點可能會有不可預測的行為。`importantForAccessibility` 屬性可通過控制視圖是否觸發無障礙事件以及是否向無障礙服務報告來解決此問題。它可以設置為 `auto`、`yes`、`no` 和 `no-hide-descendants`（最後一個值會強制無障礙服務忽略該組件及其所有子元素）。

```tsx
<View style={styles.container}>
  <View
    style={[styles.layout, {backgroundColor: 'green'}]}
    importantForAccessibility="yes">
    <Text>First layout</Text>
  </View>
  <View
    style={[styles.layout, {backgroundColor: 'yellow'}]}
    importantForAccessibility="no-hide-descendants">
    <Text>Second layout</Text>
  </View>
</View>
```

在上面的例子中，`yellow` 佈局及其子元素對 TalkBack 和所有其他無障礙服務完全不可見。因此，我們可以使用具有相同父元素的重疊視圖，而不會混淆 TalkBack。

### `onAccessibilityEscape` <div class="label ios">iOS</div>

將此屬性分配給一個自定義函數，當有人執行「escape」手勢（即兩指 Z 形手勢）時，該函數將被調用。escape 函數應在用戶界面中按層次結構後退。這可能意味著在導航層次結構中向上或返回，或者關閉模態用戶界面。如果選定的元素沒有 `onAccessibilityEscape` 函數，系統將嘗試向上遍歷視圖層次結構，直到找到一個具有該函數的視圖，或者發出「bonk」聲表示無法找到。

### `onAccessibilityTap` <div class="label ios">iOS</div>

使用此屬性分配一個自定義函數，當有人在選中可訪問元素時雙擊它時，該函數將被調用。

### `onMagicTap` <div class="label ios">iOS</div>

將此屬性分配給一個自定義函數，當有人執行「magic tap」手勢（即兩指雙擊）時，該函數將被調用。magic tap 函數應執行用戶在組件上可以採取的最相關操作。在 iPhone 的電話應用中，magic tap 可以接聽電話或結束當前通話。如果選定的元素沒有 `onMagicTap` 函數，系統將向上遍歷視圖層次結構，直到找到一個具有該函數的視圖。

### `role`

`role` 傳達組件的用途，並優先於 [`accessibilityRole`](accessibility#accessibilityrole) 屬性。

`role` 可以是以下之一：

- **alert** Used when an element contains important text to be presented to the user.
- **button** Used when the element should be treated as a button.
- **checkbox** Used when an element represents a checkbox that can be checked, unchecked, or have a mixed checked state.
- **combobox** Used when an element represents a combo box, which allows the user to select among several choices.
- **grid** Used with ScrollView, VirtualizedList, FlatList, or SectionList to represent a grid. Adds the in/out of grid announcements to the android GridView.
- **heading** Used when an element acts as a header for a content section (e.g. the title of a navigation bar).
- **img** Used when the element should be treated as an image. Can be combined with a button or link, for example.
- **link** Used when the element should be treated as a link.
- **list** Used to identify a list of items.
- **listitem** Used to itentify an item in a list.
- **menu** Used when the component is a menu of choices.
- **menubar** Used when a component is a container of multiple menus.
- **menuitem** Used to represent an item within a menu.
- **none** Used when the element has no role.
- **presentation** Used when the element has no role.
- **progressbar** Used to represent a component that indicates the progress of a task.
- **radio** Used to represent a radio button.
- **radiogroup** Used to represent a group of radio buttons.
- **scrollbar** Used to represent a scroll bar.
- **searchbox** Used when the text field element should also be treated as a search field.
- **slider** Used when an element can be "adjusted" (e.g. a slider).
- **spinbutton** Used to represent a button that opens a list of choices.
- **summary** Used when an element can be used to provide a quick summary of current conditions in the app when the app first launches.
- **switch** Used to represent a switch that can be turned on and off.
- **tab** Used to represent a tab.
- **tablist** Used to represent a list of tabs.
- **timer** Used to represent a timer.
- **toolbar** Used to represent a toolbar (a container of action buttons or components).

## Accessibility Actions

Accessibility actions allow assistive technology to programmatically invoke the action(s) of a component. To support accessibility actions, a component must do two things:

- Define the list of actions it supports via the `accessibilityActions` property.
- Implement an `onAccessibilityAction` function to handle action requests.

The `accessibilityActions` property should contain a list of action objects. Each action object should contain the following fields:

| Name  | Type   | Required |
| ----- | ------ | -------- |
| name  | string | Yes      |
| label | string | No       |

Actions either represent standard actions, such as clicking a button or adjusting a slider, or custom actions specific to a given component such as deleting an email message. The `name` field is required for both standard and custom actions, but `label` is optional for standard actions.

When adding support for standard actions, `name` must be one of the following:

- `'magicTap'` - iOS only - While VoiceOver focus is on or inside the component, the user double tapped with two fingers.
- `'escape'` - iOS only - While VoiceOver focus is on or inside the component, the user performed a two-finger scrub gesture (left, right, left).
- `'activate'` - Activate the component. This should perform the same action with, or without, assistive technology. Engaged when a screen reader user double taps the component.
- `'increment'` - Increment an adjustable component. On iOS, VoiceOver generates this action when the component has a role of `'adjustable'` and the user places focus on it and swipes upward. On Android, TalkBack generates this action when the user places accessibility focus on the component and presses the volume-up button.
- `'decrement'` - Decrement an adjustable component. On iOS, VoiceOver generates this action when the component has a role of `'adjustable'` and the user places focus on it and swipes downward. On Android, TalkBack generates this action when the user places accessibility focus on the component and presses the volume-down button.
- `'longpress'` - Android only - This action is generated when the user places accessibility focus on the component, then double-taps and holds one finger on the screen. This should perform the same action with, or without, assistive technology.

The `label` field is optional for standard actions and is often unused by assistive technologies. For custom actions, it is a localized string containing a description of the action to be presented to the user.

To handle action requests, a component must implement an `onAccessibilityAction` function. The only argument to this function is an event containing the name of the action to perform. The below example from RNTester shows how to create a component that defines and handles several custom actions.

```tsx
<View
  accessible={true}
  accessibilityActions={[
    {name: 'cut', label: 'cut'},
    {name: 'copy', label: 'copy'},
    {name: 'paste', label: 'paste'},
  ]}
  onAccessibilityAction={event => {
    switch (event.nativeEvent.actionName) {
      case 'cut':
        Alert.alert('Alert', 'cut action success');
        break;
      case 'copy':
        Alert.alert('Alert', 'copy action success');
        break;
      case 'paste':
        Alert.alert('Alert', 'paste action success');
        break;
    }
  }}
/>
```

## Checking if a Screen Reader is Enabled

The `AccessibilityInfo` API allows you to determine whether or not a screen reader is currently active. See the [AccessibilityInfo documentation](accessibilityinfo) for details.

## Sending Accessibility Events <div class="label android">Android</div>

Sometimes it is useful to trigger an accessibility event on a UI component (i.e. when a custom view appears on a screen or set accessibility focus to a view). Native UIManager module exposes a method ‘sendAccessibilityEvent’ for this purpose. It takes two arguments: a view tag and a type of event. The supported event types are `typeWindowStateChanged`, `typeViewFocused`, and `typeViewClicked`.

```tsx
import {Platform, UIManager, findNodeHandle} from 'react-native';

if (Platform.OS === 'android') {
  UIManager.sendAccessibilityEvent(
    findNodeHandle(this),
    UIManager.AccessibilityEventTypes.typeViewFocused,
  );
}
```

## Testing TalkBack Support <div class="label android">Android</div>

To enable TalkBack, go to the Settings app on your Android device or emulator. Tap Accessibility, then TalkBack. Toggle the "Use service" switch to enable or disable it.

Android emulators don't have TalkBack installed by default. You can install TalkBack on your emulator via the Google Play Store. Make sure to choose an emulator with the Google Play store installed. These are available in Android Studio.

You can use the volume key shortcut to toggle TalkBack. To turn on the volume key shortcut, go to the Settings app, then Accessibility. At the top, turn on the volume key shortcut.

To use the volume key shortcut, press both volume keys for 3 seconds to start an accessibility tool.

Additionally, if you prefer, you can toggle TalkBack via the command line with:

```shell
# disable
adb shell settings put secure enabled_accessibility_services com.android.talkback/com.google.android.marvin.talkback.TalkBackService

# enable
adb shell settings put secure enabled_accessibility_services com.google.android.marvin.talkback/com.google.android.marvin.talkback.TalkBackService
```

## Testing VoiceOver Support <div class="label ios">iOS</div>

To enable VoiceOver on your iOS or iPadOS device, go to the Settings app, tap General, then Accessibility. There you will find many tools available for people to enable their devices to be more usable, including VoiceOver. To enable VoiceOver, tap on VoiceOver under "Vision" and toggle the switch that appears at the top.

At the very bottom of the Accessibility settings, there is an "Accessibility Shortcut". You can use this to toggle VoiceOver by triple-clicking the Home button.

VoiceOver 無法透過模擬器使用，但你可以使用 Xcode 的「輔助功能檢查器」來透過應用程式使用 macOS 的 VoiceOver。請注意，最好還是使用實體裝置進行測試，因為 macOS 的 VoiceOver 可能會導致不同的體驗。

## 其他資源

- [打造無障礙的 React Native 應用程式](https://engineering.fb.com/ios/making-react-native-apps-accessible/)