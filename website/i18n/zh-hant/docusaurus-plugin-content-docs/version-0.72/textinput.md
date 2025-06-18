---
id: textinput
title: TextInput
---

一個基礎組件，用於透過鍵盤在應用程式中輸入文字。其屬性可配置多種功能，如自動校正、自動大寫、佔位文字以及不同鍵盤類型（例如數字鍵盤）。

最基本的用法是放置一個 `TextInput` 並訂閱 `onChangeText` 事件來讀取用戶輸入。還可以訂閱其他事件，如 `onSubmitEditing` 和 `onFocus`。以下是一個簡單範例：

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

透過原生元素暴露的兩個方法 `.focus()` 和 `.blur()` 可以程式化地聚焦或模糊 TextInput。

請注意，某些屬性僅在 `multiline={true/false}` 時可用。此外，若 `multiline=true`，則僅應用於元素單邊的邊框樣式（例如 `borderBottomColor`、`borderLeftWidth` 等）將不會生效。要達到相同效果，可以將 `TextInput` 包裹在 `View` 中：

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

`TextInput` 預設在其視圖底部有一個邊框。此邊框的內邊距由系統提供的背景圖片設定，無法更改。解決方案是避免明確設定高度（系統會自動正確顯示邊框位置），或透過將 `underlineColorAndroid` 設為透明來隱藏邊框。

請注意，在 Android 上，輸入框中的文字選取操作可能會將應用的活動 `windowSoftInputMode` 參數更改為 `adjustResize`。這可能導致鍵盤激活時，具有 `position: 'absolute'` 的組件出現問題。要避免此行為，可以在 AndroidManifest.xml 中指定 `windowSoftInputMode`（https://developer.android.com/guide/topics/manifest/activity-element.html），或透過原生代碼程式化控制此參數。

---

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view.md#props)。

---

### `allowFontScaling`

指定字體是否應縮放以符合「文字大小」輔助設定。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `autoCapitalize`

告訴 `TextInput` 自動大寫特定字元。此屬性不受某些鍵盤類型支援（例如 `name-phone-pad`）。

- `characters`: 所有字元。
- `words`: 每個單詞的首字母。
- `sentences`: 每個句子的首字母（_預設_）。
- `none`: 不自動大寫任何內容。

| Type                                             |
| ------------------------------------------------ |
| enum('none', 'sentences', 'words', 'characters') |

---

### `autoComplete`

為系統指定自動完成提示，以便提供自動填充功能。在 Android 上，系統會始終嘗試透過啟發式識別內容類型來提供自動填充。要禁用自動完成，請將 `autoComplete` 設為 `off`。

以下值跨平台通用：

- `additional-name`
- `address-line1`
- `address-line2`
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

以下值僅在 iOS 上有效：

- `nickname`
- `organization`
- `organization-title`
- `url`

<div class="label basic android">Android</div>

以下數值僅適用於 Android：

- `birthdate-day`
- `birthdate-full`
- `birthdate-month`
- `birthdate-year`
- `cc-csc`
- `cc-exp`
- `cc-exp-day`
- `cc-exp-month`
- `cc-exp-year`
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
- `tel-national`
- `tel-device`
- `username-new`

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('additional-name', 'address-line1', 'address-line2', 'birthdate-day', 'birthdate-full', 'birthdate-month', 'birthdate-year', 'cc-csc', 'cc-exp', 'cc-exp-day', 'cc-exp-month', 'cc-exp-year', 'cc-number', 'country', 'current-password', 'email', 'family-name', 'gender', 'given-name', 'honorific-prefix', 'honorific-suffix', 'name', 'name-family', 'name-given', 'name-middle', 'name-middle-initial', 'name-prefix', 'name-suffix', 'new-password', 'nickname', 'one-time-code', 'organization', 'organization-title', 'password', 'password-new', 'postal-address', 'postal-address-country', 'postal-address-extended', 'postal-address-extended-postal-code', 'postal-address-locality', 'postal-address-region', 'postal-code', 'street-address', 'sms-otp', 'tel', 'tel-country-code', 'tel-national', 'tel-device', 'url', 'username', 'username-new', 'off') |

---

### `autoCorrect`

若設為 `false`，則停用自動校正功能。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `autoFocus`

若設為 `true`，則會在 `componentDidMount` 或 `useEffect` 時自動聚焦輸入框。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `blurOnSubmit`

若設為 `true`，則在提交時會使文字框失去焦點。單行輸入框的預設值為 true，多行輸入框則為 false。請注意，對多行輸入框設定 `blurOnSubmit` 為 `true` 時，按下返回鍵會使輸入框失去焦點並觸發 `onSubmitEditing` 事件，而非在欄位中插入換行符號。

| Type |
| ---- |
| bool |

---

### `caretHidden`

若設為 `true`，則會隱藏游標。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `clearButtonMode` <div class="label ios">iOS</div>

決定清除按鈕應何時出現在文字輸入框的右側。此屬性僅支援單行 TextInput 元件。預設值為 `never`。

| Type                                                       |
| ---------------------------------------------------------- |
| enum('never', 'while-editing', 'unless-editing', 'always') |

---

### `clearTextOnFocus` <div class="label ios">iOS</div>

若設為 `true`，則在開始編輯時會自動清除文字框內容。

| Type |
| ---- |
| bool |

---

### `contextMenuHidden`

若設為 `true`，則會隱藏上下文選單。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `dataDetectorTypes` <div class="label ios">iOS</div>

決定文字輸入框中哪些類型的資料會被轉換為可點擊的連結。僅在 `multiline={true}` 且 `editable={false}` 時有效。預設情況下不偵測任何資料類型。

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

提供初始值，該值會在使用者開始輸入時改變。適用於您不想處理監聽事件並更新 value 屬性以保持受控狀態同步的使用情境。

| Type   |
| ------ |
| string |

---

### `cursorColor` <div class="label android">Android</div>

當提供此屬性時，它會設定元件中游標（或「插入符號」）的顏色。與 `selectionColor` 的行為不同，游標顏色將獨立於文字選取框的顏色設定。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `disableFullscreenUI` <div class="label android">Android</div>

當設為 `false` 時，如果文字輸入周圍可用空間較小（例如手機的橫向模式），作業系統可能會選擇讓使用者在全螢幕文字輸入模式中編輯文字。設為 `true` 時，此功能將被禁用，使用者將始終直接在文字輸入框中編輯文字。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `editable`

如果設為 `false`，文字將不可編輯。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `enablesReturnKeyAutomatically` <div class="label ios">iOS</div>

如果設為 `true`，鍵盤會在沒有文字時禁用返回鍵，並在有文字時自動啟用它。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `enterKeyHint`

決定返回鍵上應顯示的文字。此屬性優先於 `returnKeyType` 屬性。

以下值適用於所有平台：

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

告訴作業系統是否應將應用中的個別欄位包含在 Android API 26+ 的自動填充視圖結構中。可能的值為 `auto`、`no`、`noExcludeDescendants`、`yes` 和 `yesExcludeDescendants`。預設值為 `auto`。

- `auto`：讓 Android 系統使用其啟發式方法決定視圖是否對自動填充重要。
- `no`：此視圖對自動填充不重要。
- `noExcludeDescendants`：此視圖及其子視圖對自動填充不重要。
- `yes`：此視圖對自動填充重要。
- `yesExcludeDescendants`：此視圖對自動填充重要，但其子視圖不重要。

| Type                                                                       |
| -------------------------------------------------------------------------- |
| enum('auto', 'no', 'noExcludeDescendants', 'yes', 'yesExcludeDescendants') |

---

### `inlineImageLeft` <div class="label android">Android</div>

如果定義，提供的圖片資源將顯示在左側。圖片資源必須位於 `/android/app/src/main/res/drawable` 中，並以類似方式引用。

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

內嵌圖片（如果有）與文字輸入框本身之間的間距。

| Type   |
| ------ |
| number |

---

### `inputAccessoryViewID` <div class="label ios">iOS</div>

一個可選的標識符，用於將自定義的 [InputAccessoryView](inputaccessoryview.md) 連結到此文字輸入框。當此文字輸入框獲得焦點時，InputAccessoryView 會顯示在鍵盤上方。

| Type   |
| ------ |
| string |

---

### `inputMode`

功能類似於 HTML 中的 `inputmode` 屬性，它決定要開啟哪種鍵盤，例如 `numeric`，並且優先於 `keyboardType`。

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

決定要開啟哪種鍵盤，例如 `numeric`。

所有類型的截圖請參見[這裡](http://lefkowitz.me/2018/04/30/visual-guide-to-react-native-textinput-keyboardtype-options/)。

以下數值跨平台通用：

- `default`
- `number-pad`
- `decimal-pad`
- `numeric`
- `email-address`
- `phone-pad`
- `url`

_iOS 專用_

以下數值僅在 iOS 上有效：

- `ascii-capable`
- `numbers-and-punctuation`
- `name-phone-pad`
- `twitter`
- `web-search`

_Android 專用_

以下數值僅在 Android 上有效：

- `visible-password`

| Type                                                                                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('default', 'email-address', 'numeric', 'phone-pad', 'ascii-capable', 'numbers-and-punctuation', 'url', 'number-pad', 'name-phone-pad', 'decimal-pad', 'twitter', 'web-search', 'visible-password') |

---

### `maxFontSizeMultiplier`

指定當 `allowFontScaling` 啟用時，字體可以達到的最大縮放比例。可能的數值：

- `null/undefined`（預設）：繼承父節點或全域預設值（0）
- `0`：無最大值，忽略父節點/全域預設值
- `>= 1`：將此節點的 `maxFontSizeMultiplier` 設為此數值

| Type   |
| ------ |
| number |

---

### `maxLength`

限制可以輸入的最大字元數。使用此屬性代替在 JS 中實現邏輯以避免閃爍。

| Type   |
| ------ |
| number |

---

### `multiline`

如果為 `true`，文字輸入可以有多行。預設值為 `false`。

:::note
需要注意的是，這在 iOS 上會將文字對齊頂部，而在 Android 上會居中對齊。若要讓兩個平台的行為一致，請將 `textAlignVertical` 設為 `top`。
:::

| Type |
| ---- |
| bool |

---

### `numberOfLines` <div class="label android">Android</div>

設定 `TextInput` 的行數。與 `multiline` 設為 `true` 一起使用，以便填滿行數。

| Type   |
| ------ |
| number |

---

### `onBlur`

當文字輸入失去焦點時呼叫的回調函數。

> 注意：如果您嘗試從 `nativeEvent` 中存取 `text` 值，請注意得到的值可能是 `undefined`，這可能會導致意外錯誤。如果您想找到 TextInput 的最後一個值，可以使用 [`onEndEditing`](textinput#onendediting) 事件，該事件在編輯完成時觸發。

| Type     |
| -------- |
| function |

---

### `onChange`

當文字輸入框的文字變更時呼叫的回調函式。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {eventCount, target, text}}`) => void |

---

### `onChangeText`

當文字輸入框的文字變更時呼叫的回調函式。變更後的文字會以單一字串參數形式傳遞給回調處理器。

| Type     |
| -------- |
| function |

---

### `onContentSizeChange`

當文字輸入框的內容尺寸變更時呼叫的回調函式。

僅適用於多行文字輸入框。

| Type                                                       |
| ---------------------------------------------------------- |
| (`{nativeEvent: {contentSize: {width, height} }}`) => void |

---

### `onEndEditing`

當文字輸入結束時呼叫的回調函式。

| Type     |
| -------- |
| function |

---

### `onPressIn`

當觸碰動作開始時呼叫的回調函式。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressOut`

當觸碰動作結束時呼叫的回調函式。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onFocus`

當文字輸入框獲得焦點時呼叫的回調函式。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onKeyPress`

當按鍵被按下時呼叫的回調函式。會傳遞一個物件參數，其中 `keyValue` 會是 `'Enter'` 或 `'Backspace'` 對應按鍵，其餘輸入字元則包含空格 `' '`。此回調會在 `onChange` 之前觸發。注意：在 Android 上僅處理軟鍵盤輸入，不處理硬體鍵盤輸入。

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

當內容滾動時觸發。可能包含來自 `ScrollEvent` 的其他屬性，但在 Android 上因效能考量不會提供 `contentSize`。

| Type                                                |
| --------------------------------------------------- |
| (`{nativeEvent: {contentOffset: {x, y} }}`) => void |

---

### `onSelectionChange`

當文字輸入框的選取範圍變更時呼叫的回調函式。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {selection: {start, end} }}`) => void |

---

### `onSubmitEditing`

當文字輸入框的提交按鈕被按下時呼叫的回調函式。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {text, eventCount, target}}`) => void |

注意：在 iOS 上使用 `keyboardType="phone-pad"` 時不會呼叫此方法。

---

### `placeholder`

在文字輸入框尚未輸入內容時顯示的預留文字。

| Type   |
| ------ |
| string |

---

### `placeholderTextColor`

預留文字的字體顏色。

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

設定返回鍵的標籤文字。可替代 `returnKeyType` 使用。

| Type   |
| ------ |
| string |

---

### `returnKeyType`

決定返回鍵的外觀樣式。在 Android 上也可以使用 `returnKeyLabel`。

_跨平台支援_

以下數值適用於所有平台：

- `done` (完成)
- `go` (前往)
- `next` (下一個)
- `search` (搜尋)
- `send` (傳送)

_僅限 Android_

以下數值僅在 Android 上有效：

- `none` (無)
- `previous` (上一個)

_僅限 iOS_

以下數值僅在 iOS 上有效：

- `default` (預設)
- `emergency-call` (緊急通話)
- `google` (Google)
- `join` (加入)
- `route` (路線)
- `yahoo` (Yahoo)

| Type                                                                                                                              |
| --------------------------------------------------------------------------------------------------------------------------------- |
| enum('done', 'go', 'next', 'search', 'send', 'none', 'previous', 'default', 'emergency-call', 'google', 'join', 'route', 'yahoo') |

### `rejectResponderTermination` <div class="label ios">iOS</div>

若設為 `true`，允許 TextInput 將觸控事件傳遞給父元件。這使得如 SwipeableListView 等元件能從 TextInput 處滑動操作（Android 預設即支援此行為）。若設為 `false`，TextInput 會始終要求處理輸入（除非被禁用）。預設值為 `true`。

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

若設為 `false`，將禁用文字視窗的滾動功能。預設值為 `true`。僅在 `multiline={true}` 時有效。

| Type |
| ---- |
| bool |

---

### `secureTextEntry`

若設為 `true`，文字輸入會隱藏內容（如密碼等敏感資訊）。預設值為 `false`。不可與 `multiline={true}` 同時使用。

| Type |
| ---- |
| bool |

---

### `selection`

文字輸入選取範圍的起始與結束位置。將起始與結束設為相同值可定位游標。

| Type                                  |
| ------------------------------------- |
| object: `{start: number,end: number}` |

---

### `selectionColor`

文字輸入的選取反白與游標顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `selectTextOnFocus`

若設為 `true`，聚焦時會自動選取全部文字。

| Type |
| ---- |
| bool |

---

### `showSoftInputOnFocus`

設為 `false` 時，聚焦該欄位將不會觸發軟鍵盤彈出。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `spellCheck` <div class="label ios">iOS</div>

若設為 `false`，將禁用拼字檢查樣式（如紅色底線）。預設值繼承自 `autoCorrect`。

| Type |
| ---- |
| bool |

---

### `textAlign`

將輸入文字對齊輸入欄位的左側、中央或右側。

`textAlign` 的可能數值為：

- `left` (左對齊)
- `center` (置中)
- `right` (右對齊)

| Type                            |
| ------------------------------- |
| enum('left', 'center', 'right') |

---

### `textContentType` <div class="label ios">iOS</div>

向鍵盤和系統提供關於使用者輸入內容預期語義的資訊。

:::note
[`autoComplete`](#autocomplete) 提供相同功能且支援全平台。可使用 [`Platform.select`](/docs/next/platform#select) 處理平台差異行為。

避免同時使用 `textContentType` 和 `autoComplete`。為保持向後兼容性，當兩個屬性同時設定時，`textContentType` 會優先生效。
:::

在 iOS 11+ 上，可將 `textContentType` 設為 `username` 或 `password` 以啟用裝置鑰匙圈自動填入登入資訊功能。

在 iOS 12+ 上，`newPassword` 可用於標示使用者可能想儲存至鑰匙圈的新密碼輸入欄位，`oneTimeCode` 則可用於標示可透過簡訊驗證碼自動填入的欄位。

若要停用自動填入，請將 `textContentType` 設為 `none`。

`textContentType` 的可能值包括：

- `none`
- `URL`
- `addressCity`
- `addressCityAndState`
- `addressState`
- `countryName`
- `creditCardNumber`
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
- `nickname`
- `organizationName`
- `postalCode`
- `streetAddressLine1`
- `streetAddressLine2`
- `sublocality`
- `telephoneNumber`
- `username`
- `password`
- `newPassword`
- `oneTimeCode`

| Type                                                                                                                                                                                                                                                                                                                                                                                                       |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('none', 'URL', 'addressCity', 'addressCityAndState', 'addressState', 'countryName', 'creditCardNumber', 'emailAddress', 'familyName', 'fullStreetAddress', 'givenName', 'jobTitle', 'location', 'middleName', 'name', 'namePrefix', 'nameSuffix', 'nickname', 'organizationName', 'postalCode', 'streetAddressLine1', 'streetAddressLine2', 'sublocality', 'telephoneNumber', 'username', 'password') |

---

### `passwordRules` <div class="label ios">iOS</div>

當在 iOS 上使用 `textContentType` 設為 `newPassword` 時，可透過此屬性讓系統知悉密碼的最低要求，以便生成符合條件的密碼。建立有效的 `PasswordRules` 字串請參閱 [Apple 官方文件](https://developer.apple.com/password-rules/)。

> 若未出現密碼生成對話框，請確認：
>
> - 已啟用自動填入：**設定** → **密碼與帳號** → 開啟 **自動填入密碼**
> - 已使用 iCloud 鑰匙圈：**設定** → **Apple ID** → **iCloud** → **鑰匙圈** → 開啟 **iCloud 鑰匙圈**

| Type   |
| ------ |
| string |

---

### `style`

請注意並非所有文字樣式都受支援，以下為部分不支援的樣式清單：

- `borderLeftWidth`
- `borderTopWidth`
- `borderRightWidth`
- `borderBottomWidth`
- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomRightRadius`
- `borderBottomLeftRadius`

詳見 [Issue#7070](https://github.com/facebook/react-native/issues/7070)。

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

文字輸入框顯示的值。`TextInput` 是一個受控元件，這意味著如果提供了這個值屬性，原生值將被迫與之匹配。在大多數情況下，這運作良好，但在某些情況下可能會導致閃爍——一個常見的原因是通過保持值不變來防止編輯。除了設置相同的值外，可以設置 `editable={false}`，或者設置/更新 `maxLength` 來防止不需要的編輯而不會閃爍。

| Type   |
| ------ |
| string |

---

### `lineBreakStrategyIOS` <div class="label ios">iOS</div>

在 iOS 14+ 上設置換行策略。可能的值為 `none`、`standard`、`hangul-word` 和 `push-out`。

| Type                                                        | Default  |
| ----------------------------------------------------------- | -------- |
| enum(`'none'`, `'standard'`, `'hangul-word'`, `'push-out'`) | `'none'` |

## 方法

### `.focus()`

```tsx
focus();
```

使原生輸入請求焦點。

### `.blur()`

```tsx
blur();
```

使原生輸入失去焦點。

### `clear()`

```tsx
clear();
```

移除 `TextInput` 中的所有文字。

---

### `isFocused()`

```tsx
isFocused(): boolean;
```

如果輸入目前有焦點則返回 `true`；否則返回 `false`。

# 已知問題

- [react-native#19096](https://github.com/facebook/react-native/issues/19096): 不支援 Android 的 `onKeyPreIme`。
- [react-native#19366](https://github.com/facebook/react-native/issues/19366): 在通過返回按鈕關閉 Android 鍵盤後呼叫 .focus() 不會再次彈出鍵盤。
- [react-native#26799](https://github.com/facebook/react-native/issues/26799): 當 `keyboardType="email-address"` 或 `keyboardType="phone-pad"` 時不支援 Android 的 `secureTextEntry`。