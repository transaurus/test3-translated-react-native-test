---
id: textinput
title: TextInput
---

一個基礎組件，用於通過鍵盤向應用程序輸入文本。屬性提供了多種功能的可配置性，例如自動更正、自動大寫、佔位文本以及不同的鍵盤類型，例如數字鍵盤。

最基本的用例是放置一個 `TextInput` 並訂閱 `onChangeText` 事件來讀取用戶輸入。還有其他事件，例如 `onSubmitEditing` 和 `onFocus` 也可以訂閱。一個最小示例：

```SnackPlayer name=TextInput
import React from "react";
import { SafeAreaView, StyleSheet, TextInput } from "react-native";

const UselessTextInput = () => {
  const [text, onChangeText] = React.useState("Useless Text");
  const [number, onChangeNumber] = React.useState(null);

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

export default UselessTextInput;
```

通過原生元素暴露的兩個方法是 .focus() 和 .blur()，可以以編程方式聚焦或模糊 TextInput。

請注意，某些屬性僅在 `multiline={true/false}` 時可用。此外，如果 `multiline=true`，則僅應用於元素一側的邊框樣式（例如 `borderBottomColor`、`borderLeftWidth` 等）將不會生效。要達到相同的效果，您可以將 `TextInput` 包裹在一個 `View` 中：

```SnackPlayer name=TextInput
import React from 'react';
import { View, TextInput } from 'react-native';

const UselessTextInput = (props) => {
  return (
    <TextInput
      {...props} // Inherit any props passed to it; e.g., multiline, numberOfLines below
      editable
      maxLength={40}
    />
  );
}

const UselessTextInputMultiline = () => {
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
      <UselessTextInput
        multiline
        numberOfLines={4}
        onChangeText={text => onChangeText(text)}
        value={value}
        style={{padding: 10}}
      />
    </View>
  );
}

export default UselessTextInputMultiline;
```

`TextInput` 默認在其視圖底部有一個邊框。此邊框的填充由系統提供的背景圖像設置，無法更改。避免此問題的解決方案是不要明確設置高度，這樣系統會負責在正確的位置顯示邊框，或者通過將 `underlineColorAndroid` 設置為透明來不顯示邊框。

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
- `sentences`: 每個句子的首字母（默認）。
- `none`: 不自動大寫任何內容。

| Type                                             |
| ------------------------------------------------ |
| enum('none', 'sentences', 'words', 'characters') |

---

### `autoComplete` <div class="label android">Android</div>

為系統指定自動完成提示，以便提供自動填充。在 Android 上，系統將始終嘗試通過使用啟發式方法來識別內容類型來提供自動填充。要禁用自動完成，請將 `autoComplete` 設置為 `off`。

`autoComplete` 的可能值為：

- `birthdate-day` (出生日期-日)
- `birthdate-full` (出生日期-完整)
- `birthdate-month` (出生日期-月)
- `birthdate-year` (出生日期-年)
- `cc-csc` (信用卡安全碼)
- `cc-exp` (信用卡到期日)
- `cc-exp-day` (信用卡到期日-日)
- `cc-exp-month` (信用卡到期日-月)
- `cc-exp-year` (信用卡到期日-年)
- `cc-number` (信用卡號碼)
- `email` (電子郵件)
- `gender` (性別)
- `name` (姓名)
- `name-family` (姓氏)
- `name-given` (名字)
- `name-middle` (中間名)
- `name-middle-initial` (中間名首字母)
- `name-prefix` (姓名前綴)
- `name-suffix` (姓名後綴)
- `password` (密碼)
- `password-new` (新密碼)
- `postal-address` (郵寄地址)
- `postal-address-country` (郵寄地址-國家)
- `postal-address-extended` (郵寄地址-擴展)
- `postal-address-extended-postal-code` (郵寄地址-擴展郵遞區號)
- `postal-address-locality` (郵寄地址-地區)
- `postal-address-region` (郵寄地址-行政區)
- `postal-code` (郵遞區號)
- `street-address` (街道地址)
- `sms-otp` (簡訊驗證碼)
- `tel` (電話號碼)
- `tel-country-code` (電話國碼)
- `tel-national` (國內電話號碼)
- `tel-device` (裝置電話號碼)
- `username` (使用者名稱)
- `username-new` (新使用者名稱)
- `off` (關閉)

| Type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('birthdate-day', 'birthdate-full', 'birthdate-month', 'birthdate-year', 'cc-csc', 'cc-exp', 'cc-exp-day', 'cc-exp-month', 'cc-exp-year', 'cc-number', 'email', 'gender', 'name', 'name-family', 'name-given', 'name-middle', 'name-middle-initial', 'name-prefix', 'name-suffix', 'password', 'password-new', 'postal-address', 'postal-address-country', 'postal-address-extended', 'postal-address-extended-postal-code', 'postal-address-locality', 'postal-address-region', 'postal-code', 'street-address', 'sms-otp', 'tel', 'tel-country-code', 'tel-national', 'tel-device', 'username', 'username-new', 'off') |

---

### `autoCorrect` (自動校正)

若設為 `false`，則停用自動校正功能。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `autoFocus` (自動聚焦)

若設為 `true`，則在 `componentDidMount` 或 `useEffect` 時自動聚焦輸入框。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `blurOnSubmit` (提交時失焦)

若設為 `true`，文字欄位會在提交時失去焦點。單行欄位的預設值為 true，多行欄位則為 false。請注意，對於多行欄位，將 `blurOnSubmit` 設為 `true` 表示按下返回鍵會使欄位失焦並觸發 `onSubmitEditing` 事件，而非在欄位中插入換行符號。

| Type |
| ---- |
| bool |

---

### `caretHidden` (隱藏游標)

若設為 `true`，則隱藏輸入游標。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `clearButtonMode` (清除按鈕模式) <div class="label ios">iOS</div>

決定清除按鈕何時出現在文字輸入框的右側。此屬性僅支援單行 TextInput 元件。預設值為 `never`。

| Type                                                       |
| ---------------------------------------------------------- |
| enum('never', 'while-editing', 'unless-editing', 'always') |

---

### `clearTextOnFocus` (聚焦時清除文字) <div class="label ios">iOS</div>

若設為 `true`，則在開始編輯時自動清除文字欄位內容。

| Type |
| ---- |
| bool |

---

### `contextMenuHidden` (隱藏上下文選單)

若設為 `true`，則隱藏上下文選單。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `dataDetectorTypes` (資料偵測類型) <div class="label ios">iOS</div>

決定文字輸入框中哪些類型的資料會被轉換為可點擊的連結。僅在 `multiline={true}` 且 `editable={false}` 時有效。預設不偵測任何資料類型。

可指定單一類型或多種類型組成的陣列。

`dataDetectorTypes` 的可能值為：

- `'phoneNumber'` (電話號碼)
- `'link'` (連結)
- `'address'` (地址)
- `'calendarEvent'` (日曆事件)
- `'none'` (無)
- `'all'` (全部)

| Type                                                                                                                                                     |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('phoneNumber', 'link', 'address', 'calendarEvent', 'none', 'all'), ,array of enum('phoneNumber', 'link', 'address', 'calendarEvent', 'none', 'all') |

---

### `defaultValue` (預設值)

提供初始值，該值會在使用者開始輸入時改變。適用於不想透過監聽事件和更新 value 屬性來同步控制狀態的使用情境。

| Type   |
| ------ |
| string |

---

### `cursorColor` <div class="label android">Android</div>

當提供此屬性時，會設定元件中游標（或稱「插入符」）的顏色。與 `selectionColor` 的行為不同，游標顏色會獨立於文字選取框的顏色設定。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `disableFullscreenUI` <div class="label android">Android</div>

當設為 `false` 時，如果文字輸入框周圍可用空間較少（例如手機橫向模式），作業系統可能會選擇讓使用者在全螢幕文字輸入模式中編輯文字。設為 `true` 時，此功能會被禁用，使用者將始終直接在文字輸入框中編輯文字。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `editable`

若設為 `false`，文字將無法編輯。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `enablesReturnKeyAutomatically` <div class="label ios">iOS</div>

若設為 `true`，鍵盤會在沒有文字時禁用返回鍵，並在有文字時自動啟用。預設值為 `false`。

| Type |
| ---- |
| bool |

---

### `importantForAutofill` <div class="label android">Android</div>

告知作業系統應用程式中的個別欄位是否應包含在 Android API 26+ 的自動填充視圖結構中。可能的值為 `auto`、`no`、`noExcludeDescendants`、`yes` 和 `yesExcludeDescendants`。預設值為 `auto`。

- `auto`：讓 Android 系統使用其啟發式方法判斷視圖對自動填充是否重要。
- `no`：此視圖對自動填充不重要。
- `noExcludeDescendants`：此視圖及其子視圖對自動填充不重要。
- `yes`：此視圖對自動填充重要。
- `yesExcludeDescendants`：此視圖對自動填充重要，但其子視圖不重要。

| Type                                                                       |
| -------------------------------------------------------------------------- |
| enum('auto', 'no', 'noExcludeDescendants', 'yes', 'yesExcludeDescendants') |

---

### `inlineImageLeft` <div class="label android">Android</div>

若定義此屬性，提供的圖片資源將顯示在左側。圖片資源必須位於 `/android/app/src/main/res/drawable` 目錄下，並以如下方式引用：

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

一個可選的標識符，用於將自訂的 [InputAccessoryView](inputaccessoryview.md) 連結到此文字輸入框。當此文字輸入框獲得焦點時，InputAccessoryView 會顯示在鍵盤上方。

| Type   |
| ------ |
| string |

---

### `keyboardAppearance` <div class="label ios">iOS</div>

決定鍵盤的顏色。

| Type                             |
| -------------------------------- |
| enum('default', 'light', 'dark') |

---

### `keyboardType`

決定要開啟哪種鍵盤，例如 `numeric`。

所有類型的截圖可參見[此處](http://lefkowitz.me/2018/04/30/visual-guide-to-react-native-textinput-keyboardtype-options/)。

以下數值適用於跨平台：

- `default` (預設)
- `number-pad` (數字鍵盤)
- `decimal-pad` (小數鍵盤)
- `numeric` (數值)
- `email-address` (電子郵件地址)
- `phone-pad` (電話鍵盤)
- `url` (網址)

_iOS 專用_

以下數值僅適用於 iOS：

- `ascii-capable` (ASCII 輸入)
- `numbers-and-punctuation` (數字與標點)
- `name-phone-pad` (姓名電話鍵盤)
- `twitter` (推特)
- `web-search` (網路搜尋)

_Android 專用_

以下數值僅適用於 Android：

- `visible-password` (可見密碼)

| Type                                                                                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| enum('default', 'email-address', 'numeric', 'phone-pad', 'ascii-capable', 'numbers-and-punctuation', 'url', 'number-pad', 'name-phone-pad', 'decimal-pad', 'twitter', 'web-search', 'visible-password') |

---

### `maxFontSizeMultiplier`

當啟用 `allowFontScaling` 時，指定字體可達到的最大縮放比例。可能的值：

- `null/undefined` (預設)：繼承父節點或全域預設值 (0)
- `0`：無上限，忽略父節點/全域預設值
- `>= 1`：將此節點的 `maxFontSizeMultiplier` 設為該值

| Type   |
| ------ |
| number |

---

### `maxLength`

限制可輸入的最大字元數。使用此屬性而非在 JS 中實作邏輯以避免閃爍。

| Type   |
| ------ |
| number |

---

### `multiline`

若為 `true`，文字輸入可為多行。預設值為 `false`。

:::note
需注意在 iOS 上會將文字對齊頂部，而在 Android 上會置中。若要兩平台行為一致，請搭配將 `textAlignVertical` 設為 `top` 使用。
:::

| Type |
| ---- |
| bool |

---

### `numberOfLines` <div class="label android">Android</div>

設定 `TextInput` 的行數。需與 `multiline` 設為 `true` 搭配使用以填滿行數。

| Type   |
| ------ |
| number |

---

### `onBlur`

當文字輸入失去焦點時觸發的回調函式。

> 注意：若您嘗試從 `nativeEvent` 存取 `text` 值，請注意結果可能為 `undefined` 而導致非預期錯誤。若要取得 TextInput 的最終值，可使用 [`onEndEditing`](textinput#onendediting) 事件，該事件會在編輯完成時觸發。

| Type     |
| -------- |
| function |

---

### `onChange`

當文字輸入內容變更時觸發的回調函式。

| Type                                                     |
| -------------------------------------------------------- |
| (`{ nativeEvent: { eventCount, target, text} }`) => void |

---

### `onChangeText`

當文字輸入內容變更時觸發的回調函式。變更後的文字會以單一字串參數傳遞給回調處理函式。

| Type     |
| -------- |
| function |

---

### `onContentSizeChange`

當文字輸入的內容尺寸變更時觸發的回調函式。

僅適用於多行文字輸入。

| Type                                                            |
| --------------------------------------------------------------- |
| (`{ nativeEvent: { contentSize: { width, height } } }`) => void |

---

### `onEndEditing`

當文字輸入結束時觸發的回調函式。

| Type     |
| -------- |
| function |

---

### `onPressIn`

當觸碰開始時觸發的回調函式。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => void` |

---

### `onPressOut`

當觸控釋放時觸發的回調函數。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => void` |

---

### `onFocus`

當文字輸入框獲得焦點時觸發的回調函數。

| Type                                                       |
| ---------------------------------------------------------- |
| `md ({ nativeEvent: [LayoutEvent](layoutevent) }) => void` |

---

### `onKeyPress`

當按鍵被按下時觸發的回調函數。會傳遞一個物件，其中 `keyValue` 為 `'Enter'` 或 `'Backspace'` 對應按鍵，其他情況則為輸入的字符（包含空格 `' '`）。此回調會在 `onChange` 之前觸發。注意：在 Android 上僅處理軟鍵盤輸入，不處理硬體鍵盤輸入。

| Type                                           |
| ---------------------------------------------- |
| (`{ nativeEvent: { key: keyValue } }`) => void |

---

### `onLayout`

在元件掛載或佈局變化時觸發。

| Type                                                       |
| ---------------------------------------------------------- |
| `md ({ nativeEvent: [LayoutEvent](layoutevent) }) => void` |

---

### `onScroll`

在內容滾動時觸發。可能包含 `ScrollEvent` 的其他屬性，但在 Android 上為效能考量不提供 `contentSize`。

| Type                                                     |
| -------------------------------------------------------- |
| (`{ nativeEvent: { contentOffset: { x, y } } }`) => void |

---

### `onSelectionChange`

當文字輸入框的選取範圍變化時觸發的回調函數。

| Type                                                       |
| ---------------------------------------------------------- |
| (`{ nativeEvent: { selection: { start, end } } }`) => void |

---

### `onSubmitEditing`

當文字輸入框的提交按鈕被按下時觸發的回調函數。

| Type                                                     |
| -------------------------------------------------------- |
| (`{ nativeEvent: { text, eventCount, target }}`) => void |

注意：在 iOS 上使用 `keyboardType="phone-pad"` 時此方法不會被呼叫。

---

### `placeholder`

在文字輸入前顯示的預留字串。

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

### `returnKeyLabel` <div class="label android">Android</div>

設定返回鍵的標籤文字。可替代 `returnKeyType` 使用。

| Type   |
| ------ |
| string |

---

### `returnKeyType`

決定返回鍵的外觀樣式。在 Android 上也可使用 `returnKeyLabel`。

_跨平台支援_

以下數值適用於所有平台：

- `done`
- `go`
- `next`
- `search`
- `send`

_僅限 Android_

以下數值僅在 Android 上有效：

- `none`
- `previous`

_僅限 iOS_

以下數值僅在 iOS 上有效：

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

若為 `true`，允許 TextInput 將觸控事件傳遞給父元件。這使得如 SwipeableListView 等元件能從 TextInput 處滑動（Android 預設即支援此行為）。若為 `false`，TextInput 會始終要求處理輸入（除非被禁用）。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `scrollEnabled` <div class="label ios">iOS</div>

若設為 `false`，將停用文字視窗的滾動功能。預設值為 `true`。僅在 `multiline={true}` 時有效。

| Type |
| ---- |
| bool |

---

### `secureTextEntry`

若設為 `true`，文字輸入會遮蔽輸入內容以保護敏感資訊如密碼。預設值為 `false`。不支援 `multiline={true}`。

| Type |
| ---- |
| bool |

---

### `selection`

設定文字輸入選取範圍的起始與結束位置。將起始與結束設為相同值可定位游標。

| Type                                  |
| ------------------------------------- |
| object: `{start: number,end: number}` |

---

### `selectionColor`

文字輸入的反白標示與游標顏色。

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

設為 `false` 可防止聚焦時觸發軟鍵盤彈出。預設值為 `true`。

| Type |
| ---- |
| bool |

---

### `spellCheck` <div class="label ios">iOS</div>

若設為 `false`，停用拼字檢查樣式（如紅色底線）。預設值繼承自 `autoCorrect`。

| Type |
| ---- |
| bool |

---

### `textAlign`

將輸入文字對齊輸入欄位的左側、居中或右側。

`textAlign` 可用值為：

- `left`（左對齊）
- `center`（居中）
- `right`（右對齊）

| Type                            |
| ------------------------------- |
| enum('left', 'center', 'right') |

---

### `textContentType` <div class="label ios">iOS</div>

向鍵盤與系統提供使用者輸入內容的預期語意資訊。

在 iOS 11+ 中，可設定 `textContentType` 為 `username` 或 `password` 以啟用裝置鑰匙圈的登入資訊自動填入。

iOS 12+ 支援使用 `newPassword` 標示使用者可能想儲存至鑰匙圈的新密碼輸入欄位，`oneTimeCode` 則可標示能透過簡訊驗證碼自動填入的欄位。

設為 `none` 可停用自動填入功能。

`textContentType` 可用值包含：

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

在 iOS 上使用 `textContentType` 設為 `newPassword` 時，我們可以讓作業系統知道密碼的最低要求，以便生成符合這些要求的密碼。要建立有效的 `PasswordRules` 字串，請參考 [Apple 官方文件](https://developer.apple.com/password-rules/)。

> 如果未出現密碼生成對話框，請確認以下設定：
>
> - 已啟用自動填充功能：**設定** → **密碼與帳號** → 切換開啟 **自動填充密碼**，
> - 使用 iCloud 鑰匙圈：**設定** → **Apple ID** → **iCloud** → **鑰匙圈** → 切換開啟 **iCloud 鑰匙圈**。

| Type   |
| ------ |
| string |

---

### `style`

請注意，並非所有文字樣式都受到支援，以下是不支援的樣式部分清單：

- `borderLeftWidth`
- `borderTopWidth`
- `borderRightWidth`
- `borderBottomWidth`
- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomRightRadius`
- `borderBottomLeftRadius`

詳見 [Issue#7070](https://github.com/facebook/react-native/issues/7070) 以獲取更多細節。

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

文字輸入框顯示的值。`TextInput` 是一個受控元件，這意味著如果提供了此屬性值，原生值將被迫與之匹配。在大多數情況下，這運作良好，但在某些情況下可能會導致閃爍——常見原因之一是通過保持值不變來防止編輯。除了設定相同的值外，可以設定 `editable={false}`，或設定/更新 `maxLength` 以防止不需要的編輯而不會閃爍。

| Type   |
| ------ |
| string |

## 方法

### `.focus()`

```jsx
focus();
```

使原生輸入請求焦點。

### `.blur()`

```jsx
blur();
```

使原生輸入失去焦點。

### `clear()`

```jsx
clear();
```

清除 `TextInput` 中的所有文字。

---

### `isFocused()`

```jsx
isFocused();
```

如果輸入目前處於焦點狀態，則返回 `true`；否則返回 `false`。

# 已知問題

- [react-native#19096](https://github.com/facebook/react-native/issues/19096)：不支援 Android 的 `onKeyPreIme`。
- [react-native#19366](https://github.com/facebook/react-native/issues/19366)：在通過返回按鈕關閉 Android 鍵盤後呼叫 .focus() 不會再次喚起鍵盤。
- [react-native#26799](https://github.com/facebook/react-native/issues/26799)：當 `keyboardType="email-address"` 或 `keyboardType="phone-pad"` 時，不支援 Android 的 `secureTextEntry`。