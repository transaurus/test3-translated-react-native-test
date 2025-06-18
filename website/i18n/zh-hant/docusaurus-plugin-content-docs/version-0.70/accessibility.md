---
id: accessibility
title: Accessibility
description: Create mobile apps accessible to assistive technology with React Native's suite of APIs designed to work with Android and iOS.
---

Android 和 iOS 皆提供 API 讓應用程式能與輔助技術（如內建的螢幕閱讀器 VoiceOver（iOS）和 TalkBack（Android））整合。React Native 提供相應的 API，讓您的應用程式能服務所有使用者。

:::info
Android 和 iOS 在實作上略有不同，因此 React Native 的實作可能因平台而異。
:::

## 無障礙屬性

### `accessible`

當設為 `true` 時，表示該視圖是一個無障礙元素。當視圖為無障礙元素時，會將其子元素群組為單一可選取的元件。預設情況下，所有可觸碰元素皆為無障礙。

在 Android 上，react-native View 的 `accessible={true}` 屬性會被轉譯為原生的 `focusable={true}`。

```jsx
<View accessible={true}>
  <Text>text one</Text>
  <Text>text two</Text>
</View>
```

在上例中，我們無法分別取得「text one」和「text two」的無障礙焦點，而是會取得具有 'accessible' 屬性的父視圖焦點。

### `accessibilityLabel`

當視圖被標記為無障礙時，最佳實踐是為視圖設置 accessibilityLabel，以便使用 VoiceOver 的使用者知道他們選擇了什麼元素。當使用者選擇相關元素時，VoiceOver 會讀出此字串。

使用方式：在您的 View、Text 或 Touchable 上將 `accessibilityLabel` 屬性設為自訂字串：

```jsx
<TouchableOpacity
  accessible={true}
  accessibilityLabel="Tap me!"
  onPress={onPress}>
  <View style={styles.button}>
    <Text style={styles.buttonText}>Press me!</Text>
  </View>
</TouchableOpacity>
```

在上例中，TouchableOpacity 元素的 `accessibilityLabel` 預設為「Press me!」。標籤由所有以空格分隔的 Text 節點子元素串接而成。

### `accessibilityLabelledBy` <div class="label android">Android</div>

用於構建複雜表單的參照元素 [nativeID](view.md#nativeid)。`accessibilityLabelledBy` 的值應與相關元素的 `nativeID` 相符：

```jsx
<View>
  <Text nativeID="formLabel">Label for Input Field</Text>
  <TextInput
    accessibilityLabel="input"
    accessibilityLabelledBy="formLabel"
  />
</View>
```

在上例中，當焦點位於 TextInput 時，螢幕閱讀器會宣告「Input, Edit Box for Label for Input Field」。

### `accessibilityHint`

當無障礙標籤無法清楚表達操作結果時，無障礙提示能幫助使用者理解在無障礙元素上執行操作時會發生的結果。

使用方式：在您的 View、Text 或 Touchable 上將 `accessibilityHint` 屬性設為自訂字串：

```jsx
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

在上例中，若使用者在裝置的 VoiceOver 設定中啟用提示功能，VoiceOver 會在標籤後讀出提示。更多關於 `accessibilityHint` 的指南請參閱 [iOS 開發者文件](https://developer.apple.com/documentation/objectivec/nsobject/1615093-accessibilityhint)

<div class="label android basic">Android</div>

在上例中，TalkBack 會在標籤後讀出提示。目前 Android 上無法關閉提示功能。

### `accessibilityLanguage` <div class="label ios">iOS</div>

透過 `accessibilityLanguage` 屬性，螢幕閱讀器能理解讀取元素的**標籤**、**值**和**提示**時應使用的語言。提供的字串值必須符合 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

```jsx
<View
  accessible={true}
  accessibilityLabel="Pizza"
  accessibilityLanguage="it-IT">
  <Text>🍕</Text>
</View>
```

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

反轉螢幕色彩是一項無障礙功能，能讓iPhone和iPad對某些光敏感使用者更護眼、對色盲使用者更易辨識、對低視力使用者更清晰可辨。但有時您會有些不想被反轉的視圖（例如照片），此時可將此屬性設為`true`使特定視圖維持原色。

### `accessibilityLiveRegion` <div class="label android">Android</div>

當元件動態變化時，我們希望TalkBack能通知終端使用者。透過`accessibilityLiveRegion`屬性可實現此功能，其值可設為`none`、`polite`或`assertive`：

- **none** 無障礙服務不應通報此視圖的變更。
- **polite** 無障礙服務應通報此視圖的變更。
- **assertive** 無障礙服務應中斷當前語音立即通報此視圖的變更。

```jsx
<TouchableWithoutFeedback onPress={addOne}>
  <View style={styles.embedded}>
    <Text>Click me</Text>
  </View>
</TouchableWithoutFeedback>
<Text accessibilityLiveRegion="polite">
  Clicked {count} times
</Text>
```

上述範例中，`addOne`方法會改變狀態變數`count`。當終端使用者點擊TouchableWithoutFeedback時，由於Text視圖設有`accessibilityLiveRegion="polite"`屬性，TalkBack會朗讀其中的文字。

### `accessibilityRole`

`accessibilityRole`向輔助技術使用者傳達元件的用途。

`accessibilityRole`可為下列值之一：

- **adjustable** 用於可「調整」的元素（如滑桿）。
- **alert** 用於包含需向使用者呈現重要文字的元件。
- **button** 用於應被視為按鈕的元素。
- **checkbox** 用於代表可勾選、取消勾選或混合狀態的核取方塊。
- **combobox** 用於代表可讓使用者從多個選項中選擇的下拉式方塊。
- **header** 用於作為內容區段標題的元素（如導覽列標題）。
- **image** 用於應被視為圖像的元素，可與按鈕或連結等組合使用。
- **imagebutton** 用於應被視為按鈕且同時是圖像的元素。
- **keyboardkey** 用於作為鍵盤按鍵的元素。
- **link** 用於應被視為連結的元素。
- **menu** 用於作為選項清單的元件。
- **menubar** 用於作為多個選單容器的元件。
- **menuitem** 用於代表選單內的項目。
- **none** 用於沒有角色的元素。
- **progressbar** 用於代表顯示任務進度的元件。
- **radio** 用於代表單選按鈕。
- **radiogroup** 用於代表一組單選按鈕。
- **scrollbar** 用於代表捲軸。
- **search** 用於應同時被視為搜尋欄位的文字輸入框。
- **spinbutton** 用於代表可開啟選項清單的按鈕。
- **summary** 用於在應用程式首次啟動時提供當前狀態快速摘要的元素。
- **switch** 用於代表可切換開關狀態的元件。
- **tab** 用於代表分頁標籤。
- **tablist** 用於代表分頁標籤清單。
- **text** 用於應被視為靜態不可變文字的元件。
- **timer** 用於代表計時器。
- **togglebutton** 用於代表切換按鈕，需搭配accessibilityState的checked屬性指示開關狀態。
- **toolbar** 用於代表工具列（動作按鈕或元件的容器）。

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

使用時，請將`accessibilityState`設為具有特定定義的物件。

### `accessibilityValue`

表示元件當前值。可以是元件值的文字描述，對於範圍型元件（如滑桿和進度條），則包含範圍資訊（最小值、當前值和最大值）。

`accessibilityValue` 是一個物件，包含以下欄位：

| Name | Description                                                                                    | Type    | Required                  |
| ---- | ---------------------------------------------------------------------------------------------- | ------- | ------------------------- |
| min  | The minimum value of this component's range.                                                   | integer | Required if `now` is set. |
| max  | The maximum value of this component's range.                                                   | integer | Required if `now` is set. |
| now  | The current value of this component's range.                                                   | integer | No                        |
| text | A textual description of this component's value. Will override `min`, `now`, and `max` if set. | string  | No                        |

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

布林值，表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityViewIsModal` 設為 `true` 會導致 VoiceOver 忽略視圖 `A` 中的元素。反之，若視圖 `B` 包含子視圖 `C` 並將 `C` 的 `accessibilityViewIsModal` 設為 `true`，VoiceOver 仍不會忽略視圖 `A` 中的元素。

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

布林值，表示此無障礙元素內包含的子無障礙元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `accessibilityElementsHidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。此屬性類似 Android 的 `importantForAccessibility="no-hide-descendants"`。

### `importantForAccessibility` <div class="label android">Android</div>

當兩個同父元件的 UI 元件重疊時，預設無障礙焦點可能出現不可預測行為。`importantForAccessibility` 屬性可透過控制視圖是否觸發無障礙事件及是否回報給無障礙服務來解決此問題。可設為 `auto`、`yes`、`no` 和 `no-hide-descendants`（最後一個值會強制無障礙服務忽略該元件及其所有子元件）。

```jsx
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

上例中，`yellow` 佈局及其子元件對 TalkBack 和其他無障礙服務完全不可見。因此我們可使用同父層級的重疊視圖，而不會造成 TalkBack 混淆。

### `onAccessibilityEscape` <div class="label ios">iOS</div>

將此屬性賦予自訂函式，當使用者執行「escape」手勢（雙指 Z 形手勢）時會呼叫該函式。escape 函式應在使用者介面中依層級返回，例如在導覽層級中向上/返回，或關閉模態介面。若選定元素無 `onAccessibilityEscape` 函式，系統會嘗試向上遍歷視圖層級，直到找到符合條件的視圖或發出提示音表示找不到。

### `onAccessibilityTap` <div class="label ios">iOS</div>

使用此屬性賦予自訂函式，當使用者在選定可訪問元素時雙擊觸發該元件時會呼叫此函式。

### `onMagicTap` <div class="label ios">iOS</div>

將此屬性賦予自訂函式，當使用者執行「magic tap」手勢（雙指雙擊）時會呼叫該函式。magic tap 函式應執行使用者對元件最相關的操作。例如 iPhone 電話應用中，magic tap 可接聽或結束通話。若選定元素無 `onMagicTap` 函式，系統會向上遍歷視圖層級直到找到符合條件的視圖。

## 無障礙操作

無障礙操作允許輔助技術以程式化方式觸發元件的操作。要支援無障礙操作，元件必須完成兩件事：

- 透過 `accessibilityActions` 屬性定義支援的操作清單。
- 實作 `onAccessibilityAction` 函式來處理操作請求。

`accessibilityActions` 屬性應包含一個動作物件列表。每個動作物件應包含以下欄位：

| Name  | Type   | Required |
| ----- | ------ | -------- |
| name  | string | Yes      |
| label | string | No       |

動作可以代表標準操作（例如點擊按鈕或調整滑桿），或是特定元件的自訂操作（例如刪除電子郵件）。標準和自訂動作都必須提供 `name` 欄位，但標準動作的 `label` 欄位為選填。

當支援標準動作時，`name` 必須是以下其中之一：

- `'magicTap'` - 僅限 iOS - 當 VoiceOver 焦點位於元件上或內部時，使用者用兩指雙擊。
- `'escape'` - 僅限 iOS - 當 VoiceOver 焦點位於元件上或內部時，使用者執行兩指來回滑動手勢（左、右、左）。
- `'activate'` - 啟動元件。通常這應執行與使用者在未使用輔助技術時觸摸或點擊元件相同的操作。當螢幕閱讀器使用者雙擊元件時會觸發此動作。
- `'increment'` - 增加可調整元件的值。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點置於其上並向上滑動時，VoiceOver 會產生此動作。在 Android 上，當使用者將輔助功能焦點置於元件上並按下音量增大按鈕時，TalkBack 會產生此動作。
- `'decrement'` - 減少可調整元件的值。在 iOS 上，當元件具有 `'adjustable'` 角色且使用者將焦點置於其上並向下滑動時，VoiceOver 會產生此動作。在 Android 上，當使用者將輔助功能焦點置於元件上並按下音量減小按鈕時，TalkBack 會產生此動作。
- `'longpress'` - 僅限 Android - 當使用者將輔助功能焦點置於元件上並雙擊且按住一根手指在螢幕上時，會產生此動作。通常這應執行與使用者在未使用輔助技術時按住元件相同的操作。

`label` 欄位對標準動作是選填的，且通常不被輔助技術使用。對於自訂動作，它是一個本地化的字串，包含要向使用者呈現的動作描述。

要處理動作請求，元件必須實作 `onAccessibilityAction` 函式。此函式的唯一參數是一個包含要執行動作名稱的事件。以下來自 RNTester 的範例展示如何建立一個定義並處理多個自訂動作的元件。

```jsx
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

`AccessibilityInfo` API 可讓您判斷螢幕閱讀器當前是否處於活動狀態。詳情請參閱 [AccessibilityInfo 文件](accessibilityinfo)。

## 發送輔助功能事件 <div class="label android">Android</div>

有時需要在 UI 元件上觸發輔助功能事件（例如當自訂視圖出現在螢幕上或將輔助功能焦點設定到某個視圖時）。原生 UIManager 模組為此公開了一個方法 `sendAccessibilityEvent`。它接受兩個參數：視圖標籤和事件類型。支援的事件類型有 `typeWindowStateChanged`、`typeViewFocused` 和 `typeViewClicked`。

```jsx
import {Platform, UIManager, findNodeHandle} from 'react-native';

if (Platform.OS === 'android') {
  UIManager.sendAccessibilityEvent(
    findNodeHandle(this),
    UIManager.AccessibilityEventTypes.typeViewFocused,
  );
}
```

## 測試 TalkBack 支援 <div class="label android">Android</div>

要啟用 TalkBack，請前往 Android 裝置或模擬器上的「設定」應用程式。點擊「輔助功能」，然後選擇「TalkBack」。切換「使用服務」開關以啟用或停用它。

Android 模擬器預設未安裝 TalkBack。您可以透過 Google Play 商店在模擬器上安裝 TalkBack。請確保選擇已安裝 Google Play 商店的模擬器。這些可在 Android Studio 中找到。

您可以使用音量鍵快捷鍵來切換 TalkBack。要開啟音量鍵快捷鍵，請前往「設定」應用程式，然後選擇「輔助功能」。在頂部開啟「音量鍵快捷鍵」。

使用音量鍵快捷鍵時，按住兩個音量鍵 3 秒即可啟動輔助功能工具。

此外，如果您願意，也可以透過命令列切換 TalkBack：

```shell
# disable
adb shell settings put secure enabled_accessibility_services com.android.talkback/com.google.android.marvin.talkback.TalkBackService

# enable
adb shell settings put secure enabled_accessibility_services com.google.android.marvin.talkback/com.google.android.marvin.talkback.TalkBackService
```

## 測試 VoiceOver 支援 <div class="label ios">iOS</div>

要啟用 VoiceOver，請前往 iOS 裝置上的「設定」應用程式（模擬器不支援此功能）。點選「一般」，然後選擇「輔助使用」。在此您會找到多種提升裝置易用性的工具，例如粗體文字、增強對比度以及 VoiceOver。

要啟用 VoiceOver，請在「視覺」區段下點選「VoiceOver」，並切換頂部的開關。

在「輔助使用」設定的最底部，有一個「輔助使用快速鍵」。您可透過此功能，以三擊 Home 鍵的方式切換 VoiceOver。

## 其他資源

- [打造無障礙的 React Native 應用程式](https://engineering.fb.com/ios/making-react-native-apps-accessible/)