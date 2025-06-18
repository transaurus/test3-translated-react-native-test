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

當元件動態變化時，我們希望 TalkBack 能通知終端用戶。這可以通過 `accessibilityLiveRegion` 屬性實現，可設置為 `none`、`polite` 和 `assertive`：

- **none** 輔助服務不應宣佈此視圖的變更。
- **polite** 輔助服務應宣佈此視圖的變更。
- **assertive** 輔助服務應中斷當前語音，立即宣佈此視圖的變更。

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

上例中的 `addOne` 方法會改變狀態變量 `count`。當觸發 TouchableWithoutFeedback 時，由於 Text 視圖設有 `accessibilityLiveRegion="polite"` 屬性，TalkBack 會朗讀其中的文字。

### `accessibilityRole`

`accessibilityRole` 向輔助技術用戶傳達元件的用途。

`accessibilityRole` 可為以下值之一：

- **adjustable** 用於可「調整」的元素（如滑桿）。
- **alert** 用於包含需向用戶展示重要文本的元素。
- **button** 用於應被視為按鈕的元素。
- **checkbox** 用於表示可勾選、取消勾選或處於混合勾選狀態的核取方塊。
- **combobox** 用於表示允許用戶從多個選項中選擇的組合框。
- **header** 用於作為內容區段標題的元素（如導覽列標題）。
- **image** 用於應被視為圖像的元素，可與按鈕或連結結合使用。
- **imagebutton** 用於應被視為按鈕且同時是圖像的元素。
- **keyboardkey** 用於作為鍵盤按鍵的元素。
- **link** 用於應被視為連結的元素。
- **menu** 用於表示選項菜單的元件。
- **menubar** 用於作為多個菜單容器的元件。
- **menuitem** 用於表示菜單中的項目。
- **none** 用於沒有角色的元素。
- **progressbar** 用於表示任務進度的元件。
- **radio** 用於表示單選按鈕。
- **radiogroup** 用於表示單選按鈕群組。
- **scrollbar** 用於表示捲軸。
- **search** 用於應同時被視為搜尋欄的文字欄位元素。
- **spinbutton** 用於表示可打開選項列表的按鈕。
- **summary** 用於在應用首次啟動時提供當前狀態快速摘要的元素。
- **switch** 用於表示可開關的切換開關。
- **tab** 用於表示標籤頁。
- **tablist** 用於表示標籤頁列表。
- **text** 用於應被視為靜態不可變文字的元件。
- **timer** 用於表示計時器。
- **togglebutton** 用於表示切換按鈕，需搭配 accessibilityState checked 表示按鈕的開關狀態。
- **toolbar** 用於表示工具列（操作按鈕或元件的容器）。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 搭配使用表示網格，會為 Android 的 GridView 添加網格內/外通知。

### `accessibilityShowsLargeContentViewer` <div class="label ios">iOS</div>

布林值，決定用戶長按元素時是否顯示大內容檢視器。

需 iOS 13.0 或更高版本。

### `accessibilityLargeContentTitle` <div class="label ios">iOS</div>

當大內容檢視器顯示時，將用作標題的字串。

需將 `accessibilityShowsLargeContentViewer` 設為 `true`。

```tsx
<View
  accessibilityShowsLargeContentViewer={true}
  accessibilityLargeContentTitle="Home Tab">
  <Text>Home</Text>
</View>
```

### `accessibilityState`

描述組件當前狀態給輔助技術使用者。

`accessibilityState` 是一個物件，包含以下欄位：

| Name     | Description                                                                                                                           | Type               | Required |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | -------- |
| disabled | Indicates whether the element is disabled or not.                                                                                     | boolean            | No       |
| selected | Indicates whether a selectable element is currently selected or not.                                                                  | boolean            | No       |
| checked  | Indicates the state of a checkable element. This field can either take a boolean or the "mixed" string to represent mixed checkboxes. | boolean or 'mixed' | No       |
| busy     | Indicates whether an element is currently busy or not.                                                                                | boolean            | No       |
| expanded | Indicates whether an expandable element is currently expanded or collapsed.                                                           | boolean            | No       |

使用時，將 `accessibilityState` 設為具有特定定義的物件。

### `accessibilityValue`

代表組件的當前值。可以是組件值的文字描述，或是針對範圍型組件（如滑桿和進度條）包含範圍資訊（最小值、當前值和最大值）。

`accessibilityValue` 是一個物件，包含以下欄位：

| Name | Description                                                                                    | Type    | Required                  |
| ---- | ---------------------------------------------------------------------------------------------- | ------- | ------------------------- |
| min  | The minimum value of this component's range.                                                   | integer | Required if `now` is set. |
| max  | The maximum value of this component's range.                                                   | integer | Required if `now` is set. |
| now  | The current value of this component's range.                                                   | integer | No                        |
| text | A textual description of this component's value. Will override `min`, `now`, and `max` if set. | string  | No                        |

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

布林值，表示 VoiceOver 是否應忽略接收者同層級視圖中的元素。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityViewIsModal` 設為 `true` 會導致 VoiceOver 忽略視圖 `A` 中的元素。另一方面，如果視圖 `B` 包含子視圖 `C` 且您將視圖 `C` 的 `accessibilityViewIsModal` 設為 `true`，VoiceOver 不會忽略視圖 `A` 中的元素。

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

布林值，表示此無障礙元素內包含的無障礙元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityElementsHidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。這類似於 Android 屬性 `importantForAccessibility="no-hide-descendants"`。

### `aria-valuemax`

代表範圍型組件（如滑桿和進度條）的最大值。

### `aria-valuemin`

代表範圍型組件（如滑桿和進度條）的最小值。

### `aria-valuenow`

代表範圍型組件（如滑桿和進度條）的當前值。

### `aria-valuetext`

代表組件的文字描述。

### `aria-busy`

表示元素正在修改中，輔助技術可能需要等待變更完成後再通知使用者更新。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-checked`

表示可勾選元素的狀態。此欄位可以是布林值或字串 "mixed" 來表示混合勾選框。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

### `aria-disabled`

表示元素可感知但已停用，因此無法編輯或操作。

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

定義標記互動元素的字串值。

| Type   |
| ------ |
| string |

### `aria-labelledby` <div class="label android">Android</div>

標識用於標記當前元素的關聯元素。`aria-labelledby` 的值應與相關元素的 [`nativeID`](view.md#nativeid) 相匹配：

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

表示元素將被更新，並描述用戶代理、輔助技術和用戶可以預期的實時區域更新類型。

- **off** 輔助服務不應宣佈此視圖的變更。
- **polite** 輔助服務應宣佈此視圖的變更。
- **assertive** 輔助服務應中斷當前語音以立即宣佈此視圖的變更。

| Type                                     | Default |
| ---------------------------------------- | ------- |
| enum(`'assertive'`, `'off'`, `'polite'`) | `'off'` |

---

### `aria-modal` <div class="label ios">iOS</div>

布爾值，指示 VoiceOver 是否應忽略接收器同級視圖中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-selected`

指示可選元素當前是否被選中。

| Type    |
| ------- |
| boolean |

### `importantForAccessibility` <div class="label android">Android</div>

當兩個具有相同父級的重疊 UI 組件存在時，默認的輔助焦點可能會有不可預測的行為。`importantForAccessibility` 屬性將通過控制視圖是否觸發輔助事件以及是否報告給輔助服務來解決此問題。它可以設置為 `auto`、`yes`、`no` 和 `no-hide-descendants`（最後一個值將強制輔助服務忽略該組件及其所有子項）。

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

在上面的例子中，`yellow` 佈局及其子項對 TalkBack 和所有其他輔助服務完全不可見。因此，我們可以使用具有相同父級的重疊視圖而不會混淆 TalkBack。

### `onAccessibilityEscape` <div class="label ios">iOS</div>

將此屬性分配給一個自定義函數，當有人執行「退出」手勢（即兩指 Z 形手勢）時將調用該函數。退出函數應在用戶界面中按層次結構向上移動。這可能意味著在導航層次結構中向上或返回，或者關閉模態用戶界面。如果選定的元素沒有 `onAccessibilityEscape` 函數，系統將嘗試遍歷視圖層次結構，直到找到具有該函數的視圖，或者發出「bonk」聲表示無法找到。

### `onAccessibilityTap` <div class="label ios">iOS</div>

使用此屬性分配一個自定義函數，當有人在選中可訪問元素時雙擊激活它時將調用該函數。

### `onMagicTap` <div class="label ios">iOS</div>

將此屬性分配給一個自定義函數，當有人執行「魔法點擊」手勢（即兩指雙擊）時將調用該函數。魔法點擊函數應執行用戶在組件上可以採取的最相關操作。在 iPhone 的電話應用中，魔法點擊接聽電話或結束當前通話。如果選定的元素沒有 `onMagicTap` 函數，系統將遍歷視圖層次結構，直到找到具有該函數的視圖。

### `role`

`role` 傳達組件的用途，並優先於 [`accessibilityRole`](accessibility#accessibilityrole) 屬性。

`role` 可以是以下之一：

- **alert** 當元素包含需要向用戶展示的重要文字時使用。
- **button** 當元素應被視為按鈕時使用。
- **checkbox** 當元素代表可勾選、取消勾選或處於混合勾選狀態的核取方塊時使用。
- **combobox** 當元素代表組合框（允許用戶從多個選項中選擇）時使用。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 一起使用時，代表網格。為 Android 的 GridView 添加網格內/外公告。
- **heading** 當元素作為內容區段的標題（例如導覽列的標題）時使用。
- **img** 當元素應被視為圖片時使用。可與按鈕或連結等結合使用。
- **link** 當元素應被視為連結時使用。
- **list** 用於標識項目列表。
- **listitem** 用於標識列表中的單個項目。
- **menu** 當組件是選單選項時使用。
- **menubar** 當組件是多個選單的容器時使用。
- **menuitem** 用於代表選單中的項目。
- **none** 當元素沒有角色時使用。
- **presentation** 當元素沒有角色時使用。
- **progressbar** 用於代表顯示任務進度的組件。
- **radio** 用於代表單選按鈕。
- **radiogroup** 用於代表一組單選按鈕。
- **scrollbar** 用於代表捲軸。
- **searchbox** 當文字輸入框元素也應被視為搜尋欄位時使用。
- **slider** 當元素可被「調整」（例如滑桿）時使用。
- **spinbutton** 用於代表可打開選項列表的按鈕。
- **summary** 當元素可用於在應用程式首次啟動時提供當前條件的快速摘要時使用。
- **switch** 用於代表可開啟和關閉的開關。
- **tab** 用於代表標籤頁。
- **tablist** 用於代表標籤頁列表。
- **timer** 用於代表計時器。
- **toolbar** 用於代表工具列（操作按鈕或組件的容器）。

## 無障礙操作

無障礙操作允許輔助技術以程式化的方式調用組件的操作。要支援無障礙操作，組件必須完成兩件事：

- 通過 `accessibilityActions` 屬性定義其支援的操作列表。
- 實現 `onAccessibilityAction` 函數以處理操作請求。

`accessibilityActions` 屬性應包含一個操作對象列表。每個操作對象應包含以下字段：

| Name  | Type   | Required |
| ----- | ------ | -------- |
| name  | string | Yes      |
| label | string | No       |

操作可以是標準操作（例如點擊按鈕或調整滑桿），也可以是特定於組件的自定義操作（例如刪除電子郵件）。標準操作和自定義操作都需要 `name` 字段，但 `label` 對標準操作是可選的。

當添加對標準操作的支援時，`name` 必須是以下之一：

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

- [讓 React Native 應用程式具備無障礙功能](https://engineering.fb.com/ios/making-react-native-apps-accessible/)