---
id: accessibility
title: Accessibility
description: Create mobile apps accessible to assistive technology with React Native's suite of APIs designed to work with Android and iOS.
---

Android 和 iOS 都提供 API 讓應用程式能與輔助技術（如內建的螢幕閱讀器 VoiceOver（iOS）和 TalkBack（Android））整合。React Native 提供互補的 API，讓您的應用程式能服務所有使用者。

:::info
Android 和 iOS 的方法略有不同，因此 React Native 的實作可能會因平台而異。
:::

## 無障礙屬性

### `accessible`

當設為 `true` 時，表示該視圖是一個無障礙元素。當視圖是無障礙元素時，它會將其子元素分組為一個可選取的元件。預設情況下，所有可觸碰元素都是可無障礙的。

在 Android 上，React Native View 的 `accessible={true}` 屬性會被轉換為原生的 `focusable={true}`。

```tsx
<View accessible={true}>
  <Text>text one</Text>
  <Text>text two</Text>
</View>
```

在上面的例子中，無障礙焦點僅適用於具有 `accessible` 屬性的父視圖，而不適用於「text one」和「text two」這兩個單獨的視圖。

### `accessibilityLabel`

當視圖被標記為可無障礙時，最好在視圖上設置一個 `accessibilityLabel`，這樣使用 VoiceOver 或 TalkBack 的人就知道他們選擇了什麼元素。螢幕閱讀器會在相關元素被選中時朗讀這個字串。

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

在上面的例子中，TouchableOpacity 元素上的 `accessibilityLabel` 預設為「Press me!」。該標籤是通過將所有 Text 節點子元素以空格分隔連接起來構建的。

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

在上面的例子中，當焦點位於 TextInput 上時，螢幕閱讀器會朗讀「Input, Edit Box for Label for Input Field」。

### `accessibilityHint`

當僅從無障礙標籤無法清楚了解操作結果時，可以使用無障礙提示為用戶提供額外的上下文。

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

在上面的例子中，如果用戶在設備的 VoiceOver 設置中啟用了提示，VoiceOver 會在標籤後朗讀提示。有關 `accessibilityHint` 的指南，請參閱 [iOS 開發者文檔](https://developer.apple.com/documentation/objectivec/nsobject/1615093-accessibilityhint)。

<div class="label android basic">Android</div>

在上面的例子中，TalkBack 會在標籤後朗讀提示。目前，Android 上的提示無法關閉。

### `accessibilityLanguage` <div class="label ios">iOS</div>

通過使用 `accessibilityLanguage` 屬性，螢幕閱讀器會理解在朗讀元素的**標籤**、**值**和**提示**時應使用哪種語言。提供的字串值必須遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

```tsx
<View
  accessible={true}
  accessibilityLabel="Pizza"
  accessibilityLanguage="it-IT">
  <Text>🍕</Text>
</View>
```

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

反轉螢幕顏色是 iOS 和 iPadOS 中為色盲、低視力或視力障礙人士提供的無障礙功能。如果有一個視圖您不希望在此設置開啟時反轉（可能是照片），請將此屬性設為 `true`。

### `accessibilityLiveRegion` <div class="label android">Android</div>

當元件動態變化時，我們希望 TalkBack 能通知終端使用者。這可透過 `accessibilityLiveRegion` 屬性實現，其可設為 `none`、`polite` 和 `assertive`：

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

上述範例中，方法 `addOne` 會改變狀態變數 `count`。當觸發 TouchableWithoutFeedback 時，由於 Text 視圖設有 `accessibilityLiveRegion="polite"` 屬性，TalkBack 會朗讀其中的文字。

### `accessibilityRole`

`accessibilityRole` 向輔助技術使用者傳達元件的用途。

`accessibilityRole` 可為下列值之一：

- **adjustable** 用於可「調整」的元件（如滑桿）。
- **alert** 用於包含需向使用者呈現重要文字的元件。
- **button** 用於應被視為按鈕的元件。
- **checkbox** 用於代表可勾選、取消勾選或混合勾選狀態的核取方塊。
- **combobox** 用於代表允許使用者從多個選項中選擇的組合框。
- **header** 用於作為內容區段標題的元件（如導覽列標題）。
- **image** 用於應被視為圖像的元件，可與按鈕或連結結合使用。
- **imagebutton** 用於應被視為按鈕且同時是圖像的元件。
- **keyboardkey** 用於作為鍵盤按鍵的元件。
- **link** 用於應被視為連結的元件。
- **menu** 用於作為選單的元件。
- **menubar** 用於作為多個選單容器的元件。
- **menuitem** 用於代表選單中的項目。
- **none** 用於無角色的元件。
- **progressbar** 用於代表任務進度指示器的元件。
- **radio** 用於代表單選按鈕。
- **radiogroup** 用於代表單選按鈕群組。
- **scrollbar** 用於代表捲軸。
- **search** 用於應同時被視為搜尋欄位的文字欄位元件。
- **spinbutton** 用於代表可開啟選項清單的按鈕。
- **summary** 用於應用程式首次啟動時可提供當前狀態快速摘要的元件。
- **switch** 用於代表可開關的切換開關。
- **tab** 用於代表分頁標籤。
- **tablist** 用於代表分頁標籤清單。
- **text** 用於應被視為靜態不可變文字的元件。
- **timer** 用於代表計時器。
- **togglebutton** 用於代表切換按鈕，需搭配 accessibilityState 的 checked 屬性指示按鈕是否處於切換狀態。
- **toolbar** 用於代表工具列（動作按鈕或元件的容器）。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 搭配使用以代表網格，會為 Android 的 GridView 添加網格內/外公告。

### `accessibilityState`

向輔助技術使用者描述元件的當前狀態。

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

代表元件的當前值。可以是元件值的文字描述，或針對範圍型元件（如滑桿和進度條）包含範圍資訊（最小值、當前值和最大值）。

`accessibilityValue` 是一個物件，包含以下欄位：

| Name | Description                                                                                    | Type    | Required                  |
| ---- | ---------------------------------------------------------------------------------------------- | ------- | ------------------------- |
| min  | The minimum value of this component's range.                                                   | integer | Required if `now` is set. |
| max  | The maximum value of this component's range.                                                   | integer | Required if `now` is set. |
| now  | The current value of this component's range.                                                   | integer | No                        |
| text | A textual description of this component's value. Will override `min`, `now`, and `max` if set. | string  | No                        |

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

一個布林值，用於指示 VoiceOver 是否應忽略接收者同層級視圖中的元素。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityViewIsModal` 設為 `true` 會導致 VoiceOver 忽略視圖 `A` 中的元素。另一方面，如果視圖 `B` 包含子視圖 `C` 且你將視圖 `C` 的 `accessibilityViewIsModal` 設為 `true`，VoiceOver 則不會忽略視圖 `A` 中的元素。

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

一個布林值，用於指示此無障礙元素內包含的元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityElementsHidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。這類似於 Android 的屬性 `importantForAccessibility="no-hide-descendants"`。

### `aria-valuemax`

表示範圍型元件（如滑塊和進度條）的最大值。

### `aria-valuemin`

表示範圍型元件（如滑塊和進度條）的最小值。

### `aria-valuenow`

表示範圍型元件（如滑塊和進度條）的當前值。

### `aria-valuetext`

表示元件的文字描述。

### `aria-busy`

表示元素正在被修改，輔助技術可能需要等待修改完成後再通知使用者更新。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-checked`

表示可勾選元素的狀態。此欄位可以接受布林值或字串 "mixed" 來表示混合狀態的核取方塊。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

### `aria-disabled`

表示元素是可感知的但已被禁用，因此不可編輯或操作。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-expanded`

表示可展開元素當前是展開還是折疊狀態。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-hidden`

表示此無障礙元素內包含的元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-label`

定義一個字串值，用於標記互動式元素。

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

表示元素將被更新，並描述使用者代理、輔助技術和使用者可以從即時區域期待的更新類型。

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

當兩個重疊的 UI 組件擁有相同父元素時，預設的無障礙焦點可能會有不可預測的行為。`importantForAccessibility` 屬性可透過控制視圖是否觸發無障礙事件及是否向無障礙服務報告來解決此問題。可設置為 `auto`、`yes`、`no` 或 `no-hide-descendants`（最後一個值會強制無障礙服務忽略該組件及其所有子組件）。

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

在上述範例中，`yellow` 佈局及其子元素對 TalkBack 和其他所有無障礙服務完全不可見。因此我們可以使用同父元素的重疊視圖而不會混淆 TalkBack。

### `onAccessibilityEscape` <div class="label ios">iOS</div>

將此屬性賦予一個自訂函數，當有人執行「escape」手勢（雙指 Z 形手勢）時會呼叫該函數。escape 函數應在用戶界面中按層級後退，這可能意味著在導航層級中向上或返回，或關閉模態用戶界面。如果選定元素沒有 `onAccessibilityEscape` 函數，系統將嘗試向上遍歷視圖層級，直到找到具有該函數的視圖，或發出提示音表示找不到。

### `onAccessibilityTap` <div class="label ios">iOS</div>

使用此屬性賦予一個自訂函數，當有人在選中可訪問元素時雙擊它來激活時會呼叫該函數。

### `onMagicTap` <div class="label ios">iOS</div>

將此屬性賦予一個自訂函數，當有人執行「magic tap」手勢（雙指雙擊）時會呼叫該函數。magic tap 函數應執行用戶在組件上最相關的操作。在 iPhone 的電話應用中，magic tap 接聽電話或結束當前通話。如果選定元素沒有 `onMagicTap` 函數，系統將向上遍歷視圖層級，直到找到具有該函數的視圖。

### `role`

`role` 傳達組件的用途，並優先於 [`accessibilityRole`](accessibility#accessibilityrole) 屬性。

`role` 可以是以下之一：

- **alert** 當元素包含需要向用戶展示的重要文字時使用。
- **button** 當元素應被視為按鈕時使用。
- **checkbox** 當元素代表可勾選、取消勾選或處於混合勾選狀態的核取方塊時使用。
- **combobox** 當元素代表可讓用戶從多個選項中選擇的組合框時使用。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 一起使用時，表示網格。為 Android 的 GridView 添加網格內/外公告功能。
- **heading** 當元素作為內容區段的標題時使用（例如導覽列的標題）。
- **img** 當元素應被視為圖片時使用。可與按鈕或連結等組合使用。
- **link** 當元素應被視為連結時使用。
- **list** 用於識別項目清單。
- **listitem** 用於識別清單中的單一項目。
- **menu** 當元件是選單選項時使用。
- **menubar** 當元件是多個選單的容器時使用。
- **menuitem** 用於表示選單內的項目。
- **none** 當元素沒有任何角色時使用。
- **presentation** 當元素沒有任何角色時使用。
- **progressbar** 用於表示顯示任務進度的元件。
- **radio** 用於表示單選按鈕。
- **radiogroup** 用於表示一組單選按鈕。
- **scrollbar** 用於表示捲軸。
- **searchbox** 當文字輸入框元素也應被視為搜尋欄位時使用。
- **slider** 當元素可被「調整」時使用（例如滑桿）。
- **spinbutton** 用於表示可開啟選項清單的按鈕。
- **summary** 當元素可用於在應用程式首次啟動時提供當前條件的快速摘要時使用。
- **switch** 用於表示可開啟或關閉的開關。
- **tab** 用於表示標籤頁。
- **tablist** 用於表示標籤頁清單。
- **timer** 用於表示計時器。
- **toolbar** 用於表示工具列（動作按鈕或元件的容器）。

## 無障礙操作

無障礙操作允許輔助技術以程式化方式呼叫元件的操作。要支援無障礙操作，元件必須完成兩件事：

- 透過 `accessibilityActions` 屬性定義其支援的操作清單。
- 實作 `onAccessibilityAction` 函數來處理操作請求。

`accessibilityActions` 屬性應包含操作物件的清單。每個操作物件應包含以下欄位：

| Name  | Type   | Required |
| ----- | ------ | -------- |
| name  | string | Yes      |
| label | string | No       |

操作可以是標準操作（例如點擊按鈕或調整滑桿），也可以是特定元件的自訂操作（例如刪除電子郵件）。標準和自訂操作都必須提供 `name` 欄位，但標準操作的 `label` 欄位是選填的。

當添加對標準操作的支援時，`name` 必須是以下之一：

- `'magicTap'` - 僅限 iOS - 當 VoiceOver 焦點位於元件內或元件上時，使用者用兩指雙擊。
- `'escape'` - 僅限 iOS - 當 VoiceOver 焦點位於元件內或元件上時，使用者執行兩指擦除手勢（左、右、左）。
- `'activate'` - 啟動元件。無論是否使用輔助技術，此動作應執行相同操作。當螢幕閱讀器使用者雙擊元件時觸發。
- `'increment'` - 增加可調整元件值。在 iOS 上，當元件角色為 `'adjustable'` 且使用者聚焦後向上滑動時，VoiceOver 會產生此動作。在 Android 上，當使用者將輔助功能焦點放在元件上並按下音量增加鍵時，TalkBack 會產生此動作。
- `'decrement'` - 減少可調整元件值。在 iOS 上，當元件角色為 `'adjustable'` 且使用者聚焦後向下滑動時，VoiceOver 會產生此動作。在 Android 上，當使用者將輔助功能焦點放在元件上並按下音量減少鍵時，TalkBack 會產生此動作。
- `'longpress'` - 僅限 Android - 當使用者將輔助功能焦點放在元件上，然後雙擊並按住螢幕時會產生此動作。無論是否使用輔助技術，此動作應執行相同操作。

`label` 欄位對標準動作是選填的，且通常不會被輔助技術使用。對於自訂動作，它是一個本地化字串，包含要向使用者顯示的動作描述。

要處理動作請求，元件必須實作 `onAccessibilityAction` 函式。此函式的唯一參數是一個包含要執行動作名稱的事件。以下來自 RNTester 的範例展示如何建立一個定義並處理多個自訂動作的元件。

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

`AccessibilityInfo` API 可讓您判斷當前是否有螢幕閱讀器處於活動狀態。詳情請參閱 [AccessibilityInfo 文件](accessibilityinfo)。

## 發送輔助功能事件 <div class="label android">Android</div>

有時需要在 UI 元件上觸發輔助功能事件（例如自訂視圖出現在螢幕上時，或將輔助功能焦點設定到某視圖）。原生 UIManager 模組為此提供了一個方法 `sendAccessibilityEvent`。它接受兩個參數：視圖標籤和事件類型。支援的事件類型包括 `typeWindowStateChanged`、`typeViewFocused` 和 `typeViewClicked`。

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

要啟用 TalkBack，請前往 Android 裝置或模擬器上的「設定」應用程式。點擊「輔助功能」，然後選擇「TalkBack」。切換「使用服務」開關以啟用或停用。

Android 模擬器預設未安裝 TalkBack。您可以透過 Google Play 商店在模擬器上安裝 TalkBack。請確保選擇已安裝 Google Play 商店的模擬器。這些模擬器可在 Android Studio 中找到。

您可以使用音量鍵快捷鍵來切換 TalkBack。要開啟音量鍵快捷鍵，請前往「設定」應用程式中的「輔助功能」。在最上方開啟音量鍵快捷功能。

使用音量鍵快捷鍵時，同時按住兩個音量鍵 3 秒即可啟動輔助工具。

此外，如果您偏好，也可以透過命令列切換 TalkBack：

```shell
# disable
adb shell settings put secure enabled_accessibility_services com.android.talkback/com.google.android.marvin.talkback.TalkBackService

# enable
adb shell settings put secure enabled_accessibility_services com.google.android.marvin.talkback/com.google.android.marvin.talkback.TalkBackService
```

## 測試 VoiceOver 支援 <div class="label ios">iOS</div>

要在 iOS 或 iPadOS 裝置上啟用 VoiceOver，請前往「設定」應用程式，點擊「一般」，然後選擇「輔助功能」。您會在此找到許多工具，包括 VoiceOver。要啟用 VoiceOver，請在「視覺」下方點擊「VoiceOver」，並切換頂部的開關。

在輔助功能設定的最底部有一個「輔助功能快速鍵」。您可以使用此功能透過三擊主畫面按鈕來切換 VoiceOver。

VoiceOver 無法透過模擬器使用，但您可以使用 Xcode 的「輔助功能檢查器」來透過應用程式使用 macOS 的 VoiceOver。請注意，最佳做法始終是使用實體裝置進行測試，因為 macOS 的 VoiceOver 可能會導致不同的體驗。

## 其他資源

- [打造無障礙的 React Native 應用程式](https://engineering.fb.com/ios/making-react-native-apps-accessible/)