---
id: custom-webview-ios
title: Custom WebView
---

While the built-in web view has a lot of features, it is not possible to handle every use-case in React Native. You can, however, extend the web view with native code without forking React Native or duplicating all the existing web view code.

Before you do this, you should be familiar with the concepts in [native UI components](native-components-ios). You should also familiarise yourself with the [native code for web views](https://github.com/react-native-webview/react-native-webview/blob/master/apple/RNCWebViewManager.mm), as you will have to use this as a reference when implementing new features—although a deep understanding is not required.

## Native Code

Like for regular native components, you need a view manager and a web view.

For the view, you'll need to make a subclass of `RCTWebView`.

```objc
// RCTCustomWebView.h
#import <React/RCTWebView.h>

@interface RCTCustomWebView : RCTWebView

@end

// RCTCustomWebView.m
#import "RCTCustomWebView.h"

@interface RCTCustomWebView ()

@end

@implementation RCTCustomWebView { }

@end
```

For the view manager, you need to make a subclass `RCTWebViewManager`. You must still include:

- `(UIView *)view` that returns your custom view
- The `RCT_EXPORT_MODULE()` tag

```objc
// RCTCustomWebViewManager.h
#import <React/RCTWebViewManager.h>

@interface RCTCustomWebViewManager : RCTWebViewManager

@end

// RCTCustomWebViewManager.m
#import "RCTCustomWebViewManager.h"
#import "RCTCustomWebView.h"

@interface RCTCustomWebViewManager () <RCTWebViewDelegate>

@end

@implementation RCTCustomWebViewManager { }

RCT_EXPORT_MODULE()

- (UIView *)view
{
  RCTCustomWebView *webView = [RCTCustomWebView new];
  webView.delegate = self;
  return webView;
}

@end
```

### Adding New Events and Properties

Adding new properties and events is the same as regular UI components. For properties, you define an `@property` in the header. For events, you define a `RCTDirectEventBlock` in the view's `@interface`.

```objc
// RCTCustomWebView.h
@property (nonatomic, copy) NSString *finalUrl;

// RCTCustomWebView.m
@interface RCTCustomWebView ()

@property (nonatomic, copy) RCTDirectEventBlock onNavigationCompleted;

@end
```

Then expose it in the view manager's `@implementation`.

```objc
// RCTCustomWebViewManager.m
RCT_EXPORT_VIEW_PROPERTY(onNavigationCompleted, RCTDirectEventBlock)
RCT_EXPORT_VIEW_PROPERTY(finalUrl, NSString)
```

### Extending Existing Events

You should refer to [RCTWebView.m](https://github.com/react-native-webview/react-native-webview/blob/master/apple/RNCWebView.m) in the React Native WebView codebase to see what handlers are available and how they are implemented. You can extend any methods here to provide extra functionality.

By default, most methods aren't exposed from RCTWebView. If you need to expose them, you need to create an [Objective C category](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html), and then expose all the methods you need to use.

```objc
// RCTWebView+Custom.h
#import <React/RCTWebView.h>

@interface RCTWebView (Custom)
- (BOOL)webView:(__unused UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;
- (NSMutableDictionary<NSString *, id> *)baseEvent;
@end
```

Once these are exposed, you can reference them in your custom web view class.

```objc
// RCTCustomWebView.m

// Remember to import the category file.
#import "RCTWebView+Custom.h"

- (BOOL)webView:(__unused UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request
 navigationType:(UIWebViewNavigationType)navigationType
{
  BOOL allowed = [super webView:webView shouldStartLoadWithRequest:request navigationType:navigationType];

  if (allowed) {
    NSString* url = request.URL.absoluteString;
    if (url && [url isEqualToString:_finalUrl]) {
      if (_onNavigationCompleted) {
        NSMutableDictionary<NSString *, id> *event = [self baseEvent];
        _onNavigationCompleted(event);
      }
    }
  }

  return allowed;
}
```

## JavaScript Interface

To use your custom web view, you'll need to create a class for it. Your class must:

- Export all the prop types from `WebView.propTypes`
- Return a `WebView` component with the prop `nativeConfig.component` set to your native component (see below)

To get your native component, you must use `requireNativeComponent`: the same as for regular custom components. However, you must pass in an extra third argument, `WebView.extraNativeComponentConfig`. This third argument contains prop types that are only required for native code.

```jsx
import React, {Component, PropTypes} from 'react';
import {
  WebView,
  requireNativeComponent,
  NativeModules,
} from 'react-native';
const {CustomWebViewManager} = NativeModules;

export default class CustomWebView extends Component {
  static propTypes = WebView.propTypes;

  render() {
    return (
      <WebView
        {...this.props}
        nativeConfig={{
          component: RCTCustomWebView,
          viewManager: CustomWebViewManager,
        }}
      />
    );
  }
}

const RCTCustomWebView = requireNativeComponent(
  'RCTCustomWebView',
  CustomWebView,
  WebView.extraNativeComponentConfig,
);
```

If you want to add custom props to your native component, you can use `nativeConfig.props` on the web view. For iOS, you should also set the `nativeConfig.viewManager` prop with your custom WebView ViewManager as in the example above.

For events, the event handler must always be set to a function. This means it isn't safe to use the event handler directly from `this.props`, as the user might not have provided one. The standard approach is to create an event handler in your class, and then invoking the event handler given in `this.props` if it exists.

If you are unsure how something should be implemented from the JS side, look at [WebView.ios.tsx](https://github.com/react-native-webview/react-native-webview/blob/master/src/WebView.ios.tsx) in the React Native source.

```jsx
export default class CustomWebView extends Component {
  static propTypes = {
    ...WebView.propTypes,
    finalUrl: PropTypes.string,
    onNavigationCompleted: PropTypes.func,
  };

  static defaultProps = {
    finalUrl: 'about:blank',
  };

  _onNavigationCompleted = event => {
    const {onNavigationCompleted} = this.props;
    onNavigationCompleted && onNavigationCompleted(event);
  };

  render() {
    return (
      <WebView
        {...this.props}
        nativeConfig={{
          component: RCTCustomWebView,
          props: {
            finalUrl: this.props.finalUrl,
            onNavigationCompleted: this._onNavigationCompleted,
          },
          viewManager: CustomWebViewManager,
        }}
      />
    );
  }
}
```

與常規原生元件類似，您必須在元件中提供所有屬性類型，以便將它們轉發到原生元件。然而，如果您有一些僅在元件內部使用的屬性類型，可以將它們添加到前面提到的第三個參數的 `nativeOnly` 屬性中。對於事件處理程序，您必須使用值 `true` 而不是常規的屬性類型。

例如，如果您想添加一個名為 `onScrollToBottom` 的內部事件處理程序，您可以使用，

```jsx
const RCTCustomWebView = requireNativeComponent(
  'RCTCustomWebView',
  CustomWebView,
  {
    ...WebView.extraNativeComponentConfig,
    nativeOnly: {
      ...WebView.extraNativeComponentConfig.nativeOnly,
      onScrollToBottom: true,
    },
  },
);
```