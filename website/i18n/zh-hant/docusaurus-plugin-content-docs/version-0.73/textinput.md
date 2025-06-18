---
id: textinput
title: TextInput
---

一個基礎元件，用於透過鍵盤在應用程式中輸入文字。其屬性可配置多項功能，例如自動校正、自動大寫、佔位文字，以及不同類型的鍵盤（如數字鍵盤）。

最基本的用法是放置一個 `TextInput` 並訂閱 `onChangeText` 事件來讀取使用者輸入。還有其他事件如 `onSubmitEditing` 和 `onFocus` 可供訂閱。以下是一個簡單範例：

```SnackPlayer name=TextInput
import React from 'react';
import {SafeAreaView, StyleSheet, TextInput} from 'react-native';

const TextInputExample = () => {
  const [text, onChangeText] = React.useState('Useless Text');
  const [number, onChangeNumber] = React.useState('');

  return (
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

透過原生元素公開的兩個方法是 `.focus()` 和 `.blur()`，可用程式方式聚焦或模糊 `TextInput`。

請注意，某些屬性僅在 `multiline={true/false}` 時可用。此外，若 `multiline=true`，則僅應用於元素單邊的邊框樣式（例如 `borderBottomColor`、`borderLeftWidth` 等）將不會生效。要達到相同效果，您可以將 `TextInput` 包裹在 `View` 中：

```SnackPlayer name=TextInput
import React from 'react';
import {View, TextInput} from 'react-native';

const MultilineTextInputExample = () => {
  const [value, onChangeText] = React.useState('Useless Multiline Placeholder');

  // If you type something in the text box that is a color, the background will change to that
  // color.
  return (
    <View
      style={{
        backgroundColor: value,
        borderBottomColor: '#000000',
        borderBottomWidth: 1,
      }}>
      <TextInput
        editable
        multiline
        numberOfLines={4}
        maxLength={40}
        onChangeText={text => onChangeText(text)}
        value={value}
        style={{padding: 10}}
      />
    </View>
  );
};

export default MultilineTextInputExample;
```

`TextInput` 預設在其視圖底部有一個邊框。此邊框的內距由系統提供的背景圖片設定，無法更改。避免此問題的解決方案是不要明確設定高度（系統會負責在正確位置顯示邊框），或透過將 `underlineColorAndroid` 設為透明來不顯示邊框。

請注意，在 Android 上，在輸入框中進行文字選取可能會將應用的活動 `windowSoftInputMode` 參數更改為 `adjustResize`。這可能會導致鍵盤處於活動狀態時，具有 `position: 'absolute'` 的元件出現問題。要避免此行為，可以在 AndroidManifest.xml 中指定 `windowSoftInputMode`（https://developer.android.com/guide/topics/manifest/activity-element.html），或透過原生代碼以程式方式控制此參數。

---

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view.md#props)。

---

### `allowFontScaling`

指定字體是否應縮放以符合「文字大小」無障礙設定。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `autoCapitalize`

告訴 `TextInput` 自動將某些字符大寫。此屬性不受某些鍵盤類型（如 `name-phone-pad`）支援。

- `characters`：所有字符。
- `words`：每個單詞的首字母。
- `sentences`：每個句子的首字母（_預設_）。
- `none`：不自動大寫任何內容。

| Type                                             |
| ------------------------------------------------ |
| enum('none', 'sentences', 'words', 'characters') |

---

### `autoComplete`

為系統指定自動完成提示，以便提供自動填充功能。在 Android 上，系統始終會嘗試透過啟發式方法識別內容類型來提供自動填充。要停用自動完成，請將 `autoComplete` 設為 `off`。

以下值在所有平台上均適用：

- `additional-name` (附加名稱)
- `address-line1` (地址行1)
- `address-line2` (地址行2)
- `birthdate-day` (出生日期-日，iOS 17+)
- `birthdate-full` (完整出生日期，iOS 17+)
- `birthdate-month` (出生日期-月，iOS 17+)
- `birthdate-year` (出生日期-年，iOS 17+)
- `cc-csc` (信用卡安全碼，iOS 17+)
- `cc-exp` (信用卡到期日，iOS 17+)
- `cc-exp-day` (信用卡到期日-日，iOS 17+)
- `cc-exp-month` (信用卡到期日-月，iOS 17+)
- `cc-exp-year` (信用卡到期日-年，iOS 17+)
- `cc-number` (信用卡號碼)
- `country` (國家)
- `current-password` (當前密碼)
- `email` (電子郵件)
- `family-name` (姓氏)
- `given-name` (名字)
- `honorific-prefix` (敬稱前綴)
- `honorific-suffix` (敬稱後綴)
- `name` (姓名)
- `new-password` (新密碼)
- `off` (關閉)
- `one-time-code` (一次性驗證碼)
- `postal-code` (郵遞區號)
- `street-address` (街道地址)
- `tel` (電話號碼)
- `username` (使用者名稱)

<div class="label basic ios">iOS</div>

以下值僅在 iOS 上有效：

- `cc-family-name` (信用卡姓氏，iOS 17+)
- `cc-given-name` (信用卡名字，iOS 17+)
- `cc-middle-name` (信用卡中間名，iOS 17+)
- `cc-name` (信用卡持卡人姓名，iOS 17+)
- `cc-type` (信用卡類型，iOS 17+)
- `nickname` (暱稱)
- `organization` (組織)
- `organization-title` (組織職稱)
- `url` (網址)

<div class="label basic android">Android</div>

以下值僅在 Android 上有效：

- `gender` (性別)
- `name-family` (姓氏)
- `name-given` (名字)
- `name-middle` (中間名)
- `name-middle-initial` (中間名首字母)
- `name-prefix` (名前綴)
- `name-suffix` (名後綴)
- `password` (密碼)
- `password-new` (新密碼)
- `postal-address` (郵寄地址)
- `postal-address-country` (郵寄地址國家)
- `postal-address-extended` (郵寄地址擴展)
- `postal-address-extended-postal-code` (郵寄地址擴展郵遞區號)
- `postal-address-locality` (郵寄地址地區)
- `postal-address-region` (郵寄地址區域)
- `sms-otp` (簡訊一次性密碼)
- `tel-country-code` (電話國碼)
- `tel-device` (裝置電話號碼)
- `tel-national` (國內電話號碼)
- `username-new` (新使用者名稱)

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('additional-name', 'address-line1', 'address-line2', 'birthdate-day', 'birthdate-full', 'birthdate-month', 'birthdate-year', 'cc-csc', 'cc-exp', 'cc-exp-day', 'cc-exp-month', 'cc-exp-year', 'cc-number', 'country', 'current-password', 'email', 'family-name', 'given-name', 'honorific-prefix', 'honorific-suffix', 'name', 'new-password', 'off', 'one-time-code', 'postal-code', 'street-address', 'tel', 'username', 'cc-family-name', 'cc-given-name', 'cc-middle-name', 'cc-name', 'cc-type', 'nickname', 'organization', 'organization-title', 'url', 'gender', 'name-family', 'name-given', 'name-middle', 'name-middle-initial', 'name-prefix', 'name-suffix', 'password', 'password-new', 'postal-address', 'postal-address-country', 'postal-address-extended', 'postal-address-extended-postal-code', 'postal-address-locality', 'postal-address-region', 'sms-otp', 'tel-country-code', 'tel-device', 'tel-national', 'username-new') |

---

### `autoCorrect`

若為 `false`，則停用自動校正。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `autoFocus`

若為 `true`，則在 `componentDidMount` 或 `useEffect` 時聚焦輸入框。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `blurOnSubmit`

若為 `true`，文字欄位會在提交時失去焦點。單行欄位的預設值為 true，多行欄位則為 false。請注意，對於多行欄位，將 `blurOnSubmit` 設為 `true` 意味著按下返回鍵會使欄位失去焦點並觸發 `onSubmitEditing` 事件，而不是在欄位中插入換行符號。

| Type |
| ---- |
| bool |

---

### `caretHidden`

若為 `true`，則隱藏輸入游標。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `clearButtonMode` <div class="label ios">iOS</div>

決定清除按鈕何時出現在文字輸入框的右側。此屬性僅支援單行 TextInput 元件。預設值為 `never`。

| Type                                                       |
| ---------------------------------------------------------- |
| enum('never', 'while-editing', 'unless-editing', 'always') |

---

### `clearTextOnFocus` <div class="label ios">iOS</div>

若為 `true`，則在開始編輯時自動清除文字欄位。

| Type |
| ---- |
| bool |

---

### `contextMenuHidden`

若為 `true`，則隱藏上下文選單。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `dataDetectorTypes` <div class="label ios">iOS</div>

決定文字輸入中哪些類型的資料會被轉換為可點擊的連結。僅在 `multiline={true}` 且 `editable={false}` 時有效。預設不檢測任何資料類型。

您可以指定單一類型或多種類型的陣列。

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

提供初始值，該值會在使用者開始輸入時改變。適用於不想處理事件監聽並透過更新 value 屬性來同步控制狀態的場景。

| Type   |
| ------ |
| string |

---

### `cursorColor` <div class="label android">Android</div>

設定元件中游標（或「插入符號」）的顏色。與 `selectionColor` 的行為不同，游標顏色會獨立於文字選取框的顏色設定。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `disableFullscreenUI` <div class="label android">Android</div>

當設為 `false` 時，如果文字輸入周圍可用空間較少（例如手機橫向模式），作業系統可能會讓使用者在全螢幕文字輸入模式中編輯文字。設為 `true` 時會停用此功能，使用者將始終直接在文字輸入框中編輯文字。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `editable`

若為 `false`，文字將不可編輯。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `enablesReturnKeyAutomatically` <div class="label ios">iOS</div>

若為 `true`，鍵盤會在沒有文字時停用返回鍵，並在有文字時自動啟用。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `enterKeyHint`

決定返回鍵應顯示的文字。優先級高於 `returnKeyType` 屬性。

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

告知作業系統應用中的個別欄位是否應包含在 Android API 26+ 的自動填充視圖結構中。可能值為 `auto`、`no`、`noExcludeDescendants`、`yes` 和 `yesExcludeDescendants`。預設值為 `auto`。

- `auto`: 讓 Android 系統使用其啟發式方法判斷視圖對自動填充是否重要。
- `no`: 此視圖對自動填充不重要。
- `noExcludeDescendants`: 此視圖及其子視圖對自動填充不重要。
- `yes`: 此視圖對自動填充重要。
- `yesExcludeDescendants`: 此視圖對自動填充重要，但其子視圖不重要。

| Type                                                                       |
| -------------------------------------------------------------------------- |
| enum('auto', 'no', 'noExcludeDescendants', 'yes', 'yesExcludeDescendants') |

---

### `inlineImageLeft` <div class="label android">Android</div>

若定義此屬性，指定的圖片資源將顯示在輸入框左側。圖片資源必須位於 `/android/app/src/main/res/drawable` 目錄下，並以如下方式引用：

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

設定內嵌圖片（如有）與文字輸入框本身之間的間距。

| Type   |
| ------ |
| number |

---

### `inputAccessoryViewID` <div class="label ios">iOS</div>

可選標識符，用於將自定義的 [InputAccessoryView](inputaccessoryview.md) 與此文字輸入框關聯。當此輸入框獲得焦點時，InputAccessoryView 會顯示在鍵盤上方。

| Type   |
| ------ |
| string |

---

### `inputMode`

功能類似 HTML 中的 `inputmode` 屬性，決定開啟哪種鍵盤（例如 `numeric`），其優先級高於 `keyboardType`。

支援以下數值：

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

決定鍵盤的顏色樣式。

| Type                             |
| -------------------------------- |
| enum('default', 'light', 'dark') |

---

### `keyboardType`

決定開啟哪種鍵盤，例如 `numeric`。

各類型鍵盤的截圖可參閱[此連結](https://lefkowitz.me/2018/04/30/visual-guide-to-react-native-textinput-keyboardtype-options/)。

以下數值跨平台通用：

- `default`
- `number-pad`
- `decimal-pad`
- `numeric`
- `email-address`
- `phone-pad`
- `url`

_iOS 專用_

以下數值僅在 iOS 平台有效：

- `ascii-capable`
- `numbers-and-punctuation`
- `name-phone-pad`
- `twitter`
- `web-search`

_Android 專用_

以下數值僅在 Android 平台有效：

- `visible-password`

| Type                                                                                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('default', 'email-address', 'numeric', 'phone-pad', 'ascii-capable', 'numbers-and-punctuation', 'url', 'number-pad', 'name-phone-pad', 'decimal-pad', 'twitter', 'web-search', 'visible-password') |

---

### `maxFontSizeMultiplier`

當啟用 `allowFontScaling` 時，指定字體可縮放的最大倍率。可能數值：

- `null/undefined`（預設）：繼承父節點或全域設定（0）
- `0`：無限制，忽略父級/全域預設值
- `>= 1`：將此節點的 `maxFontSizeMultiplier` 設為該值

| Type   |
| ------ |
| number |

---

### `maxLength`

限制可輸入的最大字元數。建議使用此屬性而非在 JavaScript 中實作相同邏輯，以避免畫面閃爍。

| Type   |
| ------ |
| number |

---

### `multiline`

若設為 `true`，文字輸入框可支援多行輸入。預設值為 `false`。

:::note
請注意，這在 iOS 上會將文字對齊頂部，而在 Android 上則會置中。若要在兩個平台上獲得相同行為，請將 `textAlignVertical` 設為 `top`。
:::

| Type |
| ---- |
| bool |

---

### `numberOfLines` <div class="label android">Android</div>

設定 `TextInput` 的行數。需與 `multiline` 設為 `true` 搭配使用才能填滿行數。

| Type   |
| ------ |
| number |

---

### `onBlur`

當文字輸入框失去焦點時觸發的回調函數。

> 注意：若您嘗試從 `nativeEvent` 獲取 `text` 值，請注意結果可能是 `undefined`，這可能導致非預期錯誤。如需獲取 TextInput 的最終值，可使用 [`onEndEditing`](textinput#onendediting) 事件，該事件會在編輯完成時觸發。

| Type     |
| -------- |
| function |

---

### `onChange`

當文字輸入框內容變更時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {eventCount, target, text}}`) => void |

---

### `onChangeText`

當文字輸入框內容變更時觸發的回調函數。變更後的文字會以單一字串參數形式傳遞給回調處理函數。

| Type     |
| -------- |
| function |

---

### `onContentSizeChange`

當文字輸入框內容尺寸變更時觸發的回調函數。

僅適用於多行文字輸入框。

| Type                                                       |
| ---------------------------------------------------------- |
| (`{nativeEvent: {contentSize: {width, height} }}`) => void |

---

### `onEndEditing`

當文字輸入結束時觸發的回調函數。

| Type     |
| -------- |
| function |

---

### `onPressIn`

當觸碰開始時觸發的回調函數。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressOut`

當觸碰結束時觸發的回調函數。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onFocus`

當文字輸入框獲得焦點時觸發的回調函數。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onKeyPress`

當按鍵被按下時觸發的回調函數。會傳遞一個物件，其中 `keyValue` 對於「Enter」或「Backspace」鍵分別為對應值，其他輸入字元則為實際字元（包含空格「 」）。會在 `onChange` 回調之前觸發。注意：在 Android 上僅處理軟鍵盤輸入，不處理硬體鍵盤輸入。

| Type                                        |
| ------------------------------------------- |
| (`{nativeEvent: {key: keyValue} }`) => void |

---

### `onLayout`

在掛載時或佈局變更時觸發。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onScroll`

當內容滾動時觸發。可能包含來自 `ScrollEvent` 的其他屬性，但在 Android 上基於效能考量不會提供 `contentSize`。

| Type                                                |
| --------------------------------------------------- |
| (`{nativeEvent: {contentOffset: {x, y} }}`) => void |

---

### `onSelectionChange`

當文字輸入框選取範圍變更時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {selection: {start, end} }}`) => void |

---

### `onSubmitEditing`

當文字輸入的提交按鈕被按下時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {text, eventCount, target}}`) => void |

請注意，在 iOS 上使用 `keyboardType="phone-pad"` 時此方法不會被呼叫。

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

若為 `true`，文字將無法編輯。預設值為 `false`。

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

若為 `true`，允許 TextInput 將觸控事件傳遞給父元件。這使得如 SwipeableListView 等元件可以從 TextInput 上滑動（在 Android 上預設即如此）。若為 `false`，TextInput 總是會要求處理輸入（除非被禁用）。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `rows` <div class="label android">Android</div>

設定 TextInput 的行數。需與 `multiline={true}` 一起使用以填滿行數。

| Type   |
| ------ |
| number |

---

### `scrollEnabled` <div class="label ios">iOS</div>

若為 `false`，將禁用文字視圖的滾動。預設值為 `true`。僅在 `multiline={true}` 時有效。

| Type |
| ---- |
| bool |

---

### `secureTextEntry`

若為 `true`，文字輸入將隱藏輸入內容以保護敏感資訊如密碼。預設值為 `false`。不適用於 `multiline={true}`。

| Type |
| ---- |
| bool |

---

### `selection`

文字輸入選取的起始與結束位置。將起始與結束設為相同值可定位游標。

| Type                                  |
| ------------------------------------- |
| object: `{start: number,end: number}` |

---

### `selectionColor`

文字輸入框的高亮與游標顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `selectTextOnFocus`

若設為 `true`，聚焦時會自動選取所有文字。

| Type |
| ---- |
| bool |

---

### `showSoftInputOnFocus`

設為 `false` 可防止聚焦時彈出軟鍵盤。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `spellCheck` <div class="label ios">iOS</div>

設為 `false` 可停用拼字檢查樣式（如紅色底線）。預設值繼承自 `autoCorrect`。

| Type |
| ---- |
| bool |

---

### `textAlign`

將輸入文字對齊輸入框的左側、置中或右側。

`textAlign` 可用值為：

- `left`
- `center`
- `right`

| Type                            |
| ------------------------------- |
| enum('left', 'center', 'right') |

---

### `textContentType` <div class="label ios">iOS</div>

向鍵盤與系統提供使用者輸入內容的預期語義資訊。

:::note
[`autoComplete`](#autocomplete) 提供相同功能且支援全平台。可使用 [`Platform.select`](/docs/next/platform#select) 處理平台差異行為。

避免同時使用 `textContentType` 與 `autoComplete`。為保持向後兼容性，當兩屬性同時設定時，`textContentType` 會優先生效。
:::

可將 `textContentType` 設為 `username` 或 `password` 以啟用裝置鑰匙圈自動填入登入資訊。

`newPassword` 用於表示使用者可能想儲存至鑰匙圈的新密碼輸入欄位，`oneTimeCode` 則表示該欄位可透過簡訊收到的驗證碼自動填入。

若要停用自動填入，請將 `textContentType` 設為 `none`。

`textContentType` 可用值為：

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

在 iOS 上使用 `textContentType` 設為 `newPassword` 時，我們可以讓作業系統知道密碼的最低要求，以便生成符合這些要求的密碼。要為 `PasswordRules` 創建有效的字符串，請參考 [Apple 文檔](https://developer.apple.com/password-rules/)。

> 如果密碼生成對話框未出現，請確保：
>
> - 已啟用自動填充功能：**設定** → **密碼與帳號** → 開啟 **自動填充密碼**，
> - 使用 iCloud 鑰匙圈：**設定** → **Apple ID** → **iCloud** → **鑰匙圈** → 開啟 **iCloud 鑰匙圈**。

| Type   |
| ------ |
| string |

---

### `style`

請注意，並非所有文字樣式都受支援，以下是不支援的樣式的不完整列表：

- `borderLeftWidth`
- `borderTopWidth`
- `borderRightWidth`
- `borderBottomWidth`
- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomRightRadius`
- `borderBottomLeftRadius`

詳情請參閱 [Issue#7070](https://github.com/facebook/react-native/issues/7070)。

[樣式](style.md)

| Type                  |
| --------------------- |
| [Text](text.md#style) |

---

### `textBreakStrategy` <div class="label android">Android</div>

在 Android API Level 23+ 上設定文字斷行策略，可能的值為 `simple`、`highQuality`、`balanced`，預設值為 `highQuality`。

| Type                                      |
| ----------------------------------------- |
| enum('simple', 'highQuality', 'balanced') |

---

### `underlineColorAndroid` <div class="label android">Android</div>

`TextInput` 底線的顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `value`

文字輸入框顯示的值。`TextInput` 是一個受控組件，這意味著如果提供了這個值屬性，原生值將被迫與之匹配。對於大多數用途來說，這很好用，但在某些情況下可能會導致閃爍——一個常見的原因是通過保持值不變來防止編輯。除了設置相同的值外，還可以設置 `editable={false}`，或者設置/更新 `maxLength` 以防止不需要的編輯而不會閃爍。

| Type   |
| ------ |
| string |

---

### `lineBreakStrategyIOS` <div class="label ios">iOS</div>

在 iOS 14+ 上設定換行策略。可能的值為 `none`、`standard`、`hangul-word` 和 `push-out`。

| Type                                                        | Default  |
| ----------------------------------------------------------- | -------- |
| enum(`'none'`, `'standard'`, `'hangul-word'`, `'push-out'`) | `'none'` |

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

如果輸入框當前有焦點，則返回 `true`；否則返回 `false`。

# 已知問題

- [react-native#19096](https://github.com/facebook/react-native/issues/19096): 不支援 Android 的 `onKeyPreIme`。
- [react-native#19366](https://github.com/facebook/react-native/issues/19366): 在透過返回鍵關閉 Android 鍵盤後呼叫 .focus() 不會再次喚起鍵盤。
- [react-native#26799](https://github.com/facebook/react-native/issues/26799): 當 `keyboardType="email-address"` 或 `keyboardType="phone-pad"` 時不支援 Android 的 `secureTextEntry`。