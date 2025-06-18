---
id: textinput
title: TextInput
---

一個基礎組件，用於通過鍵盤向應用程式輸入文本。其屬性可配置多種功能，如自動校正、自動大寫、佔位文本以及不同類型的鍵盤（例如數字鍵盤）。

最基本的用法是放置一個 `TextInput` 並訂閱 `onChangeText` 事件來讀取用戶輸入。還有其他事件如 `onSubmitEditing` 和 `onFocus` 可供訂閱。以下是一個簡單示例：

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

通過原生元素暴露的兩個方法是 `.focus()` 和 `.blur()`，可以以程式方式聚焦或模糊 TextInput。

請注意，某些屬性僅在 `multiline={true/false}` 時可用。此外，如果 `multiline=true`，則僅應用於元素單側的邊框樣式（例如 `borderBottomColor`、`borderLeftWidth` 等）將不會生效。要實現相同的效果，可以將 `TextInput` 包裹在 `View` 中：

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

`TextInput` 默認在其視圖底部有一個邊框。此邊框的填充由系統提供的背景圖像設置，無法更改。避免此問題的解決方案要麼不顯式設置高度（系統會負責在正確位置顯示邊框），要麼通過將 `underlineColorAndroid` 設置為透明來不顯示邊框。

請注意，在 Android 上，在輸入框中執行文本選擇可能會將應用的活動 `windowSoftInputMode` 參數更改為 `adjustResize`。這可能會導致在鍵盤活動時具有 `position: 'absolute'` 的組件出現問題。要避免這種行為，可以在 AndroidManifest.xml 中指定 `windowSoftInputMode`（https://developer.android.com/guide/topics/manifest/activity-element.html），或通過原生代碼以程式方式控制此參數。

---

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view.md#props)。

---

### `allowFontScaling`

指定字體是否應縮放以遵循「文字大小」輔助功能設置。默認為 `true`。

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

為系統指定自動完成提示，以便提供自動填充。在 Android 上，系統始終會嘗試通過啟發式方法識別內容類型來提供自動填充。要禁用自動完成，請將 `autoComplete` 設置為 `off`。

以下值在所有平台上均適用：

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

若設為 `false`，則停用自動校正功能。預設值為 `true`。

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

> **已棄用。** 請注意 `submitBehavior` 現已取代 `blurOnSubmit`，並會覆蓋 `blurOnSubmit` 定義的任何行為。詳見 [submitBehavior](textinput#submitbehavior)

若設為 `true`，文字欄位在提交時會失去焦點。單行欄位的預設值為 true，多行欄位則為 false。請注意，對於多行欄位，將 `blurOnSubmit` 設為 `true` 意味著按下返回鍵會使欄位失去焦點並觸發 `onSubmitEditing` 事件，而非在欄位中插入換行符。

| Type |
| ---- |
| bool |

---

### `caretHidden`

若設為 `true`，則隱藏輸入游標。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `clearButtonMode` <div class="label ios">iOS</div>

決定清除按鈕何時出現在文字視圖右側。此屬性僅支援單行 TextInput 元件。預設值為 `never`。

| Type                                                       |
| ---------------------------------------------------------- |
| enum('never', 'while-editing', 'unless-editing', 'always') |

---

### `clearTextOnFocus` <div class="label ios">iOS</div>

若設為 `true`，開始編輯時會自動清除文字欄位內容。

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

提供初始值，該值會在使用者開始輸入時改變。適用於不想處理事件監聽與同步更新受控狀態值的場景。

| Type   |
| ------ |
| string |

---

### `cursorColor` <div class="label android">Android</div>

設定元件中游標（或稱「插入符」）的顏色。與 `selectionColor` 的行為不同，此屬性會獨立於文字選取框的顏色設定游標顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `disableFullscreenUI` <div class="label android">Android</div>

設為 `false` 時，若文字輸入周圍空間不足（例如手機橫向模式），作業系統可能讓使用者在全螢幕文字輸入模式中編輯。設為 `true` 則停用此功能，使用者永遠直接在文字輸入框內編輯。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `editable`

設為 `false` 時，文字不可編輯。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `enablesReturnKeyAutomatically` <div class="label ios">iOS</div>

設為 `true` 時，若無文字輸入則停用返回鍵，有文字時自動啟用。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `enterKeyHint`

決定返回鍵顯示的文字。優先級高於 `returnKeyType` 屬性。

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

告知作業系統是否應將應用程式中的個別欄位納入 Android API 26+ 的自動填充視圖結構。可能值為 `auto`、`no`、`noExcludeDescendants`、`yes` 和 `yesExcludeDescendants`。預設值為 `auto`。

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

若定義此屬性，提供的圖片資源將渲染在左側。圖片資源必須位於 `/android/app/src/main/res/drawable` 目錄下，並以如下方式引用：

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

可選標識符，用於將自訂的 [InputAccessoryView](inputaccessoryview.md) 連結到此文字輸入框。當此文字輸入框獲得焦點時，InputAccessoryView 會渲染在鍵盤上方。

| Type   |
| ------ |
| string |

---

### `inputAccessoryViewButtonLabel` <div class="label ios">iOS</div>

可選標籤，用於覆蓋預設的 [InputAccessoryView](inputaccessoryview.md) 按鈕標籤。

預設情況下，按鈕標籤未本地化。使用此屬性可提供本地化版本。

| Type   |
| ------ |
| string |

---

### `inputMode`

功能類似 HTML 中的 `inputmode` 屬性，決定開啟哪種鍵盤（例如 `numeric`），且優先級高於 `keyboardType`。

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

限制可輸入的最大字元數。使用此屬性替代在 JavaScript 中實作邏輯，以避免閃爍問題。

| Type   |
| ------ |
| number |

---

### `multiline`

若為 `true`，文字輸入框可支援多行輸入。預設值為 `false`。

:::note
需注意在 iOS 上會將文字對齊頂部，而在 Android 上會置中對齊。若要讓兩平台行為一致，請將 `textAlignVertical` 設為 `top`。
:::

| Type |
| ---- |
| bool |

---

### `numberOfLines`

:::note
iOS 上的 `numberOfLines` 僅在[新架構](/architecture/landing-page)中可用
:::

設定 `TextInput` 的最大行數。需與 `multiline` 設為 `true` 搭配使用以填滿行數。

| Type   |
| ------ |
| number |

---

### `onBlur`

當文字輸入框失去焦點時觸發的回調函式。

> 注意：若嘗試從 `nativeEvent` 存取 `text` 值，請注意結果可能為 `undefined` 而導致非預期錯誤。若要取得 TextInput 的最終值，可使用 [`onEndEditing`](textinput#onendediting) 事件，該事件會在編輯完成時觸發。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [TargetEvent](targetevent)}) => void` |

---

### `onChange`

當文字輸入框內容變更時觸發的回調函式。

| Type                                                  |
| ----------------------------------------------------- |
| (`{nativeEvent: {eventCount, target, text}}`) => void |

---

### `onChangeText`

當文字輸入框內容變更時觸發的回調函式。變更後的文字會以單一字串參數形式傳遞給回調處理函式。

| Type     |
| -------- |
| function |

---

### `onContentSizeChange`

當文字輸入框的內容尺寸變更時觸發的回調函式。

僅適用於多行文字輸入框。

| Type                                                       |
| ---------------------------------------------------------- |
| (`{nativeEvent: {contentSize: {width, height} }}`) => void |

---

### `onEndEditing`

當文字輸入結束時觸發的回調函式。

| Type     |
| -------- |
| function |

---

### `onPressIn`

當觸碰開始時觸發的回調函式。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressOut`

當觸碰結束時觸發的回調函式。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onFocus`

當文字輸入框獲得焦點時觸發的回調函式。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [TargetEvent](targetevent)}) => void` |

---

### `onKeyPress`

當按鍵被按下時觸發的回調函數。此函數會接收一個物件，其中 `keyValue` 會是 `'Enter'` 或 `'Backspace'`（對應按鍵）或其他輸入字符（包括空格 `' '`）。此回調會在 `onChange` 之前觸發。注意：在 Android 上僅處理軟鍵盤輸入，不處理硬體鍵盤輸入。

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

在內容滾動時觸發。可能包含來自 `ScrollEvent` 的其他屬性，但在 Android 上出於性能考量不會提供 `contentSize`。

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

注意：在 iOS 上使用 `keyboardType="phone-pad"` 時不會觸發此方法。

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

若為 `true`，文字將不可編輯。預設值為 `false`。

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

若為 `true`，允許 TextInput 將觸控事件傳遞給父元件。這使得如 SwipeableListView 等元件能從 TextInput 滑動（Android 預設即支援此行為）。若為 `false`，TextInput 會始終要求處理輸入（除非被禁用）。預設值為 `true`。

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

若設為 `false`，將禁用文字視窗的滾動功能。預設值為 `true`。僅在 `multiline={true}` 時有效。

| Type |
| ---- |
| bool |

---

### `secureTextEntry`

若設為 `true`，文字輸入會隱藏內容以保護敏感資訊如密碼。預設值為 `false`。不支援 `multiline={true}`。

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

文字輸入的選取高亮、選取把手與游標顏色。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `selectionHandleColor` <div class="label android">Android</div>

設定選取把手的顏色。與 `selectionColor` 不同，此屬性可獨立自訂選取把手顏色，不影響選取範圍顏色。

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

設為 `false` 時可防止聚焦時彈出虛擬鍵盤。預設值為 `true`。

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

單行輸入情況下：

- `'newline'` 預設為 `'blurAndSubmit'`
- `undefined` 預設為 `'blurAndSubmit'`

多行輸入情況下：

- `'newline'` 會新增換行
- `undefined` 預設為 `'newline'`

單行與多行輸入共通行為：

- `'submit'` 僅觸發提交事件，不失去輸入焦點
- `'blurAndSubmit'` 會同時失去焦點並觸發提交事件

| Type                                       |
| ------------------------------------------ |
| enum('submit', 'blurAndSubmit', 'newline') |

---

### `textAlign`

將輸入文字對齊輸入框的左側、中央或右側。

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

您可以將 `textContentType` 設為 `username` 或 `password` 來啟用裝置鑰匙圈的自動填寫登入資訊功能。

`newPassword` 可用於表示使用者可能想儲存至鑰匙圈的新密碼輸入欄位，而 `oneTimeCode` 則表示該欄位可透過簡訊收到的驗證碼自動填寫。

若要停用自動填寫，請將 `textContentType` 設為 `none`。

`textContentType` 的可能值包括：

- `none`（無）
- `addressCity`（城市）
- `addressCityAndState`（城市與州）
- `addressState`（州）
- `birthdate`（出生日期，iOS 17+）
- `birthdateDay`（出生日，iOS 17+）
- `birthdateMonth`（出生月，iOS 17+）
- `birthdateYear`（出生年，iOS 17+）
- `countryName`（國家名稱）
- `creditCardExpiration`（信用卡到期日，iOS 17+）
- `creditCardExpirationMonth`（信用卡到期月，iOS 17+）
- `creditCardExpirationYear`（信用卡到期年，iOS 17+）
- `creditCardFamilyName`（信用卡姓氏，iOS 17+）
- `creditCardGivenName`（信用卡名字，iOS 17+）
- `creditCardMiddleName`（信用卡中間名，iOS 17+）
- `creditCardName`（信用卡姓名，iOS 17+）
- `creditCardNumber`（信用卡號碼）
- `creditCardSecurityCode`（信用卡安全碼，iOS 17+）
- `creditCardType`（信用卡類型，iOS 17+）
- `emailAddress`（電子郵件地址）
- `familyName`（姓氏）
- `fullStreetAddress`（完整街道地址）
- `givenName`（名字）
- `jobTitle`（職稱）
- `location`（位置）
- `middleName`（中間名）
- `name`（姓名）
- `namePrefix`（姓名前綴）
- `nameSuffix`（姓名後綴）
- `newPassword`（新密碼）
- `nickname`（暱稱）
- `oneTimeCode`（一次性驗證碼）
- `organizationName`（組織名稱）
- `password`（密碼）
- `postalCode`（郵遞區號）
- `streetAddressLine1`（街道地址行1）
- `streetAddressLine2`（街道地址行2）
- `sublocality`（次行政區）
- `telephoneNumber`（電話號碼）
- `URL`（網址）
- `username`（使用者名稱）

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| enum('none', 'addressCity', 'addressCityAndState', 'addressState', 'birthdate', 'birthdateDay', 'birthdateMonth', 'birthdateYear', 'countryName', 'creditCardExpiration', 'creditCardExpirationMonth', 'creditCardExpirationYear', 'creditCardFamilyName', 'creditCardGivenName', 'creditCardMiddleName', 'creditCardName', 'creditCardNumber', 'creditCardSecurityCode', 'creditCardType', 'emailAddress', 'familyName', 'fullStreetAddress', 'givenName', 'jobTitle', 'location', 'middleName', 'name', 'namePrefix', 'nameSuffix', 'newPassword', 'nickname', 'oneTimeCode', 'organizationName', 'password', 'postalCode', 'streetAddressLine1', 'streetAddressLine2', 'sublocality', 'telephoneNumber', 'URL', 'username') |

---

### `passwordRules` <div class="label ios">iOS</div>

在 iOS 上使用 `textContentType` 設為 `newPassword` 時，我們可以讓作業系統了解密碼的最低要求，以便生成符合條件的密碼。要建立有效的 `PasswordRules` 字串，請參考 [Apple 官方文件](https://developer.apple.com/password-rules/)。

> 如果未出現密碼生成對話框，請確認：
>
> - 已啟用自動填寫功能：**設定** → **密碼與帳號** → 開啟 **自動填寫密碼**
> - 已使用 iCloud 鑰匙圈：**設定** → **Apple ID** → **iCloud** → **鑰匙圈** → 開啟 **iCloud 鑰匙圈**

| Type   |
| ------ |
| string |

---

### `style`

請注意並非所有文字樣式都受支援，以下是不完整的不支援樣式清單：

- `borderLeftWidth`（左邊框寬度）
- `borderTopWidth`（上邊框寬度）
- `borderRightWidth`（右邊框寬度）
- `borderBottomWidth`（下邊框寬度）
- `borderTopLeftRadius`（左上邊框圓角）
- `borderTopRightRadius`（右上邊框圓角）
- `borderBottomRightRadius`（右下邊框圓角）
- `borderBottomLeftRadius`（左下邊框圓角）

[樣式](style.md)

| Type                  |
| --------------------- |
| [Text](text.md#style) |

---

### `textBreakStrategy` <div class="label android">Android</div>

在 Android API Level 23+ 上設定文字斷行策略，可能值為 `simple`（簡單）、`highQuality`（高品質）、`balanced`（平衡），預設值為 `highQuality`。

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

文字輸入框顯示的值。`TextInput` 是一個受控元件，這意味著如果提供了這個屬性值，原生的值將會被強制與之匹配。在大多數情況下這運作良好，但在某些情況下可能會導致閃爍——常見原因之一是通過保持相同的值來防止編輯。除了設置相同的值外，可以設置 `editable={false}`，或者設置/更新 `maxLength` 來防止不需要的編輯而不會造成閃爍。

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
- [react-native#19366](https://github.com/facebook/react-native/issues/19366): 在通過返回按鈕關閉 Android 鍵盤後呼叫 .focus() 不會再次彈出鍵盤。
- [react-native#26799](https://github.com/facebook/react-native/issues/26799): 當 `keyboardType="email-address"` 或 `keyboardType="phone-pad"` 時不支援 Android 的 `secureTextEntry`。