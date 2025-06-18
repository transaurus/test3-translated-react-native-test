---
id: accessibility
title: Accessibility
description: Create mobile apps accessible to assistive technology with React Native's suite of APIs designed to work with Android and iOS.
---

Android 和 iOS 都提供 API 來整合應用程式與輔助技術，例如內建的螢幕閱讀器 VoiceOver (iOS) 和 TalkBack (Android)。React Native 提供互補的 API，讓您的應用程式能服務所有使用者。

:::info
Android 和 iOS 的方法略有不同，因此 React Native 的實作可能因平台而異。
:::

## 無障礙屬性

### `accessible`

當設為 `true` 時，表示該視圖可被輔助技術（如螢幕閱讀器和硬體鍵盤）發現。請注意，這並不意味著 VoiceOver 或 TalkBack 一定會聚焦該視圖。這可能有多種原因，例如 VoiceOver 不允許嵌套的無障礙元素，或 TalkBack 選擇聚焦某些父元素。

預設情況下，所有可觸摸元素都是可訪問的。

在 Android 上，`accessible` 會被轉換為原生的 [`focusable`](<https://developer.android.com/reference/android/view/View#setFocusable(boolean)>)。在 iOS 上，它會被轉換為原生的 [`isAccessibilityElement`](https://developer.apple.com/documentation/uikit/uiaccessibilityelement/isaccessibilityelement?language=objc)。

```tsx
<View>
  <View accessible={true} />
  <View />
</View>
```

在上面的例子中，無障礙焦點僅適用於第一個具有 `accessible` 屬性的子視圖，而不適用於沒有 `accessible` 屬性的父視圖或兄弟視圖。

### `accessibilityLabel`

當一個視圖被標記為可訪問時，最好在視圖上設置一個 `accessibilityLabel`，這樣使用 VoiceOver 或 TalkBack 的人就知道他們選擇了什麼元素。螢幕閱讀器會在選擇相關元素時朗讀此字串。

要使用此功能，請在您的 View、Text 或 Touchable 上將 `accessibilityLabel` 屬性設置為自定義字串：

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

在上面的例子中，TouchableOpacity 元素的 `accessibilityLabel` 預設為 "Press me!"。該標籤是通過串接所有 Text 節點子元素並以空格分隔來構建的。

### `accessibilityLabelledBy` <div class="label android">Android</div>

用於構建複雜表單的對其他元素 [nativeID](view.md#nativeid) 的引用。`accessibilityLabelledBy` 的值應與相關元素的 `nativeID` 匹配：

```tsx
<View>
  <Text nativeID="formLabel">Label for Input Field</Text>
  <TextInput
    accessibilityLabel="input"
    accessibilityLabelledBy="formLabel"
  />
</View>
```

在上面的例子中，當聚焦於 TextInput 時，螢幕閱讀器會朗讀 `Input, Edit Box for Label for Input Field`。

### `accessibilityHint`

無障礙提示可用於在無障礙標籤本身不夠清楚時，向使用者提供有關操作結果的額外上下文。

在您的 View、Text 或 Touchable 上提供 `accessibilityHint` 屬性一個自定義字串：

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

在上面的例子中，如果使用者在設備的 VoiceOver 設置中啟用了提示，VoiceOver 會在標籤後朗讀提示。有關 `accessibilityHint` 的更多指南，請參閱 [iOS 開發者文檔](https://developer.apple.com/documentation/objectivec/nsobject/1615093-accessibilityhint)。

<div class="label android basic">Android</div>

在上面的例子中，TalkBack 會在標籤後朗讀提示。目前，Android 上的提示無法關閉。

### `accessibilityLanguage` <div class="label ios">iOS</div>

通過使用 `accessibilityLanguage` 屬性，螢幕閱讀器將理解在朗讀元素的**標籤**、**值**和**提示**時應使用哪種語言。提供的字串值必須遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

```tsx
<View
  accessible={true}
  accessibilityLabel="Pizza"
  accessibilityLanguage="it-IT">
  <Text>🍕</Text>
</View>
```

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

在 iOS 和 iPadOS 中，反轉螢幕顏色是一項為色盲、低視力或視覺障礙使用者提供的無障礙功能。如果您有不想在此設定開啟時被反轉的視圖（例如照片），請將此屬性設為 `true`。

### `accessibilityLiveRegion` <div class="label android">Android</div>

當元件動態變化時，我們希望 TalkBack 能通知終端使用者。這可透過 `accessibilityLiveRegion` 屬性實現，其可設為 `none`、`polite` 和 `assertive`：

- **none** 無障礙服務不應宣讀此視圖的變更。
- **polite** 無障礙服務應宣讀此視圖的變更。
- **assertive** 無障礙服務應中斷當前語音以立即宣讀此視圖的變更。

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

上述範例中，`addOne` 方法會改變狀態變數 `count`。當 TouchableWithoutFeedback 被觸發時，由於 Text 視圖設有 `accessibilityLiveRegion="polite"` 屬性，TalkBack 會朗讀其中的文字。

### `accessibilityRole`

`accessibilityRole` 向輔助技術使用者傳達元件的用途。

`accessibilityRole` 可為以下值之一：

- **adjustable** 用於可「調整」的元素（如滑桿）。
- **alert** 用於包含需向使用者展示重要文字的元素。
- **button** 用於應被視為按鈕的元素。
- **checkbox** 用於表示可勾選、取消勾選或處於混合勾選狀態的核取方塊。
- **combobox** 用於表示組合框，允許使用者在多個選項中選擇。
- **header** 用於作為內容區段標題的元素（如導覽列標題）。
- **image** 用於應被視為圖像的元素，可與按鈕或連結結合使用。
- **imagebutton** 用於應被視為按鈕且同時是圖像的元素。
- **keyboardkey** 用於作為鍵盤按鍵的元素。
- **link** 用於應被視為連結的元素。
- **menu** 用於表示選單的元件。
- **menubar** 用於作為多個選單容器的元件。
- **menuitem** 用於表示選單中的項目。
- **none** 用於沒有角色的元素。
- **progressbar** 用於表示任務進度的元件。
- **radio** 用於表示單選按鈕。
- **radiogroup** 用於表示單選按鈕群組。
- **scrollbar** 用於表示捲軸。
- **search** 用於文字輸入框同時應被視為搜尋欄位的情況。
- **spinbutton** 用於表示可開啟選項清單的按鈕。
- **summary** 用於應用程式首次啟動時可提供當前狀態快速摘要的元素。
- **switch** 用於表示可開關的切換開關。
- **tab** 用於表示分頁標籤。
- **tablist** 用於表示分頁標籤清單。
- **text** 用於應被視為靜態不可變文字的元素。
- **timer** 用於表示計時器。
- **togglebutton** 用於表示切換按鈕，需搭配 accessibilityState 的 checked 屬性指示按鈕是否處於開啟狀態。
- **toolbar** 用於表示工具列（動作按鈕或元件的容器）。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 搭配使用表示網格，會為 Android 的 GridView 添加網格內/外宣告。

### `accessibilityShowsLargeContentViewer` <div class="label ios">iOS</div>

決定使用者在元素上長按時是否顯示大型內容檢視器的布林值。

需 iOS 13.0 或更高版本。

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

代表元件的當前值。可以是元件值的文字描述，或是針對範圍型元件（如滑桿和進度條）包含範圍資訊（最小值、當前值和最大值）。

`accessibilityValue` 是一個物件，包含以下欄位：

| Name | Description                                                                                    | Type    | Required                  |
| ---- | ---------------------------------------------------------------------------------------------- | ------- | ------------------------- |
| min  | The minimum value of this component's range.                                                   | integer | Required if `now` is set. |
| max  | The maximum value of this component's range.                                                   | integer | Required if `now` is set. |
| now  | The current value of this component's range.                                                   | integer | No                        |
| text | A textual description of this component's value. Will override `min`, `now`, and `max` if set. | string  | No                        |

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

一個布林值，指示 VoiceOver 是否應忽略接收者同層級視圖中的元素。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityViewIsModal` 設為 `true` 會導致 VoiceOver 忽略視圖 `A` 中的元素。另一方面，如果視圖 `B` 包含子視圖 `C` 並將 `accessibilityViewIsModal` 設為 `true`，VoiceOver 則不會忽略視圖 `A` 中的元素。

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

一個布林值，指示此無障礙元素內包含的無障礙元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityElementsHidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。這類似於 Android 的屬性 `importantForAccessibility="no-hide-descendants"`。

### `aria-valuemax`

代表範圍型元件（如滑桿和進度條）的最大值。

### `aria-valuemin`

代表範圍型元件（如滑桿和進度條）的最小值。

### `aria-valuenow`

代表範圍型元件（如滑桿和進度條）的當前值。

### `aria-valuetext`

代表元件的文字描述。

### `aria-busy`

指示元素正在修改中，輔助技術可能需要等待變更完成後再通知使用者。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-checked`

指示可勾選元素的狀態。此欄位可以是布林值或字串 "mixed" 以表示混合勾選框。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

### `aria-disabled`

指示元素是可感知的但已禁用，因此不可編輯或操作。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-expanded`

指示可展開元素當前是展開還是折疊狀態。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-hidden`

指示此無障礙元素內包含的無障礙元素是否隱藏。

舉例來說，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-label`

定義一個字串值，用於標記互動元素。

| Type   |
| ------ |
| string |

### `aria-labelledby` <div class="label android">Android</div>

標識出用於標記當前元素的關聯元素。`aria-labelledby` 的值應與關聯元素的 [`nativeID`](view.md#nativeid) 相匹配：

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

布林值，表示 VoiceOver 是否應忽略接收者同層級視圖中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-selected`

表示可選元素當前是否被選中。

| Type    |
| ------- |
| boolean |

### `importantForAccessibility` <div class="label android">Android</div>

當兩個重疊的 UI 組件擁有相同父元素時，預設的輔助焦點可能出現不可預測的行為。`importantForAccessibility` 屬性通過控制視圖是否觸發輔助事件及是否向輔助服務報告來解決此問題。可設為 `auto`、`yes`、`no` 或 `no-hide-descendants`（最後一個值會強制輔助服務忽略該組件及其所有子元素）。

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

在上述範例中，`yellow` 佈局及其子元素對 TalkBack 和其他所有輔助服務完全不可見。因此我們可以使用同父元素的重疊視圖而不會混淆 TalkBack。

### `onAccessibilityEscape` <div class="label ios">iOS</div>

將此屬性賦予一個自訂函式，當有人執行「退出」手勢（雙指 Z 形手勢）時會呼叫該函式。退出函式應在用戶界面中按層級向上移動，這可能意味著在導覽層級中向上或返回，或關閉模態用戶界面。如果選定元素沒有 `onAccessibilityEscape` 函式，系統會嘗試向上遍歷視圖層級，直到找到具有該函式的視圖或發出提示音表示找不到。

### `onAccessibilityTap` <div class="label ios">iOS</div>

使用此屬性賦予一個自訂函式，當有人在選中可訪問元素時雙擊激活它時會呼叫該函式。

### `onMagicTap` <div class="label ios">iOS</div>

將此屬性賦予一個自訂函式，當有人執行「魔法點擊」手勢（雙指雙擊）時會呼叫該函式。魔法點擊函式應執行用戶在組件上最相關的操作。在 iPhone 的電話應用中，魔法點擊接聽來電或結束當前通話。如果選定元素沒有 `onMagicTap` 函式，系統會向上遍歷視圖層級直到找到具有該函式的視圖。

### `role`

`role` 用於傳達元件的用途，其優先級高於 [`accessibilityRole`](accessibility#accessibilityrole) 屬性。

`role` 可以是以下其中之一：

- **alert** 當元素包含需要向用戶展示的重要文本時使用。
- **button** 當元素應被視為按鈕時使用。
- **checkbox** 當元素代表可勾選、取消勾選或處於混合勾選狀態的複選框時使用。
- **combobox** 當元素代表組合框，允許用戶從多個選項中選擇時使用。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 一起使用，表示網格。為 Android 的 GridView 添加網格內/外公告。
- **heading** 當元素作為內容區段的標題時使用（例如導航欄的標題）。
- **img** 當元素應被視為圖像時使用。可以與按鈕或鏈接等結合使用。
- **link** 當元素應被視為鏈接時使用。
- **list** 用於標識項目列表。
- **listitem** 用於標識列表中的項目。
- **menu** 當元件是選擇菜單時使用。
- **menubar** 當元件是多個菜單的容器時使用。
- **menuitem** 用於表示菜單中的項目。
- **none** 當元素沒有角色時使用。
- **presentation** 當元素沒有角色時使用。
- **progressbar** 用於表示指示任務進度的元件。
- **radio** 用於表示單選按鈕。
- **radiogroup** 用於表示一組單選按鈕。
- **scrollbar** 用於表示滾動條。
- **searchbox** 當文本字段元素也應被視為搜索字段時使用。
- **slider** 當元素可以「調整」時使用（例如滑塊）。
- **spinbutton** 用於表示打開選擇列表的按鈕。
- **summary** 當元素可用於在應用首次啟動時提供當前條件的快速摘要時使用。
- **switch** 用於表示可以開啟和關閉的開關。
- **tab** 用於表示標籤頁。
- **tablist** 用於表示標籤頁列表。
- **timer** 用於表示計時器。
- **toolbar** 用於表示工具欄（操作按鈕或元件的容器）。

## 無障礙操作

無障礙操作允許輔助技術以程式化的方式調用元件的操作。要支持無障礙操作，元件必須做兩件事：

- 通過 `accessibilityActions` 屬性定義其支持的操作列表。
- 實現 `onAccessibilityAction` 函數以處理操作請求。

`accessibilityActions` 屬性應包含一個操作對象列表。每個操作對象應包含以下字段：

| Name  | Type   | Required |
| ----- | ------ | -------- |
| name  | string | Yes      |
| label | string | No       |

操作可以代表標準操作（例如點擊按鈕或調整滑塊），也可以是特定於某個元件的自定義操作（例如刪除電子郵件）。`name` 字段對於標準和自定義操作都是必需的，但 `label` 對於標準操作是可選的。

當添加對標準操作的支持時，`name` 必須是以下之一：

- `'magicTap'` - 僅限 iOS - 當 VoiceOver 焦點位於元件內或元件上時，使用者用兩指雙擊。
- `'escape'` - 僅限 iOS - 當 VoiceOver 焦點位於元件內或元件上時，使用者執行兩指擦除手勢（左、右、左）。
- `'activate'` - 啟動元件。無論是否使用輔助技術，此操作應執行相同的動作。當螢幕閱讀器使用者雙擊元件時觸發。
- `'increment'` - 增加可調整元件。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點置於其上並向上滑動時，VoiceOver 會生成此動作。在 Android 上，當使用者將輔助功能焦點置於元件上並按下音量增加按鈕時，TalkBack 會生成此動作。
- `'decrement'` - 減少可調整元件。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點置於其上並向下滑動時，VoiceOver 會生成此動作。在 Android 上，當使用者將輔助功能焦點置於元件上並按下音量減少按鈕時，TalkBack 會生成此動作。
- `'longpress'` - 僅限 Android - 當使用者將輔助功能焦點置於元件上，然後雙擊並按住一根手指在螢幕上時，會生成此動作。無論是否使用輔助技術，此操作應執行相同的動作。
- `'expand'` - 僅限 Android - 此動作會「展開」元件，以便 TalkBack 會宣布「已展開」提示。
- `'collapse'` - 僅限 Android - 此動作會「摺疊」元件，以便 TalkBack 會宣布「已摺疊」提示。

`label` 欄位對於標準動作是可選的，且通常不被輔助技術使用。對於自訂動作，它是一個本地化的字串，包含要呈現給使用者的動作描述。

要處理動作請求，元件必須實作一個 `onAccessibilityAction` 函數。此函數的唯一參數是一個包含要執行動作名稱的事件。以下來自 RNTester 的範例展示了如何建立一個定義並處理多個自訂動作的元件。

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

`AccessibilityInfo` API 允許您確定當前是否有螢幕閱讀器處於活動狀態。詳情請參閱 [AccessibilityInfo 文件](accessibilityinfo)。

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

要啟用 TalkBack，請在您的 Android 裝置或模擬器上打開「設定」應用程式。點擊「輔助功能」，然後點擊「TalkBack」。切換「使用服務」開關以啟用或停用它。

Android 模擬器預設未安裝 TalkBack。您可以通過 Google Play 商店在模擬器上安裝 TalkBack。請確保選擇一個安裝了 Google Play 商店的模擬器。這些在 Android Studio 中可用。

您可以使用音量鍵快捷方式來切換 TalkBack。要開啟音量鍵快捷方式，請打開「設定」應用程式，然後點擊「輔助功能」。在頂部，開啟音量鍵快捷方式。

要使用音量鍵快捷方式，按住兩個音量鍵 3 秒以啟動輔助工具。

此外，如果您願意，也可以通過命令列切換 TalkBack：

```shell
# disable
adb shell settings put secure enabled_accessibility_services com.android.talkback/com.google.android.marvin.talkback.TalkBackService

# enable
adb shell settings put secure enabled_accessibility_services com.google.android.marvin.talkback/com.google.android.marvin.talkback.TalkBackService
```

## 測試 VoiceOver 支援 <div class="label ios">iOS</div>

要在您的 iOS 或 iPadOS 裝置上啟用 VoiceOver，請打開「設定」應用程式，點擊「一般」，然後點擊「輔助功能」。在那裡，您會找到許多工具，供人們啟用以使其裝置更易於使用，包括 VoiceOver。要啟用 VoiceOver，請點擊「視覺」下的「VoiceOver」，並切換頂部出現的開關。

在「輔助使用」設定的最底部，有一個「輔助使用快速鍵」。你可以使用此功能，透過快速按三下主畫面按鈕來切換 VoiceOver。

VoiceOver 無法透過模擬器使用，但你可以使用 Xcode 的「輔助使用檢查器」，透過應用程式來使用 macOS 的 VoiceOver。請注意，最好還是使用實體裝置進行測試，因為 macOS 的 VoiceOver 可能會導致不同的體驗。

## 其他資源

- [打造無障礙的 React Native 應用程式](https://engineering.fb.com/ios/making-react-native-apps-accessible/)