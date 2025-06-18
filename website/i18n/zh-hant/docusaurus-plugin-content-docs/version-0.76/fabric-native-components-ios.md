---
id: fabric-native-components-ios
title: 'Fabric Native Components: iOS'
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

Now it's time to write some iOS platform code to be able to render the web view. The steps you need to follow are:

- Run Codegen.
- Write the code for the `RCTWebView`
- Register the `RCTWebView` in the application

### 1. Run Codegen

You can [manually run](the-new-architecture/codegen-cli) the Codegen, however it's simpler to use the application you're going to demo the component in to do this for you.

```bash
cd ios
bundle install
bundle exec pods install
```

Importantly you will see logging output from Codegen, which we're going to use in Xcode to build our WebView native component.

:::warning
You should be careful about committing generated code to your repository. Generated code is specific to each version of React Native. Use npm [peerDependencies](https://nodejs.org/en/blog/npm/peer-dependencies) to restrict compatibility with version of React Native.
:::

### 3. Write the `RCTWebView`

We need to prepare your iOS project using Xcode by completing these **5 steps**:

1. Open the CocoPods generated Xcode Workspace:

```bash
cd ios
open Demo.xcworkspace
```

<img class="half-size" alt="Open Xcode Workspace" src="/docs/assets/fabric-native-components/1.webp" />

2. Right click on app and select <code>New Group</code>, call the new group `WebView`.

<img class="half-size" alt="Right click on app and select New Group" src="/docs/assets/fabric-native-components/2.webp" />

3. In the `WebView` group, create <code>New</code>→<code>File from Template</code>.

<img class="half-size" alt="Create a new file using the Cocoa Touch Classs template" src="/docs/assets/fabric-native-components/3.webp" />

4. Use the <code>Objective-C File</code> template, and name it <code>RCTWebView</code>.

<img class="half-size" alt="Create an Objective-C RCTWebView class" src="/docs/assets/fabric-native-components/4.webp" />

5. Rename <code>RCTWebView.m</code> → <code>RCTWebView.mm</code> making it an Objective-C++ file

```text title="Demo/ios"
Podfile
...
Demo
├── AppDelegate.h
├── AppDelegate.mm
...
// highlight-start
├── RCTWebView.h
├── RCTWebView.mm
// highlight-end
└── main.m
```

After creating the header file and the implementation file, you can start implementing them.

This is the code for the `RCTWebView.h` file, which declares the component interface.

```objc title="Demo/RCTWebView/RTNWebView.h"
#import <React/RCTViewComponentView.h>
#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface RCTWebView : RCTViewComponentView

// You would declare native methods you'd want to access from the view here

@end

NS_ASSUME_NONNULL_END
```

This class defines an `RCTWebView` which extends the `RCTViewComponentView` class. This is the base class for all the native components and it is provided by React Native.

The code for the implementation file (`RCTWebView.mm`) is the following:

```objc title="Demo/RCTWebView/RCTWebView.mm"
#import "RCTWebView.h"

#import <react/renderer/components/AppSpecs/ComponentDescriptors.h>
#import <react/renderer/components/AppSpecs/EventEmitters.h>
#import <react/renderer/components/AppSpecs/Props.h>
#import <react/renderer/components/AppSpecs/RCTComponentViewHelpers.h>
// highlight-next-line
#import <WebKit/WebKit.h>

using namespace facebook::react;

@interface RCTWebView () <RCTCustomWebViewViewProtocol, WKNavigationDelegate>
@end

@implementation RCTWebView {
  NSURL * _sourceURL;
  WKWebView * _webView;
}

-(instancetype)init
{
  if(self = [super init]) {
    // highlight-start
    _webView = [WKWebView new];
    _webView.navigationDelegate = self;
    [self addSubview:_webView];
    // highlight-end
  }
  return self;
}

- (void)updateProps:(Props::Shared const &)props oldProps:(Props::Shared const &)oldProps
{
  const auto &oldViewProps = *std::static_pointer_cast<CustomWebViewProps const>(_props);
  const auto &newViewProps = *std::static_pointer_cast<CustomWebViewProps const>(props);

  // Handle your props here
  if (oldViewProps.sourceURL != newViewProps.sourceURL) {
    NSString *urlString = [NSString stringWithCString:newViewProps.sourceURL.c_str() encoding:NSUTF8StringEncoding];
    _sourceURL = [NSURL URLWithString:urlString];
    // highlight-start
    if ([self urlIsValid:newViewProps.sourceURL]) {
      [_webView loadRequest:[NSURLRequest requestWithURL:_sourceURL]];
    }
    // highlight-end
  }

  [super updateProps:props oldProps:oldProps];
}

-(void)layoutSubviews
{
  [super layoutSubviews];
  _webView.frame = self.bounds;

}

#pragma mark - WKNavigationDelegate

// highlight-start
-(void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation
{
  CustomWebViewEventEmitter::OnScriptLoaded result = CustomWebViewEventEmitter::OnScriptLoaded{CustomWebViewEventEmitter::OnScriptLoadedResult::Success};
  self.eventEmitter.onScriptLoaded(result);
}

- (BOOL)urlIsValid:(std::string)propString
{
  if (propString.length() > 0 && !_sourceURL) {
    CustomWebViewEventEmitter::OnScriptLoaded result = CustomWebViewEventEmitter::OnScriptLoaded{CustomWebViewEventEmitter::OnScriptLoadedResult::Error};

    self.eventEmitter.onScriptLoaded(result);
    return NO;
  }
  return YES;
}

// Event emitter convenience method
- (const CustomWebViewEventEmitter &)eventEmitter
{
  return static_cast<const CustomWebViewEventEmitter &>(*_eventEmitter);
}
// highlight-end

+ (ComponentDescriptorProvider)componentDescriptorProvider
{
  return concreteComponentDescriptorProvider<CustomWebViewComponentDescriptor>();
}

Class<RCTComponentViewProtocol> WebViewCls(void)
{
  return RCTWebView.class;
}

@end
```

This code is written in Objective-C++ and contains various details:

- the `@interface` implements two protocols:
  - `RCTCustomWebViewViewProtocol`, generated by Codegen;
  - `WKNavigationDelegate`, provided by the WebKit frameworks to handle the web view navigation events;
- the `init` method that instantiate the `WKWebView`, adds it to the subviews and that set the `navigationDelegate`;
- the `updateProps` method that is called by React Native when the component's props change;
- the `layoutSubviews` method that describe how the custom view needs to be laid out;
- the `webView:didFinishNavigation:` method that let you handle the what do when the `WKWebView` finishes loading the page;
- the `urlIsValid:(std::string)propString` method that checks whether the URL received as prop is valid;
- the `eventEmitter` method which is a utility to retrieve a strongly typed `eventEmitter` instance
- the `componentDescriptorProvider` which returns the `ComponentDescriptor` generated by Codegen;
- the `WebViewCls` which is a helper method to register the `RCTWebView` in the application.

#### AppDelegate.mm

Finally, you can register the component in the app.
Update the `AppDelegate.mm` to make your application aware of our custom WebView component:

```objc title="Demo/ios/Demo/AppDelegate.mm"
#import "AppDelegate.h"

#import <React/RCTBundleURLProvider.h>
#import <React/RCTBridge+Private.h>
// highlight-next-line
#import "RCTWebView.h"

@implementation AppDelegate
// ...
// highlight-start
- (NSDictionary<NSString *,Class<RCTComponentViewProtocol>> *)thirdPartyFabricComponents
{
  NSMutableDictionary * dictionary = [super thirdPartyFabricComponents].mutableCopy;
  dictionary[@"CustomWebView"] = [RCTWebView class];
  return dictionary;
}
// highlight-end

@end
```

這段程式碼覆寫了 `thirdPartyFabricComponents` 方法，透過取得來自其他來源（如第三方函式庫）的第三方元件字典的可變動副本。

接著在字典中加入一個條目，使用 Codegen 規格檔案中定義的名稱。如此一來，當 React 需要載入名為 `CustomWebView` 的元件時，React Native 就會實例化一個 `RCTWebView`。

最後，它會回傳這個新的字典。

#### 加入 WebKit 框架

:::note
此步驟僅因我們要建立網頁視圖而需要。iOS 上的網頁元件需要連結至 Apple 提供的 WebKit 框架。若您的元件不需要存取網頁相關功能，可跳過此步驟。
:::

網頁視圖需要存取 Apple 透過 Xcode 和裝置內建的框架之一 WebKit 提供的某些功能。您可以在原生程式碼中看到 `#import <WebKit/WebKit.h>` 這行被加入 `RCTWebView.mm` 檔案。

要在您的應用程式中連結 WebKit 框架，請遵循以下步驟：

1. 在 Xcode 中，點擊您的專案
2. 選擇應用程式目標
3. 選擇 General 標籤頁
4. 向下滾動直到找到「Frameworks, Libraries, and Embedded Contents」區段，然後點擊 `+` 按鈕

<img class="half-size" alt="Add webkit framework to your app 1" src="/docs/assets/AddWebKitFramework1.png" />

5. 在搜尋欄中，過濾出 WebKit
6. 選擇 WebKit 框架
7. 點擊 Add

<img class="half-size" alt="Add webkit framework to your app 2" src="/docs/assets/AddWebKitFramework2.png" />