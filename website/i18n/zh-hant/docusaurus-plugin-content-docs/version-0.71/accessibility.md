---
id: accessibility
title: Accessibility
description: Create mobile apps accessible to assistive technology with React Native's suite of APIs designed to work with Android and iOS.
---

Android 和 iOS 皆提供 API 讓應用程式能與輔助技術整合，例如內建的螢幕閱讀器 VoiceOver (iOS) 和 TalkBack (Android)。React Native 提供相應的 API 讓您的應用程式能服務所有使用者。

:::info
Android 和 iOS 的實作方式略有差異，因此 React Native 的 API 可能因平台而有所不同。
:::

## 無障礙屬性

### `accessible`

當設為 `true` 時，表示該視圖是一個無障礙元素。當視圖被標記為無障礙元素時，會將其子元件合併為單一可選取的元件。預設情況下，所有可觸控元素都具有無障礙特性。

在 Android 上，React Native View 的 `accessible={true}` 屬性會轉換為原生的 `focusable={true}`。

```tsx
<View accessible={true}>
  <Text>text one</Text>
  <Text>text two</Text>
</View>
```

在上面的範例中，我們無法分別對「text one」和「text two」獲取無障礙焦點，而是會獲取到父視圖的焦點（因其設有 'accessible' 屬性）。

### `accessibilityLabel`

當視圖被標記為無障礙元素時，建議設置 accessibilityLabel 屬性，讓使用 VoiceOver 的使用者知道他們選取了什麼元素。當使用者選取相關元素時，VoiceOver 會朗讀此字串。

使用方式：在 View、Text 或 Touchable 元件上設置自定義字串作為 `accessibilityLabel` 屬性：

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

在上面的範例中，TouchableOpacity 元素的 `accessibilityLabel` 預設會是「Press me!」。此標籤是由所有 Text 子節點以空格連接組成。

### `accessibilityLabelledBy` <div class="label android">Android</div>

用於建立複雜表單的參考屬性，指向另一個元素的 [nativeID](view.md#nativeid)。`accessibilityLabelledBy` 的值應與相關元素的 `nativeID` 匹配：

```tsx
<View>
  <Text nativeID="formLabel">Label for Input Field</Text>
  <TextInput
    accessibilityLabel="input"
    accessibilityLabelledBy="formLabel"
  />
</View>
```

在上面的範例中，當焦點位於 TextInput 時，螢幕閱讀器會朗讀「Input, Edit Box for Label for Input Field」。

### `accessibilityHint`

當無障礙標籤無法明確表達操作結果時，無障礙提示能幫助使用者理解對無障礙元素執行操作後會發生的結果。

使用方式：在 View、Text 或 Touchable 元件上設置自定義字串作為 `accessibilityHint` 屬性：

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

在上面的範例中，若使用者在裝置的 VoiceOver 設定中啟用了提示功能，VoiceOver 會在朗讀標籤後接著朗讀提示。更多關於 `accessibilityHint` 的指南請參閱 [iOS 開發者文件](https://developer.apple.com/documentation/objectivec/nsobject/1615093-accessibilityhint)

<div class="label android basic">Android</div>

在上面的範例中，TalkBack 會在朗讀標籤後接著朗讀提示。目前 Android 上無法關閉提示功能。

### `accessibilityLanguage` <div class="label ios">iOS</div>

透過 `accessibilityLanguage` 屬性，螢幕閱讀器能理解朗讀元素的**標籤**、**值**和**提示**時應使用的語言。提供的字串值必須符合 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

```tsx
<View
  accessible={true}
  accessibilityLabel="Pizza"
  accessibilityLanguage="it-IT">
  <Text>🍕</Text>
</View>
```

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

反轉螢幕色彩是一項無障礙功能，能讓iPhone和iPad對光線敏感的使用者更舒適、色盲使用者更容易辨識，以及低視力使用者更易於辨認。但有時您會有些視圖（例如照片）不希望被反轉。此時可將此屬性設為`true`，使這些特定視圖保持原始色彩。

### `accessibilityLiveRegion` <div class="label android">Android</div>

當元件動態變化時，我們希望TalkBack能通知終端使用者。透過`accessibilityLiveRegion`屬性可實現此功能，其值可設為`none`、`polite`或`assertive`：

- **none** 無障礙服務不應通報此視圖的變更。
- **polite** 無障礙服務應通報此視圖的變更。
- **assertive** 無障礙服務應中斷當前語音，立即通報此視圖的變更。

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

上例中，`addOne`方法會改變狀態變數`count`。當終端使用者點擊TouchableWithoutFeedback時，由於Text視圖設有`accessibilityLiveRegion="polite"`屬性，TalkBack會朗讀其中的文字。

### `accessibilityRole`

`accessibilityRole`向輔助技術使用者傳達元件的用途。

`accessibilityRole`可為以下值之一：

- **adjustable** 用於可「調整」的元素（如滑桿）。
- **alert** 用於包含需向使用者展示重要文字的元素。
- **button** 用於應被視為按鈕的元素。
- **checkbox** 用於代表可勾選、取消勾選或混合狀態的核取方塊。
- **combobox** 用於代表可讓使用者選擇多個選項的下拉式方塊。
- **header** 用於作為內容區段標題的元素（如導覽列標題）。
- **image** 用於應被視為圖像的元素，可與按鈕或連結等組合使用。
- **imagebutton** 用於應被視為按鈕且同時是圖像的元素。
- **keyboardkey** 用於作為鍵盤按鍵的元素。
- **link** 用於應被視為連結的元素。
- **menu** 用於代表選單的元件。
- **menubar** 用於包含多個選單的容器元件。
- **menuitem** 用於代表選單中的項目。
- **none** 用於沒有角色的元素。
- **progressbar** 用於代表任務進度指示器。
- **radio** 用於代表單選按鈕。
- **radiogroup** 用於代表一組單選按鈕。
- **scrollbar** 用於代表捲軸。
- **search** 用於應同時被視為搜尋欄位的文字輸入框。
- **spinbutton** 用於代表可展開選項清單的按鈕。
- **summary** 用於在應用程式首次啟動時提供當前狀態的快速摘要。
- **switch** 用於代表可切換開關的元件。
- **tab** 用於代表分頁標籤。
- **tablist** 用於代表分頁標籤清單。
- **text** 用於應被視為靜態不可變文字的元件。
- **timer** 用於代表計時器。
- **togglebutton** 用於代表切換按鈕，需搭配accessibilityState的checked屬性指示切換狀態。
- **toolbar** 用於代表工具列（動作按鈕或元件的容器）。
- **grid** 與ScrollView、VirtualizedList、FlatList或SectionList搭配使用，代表網格佈局，會為Android的GridView添加網格內/外公告。

### `accessibilityState`

向輔助技術使用者描述元件的當前狀態。

`accessibilityState`是一個物件，包含以下欄位：

| Name     | Description                                                                                                                           | Type               | Required |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | -------- |
| disabled | Indicates whether the element is disabled or not.                                                                                     | boolean            | No       |
| selected | Indicates whether a selectable element is currently selected or not.                                                                  | boolean            | No       |
| checked  | Indicates the state of a checkable element. This field can either take a boolean or the "mixed" string to represent mixed checkboxes. | boolean or 'mixed' | No       |
| busy     | Indicates whether an element is currently busy or not.                                                                                | boolean            | No       |
| expanded | Indicates whether an expandable element is currently expanded or collapsed.                                                           | boolean            | No       |

使用時，請將 `accessibilityState` 設為具有特定定義的物件。

### `accessibilityValue`

代表元件的當前值。可以是元件值的文字描述，或是針對基於範圍的元件（如滑塊和進度條），包含範圍資訊（最小值、當前值和最大值）。

`accessibilityValue` 是一個物件，包含以下欄位：

| Name | Description                                                                                    | Type    | Required                  |
| ---- | ---------------------------------------------------------------------------------------------- | ------- | ------------------------- |
| min  | The minimum value of this component's range.                                                   | integer | Required if `now` is set. |
| max  | The maximum value of this component's range.                                                   | integer | Required if `now` is set. |
| now  | The current value of this component's range.                                                   | integer | No                        |
| text | A textual description of this component's value. Will override `min`, `now`, and `max` if set. | string  | No                        |

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

一個布林值，表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityViewIsModal` 設為 `true` 會導致 VoiceOver 忽略視圖 `A` 中的元素。另一方面，如果視圖 `B` 包含子視圖 `C` 並將 `accessibilityViewIsModal` 設為 `true`，VoiceOver 不會忽略視圖 `A` 中的元素。

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

一個布林值，表示此無障礙元素中包含的無障礙元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityElementsHidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。這類似於 Android 的屬性 `importantForAccessibility="no-hide-descendants"`。

### `aria-valuemax`

代表基於範圍的元件（如滑塊和進度條）的最大值。

### `aria-valuemin`

代表基於範圍的元件（如滑塊和進度條）的最小值。

### `aria-valuenow`

代表基於範圍的元件（如滑塊和進度條）的當前值。

### `aria-valuetext`

代表元件的文字描述。

### `aria-busy`

表示元素正在修改中，輔助技術可能需要等待更改完成後再通知使用者更新。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-checked`

表示可勾選元素的狀態。此欄位可以是布林值或字串 "mixed" 來表示混合勾選框。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

### `aria-disabled`

表示元素是可感知的但被禁用，因此不可編輯或操作。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-expanded`

表示可展開元素當前是展開還是折疊狀態。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-hidden`

表示此無障礙元素中包含的無障礙元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-label`

定義一個字串值，用於標記互動元素。

| Type   |
| ------ |
| string |

### `aria-labelledby` <div class="label android">Android</div>

標識出標註當前元素的關聯元素。`aria-labelledby` 的值應與關聯元素的 [`nativeID`](view.md#nativeid) 相匹配：

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

標示元素將被更新，並描述用戶代理、輔助技術和用戶可預期的動態區域更新類型。

- **off** 輔助服務不應通告此視圖的變更。
- **polite** 輔助服務應通告此視圖的變更。
- **assertive** 輔助服務應中斷當前語音以立即通告此視圖的變更。

| Type                                     | Default |
| ---------------------------------------- | ------- |
| enum(`'assertive'`, `'off'`, `'polite'`) | `'off'` |

---

### `aria-modal` <div class="label ios">iOS</div>

布林值，指示 VoiceOver 是否應忽略接收器同層級視圖中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

### `aria-selected`

指示可選元素當前是否被選中。

| Type    |
| ------- |
| boolean |

### `importantForAccessibility` <div class="label android">Android</div>

當兩個同父節點的 UI 組件重疊時，預設的無障礙焦點可能出現不可預測行為。`importantForAccessibility` 屬性通過控制視圖是否觸發無障礙事件及是否向輔助服務報告來解決此問題。可設為 `auto`、`yes`、`no` 或 `no-hide-descendants`（最後一個值會強制輔助服務忽略該組件及其所有子項）。

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

上述範例中，`yellow` 佈局及其子項對 TalkBack 和其他輔助服務完全不可見。因此我們可使用同父節點的重疊視圖而不會造成 TalkBack 混淆。

### `onAccessibilityEscape` <div class="label ios">iOS</div>

將此屬性賦予自訂函數，當用戶執行「退出」手勢（雙指 Z 形手勢）時會觸發該函數。退出函數應在用戶界面層級中向後移動，例如返回導覽層級或關閉模態界面。若選定元素無 `onAccessibilityEscape` 函數，系統將沿視圖層級向上查找，直到找到有效函數或發出提示音表示查找失敗。

### `onAccessibilityTap` <div class="label ios">iOS</div>

使用此屬性賦予自訂函數，當用戶雙擊選中的可訪問元素時會觸發該函數。

### `onMagicTap` <div class="label ios">iOS</div>

將此屬性賦予自訂函數，當用戶執行「魔法點擊」手勢（雙指雙擊）時會觸發該函數。魔法點擊函數應執行組件最相關的用戶操作。例如 iPhone 電話應用中，魔法點擊可接聽或掛斷電話。若選定元素無 `onMagicTap` 函數，系統將沿視圖層級向上查找。

### `role`

`role` 向輔助技術用戶傳達組件的用途。其優先級高於 [`accessibilityRole`](accessibility#accessibilityrole) 屬性。

`role` 可為以下值之一：

- **alert** 當元素包含需要向用戶展示的重要文字時使用。
- **button** 當元素應被視為按鈕時使用。
- **checkbox** 當元素代表可勾選、取消勾選或處於混合勾選狀態的核取方塊時使用。
- **combobox** 當元素代表組合框，允許用戶從多個選項中選擇時使用。
- **grid** 與 ScrollView、VirtualizedList、FlatList 或 SectionList 一起使用，表示網格。為 Android GridView 添加進出網格的語音提示。
- **heading** 當元素作為內容區段的標題時使用（例如導覽列的標題）。
- **img** 當元素應被視為圖片時使用。可與按鈕或連結等結合使用。
- **link** 當元素應被視為連結時使用。
- **list** 用於標識項目列表。
- **listitem** 用於標識列表中的單個項目。
- **menu** 當組件是選單時使用。
- **menubar** 當組件是多個選單的容器時使用。
- **menuitem** 用於表示選單中的項目。
- **none** 當元素沒有角色時使用。
- **presentation** 當元素沒有角色時使用。
- **progressbar** 用於表示顯示任務進度的組件。
- **radio** 用於表示單選按鈕。
- **radiogroup** 用於表示一組單選按鈕。
- **scrollbar** 用於表示捲軸。
- **searchbox** 當文字輸入框元素也應被視為搜尋欄時使用。
- **slider** 當元素可被「調整」時使用（例如滑桿）。
- **spinbutton** 用於表示可打開選項列表的按鈕。
- **summary** 當元素可用於在應用程式首次啟動時提供當前條件的快速摘要時使用。
- **switch** 用於表示可開啟和關閉的開關。
- **tab** 用於表示標籤頁。
- **tablist** 用於表示標籤頁列表。
- **timer** 用於表示計時器。
- **toolbar** 用於表示工具列（動作按鈕或組件的容器）。

## 無障礙操作

無障礙操作允許輔助技術以程式化的方式調用組件的操作。要支援無障礙操作，組件必須完成兩件事：

- 通過 `accessibilityActions` 屬性定義其支援的操作列表。
- 實現 `onAccessibilityAction` 函數以處理操作請求。

`accessibilityActions` 屬性應包含一個操作對象列表。每個操作對象應包含以下字段：

| Name  | Type   | Required |
| ----- | ------ | -------- |
| name  | string | Yes      |
| label | string | No       |

操作可以是標準操作（例如點擊按鈕或調整滑桿），也可以是特定組件的自定義操作（例如刪除電子郵件）。`name` 字段對於標準和自定義操作都是必需的，但 `label` 對於標準操作是可選的。

當添加對標準操作的支援時，`name` 必須是以下之一：

- `'magicTap'` - 僅限 iOS - 當 VoiceOver 焦點位於元件上或內部時，使用者用兩指雙擊。
- `'escape'` - 僅限 iOS - 當 VoiceOver 焦點位於元件上或內部時，使用者執行兩指擦滑手勢（左、右、左）。
- `'activate'` - 啟動元件。通常這應執行與使用者在不使用輔助技術時觸摸或點擊元件相同的動作。當螢幕閱讀器使用者雙擊元件時會產生此動作。
- `'increment'` - 增加可調整元件。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點放在其上並向上滑動時，VoiceOver 會產生此動作。在 Android 上，當使用者將輔助功能焦點放在元件上並按下音量增加按鈕時，TalkBack 會產生此動作。
- `'decrement'` - 減少可調整元件。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點放在其上並向下滑動時，VoiceOver 會產生此動作。在 Android 上，當使用者將輔助功能焦點放在元件上並按下音量減少按鈕時，TalkBack 會產生此動作。
- `'longpress'` - 僅限 Android - 當使用者將輔助功能焦點放在元件上並雙擊並按住一根手指在螢幕上時，會產生此動作。通常這應執行與使用者在不使用輔助技術時按住元件相同的動作。

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

要啟用 TalkBack，請在您的 Android 裝置或模擬器上打開「設定」應用程式。點擊「輔助功能」，然後是「TalkBack」。切換「使用服務」開關以啟用或停用它。

Android 模擬器預設未安裝 TalkBack。您可以通過 Google Play 商店在模擬器上安裝 TalkBack。請確保選擇安裝了 Google Play 商店的模擬器。這些在 Android Studio 中可用。

您可以使用音量鍵快捷鍵來切換 TalkBack。要開啟音量鍵快捷鍵，請前往「設定」應用程式，然後是「輔助功能」。在頂部，開啟「音量鍵快捷鍵」。

要使用音量鍵快捷鍵，按住兩個音量鍵 3 秒以啟動輔助功能工具。

此外，如果您願意，可以通過命令行切換 TalkBack：

```shell
# disable
adb shell settings put secure enabled_accessibility_services com.android.talkback/com.google.android.marvin.talkback.TalkBackService

# enable
adb shell settings put secure enabled_accessibility_services com.google.android.marvin.talkback/com.google.android.marvin.talkback.TalkBackService
```

## 測試 VoiceOver 支援 <div class="label ios">iOS</div>

要啟用 VoiceOver，請在您的 iOS 裝置上打開「設定」應用程式（模擬器上不可用）。點擊「一般」，然後是「輔助功能」。在那裡您會找到許多工具，人們用來使他們的裝置更易於使用，例如粗體文字、增加對比度和 VoiceOver。

要啟用 VoiceOver，點擊「視覺」下的「VoiceOver」並切換頂部出現的開關。

在「輔助使用」設定的最底部，有一個「輔助使用快速鍵」。你可以透過連續按三下主畫面按鈕來切換 VoiceOver。

## 其他資源

- [打造無障礙的 React Native 應用程式](https://engineering.fb.com/ios/making-react-native-apps-accessible/)