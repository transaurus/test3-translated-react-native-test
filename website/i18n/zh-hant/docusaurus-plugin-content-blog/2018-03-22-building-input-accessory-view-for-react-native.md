---
title: 'Building <InputAccessoryView> For React Native'
author: Peter Argany
authorTitle: Software Engineer at Facebook
authorURL: 'https://github.com/PeteTheHeat'
authorImageURL: 'https://avatars3.githubusercontent.com/u/6011080?s=400&u=028e28081107d0ab16a5cb22baca43c080f5fa50&v=4'
authorTwitter: peterargany
tags: [engineering]
---

## Motivation

Three years ago, a GitHub issue was opened to support input accessory view from React Native.

<img src="/blog/assets/input-accessory-1.png" />

In the ensuing years, there have been countless '+1s', various workarounds, and zero concrete changes to RN on this issue - until today. Starting with iOS, [we're exposing an API](/docs/inputaccessoryview) for accessing the native input accessory view and we are excited to share how we built it.

## Background

What exactly is an input accessory view? Reading [Apple's developer documentation](https://developer.apple.com/documentation/uikit/uiresponder/1621119-inputaccessoryview?language=objc), we learn that it's a custom view which can be anchored to the top of the system keyboard whenever a receiver becomes the first responder. Anything that inherits from `UIResponder` can redeclare the `.inputAccessoryView` property as read-write, and manage a custom view here. The responder infrastructure mounts the view, and keeps it in sync with the system keyboard. Gestures which dismiss the keyboard, like a drag or tap, are applied to the input accessory view at the framework level. This allows us to build content with interactive keyboard dismissal, an integral feature in top-tier messaging apps like iMessage and WhatsApp.

There are two common use cases for anchoring a view to the top of the keyboard. The first is creating a keyboard toolbar, like the Facebook composer background picker.

<img src="/blog/assets/input-accessory-2.gif" style={{float: 'left', paddingRight: 70, paddingTop: 20}} />

In this scenario, the keyboard is focused on a text input field, and the input accessory view is used to provide additional keyboard functionality. This functionality is contextual to the type of input field. In a mapping application it could be address suggestions, or in a text editor, it could be rich text formatting tools.

<hr style={{clear: 'both', marginBottom: 20}} />

The Objective-C UIResponder who owns the `<InputAccessoryView>` in this scenario should be clear. The `<TextInput>` has become first responder, and under the hood this becomes an instance of `UITextView` or `UITextField`.

The second common scenario is sticky text inputs:

<img src="/blog/assets/input-accessory-3.gif" style={{float: 'left', paddingRight: 70, paddingTop: 20}} />

Here, the text input is actually part of the input accessory view itself. This is commonly used in messaging applications, where a message can be composed while scrolling through a thread of previous messages.

<hr style={{clear: 'both', marginBottom: 20}} />

Who owns the `<InputAccessoryView>` in this example? Can it be the `UITextView` or `UITextField` again? The text input is _inside_ the input accessory view, this sounds like a circular dependency. Solving this issue alone is [another blog post](https://derpturkey.com/uitextfield-docked-like-ios-messenger/) in itself. Spoilers: the owner is a generic `UIView` subclass who we manually tell to [becomeFirstResponder](https://developer.apple.com/documentation/uikit/uiresponder/1621113-becomefirstresponder?language=objc).

## API Design

We now know what an `<InputAccessoryView>` is, and how we want to use it. The next step is designing an API that makes sense for both use cases, and works well with existing React Native components like `<TextInput>`.

For keyboard toolbars, there are a few things we want to consider:

1. We want to be able to hoist any generic React Native view hierarchy into the `<InputAccessoryView>`.
2. We want this generic and detached view hierarchy to accept touches and be able to manipulate application state.
3. We want to link an `<InputAccessoryView>` to a particular `<TextInput>`.
4. We want to be able to share an `<InputAccessoryView>` across multiple text inputs, without duplicating any code.

We can achieve #1 using a concept similar to [React portals](https://reactjs.org/docs/portals.html). In this design, we portal React Native views to a `UIView` hierarchy managed by the responder infrastructure. Since React Native views render as UIViews, this is actually quite straightforward - we can just override:

`- (void)insertReactSubview:(UIView *)subview atIndex:(NSInteger)atIndex`

並將所有子視圖傳輸到一個新的 UIView 層次結構中。對於 #2，我們為 `<InputAccessoryView>` 設置了一個新的 [RCTTouchHandler](https://github.com/facebook/react-native/blob/master/React/Base/RCTTouchHandler.h)。狀態更新通過常規的事件回調來實現。對於 #3 和 #4，我們使用 [nativeID](https://github.com/facebook/react-native/blob/master/React/Views/UIView%2BReact.h#L28) 字段在原生代碼中創建 `<TextInput>` 組件時定位附件視圖的 UIView 層次結構。此函數使用底層原生文本輸入的 `.inputAccessoryView` 屬性。這樣做有效地在它們的 ObjC 實現中將 `<InputAccessoryView>` 與 `<TextInput>` 連結起來。

支持粘性文本輸入（情景 2）增加了更多的限制。對於這種設計，輸入附件視圖有一個文本輸入作為子視圖，因此通過 nativeID 連結是不可行的。相反，我們將一個通用的離屏 `UIView` 的 `.inputAccessoryView` 設置為我們的原生 `<InputAccessoryView>` 層次結構。通過手動告訴這個通用的 `UIView` 成為第一響應者，該層次結構由響應者基礎設施掛載。這個概念在前述的博客文章中有詳細解釋。

## 陷阱

當然，在構建這個 API 的過程中並非一帆風順。以下是我們遇到的一些陷阱，以及我們如何解決它們。

構建這個 API 的初始想法涉及監聽 `NSNotificationCenter` 的 UIKeyboardWill(Show/Hide/ChangeFrame) 事件。這種模式在一些開源庫和 Facebook 應用內部的一些部分中使用。不幸的是，`UIKeyboardDidChangeFrame` 事件沒有及時被調用來更新 `<InputAccessoryView>` 的框架在滑動時。此外，鍵盤高度的變化未被這些事件捕獲。這導致了一類錯誤，表現如下：

<img src="/blog/assets/input-accessory-4.gif" style={{float: 'left', paddingRight: 70, paddingTop: 20}} />

在 iPhone X 上，文本和表情鍵盤的高度不同。大多數使用鍵盤事件來操作文本輸入框架的應用程序都必須修復上述錯誤。我們的解決方案是承諾使用 `.inputAccessoryView` 屬性，這意味著響應者基礎設施會處理這樣的框架更新。

<hr style={{clear: 'both', marginBottom: 20}} />

我們遇到的另一個棘手的錯誤是避免 iPhone X 上的主頁指示條。你可能會想，“Apple 開發了 [safeAreaLayoutGuide](https://developer.apple.com/documentation/uikit/uiview/2891102-safearealayoutguide?language=objc) 正是為此，這很簡單！”。我們也曾如此天真。第一個問題是原生的 `<InputAccessoryView>` 實現沒有窗口可以錨定，直到它即將出現的那一刻。這沒關係，我們可以覆蓋 `-(BOOL)becomeFirstResponder` 並在那裡強制執行佈局約束。遵守這些約束會將附件視圖向上推，但另一個錯誤出現了：<img src="/blog/assets/input-accessory-5.gif" style={{float: 'left', paddingRight: 70, paddingTop: 20}} />

輸入附件視圖成功避開了主頁指示條，但現在不安全區域後面的內容可見。解決方案在於這個 [radar](https://www.openradar.me/34411433)。我將原生的 `<InputAccessoryView>` 層次結構包裹在一個不遵守 `safeAreaLayoutGuide` 約束的容器中。原生容器覆蓋了不安全區域的內容，而 `<InputAccessoryView>` 保持在安全區域的邊界內。

<hr style={{clear: 'both', marginBottom: 20}} />

## 示例用法

這是一個構建鍵盤工具欄按鈕以重置 `<TextInput>` 狀態的示例。

```jsx
class TextInputAccessoryViewExample extends React.Component<
  {},
  *,
> {
  constructor(props) {
    super(props);
    this.state = {text: 'Placeholder Text'};
  }

  render() {
    const inputAccessoryViewID = 'inputAccessoryView1';
    return (
      <View>
        <TextInput
          style={styles.default}
          inputAccessoryViewID={inputAccessoryViewID}
          onChangeText={text => this.setState({text})}
          value={this.state.text}
        />
        <InputAccessoryView nativeID={inputAccessoryViewID}>
          <View style={{backgroundColor: 'white'}}>
            <Button
              onPress={() =>
                this.setState({text: 'Placeholder Text'})
              }
              title="Reset Text"
            />
          </View>
        </InputAccessoryView>
      </View>
    );
  }
}
```

另一個 [粘性文本輸入的示例可以在存儲庫中找到](https://github.com/facebook/react-native/blob/84ef7bc372ad870127b3e1fb8c13399fe09ecd4d/RNTester/js/InputAccessoryViewExample.js)。

## 我什麼時候可以使用這個？

The full commit for this feature implementation is [here](https://github.com/facebook/react-native/commit/38197c8230657d567170cdaf8ff4bbb4aee732b8). [`<InputAccessoryView>`](/docs/next/inputaccessoryview) will be available in the upcoming v0.55.0 release.

Happy keyboarding :)