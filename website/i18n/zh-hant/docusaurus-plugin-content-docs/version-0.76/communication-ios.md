---
id: communication-ios
title: Communication between native and React Native
---

在[與現有應用整合指南](integration-with-existing-apps)和[原生UI元件指南](legacy/native-components-ios)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當我們混合使用原生和React Native元件時，最終會需要在這兩個世界之間進行通信。其他指南中已經提到了一些實現方法。本文總結了可用的技術。

## 簡介

React Native的靈感來自React，因此信息流的基本概念是相似的。React中的流動是單向的。我們維護一個元件層次結構，其中每個元件僅依賴於其父元件和自身的內部狀態。我們通過屬性來實現這一點：數據以自上而下的方式從父元件傳遞給其子元件。如果祖先元件依賴於其後代元件的狀態，則應向下傳遞一個回調函數，供後代元件用來更新祖先元件。

同樣的概念適用於React Native。只要我們完全在框架內構建應用程序，我們就可以通過屬性和回調來驅動我們的應用程序。但是，當我們混合使用React Native和原生元件時，我們需要一些特定的跨語言機制來在它們之間傳遞信息。

## 屬性

屬性是跨元件通信最直接的方式。因此，我們需要一種方法來將屬性從原生傳遞到React Native，以及從React Native傳遞到原生。

### 將屬性從原生傳遞到React Native

為了將React Native視圖嵌入原生元件中，我們使用`RCTRootView`。`RCTRootView`是一個`UIView`，它包含一個React Native應用程序。它還提供了原生端與託管應用程序之間的接口。

`RCTRootView`有一個初始化器，允許你將任意屬性傳遞給React Native應用程序。`initialProperties`參數必須是`NSDictionary`的實例。該字典在內部被轉換為頂層JS元件可以引用的JSON對象。

```objectivec
NSArray *imageList = @[@"https://dummyimage.com/600x400/ffffff/000000.png",
                       @"https://dummyimage.com/600x400/000000/ffffff.png"];

NSDictionary *props = @{@"images" : imageList};

RCTRootView *rootView = [[RCTRootView alloc] initWithBridge:bridge
                                                 moduleName:@"ImageBrowserApp"
                                          initialProperties:props];
```

```tsx
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

`RCTRootView`還提供了一個讀寫屬性`appProperties`。設置`appProperties`後，React Native應用程序會使用新屬性重新渲染。僅當新更新的屬性與之前的屬性不同時，才會執行更新。

```objectivec
NSArray *imageList = @[@"https://dummyimage.com/600x400/ff0000/000000.png",
                       @"https://dummyimage.com/600x400/ffffff/ff0000.png"];

rootView.appProperties = @{@"images" : imageList};
```

隨時更新屬性是沒有問題的。但是，更新必須在主線程上執行。你可以在任何線程上使用getter。

:::note
目前有一個已知問題，即在橋接啟動期間設置appProperties時，更改可能會丟失。詳見https://github.com/facebook/react-native/issues/20115。
:::

無法一次只更新部分屬性。我們建議你將其構建到自己的包裝器中。

### 將屬性從React Native傳遞到原生

[這篇文章](legacy/native-components-ios#properties)詳細介紹了暴露原生元件屬性的問題。簡而言之，在你的自定義原生元件中使用`RCT_CUSTOM_VIEW_PROPERTY`宏導出屬性，然後在React Native中使用它們，就像該元件是一個普通的React Native元件一樣。

### 屬性的限制

跨語言屬性的主要缺點是它們不支持回調，而回調可以讓我們處理自下而上的數據綁定。想像一下，你有一個小的RN視圖，你希望由於JS操作而從原生父視圖中刪除它。使用屬性無法實現這一點，因為信息需要自下而上傳遞。

儘管我們有一種跨語言回調的變體（[在此描述](legacy/native-modules-ios#callbacks)），但這些回調並不總是我們需要的。主要問題是它們不打算作為屬性傳遞。相反，這種機制允許我們從JS觸發原生操作，並在JS中處理該操作的結果。

## 其他跨語言交互方式（事件和原生模塊）

As stated in the previous chapter, using properties comes with some limitations. Sometimes properties are not enough to drive the logic of our app and we need a solution that gives more flexibility. This chapter covers other communication techniques available in React Native. They can be used for internal communication (between JS and native layers in RN) as well as for external communication (between RN and the 'pure native' part of your app).

React Native enables you to perform cross-language function calls. You can execute custom native code from JS and vice versa. Unfortunately, depending on the side we are working on, we achieve the same goal in different ways. For native - we use events mechanism to schedule an execution of a handler function in JS, while for React Native we directly call methods exported by native modules.

### Calling React Native functions from native (events)

Events are described in detail in [this article](legacy/native-components-ios#events). Note that using events gives us no guarantees about execution time, as the event is handled on a separate thread.

Events are powerful, because they allow us to change React Native components without needing a reference to them. However, there are some pitfalls that you can fall into while using them:

- As events can be sent from anywhere, they can introduce spaghetti-style dependencies into your project.
- Events share namespace, which means that you may encounter some name collisions. Collisions will not be detected statically, which makes them hard to debug.
- If you use several instances of the same React Native component and you want to distinguish them from the perspective of your event, you'll likely need to introduce identifiers and pass them along with events (you can use the native view's `reactTag` as an identifier).

The common pattern we use when embedding native in React Native is to make the native component's RCTViewManager a delegate for the views, sending events back to JavaScript via the bridge. This keeps related event calls in one place.

### Calling native functions from React Native (native modules)

Native modules are Objective-C classes that are available in JS. Typically one instance of each module is created per JS bridge. They can export arbitrary functions and constants to React Native. They have been covered in detail in [this article](legacy/native-modules-ios#content).

The fact that native modules are singletons limits the mechanism in the context of embedding. Let's say we have a React Native component embedded in a native view and we want to update the native, parent view. Using the native module mechanism, we would export a function that not only takes expected arguments, but also an identifier of the parent native view. The identifier would be used to retrieve a reference to the parent view to update. That said, we would need to keep a mapping from identifiers to native views in the module.

Although this solution is complex, it is used in `RCTUIManager`, which is an internal React Native class that manages all React Native views.

Native modules can also be used to expose existing native libraries to JS. The [Geolocation library](https://github.com/michalchudziak/react-native-geolocation) is a living example of the idea.

:::caution
All native modules share the same namespace. Watch out for name collisions when creating new ones.
:::

## Layout computation flow

When integrating native and React Native, we also need a way to consolidate two different layout systems. This section covers common layout problems and provides a brief description of mechanisms to address them.

### Layout of a native component embedded in React Native

This case is covered in [this article](legacy/native-components-ios#styles). To summarize, since all our native react views are subclasses of `UIView`, most style and size attributes will work like you would expect out of the box.

### Layout of a React Native component embedded in native

#### React Native content with fixed size

The general scenario is when we have a React Native app with a fixed size, which is known to the native side. In particular, a full-screen React Native view falls into this case. If we want a smaller root view, we can explicitly set RCTRootView's frame.

舉例來說，若要讓一個 RN 應用程式高度為 200（邏輯）像素，並與宿主視圖同寬，我們可以這樣做：

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

當我們有一個固定尺寸的根視圖時，需要在 JS 端尊重其邊界。換句話說，我們需要確保 React Native 內容能被限制在固定尺寸的根視圖內。最簡單的方法是使用 Flexbox 佈局。如果使用絕對定位，且 React 元件在根視圖邊界外可見，將會與原生視圖產生重疊，導致某些功能表現異常。例如，'TouchableHighlight' 將不會在根視圖邊界外的高亮觸碰區域生效。

動態更新根視圖尺寸（透過重新設定其 frame 屬性）是完全可行的，React Native 會自動處理內容的佈局調整。

#### 彈性尺寸的 React Native 內容

某些情況下，我們需要渲染初始尺寸未知的內容。假設尺寸將由 JS 動態定義，我們有兩種解決方案：

1. 可用 `ScrollView` 元件包裹 React Native 視圖，確保內容始終可存取且不會與原生視圖重疊。
2. React Native 允許在 JS 中計算 RN 應用的尺寸，並提供給宿主 `RCTRootView` 的所有者。所有者需負責重新佈局子視圖以維持 UI 一致性，這是透過 `RCTRootView` 的彈性模式實現的。

`RCTRootView` 支援四種尺寸彈性模式：

```objectivec title='RCTRootView.h'
typedef NS_ENUM(NSInteger, RCTRootViewSizeFlexibility) {
  RCTRootViewSizeFlexibilityNone = 0,
  RCTRootViewSizeFlexibilityWidth,
  RCTRootViewSizeFlexibilityHeight,
  RCTRootViewSizeFlexibilityWidthAndHeight,
};
```

預設值 `RCTRootViewSizeFlexibilityNone` 會固定根視圖尺寸（仍可透過 `setFrame:` 更新）。其他三種模式能追蹤 React Native 內容的尺寸變化。例如，設定為 `RCTRootViewSizeFlexibilityHeight` 時，React Native 會測量內容高度並將資訊回傳給 `RCTRootView` 的委派。委派可執行任何操作（包括調整根視圖 frame 以適應內容），且僅在內容尺寸變化時觸發。

:::caution
若在 JS 與原生端同時設定維度為彈性，將導致未定義行為。例如：請勿在頂層 React 元件使用 `flexbox` 彈性寬度的同時，對宿主 `RCTRootView` 啟用 `RCTRootViewSizeFlexibilityWidth`。
:::

以下為範例說明：

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

範例中的 `FlexibleSizeExampleView` 視圖持有根視圖。我們建立根視圖後初始化並設定委派來處理尺寸更新。將根視圖的尺寸彈性設為 `RCTRootViewSizeFlexibilityHeight`，意味著每次 React Native 內容高度變化時，都會呼叫 `rootViewDidChangeIntrinsicSize:` 方法。最後設定根視圖的寬度與位置（注意高度設定此時無效，因為高度已交由 RN 控制）。

完整範例程式碼可參閱[此處](https://github.com/facebook/react-native/blob/main/packages/rn-tester/RNTester/NativeExampleViews/FlexibleSizeExampleView.mm)。

動態變更根視圖的尺寸彈性模式是可行的。變更後會觸發佈局重新計算，待內容尺寸確定後即呼叫委派的 `rootViewDidChangeIntrinsicSize:` 方法。

:::note
React Native 的佈局計算在獨立線程執行，而原生 UI 更新則在主線程進行。這可能導致原生與 React Native 間的暫時性 UI 不一致。此為已知問題，團隊正致力於同步不同來源的 UI 更新。
:::

:::note
React Native 在根視圖成為其他視圖的子視圖之前，不會執行任何佈局計算。
如果你想在知道尺寸前隱藏 React Native 視圖，可以將根視圖添加為子視圖並使其初始狀態為隱藏（使用 `UIView` 的 `hidden` 屬性）。然後在委派方法中更改其可見性。
:::