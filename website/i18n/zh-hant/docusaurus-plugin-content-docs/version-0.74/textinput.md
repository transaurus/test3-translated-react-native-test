---
id: textinput
title: TextInput
---

一個基礎組件，用於通過鍵盤向應用程序輸入文本。其屬性可配置多種功能，如自動校正、自動大寫、佔位文本以及不同類型的鍵盤（例如數字鍵盤）。

最基本的用法是放置一個 `TextInput` 並訂閱 `onChangeText` 事件來讀取用戶輸入。還有其他事件，如 `onSubmitEditing` 和 `onFocus` 也可以訂閱。一個最小示例：

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

通過原生元素暴露的兩個方法是 .focus() 和 .blur()，可以以編程方式聚焦或模糊 TextInput。

請注意，某些屬性僅在 `multiline={true/false}` 時可用。此外，如果 `multiline=true`，則僅應用於元素一側的邊框樣式（例如 `borderBottomColor`、`borderLeftWidth` 等）將不會生效。要實現相同的效果，可以將 `TextInput` 包裹在 `View` 中：

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

`TextInput` 默認在其視圖底部有一個邊框。此邊框的填充由系統提供的背景圖像設置，無法更改。避免此問題的解決方案要麼不顯式設置高度（在這種情況下，系統會負責在正確的位置顯示邊框），要麼通過將 `underlineColorAndroid` 設置為透明來不顯示邊框。

請注意，在 Android 上，在輸入中執行文本選擇可能會將應用的活動 `windowSoftInputMode` 參數更改為 `adjustResize`。這可能會導致在鍵盤處於活動狀態時，具有 `position: 'absolute'` 的組件出現問題。要避免這種行為，可以在 AndroidManifest.xml 中指定 `windowSoftInputMode`（https://developer.android.com/guide/topics/manifest/activity-element.html），或者通過原生代碼以編程方式控制此參數。

---

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view.md#props)。

---

### `allowFontScaling`

指定字體是否應縮放以尊重文本大小的無障礙設置。默認為 `true`。

| Type |
| ---- |
| bool |

---

### `autoCapitalize`

告訴 `TextInput` 自動大寫某些字符。此屬性不受某些鍵盤類型（如 `name-phone-pad`）支持。

- `characters`: 所有字符。
- `words`: 每個單詞的首字母。
- `sentences`: 每個句子的首字母（_默認_）。
- `none`: 不自動大寫任何內容。

| Type                                             |
| ------------------------------------------------ |
| enum('none', 'sentences', 'words', 'characters') |

---

### `autoComplete`

為系統指定自動完成提示，以便提供自動填充。在 Android 上，系統始終會嘗試通過使用啟發式方法來識別內容類型來提供自動填充。要禁用自動完成，請將 `autoComplete` 設置為 `off`。

以下值在所有平台上都適用：

- `additional-name` (附加名稱)
- `address-line1` (地址行1)
- `address-line2` (地址行2)
- `birthdate-day` (出生日期-日，iOS 17+)
- `birthdate-full` (出生日期-完整，iOS 17+)
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
- `cc-name` (信用卡姓名，iOS 17+)
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
- `tel-country-code` (電話國家代碼)
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

若為 `true`，則提交時文字欄位會失去焦點。單行欄位的預設值為 `true`，多行欄位則為 `false`。請注意，對於多行欄位，將 `blurOnSubmit` 設為 `true` 表示按下返回鍵會使欄位失去焦點並觸發 `onSubmitEditing` 事件，而不是在欄位中插入換行符。

| Type |
| ---- |
| bool |

---

### `caretHidden`

若為 `true`，則隱藏游標。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `clearButtonMode` <div class="label ios">iOS</div>

清除按鈕應出現在文字視圖右側的時機。此屬性僅支援單行 TextInput 元件。預設值為 `never`。

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

Determines the types of data converted to clickable URLs in the text input. Only valid if `multiline={true}` and `editable={false}`. By default no data types are detected.

You can provide one type or an array of many types.

Possible values for `dataDetectorTypes` are:

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

Provides an initial value that will change when the user starts typing. Useful for use-cases where you do not want to deal with listening to events and updating the value prop to keep the controlled state in sync.

| Type   |
| ------ |
| string |

---

### `cursorColor` <div class="label android">Android</div>

When provided it will set the color of the cursor (or "caret") in the component. Unlike the behavior of `selectionColor` the cursor color will be set independently from the color of the text selection box.

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `disableFullscreenUI` <div class="label android">Android</div>

When `false`, if there is a small amount of space available around a text input (e.g. landscape orientation on a phone), the OS may choose to have the user edit the text inside of a full screen text input mode. When `true`, this feature is disabled and users will always edit the text directly inside of the text input. Defaults to `false`.

| Type |
| ---- |
| bool |

---

### `editable`

If `false`, text is not editable. The default value is `true`.

| Type |
| ---- |
| bool |

---

### `enablesReturnKeyAutomatically` <div class="label ios">iOS</div>

If `true`, the keyboard disables the return key when there is no text and automatically enables it when there is text. The default value is `false`.

| Type |
| ---- |
| bool |

---

### `enterKeyHint`

Determines what text should be shown to the return key. Has precedence over the `returnKeyType` prop.

The following values work across platforms:

- `enter`
- `done`
- `next`
- `search`
- `send`

_Android Only_

The following values work on Android only:

- `previous`

| Type                                                        |
| ----------------------------------------------------------- |
| enum('enter', 'done', 'next', 'previous', 'search', 'send') |

---

### `importantForAutofill` <div class="label android">Android</div>

Tells the operating system whether the individual fields in your app should be included in a view structure for autofill purposes on Android API Level 26+. Possible values are `auto`, `no`, `noExcludeDescendants`, `yes`, and `yesExcludeDescendants`. The default value is `auto`.

- `auto`: Let the Android System use its heuristics to determine if the view is important for autofill.
- `no`: This view isn't important for autofill.
- `noExcludeDescendants`: This view and its children aren't important for autofill.
- `yes`: This view is important for autofill.
- `yesExcludeDescendants`: This view is important for autofill, but its children aren't important for autofill.

| Type                                                                       |
| -------------------------------------------------------------------------- |
| enum('auto', 'no', 'noExcludeDescendants', 'yes', 'yesExcludeDescendants') |

---

### `inlineImageLeft` <div class="label android">Android</div>

若定義此屬性，提供的圖片資源將顯示在輸入框左側。圖片資源必須位於 `/android/app/src/main/res/drawable` 目錄下，並以以下方式引用：

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

可選標識符，用於將自定義的 [InputAccessoryView](inputaccessoryview.md) 與此文字輸入框連結。當此文字輸入框獲得焦點時，InputAccessoryView 會顯示在鍵盤上方。

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

決定鍵盤的顏色。

| Type                             |
| -------------------------------- |
| enum('default', 'light', 'dark') |

---

### `keyboardType`

決定開啟哪種鍵盤，例如 `numeric`。

所有鍵盤類型的截圖可參見[此處](https://lefkowitz.me/2018/04/30/visual-guide-to-react-native-textinput-keyboardtype-options/)。

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

當啟用 `allowFontScaling` 時，指定字體可達到的最大縮放比例。可能數值：

- `null/undefined`（預設）：繼承父節點或全域預設值（0）
- `0`：無限制，忽略父節點/全域預設值
- `>= 1`：將此節點的 `maxFontSizeMultiplier` 設為此值

| Type   |
| ------ |
| number |

---

### `maxLength`

限制可輸入的最大字元數。使用此屬性替代在 JS 中實作邏輯，以避免閃爍現象。

| Type   |
| ------ |
| number |

---

### `multiline`

若設為 `true`，文字輸入框可支援多行輸入。預設值為 `false`。

:::note
需要注意的是，這會使文字在 iOS 上對齊頂部，而在 Android 上居中顯示。若要讓兩個平台的行為一致，請將 `textAlignVertical` 設為 `top`。
:::

| Type |
| ---- |
| bool |

---

### `numberOfLines` <div class="label android">Android</div>

設定 `TextInput` 的行數。需搭配 `multiline` 設為 `true` 才能填滿這些行。

| Type   |
| ------ |
| number |

---

### `onBlur`

當文字輸入框失去焦點時觸發的回調函數。

> 注意：若您嘗試從 `nativeEvent` 獲取 `text` 值，請注意結果可能是 `undefined`，這可能導致非預期的錯誤。若要取得 TextInput 的最終值，可以使用 [`onEndEditing`](textinput#onendediting) 事件，該事件會在編輯完成時觸發。

| Type     |
| -------- |
| function |

---

### `onChange`

當文字輸入框的文字內容變更時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {eventCount, target, text}}`) => void |

---

### `onChangeText`

當文字輸入框的文字內容變更時觸發的回調函數。變更後的文字會以單一字串參數的形式傳遞給回調處理函數。

| Type     |
| -------- |
| function |

---

### `onContentSizeChange`

當文字輸入框的內容大小變更時觸發的回調函數。

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

當按鍵被按下時觸發的回調函數。會傳遞一個物件，其中 `keyValue` 對於 Enter 鍵為 `'Enter'`，Backspace 鍵為 `'Backspace'`，其他輸入字元則為對應的字元（包括空格 `' '`）。此回調會在 `onChange` 之前觸發。注意：在 Android 上僅處理軟鍵盤的輸入，不處理硬體鍵盤的輸入。

| Type                                        |
| ------------------------------------------- |
| (`{nativeEvent: {key: keyValue} }`) => void |

---

### `onLayout`

在元件掛載或佈局變更時觸發。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onScroll`

當內容滾動時觸發。可能包含 `ScrollEvent` 的其他屬性，但在 Android 上為了效能考量不會提供 `contentSize`。

| Type                                                |
| --------------------------------------------------- |
| (`{nativeEvent: {contentOffset: {x, y} }}`) => void |

---

### `onSelectionChange`

當文字輸入框的選取範圍變更時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {selection: {start, end} }}`) => void |

---

### `onSubmitEditing`

當文字輸入的提交按鈕被按下時觸發的回調函數。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {text, eventCount, target}}`) => void |

請注意，在 iOS 上使用 `keyboardType="phone-pad"` 時不會調用此方法。

---

### `placeholder`

在文字輸入前顯示的提示字串。

| Type   |
| ------ |
| string |

---

### `placeholderTextColor`

提示字串的文字顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `readOnly`

若為 `true`，則文字不可編輯。預設值為 `false`。

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

若為 `true`，允許 TextInput 將觸控事件傳遞給父組件。這使得如 SwipeableListView 等組件可以從 TextInput 上滑動（在 Android 上預設即如此）。若為 `false`，TextInput 總是要求處理輸入（除非被禁用）。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `rows` <div class="label android">Android</div>

設定 `TextInput` 的行數。需與 `multiline` 設為 `true` 搭配使用以填滿行數。

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

若為 `true`，文字輸入會隱藏輸入內容以保護如密碼等敏感資訊。預設值為 `false`。不適用於 `multiline={true}`。

| Type |
| ---- |
| bool |

---

### `selection`

文字輸入的選取範圍起點與終點。將起點與終點設為相同值可定位游標位置。

| Type                                  |
| ------------------------------------- |
| object: `{start: number,end: number}` |

---

### `selectionColor`

文字輸入框的高亮、選取控制柄和游標顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `selectionHandleColor` <div class="label android">Android</div>

設定選取控制柄的顏色。與 `selectionColor` 不同，它允許獨立於選取顏色自訂選取控制柄的顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `selectTextOnFocus`

若為 `true`，所有文字將在獲得焦點時自動被選取。

| Type |
| ---- |
| bool |

---

### `showSoftInputOnFocus`

當設為 `false` 時，將阻止欄位獲得焦點時顯示軟鍵盤。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `spellCheck` <div class="label ios">iOS</div>

若為 `false`，則停用拼字檢查樣式（即紅色底線）。預設值繼承自 `autoCorrect`。

| Type |
| ---- |
| bool |

---

### `textAlign`

將輸入文字對齊輸入欄位的左側、中央或右側。

`textAlign` 的可能值為：

- `left`
- `center`
- `right`

| Type                            |
| ------------------------------- |
| enum('left', 'center', 'right') |

---

### `textContentType` <div class="label ios">iOS</div>

向鍵盤和系統提供關於使用者輸入內容預期語義意義的資訊。

:::note
[`autoComplete`](#autocomplete) 提供相同的功能且適用於所有平台。您可以使用 [`Platform.select`](/docs/next/platform#select) 來處理不同平台的行為差異。

避免同時使用 `textContentType` 和 `autoComplete`。為了向後兼容，當兩個屬性都設定時，`textContentType` 將優先。
:::

您可以將 `textContentType` 設為 `username` 或 `password` 以啟用從裝置鑰匙圈自動填入登入詳細資訊。

`newPassword` 可用於表示使用者可能想儲存在鑰匙圈中的新密碼輸入，而 `oneTimeCode` 可用於表示欄位可透過簡訊收到的代碼自動填入。

若要停用自動填入，請將 `textContentType` 設為 `none`。

`textContentType` 的可能值為：

- `none` (無)
- `addressCity` (城市)
- `addressCityAndState` (城市與州)
- `addressState` (州)
- `birthdate` (iOS 17+ 生日)
- `birthdateDay` (iOS 17+ 生日日期)
- `birthdateMonth` (iOS 17+ 生日月份)
- `birthdateYear` (iOS 17+ 生日年份)
- `countryName` (國家名稱)
- `creditCardExpiration` (iOS 17+ 信用卡到期日)
- `creditCardExpirationMonth` (iOS 17+ 信用卡到期月份)
- `creditCardExpirationYear` (iOS 17+ 信用卡到期年份)
- `creditCardFamilyName` (iOS 17+ 信用卡姓氏)
- `creditCardGivenName` (iOS 17+ 信用卡名字)
- `creditCardMiddleName` (iOS 17+ 信用卡中間名)
- `creditCardName` (iOS 17+ 信用卡姓名)
- `creditCardNumber` (信用卡號碼)
- `creditCardSecurityCode` (iOS 17+ 信用卡安全碼)
- `creditCardType` (iOS 17+ 信用卡類型)
- `emailAddress` (電子郵件地址)
- `familyName` (姓氏)
- `fullStreetAddress` (完整街道地址)
- `givenName` (名字)
- `jobTitle` (職稱)
- `location` (位置)
- `middleName` (中間名)
- `name` (姓名)
- `namePrefix` (姓名前綴)
- `nameSuffix` (姓名後綴)
- `newPassword` (新密碼)
- `nickname` (暱稱)
- `oneTimeCode` (一次性代碼)
- `organizationName` (組織名稱)
- `password` (密碼)
- `postalCode` (郵政編碼)
- `streetAddressLine1` (街道地址行1)
- `streetAddressLine2` (街道地址行2)
- `sublocality` (次區域)
- `telephoneNumber` (電話號碼)
- `URL` (網址)
- `username` (使用者名稱)

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| enum('none', 'addressCity', 'addressCityAndState', 'addressState', 'birthdate', 'birthdateDay', 'birthdateMonth', 'birthdateYear', 'countryName', 'creditCardExpiration', 'creditCardExpirationMonth', 'creditCardExpirationYear', 'creditCardFamilyName', 'creditCardGivenName', 'creditCardMiddleName', 'creditCardName', 'creditCardNumber', 'creditCardSecurityCode', 'creditCardType', 'emailAddress', 'familyName', 'fullStreetAddress', 'givenName', 'jobTitle', 'location', 'middleName', 'name', 'namePrefix', 'nameSuffix', 'newPassword', 'nickname', 'oneTimeCode', 'organizationName', 'password', 'postalCode', 'streetAddressLine1', 'streetAddressLine2', 'sublocality', 'telephoneNumber', 'URL', 'username') |

---

### `passwordRules` <div class="label ios">iOS</div>

當在 iOS 上使用 `textContentType` 設為 `newPassword` 時，我們可以讓作業系統知道密碼的最低要求，以便生成符合這些要求的密碼。要創建有效的 `PasswordRules` 字符串，請參考 [Apple 文檔](https://developer.apple.com/password-rules/)。

> 如果密碼生成對話框未出現，請確保：
>
> - 已啟用自動填充：**設定** → **密碼與帳號** → 切換「**自動填充密碼**」為「開啟」，
> - 使用 iCloud 鑰匙圈：**設定** → **Apple ID** → **iCloud** → **鑰匙圈** → 切換「**iCloud 鑰匙圈**」為「開啟」。

| Type   |
| ------ |
| string |

---

### `style`

請注意，並非所有文字樣式都受到支持，以下是不支持的部分樣式列表：

- `borderLeftWidth` (左邊框寬度)
- `borderTopWidth` (上邊框寬度)
- `borderRightWidth` (右邊框寬度)
- `borderBottomWidth` (下邊框寬度)
- `borderTopLeftRadius` (左上邊框半徑)
- `borderTopRightRadius` (右上邊框半徑)
- `borderBottomRightRadius` (右下邊框半徑)
- `borderBottomLeftRadius` (左下邊框半徑)

[樣式](style.md)

| Type                  |
| --------------------- |
| [Text](text.md#style) |

---

### `textBreakStrategy` <div class="label android">Android</div>

在 Android API Level 23+ 上設置文字斷行策略，可能的值為 `simple` (簡單)、`highQuality` (高品質)、`balanced` (平衡)，預設值為 `highQuality`。

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

文字輸入框顯示的值。`TextInput` 是一個受控組件，這意味著如果提供了此值屬性，原生值將被強制匹配此值。在大多數情況下，這運作良好，但在某些情況下可能會導致閃爍——一個常見的原因是通過保持值不變來防止編輯。除了設置相同的值外，可以設置 `editable={false}`，或設置/更新 `maxLength` 以防止不需要的編輯而不會閃爍。

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

使原生輸入請求獲得焦點。

### `.blur()`

```tsx
blur();
```

使原生輸入失去焦點。

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

如果輸入目前獲得焦點則返回 `true`；否則返回 `false`。

# 已知問題

- [react-native#19096](https://github.com/facebook/react-native/issues/19096): 不支援 Android 的 `onKeyPreIme`。
- [react-native#19366](https://github.com/facebook/react-native/issues/19366): 在透過返回按鈕關閉 Android 鍵盤後呼叫 .focus() 不會再次喚起鍵盤。
- [react-native#26799](https://github.com/facebook/react-native/issues/26799): 當 `keyboardType="email-address"` 或 `keyboardType="phone-pad"` 時不支援 Android 的 `secureTextEntry`。