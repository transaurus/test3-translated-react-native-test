---
id: communication-ios
title: Communication between native and React Native
---

在[整合既有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](legacy/native-components-ios)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當我們混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通管道。其他指南已提及部分實現方法，本文將總結現有技術方案。

## 簡介

React Native的設計靈感源自React，因此資訊流的基本概念相似。React中的資料流是單向的——我們維護著元件層級結構，每個元件僅依賴其父元件與自身內部狀態。這通過屬性(properties)實現：資料以自上而下的方式從父元件傳遞給子元件。若祖先元件需依賴後代元件的狀態，則應向下傳遞回調函數供後代元件更新祖先狀態。

此概念同樣適用於React Native。只要我們完全在框架內構建應用程式，就能透過屬性和回調驅動應用。但當混合使用React Native與原生元件時，我們需要特定的跨語言機制來實現兩者間的資訊傳遞。

## 屬性

屬性是最直接的跨元件通訊方式。因此我們需要實現雙向屬性傳遞：從原生到React Native，以及從React Native到原生。

### 從原生向React Native傳遞屬性

要在原生元件中嵌入React Native視圖，我們使用`RCTRootView`。這個`UIView`子類承載著React Native應用，同時提供原生端與宿主應用間的接口。

`RCTRootView`的初始化方法允許向下傳遞任意屬性至React Native應用。`initialProperties`參數必須是`NSDictionary`實例，該字典會在內部轉換為頂層JS元件可引用的JSON物件。

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

隨時更新屬性皆可，但更新操作必須在主線程執行。而讀取操作可在任意線程進行。

:::note
需注意目前存在已知問題：若在橋接器(bridge)啟動期間設置appProperties，更改可能會失效。詳見https://github.com/facebook/react-native/issues/20115。
:::

現無法實現部分屬性更新。建議自行建立封裝層處理此需求。

### 從React Native向原生傳遞屬性

原生元件屬性的暴露問題在[此文章](legacy/native-components-ios#properties)中有詳細說明。簡言之，在自訂原生元件中使用`RCT_CUSTOM_VIEW_PROPERTY`宏導出屬性，即可在React Native中像使用普通元件那樣調用這些屬性。

### 屬性的限制

跨語言屬性的主要缺陷在於不支援回調機制，而回調正是實現自下而上資料綁定的關鍵。例如當你需要透過JS操作移除原生父視圖中的RN子視圖時，純屬性方案無法實現，因為這需要資訊逆向傳遞。

雖然存在某種跨語言回調方案([參見此處](legacy/native-modules-ios#callbacks))，但這些回調並非萬能解藥。主要問題在於它們並非設計為屬性傳遞機制，而是用於從JS觸發原生操作後，在JS端處理操作結果。

## 其他跨語言交互方式（事件與原生模組）

如前一章所述，使用屬性存在一些限制。有時屬性不足以驅動應用程式的邏輯，我們需要一個提供更大靈活性的解決方案。本章涵蓋了 React Native 中可用的其他通訊技術，這些技術既可用於內部通訊（RN 中 JS 與原生層之間的溝通），也可用於外部通訊（RN 與應用程式中「純原生」部分之間的互動）。

React Native 允許您執行跨語言函數呼叫。您可以從 JS 執行自訂原生代碼，反之亦然。遺憾的是，根據我們所處的環境，實現相同目標的方式有所不同。對於原生端，我們使用事件機制來安排在 JS 中執行處理函數；而對於 React Native 端，我們直接呼叫由原生模組匯出的方法。

### 從原生端呼叫 React Native 函數（事件）

事件在[這篇文章](legacy/native-components-ios#events)中有詳細說明。請注意，使用事件無法保證執行時間，因為事件是在單獨的執行緒上處理的。

事件非常強大，因為它們允許我們在不需持有元件參考的情況下修改 React Native 元件。然而，使用時可能會遇到一些陷阱：

- 由於事件可以從任何地方發送，它們可能會在專案中引入義大利麵條式的依賴關係。
- 事件共享命名空間，這意味著您可能會遇到名稱衝突。這些衝突無法靜態檢測，因此難以調試。
- 如果您使用多個相同 React Native 元件的實例，並希望從事件的角度區分它們，您可能需要引入標識符並隨事件一起傳遞（可以使用原生視圖的 `reactTag` 作為標識符）。

在將原生元件嵌入 React Native 時，我們常用的模式是讓原生元件的 RCTViewManager 作為視圖的委託，透過橋接器將事件發送回 JavaScript。這將相關的事件呼叫集中在一個地方。

### 從 React Native 呼叫原生函數（原生模組）

原生模組是在 JS 中可用的 Objective-C 類別。通常每個 JS 橋接器會創建每個模組的一個實例。它們可以向 React Native 匯出任意函數和常數。[這篇文章](legacy/native-modules-ios#content)對此有詳細說明。

原生模組是單例的這一事實限制了嵌入情境下的機制。假設我們有一個嵌入原生視圖中的 React Native 元件，我們希望更新原生的父視圖。使用原生模組機制，我們將匯出一個函數，該函數不僅接受預期的參數，還接受父原生視圖的標識符。該標識符將用於獲取父視圖的參考以進行更新。也就是說，我們需要在模組中維護一個從標識符到原生視圖的映射。

儘管這個解決方案很複雜，但它被用於 `RCTUIManager` 中，這是 React Native 內部管理所有 React Native 視圖的類別。

原生模組還可用於向 JS 公開現有的原生函式庫。[Geolocation 函式庫](https://github.com/michalchudziak/react-native-geolocation)就是這個想法的一個實際例子。

:::caution
所有原生模組共享相同的命名空間。在創建新模組時，請注意名稱衝突。
:::

## 佈局計算流程

在整合原生與 React Native 時，我們還需要一種方法來統一兩種不同的佈局系統。本節涵蓋常見的佈局問題，並提供解決這些機制的簡要說明。

### 嵌入 React Native 的原生元件佈局

[這篇文章](legacy/native-components-ios#styles)涵蓋了這種情況。總結來說，由於我們所有的原生 React 視圖都是 `UIView` 的子類別，大多數樣式和尺寸屬性都會如預期般直接生效。

### 嵌入原生端的 React Native 元件佈局

#### 固定尺寸的 React Native 內容

一般情境是當我們有一個固定尺寸的 React Native 應用程式，且該尺寸對原生端已知。特別是，全螢幕的 React Native 視圖就屬於這種情況。如果我們想要一個較小的根視圖，可以明確設置 RCTRootView 的框架。

例如，要讓一個 RN 應用程式的高度為 200（邏輯）像素，寬度與宿主視圖相同，我們可以這樣做：

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

當我們有一個固定大小的根視圖時，我們需要在 JS 端尊重其邊界。換句話說，我們需要確保 React Native 內容能夠被包含在固定大小的根視圖內。最簡單的方法是使用 Flexbox 佈局。如果你使用絕對定位，且 React 元件在根視圖邊界外可見，將會與原生視圖重疊，導致某些功能行為異常。例如，'TouchableHighlight' 將不會在根視圖邊界外高亮你的觸摸。

動態更新根視圖的大小是完全可行的，只需重新設置其 frame 屬性即可。React Native 會負責內容的佈局。

#### 具有彈性大小的 React Native 內容

在某些情況下，我們希望渲染初始大小未知的內容。假設大小將在 JS 中動態定義。我們有兩種解決方案。

1. 你可以將你的 React Native 視圖包裹在 `ScrollView` 元件中。這保證了你的內容始終可用且不會與原生視圖重疊。
2. React Native 允許你在 JS 中確定 RN 應用的大小，並將其提供給宿主 `RCTRootView` 的所有者。所有者負責重新佈局子視圖並保持 UI 一致性。我們通過 `RCTRootView` 的彈性模式來實現這一點。

`RCTRootView` 支援 4 種不同的尺寸彈性模式：

```objectivec title='RCTRootView.h'
typedef NS_ENUM(NSInteger, RCTRootViewSizeFlexibility) {
  RCTRootViewSizeFlexibilityNone = 0,
  RCTRootViewSizeFlexibilityWidth,
  RCTRootViewSizeFlexibilityHeight,
  RCTRootViewSizeFlexibilityWidthAndHeight,
};
```

`RCTRootViewSizeFlexibilityNone` 是預設值，它使根視圖的大小固定（但仍可以通過 `setFrame:` 更新）。其他三種模式允許我們追蹤 React Native 內容的大小更新。例如，將模式設置為 `RCTRootViewSizeFlexibilityHeight` 會使 React Native 測量內容的高度並將該信息傳遞回 `RCTRootView` 的委託。可以在委託中執行任意操作，包括設置根視圖的 frame，以便內容適應。僅當內容大小發生變化時才會調用委託。

:::caution
在 JS 和原生端同時使一個維度彈性會導致未定義的行為。例如，不要在頂層 React 元件的寬度彈性（使用 `flexbox`）的同時，在宿主 `RCTRootView` 上使用 `RCTRootViewSizeFlexibilityWidth`。
:::

讓我們看一個例子。

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

在這個例子中，我們有一個 `FlexibleSizeExampleView` 視圖，它持有一個根視圖。我們創建根視圖，初始化它並設置委託。委託將處理大小更新。然後，我們將根視圖的大小彈性設置為 `RCTRootViewSizeFlexibilityHeight`，這意味著每次 React Native 內容改變其高度時，`rootViewDidChangeIntrinsicSize:` 方法將被調用。最後，我們設置根視圖的寬度和位置。注意，我們也設置了高度，但由於我們使高度依賴於 RN，所以這不會產生效果。

你可以查看完整的示例源代碼 [這裡](https://github.com/facebook/react-native/blob/main/packages/rn-tester/RNTester/NativeExampleViews/FlexibleSizeExampleView.mm)。

動態更改根視圖的大小彈性模式是可行的。更改根視圖的彈性模式將安排重新計算佈局，並且一旦內容大小已知，委託的 `rootViewDidChangeIntrinsicSize:` 方法將被調用。

:::note
React Native 的佈局計算在單獨的線程上執行，而原生 UI 視圖更新則在主線程上進行。
這可能導致原生和 React Native 之間的臨時 UI 不一致。這是一個已知問題，我們的團隊正在努力同步來自不同來源的 UI 更新。
:::

:::note
React Native 在根視圖成為其他視圖的子視圖之前，不會執行任何佈局計算。
如果您想在知道尺寸前隱藏 React Native 視圖，可先將根視圖添加為子視圖並設定為初始隱藏狀態（使用 `UIView` 的 `hidden` 屬性），然後在委派方法中變更其可見性。
:::