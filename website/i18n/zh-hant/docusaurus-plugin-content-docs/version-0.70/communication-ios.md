---
id: communication-ios
title: Communication between native and React Native
---

在[整合既有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](native-components-ios)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通管道。其他指南已提及部分實現方法，本文將統整現有技術方案。

## 導論

React Native的設計靈感源自React，因此資訊流的基本概念相似。React中的資料流是單向的——我們維護元件層級結構，每個元件僅依賴其父元件與自身內部狀態。這透過屬性(properties)實現：資料以自上而下的方式從父元件傳遞給子元件。若祖先元件需依賴後代元件的狀態，則應向下傳遞回調函數供後代元件更新祖先狀態。

此概念同樣適用於React Native。只要在框架內構建應用程式，我們就能透過屬性和回調驅動應用。但當混合使用React Native與原生元件時，需要特定的跨語言機制來實現兩者間的資訊傳遞。

## 屬性

屬性是最直接的跨元件通訊方式，因此我們需要實現雙向屬性傳遞：從原生到React Native，以及從React Native到原生。

### 從原生傳遞屬性至React Native

要在原生元件中嵌入React Native視圖，我們使用`RCTRootView`。這個`UIView`子類不僅承載React Native應用，還提供原生端與宿主應用間的介面。

`RCTRootView`的初始化方法允許傳遞任意屬性至React Native應用。`initialProperties`參數必須是`NSDictionary`實例，該字典會在內部轉換為JSON物件，供頂層JS元件引用。

```objectivec
NSArray *imageList = @[@"http://foo.com/bar1.png",
                       @"http://foo.com/bar2.png"];

NSDictionary *props = @{@"images" : imageList};

RCTRootView *rootView = [[RCTRootView alloc] initWithBridge:bridge
                                                 moduleName:@"ImageBrowserApp"
                                          initialProperties:props];
```

```jsx
import React from 'react';
import {View, Image} from 'react-native';

export default class ImageBrowserApp extends React.Component {
  renderImage(imgURI) {
    return <Image source={{uri: imgURI}} />;
  }
  render() {
    return <View>{this.props.images.map(this.renderImage)}</View>;
  }
}
```

`RCTRootView`還提供可讀寫屬性`appProperties`。設定此屬性後，React Native應用會使用新屬性重新渲染。僅當新屬性與先前值不同時才會觸發更新。

```objectivec
NSArray *imageList = @[@"http://foo.com/bar3.png",
                       @"http://foo.com/bar4.png"];

rootView.appProperties = @{@"images" : imageList};
```

隨時更新屬性皆可，但更新操作必須在主線程執行，而讀取操作可在任意線程進行。

:::note
請注意：目前存在已知問題，若在橋接器啟動階段設定appProperties，變更可能會遺失。詳見https://github.com/facebook/react-native/issues/20115。
:::

無法僅更新部分屬性，建議自行實裝包裝層處理此需求。

### 從React Native傳遞屬性至原生

原生元件屬性的暴露方法詳見[此文](native-components-ios#properties)。簡言之，在自訂原生元件中使用`RCT_CUSTOM_VIEW_PROPERTY`宏導出屬性，即可在React Native中像普通元件般使用這些屬性。

### 屬性的限制

跨語言屬性的主要缺陷在於不支援回調機制，這使得我們無法實現自下而上的資料綁定。例如當需要透過JS操作移除原生父視圖中的RN子視圖時，純屬性系統無法實現此類向上傳遞的需求。

雖然存在跨語言回調的變體方案（參見[此處說明](native-modules-ios#callbacks)），但這些回調並非萬用解方。主要問題在於它們不適合作為屬性傳遞，該機制設計初衷是讓JS觸發原生操作後，仍在JS端處理操作結果。

## 其他跨語言互動方式（事件與原生模組）

As stated in the previous chapter, using properties comes with some limitations. Sometimes properties are not enough to drive the logic of our app and we need a solution that gives more flexibility. This chapter covers other communication techniques available in React Native. They can be used for internal communication (between JS and native layers in RN) as well as for external communication (between RN and the 'pure native' part of your app).

React Native enables you to perform cross-language function calls. You can execute custom native code from JS and vice versa. Unfortunately, depending on the side we are working on, we achieve the same goal in different ways. For native - we use events mechanism to schedule an execution of a handler function in JS, while for React Native we directly call methods exported by native modules.

### Calling React Native functions from native (events)

Events are described in detail in [this article](native-components-ios#events). Note that using events gives us no guarantees about execution time, as the event is handled on a separate thread.

Events are powerful, because they allow us to change React Native components without needing a reference to them. However, there are some pitfalls that you can fall into while using them:

- As events can be sent from anywhere, they can introduce spaghetti-style dependencies into your project.
- Events share namespace, which means that you may encounter some name collisions. Collisions will not be detected statically, which makes them hard to debug.
- If you use several instances of the same React Native component and you want to distinguish them from the perspective of your event, you'll likely need to introduce identifiers and pass them along with events (you can use the native view's `reactTag` as an identifier).

The common pattern we use when embedding native in React Native is to make the native component's RCTViewManager a delegate for the views, sending events back to JavaScript via the bridge. This keeps related event calls in one place.

### Calling native functions from React Native (native modules)

Native modules are Objective-C classes that are available in JS. Typically one instance of each module is created per JS bridge. They can export arbitrary functions and constants to React Native. They have been covered in detail in [this article](native-modules-ios#content).

The fact that native modules are singletons limits the mechanism in the context of embedding. Let's say we have a React Native component embedded in a native view and we want to update the native, parent view. Using the native module mechanism, we would export a function that not only takes expected arguments, but also an identifier of the parent native view. The identifier would be used to retrieve a reference to the parent view to update. That said, we would need to keep a mapping from identifiers to native views in the module.

Although this solution is complex, it is used in `RCTUIManager`, which is an internal React Native class that manages all React Native views.

Native modules can also be used to expose existing native libraries to JS. The [Geolocation library](https://github.com/michalchudziak/react-native-geolocation) is a living example of the idea.

:::caution
All native modules share the same namespace. Watch out for name collisions when creating new ones.
:::

## Layout computation flow

When integrating native and React Native, we also need a way to consolidate two different layout systems. This section covers common layout problems and provides a brief description of mechanisms to address them.

### Layout of a native component embedded in React Native

This case is covered in [this article](native-components-ios#styles). To summarize, since all our native react views are subclasses of `UIView`, most style and size attributes will work like you would expect out of the box.

### Layout of a React Native component embedded in native

#### React Native content with fixed size

The general scenario is when we have a React Native app with a fixed size, which is known to the native side. In particular, a full-screen React Native view falls into this case. If we want a smaller root view, we can explicitly set RCTRootView's frame.

For instance, to make an RN app 200 (logical) pixels high, and the hosting view's width wide, we could do:

```objectivec title='SomeViewController.m'
- (void)viewDidLoad
{
  [...]
  RCTRootView *rootView = [[RCTRootView alloc] initWithBridge:bridge
                                                   moduleName:appName
                                            initialProperties:props];
  rootView.frame = CGRectMake(0, 0, self.view.width, 200);
  [self.view addSubview:rootView];
}
```

When we have a fixed size root view, we need to respect its bounds on the JS side. In other words, we need to ensure that the React Native content can be contained within the fixed-size root view. The easiest way to ensure this is to use Flexbox layout. If you use absolute positioning, and React components are visible outside the root view's bounds, you'll get overlap with native views, causing some features to behave unexpectedly. For instance, 'TouchableHighlight' will not highlight your touches outside the root view's bounds.

It's totally fine to update root view's size dynamically by re-setting its frame property. React Native will take care of the content's layout.

#### React Native content with flexible size

In some cases we'd like to render content of initially unknown size. Let's say the size will be defined dynamically in JS. We have two solutions to this problem.

1. You can wrap your React Native view in a `ScrollView` component. This guarantees that your content will always be available and it won't overlap with native views.
2. React Native allows you to determine, in JS, the size of the RN app and provide it to the owner of the hosting `RCTRootView`. The owner is then responsible for re-laying out the subviews and keeping the UI consistent. We achieve this with `RCTRootView`'s flexibility modes.

`RCTRootView` supports 4 different size flexibility modes:

```objectivec title='RCTRootView.h'
typedef NS_ENUM(NSInteger, RCTRootViewSizeFlexibility) {
  RCTRootViewSizeFlexibilityNone = 0,
  RCTRootViewSizeFlexibilityWidth,
  RCTRootViewSizeFlexibilityHeight,
  RCTRootViewSizeFlexibilityWidthAndHeight,
};
```

`RCTRootViewSizeFlexibilityNone` is the default value, which makes a root view's size fixed (but it still can be updated with `setFrame:`). The other three modes allow us to track React Native content's size updates. For instance, setting mode to `RCTRootViewSizeFlexibilityHeight` will cause React Native to measure the content's height and pass that information back to `RCTRootView`'s delegate. An arbitrary action can be performed within the delegate, including setting the root view's frame, so the content fits. The delegate is called only when the size of the content has changed.

:::caution
Making a dimension flexible in both JS and native leads to undefined behavior. For example - don't make a top-level React component's width flexible (with `flexbox`) while you're using `RCTRootViewSizeFlexibilityWidth` on the hosting `RCTRootView`.
:::

Let's look at an example.

```objectivec title='FlexibleSizeExampleView.m'
- (instancetype)initWithFrame:(CGRect)frame
{
  [...]

  _rootView = [[RCTRootView alloc] initWithBridge:bridge
  moduleName:@"FlexibilityExampleApp"
  initialProperties:@{}];

  _rootView.delegate = self;
  _rootView.sizeFlexibility = RCTRootViewSizeFlexibilityHeight;
  _rootView.frame = CGRectMake(0, 0, self.frame.size.width, 0);
}

#pragma mark - RCTRootViewDelegate
- (void)rootViewDidChangeIntrinsicSize:(RCTRootView *)rootView
{
  CGRect newFrame = rootView.frame;
  newFrame.size = rootView.intrinsicContentSize;

  rootView.frame = newFrame;
}
```

In the example we have a `FlexibleSizeExampleView` view that holds a root view. We create the root view, initialize it and set the delegate. The delegate will handle size updates. Then, we set the root view's size flexibility to `RCTRootViewSizeFlexibilityHeight`, which means that `rootViewDidChangeIntrinsicSize:` method will be called every time the React Native content changes its height. Finally, we set the root view's width and position. Note that we set there height as well, but it has no effect as we made the height RN-dependent.

You can checkout full source code of the example [here](https://github.com/facebook/react-native/blob/0.70-stable/packages/rn-tester/RNTester/NativeExampleViews/FlexibleSizeExampleView.m).

It's fine to change root view's size flexibility mode dynamically. Changing flexibility mode of a root view will schedule a layout recalculation and the delegate `rootViewDidChangeIntrinsicSize:` method will be called once the content size is known.

:::note
React Native layout calculation is performed on a separate thread, while native UI view updates are done on the main thread.
This may cause temporary UI inconsistencies between native and React Native. This is a known problem and our team is working on synchronizing UI updates coming from different sources.
:::

:::note
React Native does not perform any layout calculations until the root view becomes a subview of some other views.
If you want to hide React Native view until its dimensions are known, add the root view as a subview and make it initially hidden (use `UIView`'s `hidden` property). Then change its visibility in the delegate method.
:::