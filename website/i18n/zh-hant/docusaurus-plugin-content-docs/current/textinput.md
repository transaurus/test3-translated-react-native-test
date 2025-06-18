---
id: textinput
title: TextInput
---

A foundational component for inputting text into the app via a keyboard. Props provide configurability for several features, such as auto-correction, auto-capitalization, placeholder text, and different keyboard types, such as a numeric keypad.

The most basic use case is to plop down a `TextInput` and subscribe to the `onChangeText` events to read the user input. There are also other events, such as `onSubmitEditing` and `onFocus` that can be subscribed to. A minimal example:

```SnackPlayer name=TextInput%20Example
import React from 'react';
import {StyleSheet, TextInput} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const TextInputExample = () => {
  const [text, onChangeText] = React.useState('Useless Text');
  const [number, onChangeNumber] = React.useState('');

  return (
    <SafeAreaProvider>
      <SafeAreaView>
        <TextInput
          style={styles.input}
          onChangeText={onChangeText}
          value={text}
        />
        <TextInput
          style={styles.input}
          onChangeText={onChangeNumber}
          value={number}
          placeholder="useless placeholder"
          keyboardType="numeric"
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  input: {
    height: 40,
    margin: 12,
    borderWidth: 1,
    padding: 10,
  },
});

export default TextInputExample;
```

Two methods exposed via the native element are `.focus()` and `.blur()` that will focus or blur the TextInput programmatically.

Note that some props are only available with `multiline={true/false}`. Additionally, border styles that apply to only one side of the element (e.g., `borderBottomColor`, `borderLeftWidth`, etc.) will not be applied if `multiline=true`. To achieve the same effect, you can wrap your `TextInput` in a `View`:

```SnackPlayer name=Multiline%20TextInput%20Example
import React from 'react';
import {TextInput, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const MultilineTextInputExample = () => {
  const [value, onChangeText] = React.useState('Useless Multiline Placeholder');

  // If you type something in the text box that is a color,
  // the background will change to that color.
  return (
    <SafeAreaProvider>
      <SafeAreaView
        style={[
          styles.container,
          {
            backgroundColor: value,
          },
        ]}>
        <TextInput
          editable
          multiline
          numberOfLines={4}
          maxLength={40}
          onChangeText={text => onChangeText(text)}
          value={value}
          style={styles.textInput}
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    borderBottomColor: '#000',
    borderBottomWidth: 1,
  },
  textInput: {
    padding: 10,
  },
});

export default MultilineTextInputExample;
```

`TextInput` has a border at the bottom of its view by default. This border has its padding set by the background image provided by the system, and it cannot be changed. Solutions to avoid this are to either not set height explicitly, in which case the system will take care of displaying the border in the correct position, or to not display the border by setting `underlineColorAndroid` to transparent.

Note that on Android performing text selection in an input can change the app's activity `windowSoftInputMode` param to `adjustResize`. This may cause issues with components that have position: 'absolute' while the keyboard is active. To avoid this behavior either specify `windowSoftInputMode` in AndroidManifest.xml ( https://developer.android.com/guide/topics/manifest/activity-element.html ) or control this param programmatically with native code.

---

# Reference

## Props

### [View Props](view.md#props)

Inherits [View Props](view.md#props).

---

### `allowFontScaling`

Specifies whether fonts should scale to respect Text Size accessibility settings. The default is `true`.

| Type |
| ---- |
| bool |

---

### `autoCapitalize`

Tells `TextInput` to automatically capitalize certain characters. This property is not supported by some keyboard types such as `name-phone-pad`.

- `characters`: all characters.
- `words`: first letter of each word.
- `sentences`: first letter of each sentence (_default_).
- `none`: don't auto capitalize anything.

| Type                                             |
| ------------------------------------------------ |
| enum('none', 'sentences', 'words', 'characters') |

---

### `autoComplete`

Specifies autocomplete hints for the system, so it can provide autofill. On Android, the system will always attempt to offer autofill by using heuristics to identify the type of content. To disable autocomplete, set `autoComplete` to `off`.

The following values work across platforms:

- `additional-name`
- `address-line1`
- `address-line2`
- `birthdate-day` (iOS 17+)
- `birthdate-full` (iOS 17+)
- `birthdate-month` (iOS 17+)
- `birthdate-year` (iOS 17+)
- `cc-csc` (iOS 17+)
- `cc-exp` (iOS 17+)
- `cc-exp-day` (iOS 17+)
- `cc-exp-month` (iOS 17+)
- `cc-exp-year` (iOS 17+)
- `cc-number`
- `country`
- `current-password`
- `email`
- `family-name`
- `given-name`
- `honorific-prefix`
- `honorific-suffix`
- `name`
- `new-password`
- `off`
- `one-time-code`
- `postal-code`
- `street-address`
- `tel`
- `username`

<div class="label basic ios">iOS</div>

以下值僅適用於 iOS：

- `cc-family-name` (iOS 17+)
- `cc-given-name` (iOS 17+)
- `cc-middle-name` (iOS 17+)
- `cc-name` (iOS 17+)
- `cc-type` (iOS 17+)
- `nickname`
- `organization`
- `organization-title`
- `url`

<div class="label basic android">Android</div>

以下值僅適用於 Android：

- `gender`
- `name-family`
- `name-given`
- `name-middle`
- `name-middle-initial`
- `name-prefix`
- `name-suffix`
- `password`
- `password-new`
- `postal-address`
- `postal-address-country`
- `postal-address-extended`
- `postal-address-extended-postal-code`
- `postal-address-locality`
- `postal-address-region`
- `sms-otp`
- `tel-country-code`
- `tel-device`
- `tel-national`
- `username-new`

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('additional-name', 'address-line1', 'address-line2', 'birthdate-day', 'birthdate-full', 'birthdate-month', 'birthdate-year', 'cc-csc', 'cc-exp', 'cc-exp-day', 'cc-exp-month', 'cc-exp-year', 'cc-number', 'country', 'current-password', 'email', 'family-name', 'given-name', 'honorific-prefix', 'honorific-suffix', 'name', 'new-password', 'off', 'one-time-code', 'postal-code', 'street-address', 'tel', 'username', 'cc-family-name', 'cc-given-name', 'cc-middle-name', 'cc-name', 'cc-type', 'nickname', 'organization', 'organization-title', 'url', 'gender', 'name-family', 'name-given', 'name-middle', 'name-middle-initial', 'name-prefix', 'name-suffix', 'password', 'password-new', 'postal-address', 'postal-address-country', 'postal-address-extended', 'postal-address-extended-postal-code', 'postal-address-locality', 'postal-address-region', 'sms-otp', 'tel-country-code', 'tel-device', 'tel-national', 'username-new') |

---

### `autoCorrect`

若設為 `false`，則停用自動校正。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `autoFocus`

若設為 `true`，則自動聚焦輸入框。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `blurOnSubmit`

> **已棄用。** 請注意，`submitBehavior` 現已取代 `blurOnSubmit`，並會覆蓋 `blurOnSubmit` 定義的任何行為。詳見 [submitBehavior](textinput#submitbehavior)

若設為 `true`，文字框在提交時會失去焦點。單行欄位的預設值為 true，多行欄位則為 false。請注意，對於多行欄位，將 `blurOnSubmit` 設為 `true` 意味著按下返回鍵會使欄位失去焦點並觸發 `onSubmitEditing` 事件，而不是在欄位中插入換行符。

| Type |
| ---- |
| bool |

---

### `caretHidden`

若設為 `true`，則隱藏游標。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `clearButtonMode` <div class="label ios">iOS</div>

決定清除按鈕何時出現在文字視圖的右側。此屬性僅支援單行 TextInput 元件。預設值為 `never`。

| Type                                                       |
| ---------------------------------------------------------- |
| enum('never', 'while-editing', 'unless-editing', 'always') |

---

### `clearTextOnFocus` <div class="label ios">iOS</div>

若設為 `true`，開始編輯時會自動清除文字框內容。

| Type |
| ---- |
| bool |

---

### `contextMenuHidden`

若設為 `true`，則隱藏上下文選單。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `dataDetectorTypes` <div class="label ios">iOS</div>

決定文字輸入中哪些類型的資料會被轉換為可點擊的連結。僅在 `multiline={true}` 且 `editable={false}` 時有效。預設不檢測任何資料類型。

可指定單一類型或多種類型組成的陣列。

`dataDetectorTypes` 的可能值包括：

- `'phoneNumber'`
- `'link'`
- `'address'`
- `'calendarEvent'`
- `'none'`
- `'all'`

| Type                                                                                                                                                     |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('phoneNumber', 'link', 'address', 'calendarEvent', 'none', 'all'), ,array of enum('phoneNumber', 'link', 'address', 'calendarEvent', 'none', 'all') |

---

### `defaultValue`

提供初始值，該值會在使用者開始輸入時改變。適用於不想處理事件監聽並同步更新受控狀態值的場景。

| Type   |
| ------ |
| string |

---

### `cursorColor` <div class="label android">Android</div>

設定元件中游標（或「插入符」）的顏色。與 `selectionColor` 的行為不同，游標顏色會獨立於文字選取框的顏色設定。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `disableFullscreenUI` <div class="label android">Android</div>

當設為 `false` 時，若文字輸入周圍空間有限（例如手機橫向模式），作業系統可能會讓使用者在全螢幕文字輸入模式中編輯文字。設為 `true` 則停用此功能，使用者將始終直接在文字輸入框中編輯。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `editable`

若設為 `false`，則文字不可編輯。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `enablesReturnKeyAutomatically` <div class="label ios">iOS</div>

若設為 `true`，當沒有文字時鍵盤會停用返回鍵，並在有文字時自動啟用。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `enterKeyHint`

決定返回鍵上顯示的文字。優先級高於 `returnKeyType` 屬性。

以下值跨平台通用：

- `enter`
- `done`
- `next`
- `search`
- `send`

_僅限 Android_

以下值僅在 Android 上有效：

- `previous`

| Type                                                        |
| ----------------------------------------------------------- |
| enum('enter', 'done', 'next', 'previous', 'search', 'send') |

---

### `importantForAutofill` <div class="label android">Android</div>

告知作業系統是否應將應用中的個別欄位納入 Android API 26+ 的自動填充視圖結構。可能值為 `auto`、`no`、`noExcludeDescendants`、`yes` 和 `yesExcludeDescendants`。預設值為 `auto`。

- `auto`: 讓 Android 系統使用其啟發式方法判斷該視圖是否對自動填充重要。
- `no`: 此視圖對自動填充不重要。
- `noExcludeDescendants`: 此視圖及其子視圖對自動填充不重要。
- `yes`: 此視圖對自動填充重要。
- `yesExcludeDescendants`: 此視圖對自動填充重要，但其子視圖不重要。

| Type                                                                       |
| -------------------------------------------------------------------------- |
| enum('auto', 'no', 'noExcludeDescendants', 'yes', 'yesExcludeDescendants') |

---

### `inlineImageLeft` <div class="label android">Android</div>

若定義，提供的圖片資源將渲染在左側。圖片資源必須位於 `/android/app/src/main/res/drawable` 目錄下，並以如下方式引用：

```
<TextInput
 inlineImageLeft='search_icon'
/>
```

| Type   |
| ------ |
| string |

---

### `inlineImagePadding` <div class="label android">Android</div>

內嵌圖片（如有）與文字輸入框本身之間的間距。

| Type   |
| ------ |
| number |

---

### `inputAccessoryViewID` <div class="label ios">iOS</div>

一個可選標識符，用於將自定義的 [InputAccessoryView](inputaccessoryview.md) 連結到此文字輸入框。當此文字輸入框獲得焦點時，InputAccessoryView 會渲染在鍵盤上方。

| Type   |
| ------ |
| string |

---

### `inputAccessoryViewButtonLabel` <div class="label ios">iOS</div>

一個可選標籤，用於覆蓋預設的 [InputAccessoryView](inputaccessoryview.md) 按鈕標籤。

預設情況下，預設按鈕標籤未本地化。使用此屬性可提供本地化版本。

| Type   |
| ------ |
| string |

---

### `inputMode`

功能類似 HTML 中的 `inputmode` 屬性，決定開啟哪種鍵盤（例如 `numeric`），且優先於 `keyboardType`。

支援以下值：

- `none`
- `text`
- `decimal`
- `numeric`
- `tel`
- `search`
- `email`
- `url`

| Type                                                                        |
| --------------------------------------------------------------------------- |
| enum('decimal', 'email', 'none', 'numeric', 'search', 'tel', 'text', 'url') |

---

### `keyboardAppearance` <div class="label ios">iOS</div>

決定鍵盤的顏色。

| Type                             |
| -------------------------------- |
| enum('default', 'light', 'dark') |

---

### `keyboardType`

決定開啟哪種鍵盤，例如 `numeric`。

所有類型的截圖可參見[此處](https://lefkowitz.me/2018/04/30/visual-guide-to-react-native-textinput-keyboardtype-options/)。

以下值跨平台通用：

- `default`
- `number-pad`
- `decimal-pad`
- `numeric`
- `email-address`
- `phone-pad`
- `url`

_iOS 專用_

以下值僅在 iOS 上有效：

- `ascii-capable`
- `numbers-and-punctuation`
- `name-phone-pad`
- `twitter`
- `web-search`

_Android 專用_

以下值僅在 Android 上有效：

- `visible-password`

| Type                                                                                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('default', 'email-address', 'numeric', 'phone-pad', 'ascii-capable', 'numbers-and-punctuation', 'url', 'number-pad', 'name-phone-pad', 'decimal-pad', 'twitter', 'web-search', 'visible-password') |

---

### `maxFontSizeMultiplier`

指定當啟用 `allowFontScaling` 時，字體可達到的最大縮放比例。可能的值：

- `null/undefined`（預設）：繼承父節點或全域預設值（0）
- `0`：無最大值限制，忽略父節點/全域預設值
- `>= 1`：將此節點的 `maxFontSizeMultiplier` 設為此值

| Type   |
| ------ |
| number |

---

### `maxLength`

限制可輸入的最大字元數。使用此屬性替代在 JS 中實作邏輯以避免閃爍。

| Type   |
| ------ |
| number |

---

### `multiline`

若為 `true`，文字輸入框可支援多行。預設值為 `false`。

:::note
需注意在 iOS 上文字會對齊頂部，而在 Android 上會置中。若要讓兩平台行為一致，請搭配設定 `textAlignVertical` 為 `top`。
:::

| Type |
| ---- |
| bool |

---

### `numberOfLines`

:::note
iOS 上的 `numberOfLines` 僅在[新架構](/architecture/landing-page)中可用
:::

設定 `TextInput` 的最大行數。需搭配將 `multiline` 設為 `true` 以填滿行數。

| Type   |
| ------ |
| number |

---

### `onBlur`

當文字輸入框失去焦點時觸發的回呼函式。

> 注意：若嘗試從 `nativeEvent` 存取 `text` 值，請注意結果可能為 `undefined` 而導致非預期錯誤。若要取得 TextInput 的最終值，可使用編輯完成時觸發的 [`onEndEditing`](textinput#onendediting) 事件。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [TargetEvent](targetevent)}) => void` |

---

### `onChange`

當文字輸入框內容變更時觸發的回呼函式。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {eventCount, target, text}}`) => void |

---

### `onChangeText`

當文字輸入框內容變更時觸發的回呼函式。變更後的文字會以單一字串參數形式傳遞給回呼處理函式。

| Type     |
| -------- |
| function |

---

### `onContentSizeChange`

當文字輸入框內容尺寸變更時觸發的回呼函式。

僅適用於多行文字輸入框。

| Type                                                       |
| ---------------------------------------------------------- |
| (`{nativeEvent: {contentSize: {width, height} }}`) => void |

---

### `onEndEditing`

當文字輸入結束時觸發的回呼函式。

| Type     |
| -------- |
| function |

---

### `onPressIn`

當觸碰開始時觸發的回呼函式。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressOut`

當觸碰結束時觸發的回呼函式。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onFocus`

當文字輸入框獲得焦點時觸發的回呼函式。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [TargetEvent](targetevent)}) => void` |

---

### `onKeyPress`

當按鍵被按下時觸發的回調函數。此函數會接收一個物件，其中 `keyValue` 為 `'Enter'` 或 `'Backspace'`（對應按鍵）或其他輸入字符（包括空格 `' '`）。此回調會在 `onChange` 之前觸發。注意：在 Android 上僅處理軟鍵盤輸入，不處理硬體鍵盤輸入。

| Type                                        |
| ------------------------------------------- |
| (`{nativeEvent: {key: keyValue} }`) => void |

---

### `onLayout`

在元件掛載或佈局變化時觸發。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onScroll`

在內容滾動時觸發。可能包含 `ScrollEvent` 的其他屬性，但在 Android 上為效能考量不提供 `contentSize`。

| Type                                                |
| --------------------------------------------------- |
| (`{nativeEvent: {contentOffset: {x, y} }}`) => void |

---

### `onSelectionChange`

當文字輸入的選取範圍變更時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {selection: {start, end} }}`) => void |

---

### `onSubmitEditing`

當文字輸入的提交按鈕被按下時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {text, eventCount, target}}`) => void |

注意：在 iOS 上使用 `keyboardType="phone-pad"` 時此方法不會被呼叫。

---

### `placeholder`

在文字輸入前顯示的預留字串。

| Type   |
| ------ |
| string |

---

### `placeholderTextColor`

預留字串的文字顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `readOnly`

若為 `true`，文字不可編輯。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `returnKeyLabel` <div class="label android">Android</div>

設定返回鍵的標籤。可替代 `returnKeyType` 使用。

| Type   |
| ------ |
| string |

---

### `returnKeyType`

決定返回鍵的外觀。在 Android 上也可使用 `returnKeyLabel`。

_跨平台_

以下值適用於所有平台：

- `done`
- `go`
- `next`
- `search`
- `send`

_僅限 Android_

以下值僅在 Android 上有效：

- `none`
- `previous`

_僅限 iOS_

以下值僅在 iOS 上有效：

- `default`
- `emergency-call`
- `google`
- `join`
- `route`
- `yahoo`

| Type                                                                                                                              |
| --------------------------------------------------------------------------------------------------------------------------------- |
| enum('done', 'go', 'next', 'search', 'send', 'none', 'previous', 'default', 'emergency-call', 'google', 'join', 'route', 'yahoo') |

### `rejectResponderTermination` <div class="label ios">iOS</div>

若為 `true`，允許 TextInput 將觸控事件傳遞給父元件。這使得如 SwipeableListView 等元件能從 TextInput 滑動（Android 預設即支援）。若為 `false`，TextInput 始終要求處理輸入（除非被禁用）。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `rows` <div class="label android">Android</div>

設定 `TextInput` 的行數。需與 `multiline={true}` 搭配使用以填滿行數。

| Type   |
| ------ |
| number |

---

### `scrollEnabled` <div class="label ios">iOS</div>

若設為 `false`，將禁用文字視圖的滾動功能。預設值為 `true`。僅在 `multiline={true}` 時有效。

| Type |
| ---- |
| bool |

---

### `secureTextEntry`

若設為 `true`，文字輸入會遮蔽輸入內容以保護敏感資訊如密碼。預設值為 `false`。不支援與 `multiline={true}` 同時使用。

| Type |
| ---- |
| bool |

---

### `selection`

設定文字輸入選取的起始與結束位置。將起始與結束設為相同值可定位游標。

| Type                                  |
| ------------------------------------- |
| object: `{start: number,end: number}` |

---

### `selectionColor`

設定文字輸入的選取高亮、選取把手與游標顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `selectionHandleColor` <div class="label android">Android</div>

設定選取把手的顏色。與 `selectionColor` 不同，此屬性允許獨立自訂選取把手顏色，不影響選取區顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `selectTextOnFocus`

若設為 `true`，聚焦時將自動選取全部文字。

| Type |
| ---- |
| bool |

---

### `showSoftInputOnFocus`

設為 `false` 可防止聚焦時顯示軟體鍵盤。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `spellCheck` <div class="label ios">iOS</div>

若設為 `false`，將停用拼寫檢查樣式（如紅色底線）。預設值繼承自 `autoCorrect`。

| Type |
| ---- |
| bool |

---

### `submitBehavior`

當按下返回鍵時：

單行輸入框：

- `'newline'` 預設為 `'blurAndSubmit'`
- `undefined` 預設為 `'blurAndSubmit'`

多行輸入框：

- `'newline'` 會新增換行
- `undefined` 預設為 `'newline'`

單行與多行輸入框共通：

- `'submit'` 僅觸發提交事件，不失去焦點
- `'blurAndSubmit'` 會同時失去焦點並觸發提交事件

| Type                                       |
| ------------------------------------------ |
| enum('submit', 'blurAndSubmit', 'newline') |

---

### `textAlign`

將輸入文字對齊輸入框的左側、居中或右側。

`textAlign` 可用值為：

- `left`（左對齊）
- `center`（居中）
- `right`（右對齊）

| Type                            |
| ------------------------------- |
| enum('left', 'center', 'right') |

---

### `textContentType` <div class="label ios">iOS</div>

向鍵盤與系統提供使用者輸入內容的預期語義資訊。

:::note
[`autoComplete`](#autocomplete) 提供相同功能且支援所有平台。您可以使用 [`Platform.select`](/docs/next/platform#select) 來處理不同平台的行為差異。

請避免同時使用 `textContentType` 和 `autoComplete`。為了向後兼容，當兩個屬性都設定時，`textContentType` 會優先生效。
:::

您可以將 `textContentType` 設為 `username` 或 `password` 來啟用裝置鑰匙圈的登入資訊自動填入功能。

`newPassword` 可用於表示使用者可能想儲存至鑰匙圈的新密碼輸入欄位，而 `oneTimeCode` 則表示該欄位可透過簡訊收到的驗證碼自動填入。

若要停用自動填入，請將 `textContentType` 設為 `none`。

`textContentType` 的可能值包括：

- `none`
- `addressCity`
- `addressCityAndState`
- `addressState`
- `birthdate` (iOS 17+)
- `birthdateDay` (iOS 17+)
- `birthdateMonth` (iOS 17+)
- `birthdateYear` (iOS 17+)
- `countryName`
- `creditCardExpiration` (iOS 17+)
- `creditCardExpirationMonth` (iOS 17+)
- `creditCardExpirationYear` (iOS 17+)
- `creditCardFamilyName` (iOS 17+)
- `creditCardGivenName` (iOS 17+)
- `creditCardMiddleName` (iOS 17+)
- `creditCardName` (iOS 17+)
- `creditCardNumber`
- `creditCardSecurityCode` (iOS 17+)
- `creditCardType` (iOS 17+)
- `emailAddress`
- `familyName`
- `fullStreetAddress`
- `givenName`
- `jobTitle`
- `location`
- `middleName`
- `name`
- `namePrefix`
- `nameSuffix`
- `newPassword`
- `nickname`
- `oneTimeCode`
- `organizationName`
- `password`
- `postalCode`
- `streetAddressLine1`
- `streetAddressLine2`
- `sublocality`
- `telephoneNumber`
- `URL`
- `username`

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| enum('none', 'addressCity', 'addressCityAndState', 'addressState', 'birthdate', 'birthdateDay', 'birthdateMonth', 'birthdateYear', 'countryName', 'creditCardExpiration', 'creditCardExpirationMonth', 'creditCardExpirationYear', 'creditCardFamilyName', 'creditCardGivenName', 'creditCardMiddleName', 'creditCardName', 'creditCardNumber', 'creditCardSecurityCode', 'creditCardType', 'emailAddress', 'familyName', 'fullStreetAddress', 'givenName', 'jobTitle', 'location', 'middleName', 'name', 'namePrefix', 'nameSuffix', 'newPassword', 'nickname', 'oneTimeCode', 'organizationName', 'password', 'postalCode', 'streetAddressLine1', 'streetAddressLine2', 'sublocality', 'telephoneNumber', 'URL', 'username') |

---

### `passwordRules` <div class="label ios">iOS</div>

在 iOS 上使用 `textContentType` 設為 `newPassword` 時，我們可以透過此屬性讓系統知道密碼的最低要求，以便生成符合條件的密碼。要建立有效的 `PasswordRules` 字串，請參考 [Apple 官方文件](https://developer.apple.com/password-rules/)。

> 若未出現密碼生成對話框，請確認以下設定：
>
> - 已啟用自動填入：**設定** → **密碼與帳號** → 開啟 **自動填入密碼**
> - 已使用 iCloud 鑰匙圈：**設定** → **Apple ID** → **iCloud** → **鑰匙圈** → 開啟 **iCloud 鑰匙圈**

| Type   |
| ------ |
| string |

---

### `style`

請注意並非所有文字樣式都受支援，以下列出部分不支援的樣式：

- `borderLeftWidth`
- `borderTopWidth`
- `borderRightWidth`
- `borderBottomWidth`
- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomRightRadius`
- `borderBottomLeftRadius`

[樣式](style.md)

| Type                  |
| --------------------- |
| [Text](text.md#style) |

---

### `textBreakStrategy` <div class="label android">Android</div>

在 Android API Level 23+ 上設定文字斷行策略，可能值為 `simple`、`highQuality`、`balanced`，預設值為 `highQuality`。

| Type                                      |
| ----------------------------------------- |
| enum('simple', 'highQuality', 'balanced') |

---

### `underlineColorAndroid` <div class="label android">Android</div>

設定 `TextInput` 底線的顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `value`

文字輸入框顯示的值。`TextInput` 是一個受控元件，這意味著如果提供了這個屬性，原生值將被強制與之匹配。在大多數情況下這運作良好，但在某些情況下可能會導致閃爍——一個常見原因是通過保持值不變來防止編輯。除了設置相同的值外，可以設置 `editable={false}`，或設置/更新 `maxLength` 來避免不需要的編輯而不產生閃爍。

| Type   |
| ------ |
| string |

---

### `lineBreakStrategyIOS` <div class="label ios">iOS</div>

在 iOS 14+ 上設定換行策略。可能的值有 `none`、`standard`、`hangul-word` 和 `push-out`。

| Type                                                        | Default  |
| ----------------------------------------------------------- | -------- |
| enum(`'none'`, `'standard'`, `'hangul-word'`, `'push-out'`) | `'none'` |

---

### `disableKeyboardShortcuts` <div class="label ios">iOS</div>

如果設為 `true`，則禁用鍵盤快捷鍵（撤銷/重做和複製按鈕）。預設值為 `false`。

| Type |
| ---- |
| bool |

## 方法

### `.focus()`

```tsx
focus();
```

使原生輸入框請求焦點。

### `.blur()`

```tsx
blur();
```

使原生輸入框失去焦點。

### `clear()`

```tsx
clear();
```

清除 `TextInput` 中的所有文字。

---

### `isFocused()`

```tsx
isFocused(): boolean;
```

如果輸入框當前獲得焦點則返回 `true`；否則返回 `false`。

# 已知問題

- [react-native#19096](https://github.com/facebook/react-native/issues/19096): 不支援 Android 的 `onKeyPreIme`。
- [react-native#19366](https://github.com/facebook/react-native/issues/19366): 在通過返回按鈕關閉 Android 鍵盤後呼叫 .focus() 不會再次喚起鍵盤。
- [react-native#26799](https://github.com/facebook/react-native/issues/26799): 當 `keyboardType="email-address"` 或 `keyboardType="phone-pad"` 時不支援 Android 的 `secureTextEntry`。