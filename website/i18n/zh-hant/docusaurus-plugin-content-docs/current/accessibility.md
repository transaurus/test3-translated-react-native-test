---
id: accessibility
title: Accessibility
description: Create mobile apps accessible to assistive technology with React Native's suite of APIs designed to work with Android and iOS.
---

Android 和 iOS 皆提供 API 來整合應用程式與輔助技術，例如內建的螢幕閱讀器 VoiceOver (iOS) 和 TalkBack (Android)。React Native 提供相應的 API，讓您的應用程式能服務所有使用者。

:::info
Android 和 iOS 在實現方式上略有差異，因此 React Native 的實作可能因平台而有所不同。
:::

## 無障礙屬性

### `accessible`

當設為 `true` 時，表示該視圖可被輔助技術（如螢幕閱讀器和硬體鍵盤）發現。請注意，這並不意味著該視圖一定會被 VoiceOver 或 TalkBack 聚焦。原因可能包括 VoiceOver 不允許嵌套的無障礙元素，或 TalkBack 選擇聚焦於某些父元素。

預設情況下，所有可觸控元素都是可訪問的。

在 Android 上，`accessible` 會被轉換為原生的 [`focusable`](<https://developer.android.com/reference/android/view/View#setFocusable(boolean)>)。在 iOS 上，則轉換為原生的 [`isAccessibilityElement`](https://developer.apple.com/documentation/uikit/uiaccessibilityelement/isaccessibilityelement?language=objc)。

```tsx
<View>
  <View accessible={true} />
  <View />
</View>
```

在上面的範例中，無障礙焦點僅適用於具有 `accessible` 屬性的第一個子視圖，而不適用於父視圖或沒有 `accessible` 的兄弟視圖。

### `accessibilityLabel`

當視圖標記為可訪問時，建議在視圖上設置 `accessibilityLabel`，以便使用 VoiceOver 或 TalkBack 的使用者知道他們選擇了什麼元素。螢幕閱讀器會在選中相關元素時朗讀此字串。

使用方法是在您的 View、Text 或 Touchable 上設置 `accessibilityLabel` 屬性為自訂字串：

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

在上面的範例中，TouchableOpacity 元素的 `accessibilityLabel` 預設為「Press me!」。標籤是通過串接所有 Text 節點子元素並以空格分隔來構建的。

### `accessibilityLabelledBy` <div class="label android">Android</div>

用於構建複雜表單的參考其他元素的 [nativeID](view.md#nativeid)。
`accessibilityLabelledBy` 的值應與相關元素的 `nativeID` 匹配：

```tsx
<View>
  <Text nativeID="formLabel">Label for Input Field</Text>
  <TextInput
    accessibilityLabel="input"
    accessibilityLabelledBy="formLabel"
  />
</View>
```

在上面的範例中，當聚焦於 TextInput 時，螢幕閱讀器會朗讀「Input, Edit Box for Label for Input Field」。

### `accessibilityHint`

無障礙提示可用於在無障礙標籤本身不夠清楚時，為使用者提供操作結果的額外上下文。

在您的 View、Text 或 Touchable 上提供 `accessibilityHint` 屬性為自訂字串：

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

在上面的範例中，如果使用者在設備的 VoiceOver 設置中啟用了提示，VoiceOver 會在標籤後讀取提示。更多關於 `accessibilityHint` 的指南，請參閱 [iOS 開發者文檔](https://developer.apple.com/documentation/objectivec/nsobject/1615093-accessibilityhint)。

<div class="label android basic">Android</div>

在上面的範例中，TalkBack 會在標籤後讀取提示。目前，Android 上的提示無法關閉。

### `accessibilityLanguage` <div class="label ios">iOS</div>

通過使用 `accessibilityLanguage` 屬性，螢幕閱讀器會理解在讀取元素的**標籤**、**值**和**提示**時應使用哪種語言。提供的字串值必須遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

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

When components dynamically change, we want TalkBack to alert the end user. This is made possible by the `accessibilityLiveRegion` property. It can be set to `none`, `polite`, and `assertive`:

- **none** Accessibility services should not announce changes to this view.
- **polite** Accessibility services should announce changes to this view.
- **assertive** Accessibility services should interrupt ongoing speech to immediately announce changes to this view.

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

In the above example method `addOne` changes the state variable `count`. When the TouchableWithoutFeedback is triggered, TalkBack reads the text in the Text view because of its `accessibilityLiveRegion="polite"` property.

### `accessibilityRole`

`accessibilityRole` communicates the purpose of a component to the user of assistive technology.

`accessibilityRole` can be one of the following:

- **adjustable** Used when an element can be "adjusted" (e.g. a slider).
- **alert** Used when an element contains important text to be presented to the user.
- **button** Used when the element should be treated as a button.
- **checkbox** Used when an element represents a checkbox that can be checked, unchecked, or have a mixed checked state.
- **combobox** Used when an element represents a combo box, which allows the user to select among several choices.
- **header** Used when an element acts as a header for a content section (e.g. the title of a navigation bar).
- **image** Used when the element should be treated as an image. Can be combined with a button or link.
- **imagebutton** Used when the element should be treated as a button and is also an image.
- **keyboardkey** Used when the element acts as a keyboard key.
- **link** Used when the element should be treated as a link.
- **menu** Used when the component is a menu of choices.
- **menubar** Used when a component is a container of multiple menus.
- **menuitem** Used to represent an item within a menu.
- **none** Used when the element has no role.
- **progressbar** Used to represent a component that indicates the progress of a task.
- **radio** Used to represent a radio button.
- **radiogroup** Used to represent a group of radio buttons.
- **scrollbar** Used to represent a scroll bar.
- **search** Used when a text field element should also be treated as a search field.
- **spinbutton** Used to represent a button that opens a list of choices.
- **summary** Used when an element can be used to provide a quick summary of current conditions in the app when the app first launches.
- **switch** Used to represent a switch that can be turned on and off.
- **tab** Used to represent a tab.
- **tablist** Used to represent a list of tabs.
- **text** Used when the element should be treated as static text that cannot change.
- **timer** Used to represent a timer.
- **togglebutton** Used to represent a toggle button. Should be used with accessibilityState checked to indicate if the button is toggled on or off.
- **toolbar** Used to represent a toolbar (a container of action buttons or components).
- **grid** Used with ScrollView, VirtualizedList, FlatList, or SectionList to represent a grid. Adds the in/out of grid announcements to Android's GridView.

### `accessibilityShowsLargeContentViewer` <div class="label ios">iOS</div>

A boolean value that determines whether the large content viewer is shown when the user performs a long press on the element.

Available in iOS 13.0 and later.

### `accessibilityLargeContentTitle` <div class="label ios">iOS</div>

當大內容檢視器顯示時，此字串將作為其標題使用。

需將 `accessibilityShowsLargeContentViewer` 設為 `true`。

```tsx
<View
  accessibilityShowsLargeContentViewer={true}
  accessibilityLargeContentTitle="Home Tab">
  <Text>Home</Text>
</View>
```

### `accessibilityState`

向輔助技術使用者描述元件當前的狀態。

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

代表元件的當前值。可以是元件值的文字描述，或是基於範圍的元件（如滑桿和進度條）的範圍資訊（最小值、當前值和最大值）。

`accessibilityValue` 是一個物件，包含以下欄位：

| Name | Description                                                                                    | Type    | Required                  |
| ---- | ---------------------------------------------------------------------------------------------- | ------- | ------------------------- |
| min  | The minimum value of this component's range.                                                   | integer | Required if `now` is set. |
| max  | The maximum value of this component's range.                                                   | integer | Required if `now` is set. |
| now  | The current value of this component's range.                                                   | integer | No                        |
| text | A textual description of this component's value. Will override `min`, `now`, and `max` if set. | string  | No                        |

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

一個布林值，表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityViewIsModal` 設為 `true` 會導致 VoiceOver 忽略視圖 `A` 中的元素。另一方面，如果視圖 `B` 包含子視圖 `C` 且你將視圖 `C` 的 `accessibilityViewIsModal` 設為 `true`，VoiceOver 不會忽略視圖 `A` 中的元素。

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

一個布林值，表示此輔助技術元素內包含的輔助技術元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityElementsHidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。這類似於 Android 的屬性 `importantForAccessibility="no-hide-descendants"`。

### `aria-valuemax`

代表基於範圍的元件（如滑桿和進度條）的最大值。

### `aria-valuemin`

代表基於範圍的元件（如滑桿和進度條）的最小值。

### `aria-valuenow`

代表基於範圍的元件（如滑桿和進度條）的當前值。

### `aria-valuetext`

代表元件的文字描述。

### `aria-busy`

表示元素正在被修改，輔助技術可能需要等待修改完成後再通知使用者更新。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-checked`

表示可勾選元素的狀態。此欄位可以是布林值或字串 "mixed" 來表示混合狀態的核取方塊。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

### `aria-disabled`

表示元素是可感知的但已禁用，因此不可編輯或操作。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-expanded`

表示可展開元素當前是展開還是折疊的狀態。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-hidden`

表示此輔助技術元素內包含的輔助技術元素是否隱藏。

For example, in a window that contains sibling views `A` and `B`, setting `aria-hidden` to `true` on view `B` causes VoiceOver to ignore the elements in view `B`.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-label`

Defines a string value that labels an interactive element.

| Type   |
| ------ |
| string |

### `aria-labelledby` <div class="label android">Android</div>

Identifies the element that labels the element it is applied to. The value of `aria-labelledby` should match the [`nativeID`](view.md#nativeid) of the related element:

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

Indicates that an element will be updated and describes the types of updates the user agents, assistive technologies, and user can expect from the live region.

- **off** Accessibility services should not announce changes to this view.
- **polite** Accessibility services should announce changes to this view.
- **assertive** Accessibility services should interrupt ongoing speech to immediately announce changes to this view.

| Type                                     | Default |
| ---------------------------------------- | ------- |
| enum(`'assertive'`, `'off'`, `'polite'`) | `'off'` |

---

### `aria-modal` <div class="label ios">iOS</div>

Boolean value indicating whether VoiceOver should ignore the elements within views that are siblings of the receiver.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-selected`

Indicates whether a selectable element is currently selected or not.

| Type    |
| ------- |
| boolean |

### `importantForAccessibility` <div class="label android">Android</div>

In the case of two overlapping UI components with the same parent, default accessibility focus can have unpredictable behavior. The `importantForAccessibility` property will resolve this by controlling if a view fires accessibility events and if it is reported to accessibility services. It can be set to `auto`, `yes`, `no` and `no-hide-descendants` (the last value will force accessibility services to ignore the component and all of its children).

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

In the above example, the `yellow` layout and its descendants are completely invisible to TalkBack and all other accessibility services. So we can use overlapping views with the same parent without confusing TalkBack.

### `onAccessibilityEscape` <div class="label ios">iOS</div>

Assign this property to a custom function which will be called when someone performs the "escape" gesture, which is a two finger Z shaped gesture. An escape function should move back hierarchically in the user interface. This can mean moving up or back in a navigation hierarchy or dismissing a modal user interface. If the selected element does not have an `onAccessibilityEscape` function, the system will attempt to traverse up the view hierarchy until it finds a view that does or bonk to indicate it was unable to find one.

### `onAccessibilityTap` <div class="label ios">iOS</div>

Use this property to assign a custom function to be called when someone activates an accessible element by double tapping on it while it's selected.

### `onMagicTap` <div class="label ios">iOS</div>

Assign this property to a custom function which will be called when someone performs the "magic tap" gesture, which is a double-tap with two fingers. A magic tap function should perform the most relevant action a user could take on a component. In the Phone app on iPhone, a magic tap answers a phone call or ends the current one. If the selected element does not have an `onMagicTap` function, the system will traverse up the view hierarchy until it finds a view that does.

### `role`

`role` communicates the purpose of a component and has precedence over the [`accessibilityRole`](accessibility#accessibilityrole) prop.

`role` can be one of the following:

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

- `'magicTap'` - 僅限 iOS - 當 VoiceOver 焦點位於元件上或內部時，使用者用兩指雙擊。
- `'escape'` - 僅限 iOS - 當 VoiceOver 焦點位於元件上或內部時，使用者執行兩指擦除手勢（左、右、左）。
- `'activate'` - 啟動元件。無論是否使用輔助技術，此動作應執行相同的操作。當螢幕閱讀器使用者雙擊元件時觸發。
- `'increment'` - 增加可調整元件。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點置於其上並向上滑動時，VoiceOver 會生成此動作。在 Android 上，當使用者將輔助功能焦點置於元件上並按下音量增加按鈕時，TalkBack 會生成此動作。
- `'decrement'` - 減少可調整元件。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點置於其上並向下滑動時，VoiceOver 會生成此動作。在 Android 上，當使用者將輔助功能焦點置於元件上並按下音量減少按鈕時，TalkBack 會生成此動作。
- `'longpress'` - 僅限 Android - 當使用者將輔助功能焦點置於元件上，然後雙擊並按住一根手指在螢幕上時，會生成此動作。無論是否使用輔助技術，此動作應執行相同的操作。
- `'expand'` - 僅限 Android - 此動作「展開」元件，以便 TalkBack 會宣布「已展開」提示。
- `'collapse'` - 僅限 Android - 此動作「摺疊」元件，以便 TalkBack 會宣布「已摺疊」提示。

`label` 欄位對於標準動作是可選的，且通常不被輔助技術使用。對於自訂動作，它是一個本地化的字串，包含要呈現給使用者的動作描述。

為了處理動作請求，元件必須實作一個 `onAccessibilityAction` 函數。此函數的唯一參數是一個包含要執行動作名稱的事件。以下來自 RNTester 的範例展示了如何建立一個定義並處理多個自訂動作的元件。

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

## 檢查螢幕閱讀器是否啟用

`AccessibilityInfo` API 允許您確定螢幕閱讀器當前是否處於活動狀態。詳情請參閱 [AccessibilityInfo 文件](accessibilityinfo)。

## 發送輔助功能事件 <div class="label android">Android</div>

有時在 UI 元件上觸發輔助功能事件很有用（例如當自訂視圖出現在螢幕上或將輔助功能焦點設置到視圖時）。原生 UIManager 模組為此目的公開了一個方法 `sendAccessibilityEvent`。它接受兩個參數：視圖標籤和事件類型。支援的事件類型有 `typeWindowStateChanged`、`typeViewFocused` 和 `typeViewClicked`。

```tsx
import {Platform, UIManager, findNodeHandle} from 'react-native';

if (Platform.OS === 'android') {
  UIManager.sendAccessibilityEvent(
    findNodeHandle(this),
    UIManager.AccessibilityEventTypes.typeViewFocused,
  );
}
```

## 測試 TalkBack 支援 <div class="label android">Android</div>

要啟用 TalkBack，請在您的 Android 設備或模擬器上打開「設定」應用程式。點擊「輔助功能」，然後點擊「TalkBack」。切換「使用服務」開關以啟用或停用它。

Android 模擬器預設未安裝 TalkBack。您可以通過 Google Play 商店在模擬器上安裝 TalkBack。請確保選擇安裝了 Google Play 商店的模擬器。這些在 Android Studio 中可用。

您可以使用音量鍵快捷鍵來切換 TalkBack。要開啟音量鍵快捷鍵，請打開「設定」應用程式，然後點擊「輔助功能」。在頂部，開啟音量鍵快捷鍵。

要使用音量鍵快捷鍵，按住兩個音量鍵 3 秒以啟動輔助工具。

此外，如果您願意，也可以通過命令列切換 TalkBack：

```shell
# disable
adb shell settings put secure enabled_accessibility_services com.android.talkback/com.google.android.marvin.talkback.TalkBackService

# enable
adb shell settings put secure enabled_accessibility_services com.google.android.marvin.talkback/com.google.android.marvin.talkback.TalkBackService
```

## 測試 VoiceOver 支援 <div class="label ios">iOS</div>

要在您的 iOS 或 iPadOS 設備上啟用 VoiceOver，請打開「設定」應用程式，點擊「一般」，然後點擊「輔助功能」。在那裡您會找到許多工具，供人們啟用以使其設備更易於使用，包括 VoiceOver。要啟用 VoiceOver，請點擊「視覺」下的「VoiceOver」，然後切換頂部出現的開關。

在「輔助使用」設定的最底部，有一個「輔助使用快速鍵」。你可以透過連續按三下主畫面按鈕來切換 VoiceOver。

VoiceOver 無法透過模擬器使用，但你可以使用 Xcode 的「輔助使用檢查器」來透過應用程式使用 macOS 的 VoiceOver。請注意，最好還是使用實體裝置進行測試，因為 macOS 的 VoiceOver 可能會導致不同的體驗。

## 其他資源

- [讓 React Native 應用程式具備無障礙功能](https://engineering.fb.com/ios/making-react-native-apps-accessible/)