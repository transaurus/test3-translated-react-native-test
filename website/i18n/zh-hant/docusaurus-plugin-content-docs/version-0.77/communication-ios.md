---
id: communication-ios
title: Communication between native and React Native
---

在[整合既有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](legacy/native-components-ios)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當我們混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通管道。其他指南已提及部分實現方法，本文將總結現有技術方案。

## 簡介

React Native的設計靈感來自React，因此資訊流的基本概念相似。React中的資料流是單向的——我們維護一個元件層級結構，每個元件僅依賴其父元件和自身內部狀態。這通過屬性(properties)實現：資料以自上而下的方式從父元件傳遞給子元件。若祖先元件需要依賴後代元件的狀態，則應向下傳遞回調函數供後代元件更新祖先狀態。

相同概念適用於React Native。只要我們完全在框架內構建應用程式，就可以通過屬性和回調驅動應用。但當混合使用React Native與原生元件時，我們需要特定的跨語言機制來實現它們之間的資訊傳遞。

## 屬性

屬性是跨元件通訊最直接的方式。因此我們需要實現雙向屬性傳遞：從原生到React Native，以及從React Native到原生。

### 從原生傳遞屬性至React Native

要在原生元件中嵌入React Native視圖，我們使用`RCTRootView`。這個`UIView`子類承載著React Native應用，同時提供原生端與宿主應用之間的接口。

`RCTRootView`的初始化方法允許向下傳遞任意屬性到React Native應用。`initialProperties`參數必須是`NSDictionary`實例，該字典會在內部轉換為頂層JS元件可引用的JSON物件。

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

`RCTRootView`還提供可讀寫屬性`appProperties`。設置此屬性後，React Native應用會使用新屬性重新渲染。僅當新屬性與先前值不同時才會執行更新。

```objectivec
NSArray *imageList = @[@"https://dummyimage.com/600x400/ff0000/000000.png",
                       @"https://dummyimage.com/600x400/ffffff/ff0000.png"];

rootView.appProperties = @{@"images" : imageList};
```

隨時更新屬性在技術上是可行的，但更新操作必須在主線程執行。而讀取操作可在任意線程進行。

:::note
請注意目前存在已知問題：若在橋接器(bridge)啟動期間設置appProperties，更改可能會失效。詳見https://github.com/facebook/react-native/issues/20115
:::

現有機制不支持部分屬性更新。建議您自行建立封裝層來實現此功能。

### 從React Native傳遞屬性至原生

原生元件屬性的暴露問題在[這篇文章](legacy/native-components-ios#properties)中有詳細說明。簡而言之，在自訂原生元件中使用`RCT_CUSTOM_VIEW_PROPERTY`宏導出屬性，即可在React Native中像使用普通元件那樣調用這些屬性。

### 屬性的限制

跨語言屬性的主要缺陷是不支援回調機制，而回調正是實現自下而上資料綁定的關鍵。例如當您需要通過JS操作移除原生父視圖中的RN子視圖時，純屬性方案無法實現這種自下而上的資訊流動。

雖然存在某種跨語言回調機制([參見此處說明](legacy/native-modules-ios#callbacks))，但這些回調並非萬能解藥。核心問題在於它們並非設計用來作為屬性傳遞，而是作為觸發原生動作後在JS端處理結果的機制。

## 其他跨語言互動方式（事件與原生模組）

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

舉例來說，若要讓一個 React Native 應用程式高度為 200（邏輯）像素，寬度與宿主視圖同寬，我們可以這樣做：

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

當我們有一個固定尺寸的根視圖時，需要在 JavaScript 端尊重其邊界。換句話說，我們需要確保 React Native 內容能被限制在固定尺寸的根視圖內。最簡單的方法是使用 Flexbox 佈局。如果使用絕對定位，且 React 元件在根視圖邊界外可見，將會與原生視圖重疊，導致某些功能出現非預期行為。例如，'TouchableHighlight' 將不會在根視圖邊界外觸發高亮效果。

動態調整根視圖尺寸（透過重新設定 frame 屬性）是完全可行的，React Native 會自動處理內容的佈局。

#### 彈性尺寸的 React Native 內容

某些情況下，我們需要渲染初始尺寸未知的內容。假設尺寸將由 JavaScript 動態決定，我們有兩種解決方案：

1. 可用 `ScrollView` 元件包裹 React Native 視圖，確保內容始終可存取且不會與原生視圖重疊。
2. React Native 允許在 JavaScript 中計算應用程式尺寸，並將結果傳遞給宿主 `RCTRootView` 的擁有者。擁有者需負責重新佈局子視圖以維持介面一致性，這是透過 `RCTRootView` 的彈性模式實現的。

`RCTRootView` 支援四種尺寸彈性模式：

```objectivec title='RCTRootView.h'
typedef NS_ENUM(NSInteger, RCTRootViewSizeFlexibility) {
  RCTRootViewSizeFlexibilityNone = 0,
  RCTRootViewSizeFlexibilityWidth,
  RCTRootViewSizeFlexibilityHeight,
  RCTRootViewSizeFlexibilityWidthAndHeight,
};
```

預設值 `RCTRootViewSizeFlexibilityNone` 會固定根視圖尺寸（仍可透過 `setFrame:` 更新）。其他三種模式能追蹤 React Native 內容的尺寸變化。例如，設定為 `RCTRootViewSizeFlexibilityHeight` 時，React Native 會測量內容高度並將資訊回傳給 `RCTRootView` 的委派對象。委派方法中可執行任何操作（包括調整根視圖 frame 以適應內容），且僅在內容尺寸變化時觸發。

:::caution
若同時在 JavaScript 與原生端設定彈性尺寸會導致未定義行為。例如：不應在頂層 React 元件使用 `flexbox` 彈性寬度時，又對宿主 `RCTRootView` 啟用 `RCTRootViewSizeFlexibilityWidth`。
:::

以下為具體範例：

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

範例中的 `FlexibleSizeExampleView` 包含一個根視圖。我們建立並初始化根視圖後設定委派對象來處理尺寸更新。將尺寸彈性設為 `RCTRootViewSizeFlexibilityHeight` 後，每當 React Native 內容高度變化時，`rootViewDidChangeIntrinsicSize:` 方法就會被呼叫。最後設定根視圖的寬度與位置（注意高度設定此時無效，因為高度已交由 React Native 決定）。

完整範例程式碼可參閱[此處](https://github.com/facebook/react-native/blob/main/packages/rn-tester/RNTester/NativeExampleViews/FlexibleSizeExampleView.mm)。

動態變更根視圖的尺寸彈性模式是可行的。變更後會觸發佈局重新計算，內容尺寸確定後即呼叫委派方法 `rootViewDidChangeIntrinsicSize:`。

:::note
React Native 的佈局計算在背景執行緒進行，而原生 UI 更新則在主執行緒處理。
這可能導致原生與 React Native 介面暫時不一致，此為已知問題，團隊正在努力同步不同來源的 UI 更新。
:::

:::note
React Native 在根視圖成為其他視圖的子視圖之前，不會執行任何佈局計算。
若您希望在知道尺寸前隱藏 React Native 視圖，可先將根視圖添加為子視圖並設定為初始隱藏狀態（使用 `UIView` 的 `hidden` 屬性）。隨後在委派方法中變更其可見性。
:::