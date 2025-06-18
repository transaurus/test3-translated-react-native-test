---
id: text
title: Text
---

A React component for displaying text.

`Text` supports nesting, styling, and touch handling.

In the following example, the nested title and body text will inherit the `fontFamily` from `styles.baseText`, but the title provides its own additional styles. The title and body will stack on top of each other on account of the literal newlines:

```SnackPlayer name=Text%20Function%20Component%20Example
import React, {useState} from 'react';
import {Text, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const TextInANest = () => {
  const [titleText, setTitleText] = useState("Bird's Nest");
  const bodyText = 'This is not really a bird nest.';

  const onPressTitle = () => {
    setTitleText("Bird's Nest [pressed]");
  };

  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.container}>
        <Text style={styles.baseText}>
          <Text style={styles.titleText} onPress={onPressTitle}>
            {titleText}
            {'\n'}
            {'\n'}
          </Text>
          <Text numberOfLines={5}>{bodyText}</Text>
        </Text>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  baseText: {
    fontFamily: 'Cochin',
  },
  titleText: {
    fontSize: 20,
    fontWeight: 'bold',
  },
});

export default TextInANest;
```

## Nested text

Both Android and iOS allow you to display formatted text by annotating ranges of a string with specific formatting like bold or colored text (`NSAttributedString` on iOS, `SpannableString` on Android). In practice, this is very tedious. For React Native, we decided to use the web paradigm for this, where you can nest text to achieve the same effect.

```SnackPlayer name=Nested%20Text%20Example
import React from 'react';
import {Text, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const BoldAndBeautiful = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Text style={styles.baseText}>
        I am bold
        <Text style={styles.innerText}> and red</Text>
      </Text>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  baseText: {
    fontWeight: 'bold',
  },
  innerText: {
    color: 'red',
  },
});

export default BoldAndBeautiful;
```

Behind the scenes, React Native converts this to a flat `NSAttributedString` or `SpannableString` that contains the following information:

```
"I am bold and red"
0-9: bold
9-17: bold, red
```

## Containers

The `<Text>` element is unique relative to layout: everything inside is no longer using the Flexbox layout but using text layout. This means that elements inside of a `<Text>` are no longer rectangles, but wrap when they see the end of the line.

```tsx
<Text>
  <Text>First part and </Text>
  <Text>second part</Text>
</Text>
// Text container: the text will be inline, if the space allows it
// |First part and second part|

// otherwise, the text will flow as if it was one
// |First part |
// |and second |
// |part       |

<View>
  <Text>First part and </Text>
  <Text>second part</Text>
</View>
// View container: each text is its own block
// |First part and|
// |second part   |

// otherwise, the text will flow in its own block
// |First part |
// |and        |
// |second part|
```

## Limited Style Inheritance

On the web, the usual way to set a font family and size for the entire document is to take advantage of inherited CSS properties like so:

```css
html {
  font-family:
    'lucida grande', tahoma, verdana, arial, sans-serif;
  font-size: 11px;
  color: #141823;
}
```

All elements in the document will inherit this font unless they or one of their parents specifies a new rule.

In React Native, we are more strict about it: **you must wrap all the text nodes inside of a `<Text>` component**. You cannot have a text node directly under a `<View>`.

```tsx
// BAD: will raise exception, can't have a text node as child of a <View>
<View>
  Some text
</View>

// GOOD
<View>
  <Text>
    Some text
  </Text>
</View>
```

You also lose the ability to set up a default font for an entire subtree. Meanwhile, `fontFamily` only accepts a single font name, which is different from `font-family` in CSS. The recommended way to use consistent fonts and sizes across your application is to create a component `MyAppText` that includes them and use this component across your app. You can also use this component to make more specific components like `MyAppHeaderText` for other kinds of text.

```tsx
<View>
  <MyAppText>
    Text styled with the default font for the entire application
  </MyAppText>
  <MyAppHeaderText>Text styled as a header</MyAppHeaderText>
</View>
```

Assuming that `MyAppText` is a component that only renders out its children into a `Text` component with styling, then `MyAppHeaderText` can be defined as follows:

```tsx
const MyAppHeaderText = ({children}) => {
  return (
    <MyAppText>
      <Text style={{fontSize: 20}}>{children}</Text>
    </MyAppText>
  );
};
```

Composing `MyAppText` in this way ensures that we get the styles from a top-level component, but leaves us the ability to add/override them in specific use cases.

React Native still has the concept of style inheritance, but limited to text subtrees. In this case, the second part will be both bold and red.

```tsx
<Text style={{fontWeight: 'bold'}}>
  I am bold
  <Text style={{color: 'red'}}>and red</Text>
</Text>
```

We believe that this more constrained way to style text will yield better apps:

- (Developer) React components are designed with strong isolation in mind: You should be able to drop a component anywhere in your application, trusting that as long as the props are the same, it will look and behave the same way. Text properties that could inherit from outside of the props would break this isolation.

- (Implementor) The implementation of React Native is also simplified. We do not need to have a `fontFamily` field on every single element, and we do not need to potentially traverse the tree up to the root every time we display a text node. The style inheritance is only encoded inside of the native Text component and doesn't leak to other components or the system itself.

---

# Reference

## Props

### `accessibilityHint`

輔助功能提示（accessibility hint）可幫助使用者理解當他們對輔助功能元素執行操作時會發生什麼結果，尤其是當該結果無法從輔助功能標籤中明確得知時。

| Type   |
| ------ |
| string |

---

### `accessibilityLanguage` <div class="label ios">iOS</div>

此值指示螢幕閱讀器在使用者與元素互動時應使用的語言。其格式應遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

詳見 [iOS `accessibilityLanguage` 文件](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage)。

| Type   |
| ------ |
| string |

---

### `accessibilityLabel`

覆寫螢幕閱讀器在使用者與元素互動時朗讀的文字。預設情況下，標籤會透過遍歷所有子元素並以空格分隔累積所有 `Text` 節點來建構。

| Type   |
| ------ |
| string |

---

### `accessibilityRole`

告知螢幕閱讀器將當前聚焦的元素視為具有特定角色。

在 iOS 上，這些角色會映射到對應的輔助功能特徵（Accessibility Traits）。圖片按鈕的功能與同時設定「image」和「button」特徵相同。詳見 [輔助功能指南](accessibility.md#accessibilitytraits-ios)。

在 Android 上，這些角色在 TalkBack 中的功能類似於 iOS VoiceOver 中添加輔助功能特徵的效果。

| Type                                                 |
| ---------------------------------------------------- |
| [AccessibilityRole](accessibility#accessibilityrole) |

---

### `accessibilityState`

告知螢幕閱讀器將當前聚焦的元素視為處於特定狀態。

您可以提供一個狀態、無狀態或多個狀態。狀態必須透過物件傳遞，例如 `{selected: true, disabled: true}`。

| Type                                                   |
| ------------------------------------------------------ |
| [AccessibilityState](accessibility#accessibilitystate) |

---

### `accessibilityActions`

輔助功能操作允許輔助技術以程式化方式呼叫元件的操作。`accessibilityActions` 屬性應包含一個操作物件列表，每個操作物件應包含名稱（name）和標籤（label）欄位。

詳見 [輔助功能指南](accessibility.md#accessibility-actions)。

| Type  | Required |
| ----- | -------- |
| array | No       |

---

### `onAccessibilityAction`

當使用者執行輔助功能操作時觸發。此函式的唯一參數是一個包含要執行操作名稱的事件物件。

詳見 [輔助功能指南](accessibility.md#accessibility-actions)。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `accessible`

設為 `true` 時，表示該視圖是一個輔助功能元素。

詳見 [輔助功能指南](accessibility#accessible-ios-android)。

| Type    | Default |
| ------- | ------- |
| boolean | `true`  |

---

### `adjustsFontSizeToFit`

指定字型是否應自動縮小以符合給定的樣式約束。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `allowFontScaling`

指定字型是否應根據「文字大小」輔助功能設定進行縮放。

| Type    | Default |
| ------- | ------- |
| boolean | `true`  |

---

### `android_hyphenationFrequency` <div class="label android">Android</div>

在 Android API 等級 23+ 上設定自動斷字頻率，用於決定單詞斷開方式。

| Type                                | Default  |
| ----------------------------------- | -------- |
| enum(`'none'`, `'normal'`,`'full'`) | `'none'` |

---

### `aria-busy`

Indicates an element is being modified and that assistive technologies may want to wait until the changes are complete before informing the user about the update.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-checked`

Indicates the state of a checkable element. This field can either take a boolean or the "mixed" string to represent mixed checkboxes.

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

---

### `aria-disabled`

Indicates that the element is perceivable but disabled, so it is not editable or otherwise operable.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-expanded`

Indicates whether an expandable element is currently expanded or collapsed.

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-label`

Defines a string value that labels an interactive element.

| Type   |
| ------ |
| string |

---

### `aria-selected`

Indicates whether a selectable element is currently selected or not.

| Type    |
| ------- |
| boolean |

### `dataDetectorType` <div class="label android">Android</div>

Determines the types of data converted to clickable URLs in the text element. By default, no data types are detected.

You can provide only one type.

| Type                                                          | Default  |
| ------------------------------------------------------------- | -------- |
| enum(`'phoneNumber'`, `'link'`, `'email'`, `'none'`, `'all'`) | `'none'` |

---

### `disabled` <div class="label android">Android</div>

Specifies the disabled state of the text view for testing purposes.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `dynamicTypeRamp` <div class="label ios">iOS</div>

The [Dynamic Type](https://developer.apple.com/documentation/uikit/uifont/scaling_fonts_automatically) ramp to apply to this element on iOS.

| Type                                                                                                                                                     | Default  |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| enum(`'caption2'`, `'caption1'`, `'footnote'`, `'subheadline'`, `'callout'`, `'body'`, `'headline'`, `'title3'`, `'title2'`, `'title1'`, `'largeTitle'`) | `'body'` |

---

### `ellipsizeMode`

When `numberOfLines` is set, this prop defines how the text will be truncated. `numberOfLines` must be set in conjunction with this prop.

This can be one of the following values:

- `head` - The line is displayed so that the end fits in the container and the missing text at the beginning of the line is indicated by an ellipsis glyph. e.g., "...wxyz"
- `middle` - The line is displayed so that the beginning and end fit in the container and the missing text in the middle is indicated by an ellipsis glyph. "ab...yz"
- `tail` - The line is displayed so that the beginning fits in the container and the missing text at the end of the line is indicated by an ellipsis glyph. e.g., "abcd..."
- `clip` - Lines are not drawn past the edge of the text container.

> On Android, when `numberOfLines` is set to a value higher than `1`, only `tail` value will work correctly.

| Type                                           | Default |
| ---------------------------------------------- | ------- |
| enum(`'head'`, `'middle'`, `'tail'`, `'clip'`) | `tail`  |

---

### `id`

Used to locate this view from native code. Has precedence over `nativeID` prop.

| Type   |
| ------ |
| string |

---

### `maxFontSizeMultiplier`

指定當 `allowFontScaling` 啟用時，字體可達到的最大縮放比例。可能的值：

- `null/undefined`：繼承父節點或全域預設值 (0)
- `0`：無最大值，忽略父節點/全域預設值
- `>= 1`：將此節點的 `maxFontSizeMultiplier` 設為此值

| Type   | Default     |
| ------ | ----------- |
| number | `undefined` |

---

### `minimumFontScale` <div class="label ios">iOS</div>

指定當 `adjustsFontSizeToFit` 啟用時，字體可縮小的最小比例 (值為 0.01-1.0)。

| Type   |
| ------ |
| number |

---

### `nativeID`

用於從原生程式碼定位此視圖。

| Type   |
| ------ |
| string |

---

### `numberOfLines`

用於在計算文字佈局（包括自動換行）後，以省略號截斷文字，使總行數不超過此數值。將此屬性設為 `0` 會取消此限制，表示不應用任何行數限制。

此屬性通常與 `ellipsizeMode` 一起使用。

| Type   | Default |
| ------ | ------- |
| number | `0`     |

---

### `onLayout`

在掛載時或佈局變更時觸發。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onLongPress`

此函式在長按時呼叫。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onMoveShouldSetResponder`

此視圖是否要「聲明」觸控回應？當此 `View` 不是回應者時，每次觸控移動都會呼叫此函式。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onPress`

使用者按壓時呼叫的函式，在 `onPressOut` 之後觸發。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressIn`

觸控開始時立即呼叫，在 `onPressOut` 和 `onPress` 之前。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onPressOut`

觸控結束時呼叫。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderGrant`

此視圖現在回應觸控事件。此時應高亮顯示並向使用者展示正在發生的操作。

在 Android 上，從此回調返回 true 可阻止其他原生元件在此回應者終止前成為回應者。

| Type                                                              |
| ----------------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void ｜ boolean` |

---

### `onResponderMove`

使用者正在移動手指。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderRelease`

觸控結束時觸發。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminate`

回應者權限已從此 `View` 移除。可能在呼叫 `onResponderTerminationRequest` 後被其他視圖取得，也可能被作業系統未經詢問直接取得（例如 iOS 的控制中心/通知中心）

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminationRequest`

當其他 `View` 想要成為觸控回應者時，會要求此 `View` 釋放其回應者權限。返回 `true` 表示允許釋放。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onStartShouldSetResponderCapture`

若父層 `View` 欲阻止子層 `View` 在觸控開始時成為回應者，應設置此處理函數並返回 `true`。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onTextLayout`

在文字佈局變更時觸發。

| Type                                                 |
| ---------------------------------------------------- |
| ([`TextLayoutEvent`](text#textlayoutevent)) => mixed |

---

### `pressRetentionOffset`

當滾動視圖被禁用時，此屬性定義觸控點可偏離按鈕多遠才會停用按鈕。一旦停用，若將觸控點移回範圍內，按鈕會重新啟用。在滾動視圖禁用狀態下反覆測試此行為時，請傳入常數值以減少記憶體分配。

| Type                 |
| -------------------- |
| [Rect](rect), number |

---

### `role`

`role` 向輔助技術使用者傳達元件的用途，其優先級高於 [`accessibilityRole`](text#accessibilityrole) 屬性。

| Type                       |
| -------------------------- |
| [Role](accessibility#role) |

---

### `selectable`

允許使用者選取文字，以使用原生複製貼上功能。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `selectionColor` <div class="label android">Android</div>

文字選取時的高亮顏色。

| Type            |
| --------------- |
| [color](colors) |

---

### `style`

| Type                                                                 |
| -------------------------------------------------------------------- |
| [Text Style](text-style-props), [View Style Props](view-style-props) |

---

### `suppressHighlighting` <div class="label ios">iOS</div>

設為 `true` 時，文字被按下不會有視覺變化。預設情況下，按下時會顯示灰色橢圓形高亮效果。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |

---

### `testID`

用於在端對端測試中定位此視圖。

| Type   |
| ------ |
| string |

---

### `textBreakStrategy` <div class="label android">Android</div>

在 Android API 等級 23+ 設定文字斷行策略，可選值為 `simple`、`highQuality`、`balanced`。

| Type                                            | Default       |
| ----------------------------------------------- | ------------- |
| enum(`'simple'`, `'highQuality'`, `'balanced'`) | `highQuality` |

---

### `lineBreakStrategyIOS` <div class="label ios">iOS</div>

在 iOS 14+ 設定換行策略，可選值為 `none`、`standard`、`hangul-word` 和 `push-out`。

| Type                                                        | Default  |
| ----------------------------------------------------------- | -------- |
| enum(`'none'`, `'standard'`, `'hangul-word'`, `'push-out'`) | `'none'` |

## 類型定義

### TextLayout

`TextLayout` 物件是 [`TextLayoutEvent`](text#textlayoutevent) 回調的一部分，包含 `Text` 行文字的測量數據。

#### 範例

```js
{
    capHeight: 10.496,
    ascender: 14.624,
    descender: 4,
    width: 28.224,
    height: 18.624,
    xHeight: 6.048,
    x: 0,
    y: 0
}
```

#### 屬性

| Name      | Type   | Optional | Description                                                         |
| --------- | ------ | -------- | ------------------------------------------------------------------- |
| ascender  | number | No       | The line ascender height after the text layout changes.             |
| capHeight | number | No       | Height of capital letter above the baseline.                        |
| descender | number | No       | The line descender height after the text layout changes.            |
| height    | number | No       | Height of the line after the text layout changes.                   |
| width     | number | No       | Width of the line after the text layout changes.                    |
| x         | number | No       | Line X coordinate inside the Text component.                        |
| xHeight   | number | No       | Distance between the baseline and median of the line (corpus size). |
| y         | number | No       | Line Y coordinate inside the Text component.                        |

### TextLayoutEvent

`TextLayoutEvent` object is returned in the callback as a result of a component layout change. It contains a key called `lines` with a value which is an array containing [`TextLayout`](text#textlayout) object corresponded to every rendered text line.

#### Example

```js
{
  lines: [
    TextLayout,
    TextLayout,
    // ...
  ];
  target: 1127;
}
```

#### Properties

| Name   | Type                                    | Optional | Description                                           |
| ------ | --------------------------------------- | -------- | ----------------------------------------------------- |
| lines  | array of [TextLayout](text#textlayout)s | No       | Provides the TextLayout data for every rendered line. |
| target | number                                  | No       | The node id of the element.                           |