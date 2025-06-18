---
id: communication-ios
title: Communication between native and React Native
---

在[與現有應用整合指南](integration-with-existing-apps)和[原生UI元件指南](native-components-ios)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當我們混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通管道。其他指南已提及部分實現方法，本文將總結現有技術方案。

## 簡介

React Native的設計靈感源自React，因此資訊流的基本概念相似。React中的資料流是單向的——我們維護元件層級結構，每個元件僅依賴其父元件和自身內部狀態。這通過屬性(properties)實現：資料以自上而下的方式從父元件傳遞給子元件。若祖先元件需依賴後代元件的狀態，則應向下傳遞回調函數供後代更新祖先狀態。

此概念同樣適用於React Native。只要我們完全在框架內構建應用程式，就能透過屬性和回調驅動應用。但當混合使用React Native與原生元件時，我們需要特定的跨語言機制來實現它們之間的資訊傳遞。

## 屬性

屬性是最直接的跨元件通訊方式。因此我們需要實現雙向屬性傳遞：從原生到React Native，以及從React Native到原生。

### 從原生向React Native傳遞屬性

要在原生元件中嵌入React Native視圖，我們使用`RCTRootView`。這個`UIView`子類不僅承載React Native應用，還提供原生端與宿主應用之間的接口。

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

`RCTRootView`還提供可讀寫屬性`appProperties`。設置此屬性後，React Native應用會使用新屬性重新渲染。僅當新屬性與先前值不同時才會觸發更新。

```objectivec
NSArray *imageList = @[@"https://dummyimage.com/600x400/ff0000/000000.png",
                       @"https://dummyimage.com/600x400/ffffff/ff0000.png"];

rootView.appProperties = @{@"images" : imageList};
```

隨時更新屬性皆可，但更新操作必須在主線程執行。而讀取(getter)操作可在任意線程進行。

:::note
需注意：目前在橋接器(bridge)啟動階段設置appProperties可能導致變更失效。詳見https://github.com/facebook/react-native/issues/20115
:::

目前無法實現部分屬性更新。建議自行建立封裝層處理此需求。

### 從React Native向原生傳遞屬性

[此文章](native-components-ios#properties)詳細說明了原生元件屬性的暴露方法。簡言之，在自訂原生元件中使用`RCT_CUSTOM_VIEW_PROPERTY`宏導出屬性，即可在React Native中像使用普通元件那樣調用這些屬性。

### 屬性的限制

跨語言屬性的主要缺陷是不支援回調機制，而回調正是實現自下而上資料綁定的關鍵。例如當你需要透過JS操作移除原生父視圖中的RN子視圖時，僅靠屬性無法實現這種自下而上的資訊傳遞。

雖然存在跨語言回調的變通方案([參見此處](native-modules-ios#callbacks))，但這些回調並非萬能解藥。主要問題在於它們不適合作為屬性傳遞，該機制實際是用於從JS觸發原生操作後，在JS端處理操作結果。

## 其他跨語言交互方式（事件與原生模組）

如前一章所述，使用屬性存在一些限制。有時屬性不足以驅動應用程式的邏輯，我們需要一個提供更大靈活性的解決方案。本章涵蓋了 React Native 中可用的其他通訊技術，這些技術既可用於內部通訊（RN 中 JS 與原生層之間的互動），也可用於外部通訊（RN 與應用程式中「純原生」部分之間的互動）。

React Native 允許您執行跨語言函數呼叫。您可以從 JS 執行自訂原生程式碼，反之亦然。遺憾的是，根據我們所處的端點，我們會以不同的方式實現相同的目標。對於原生端，我們使用事件機制來安排在 JS 中執行處理函數；而對於 React Native 端，我們則直接呼叫由原生模組匯出的方法。

### 從原生端呼叫 React Native 函數（事件）

事件在[這篇文章](native-components-ios#events)中有詳細描述。請注意，使用事件無法保證執行時間，因為事件是在單獨的執行緒上處理的。

事件非常強大，因為它們允許我們在不需持有 React Native 元件參考的情況下修改這些元件。然而，在使用事件時可能會遇到一些陷阱：

- 由於事件可以從任何地方發送，它們可能會在專案中引入「義大利麵式」的依賴關係。
- 事件共享命名空間，這意味著可能會遇到名稱衝突。這些衝突無法靜態檢測，因此難以除錯。
- 如果使用多個相同 React Native 元件的實例，並希望從事件的角度區分它們，可能需要引入識別符並隨事件一起傳遞（可以使用原生視圖的 `reactTag` 作為識別符）。

我們在將原生元件嵌入 React Native 時常用的模式是讓原生元件的 RCTViewManager 成為視圖的委託，透過橋接器將事件發送回 JavaScript。這將相關的事件呼叫集中在一個地方。

### 從 React Native 呼叫原生函數（原生模組）

原生模組是在 JS 中可用的 Objective-C 類別。通常每個模組在 JS 橋接器中會建立一個實例。它們可以向 React Native 匯出任意函數和常數。這些已在[這篇文章](native-modules-ios#content)中有詳細說明。

原生模組是單例的事實限制了在嵌入情境下的機制。假設我們有一個嵌入原生視圖中的 React Native 元件，我們希望更新原生父視圖。使用原生模組機制，我們需要匯出一個不僅接受預期參數，還接受父原生視圖識別符的函數。該識別符將用於取得父視圖的參考以進行更新。也就是說，我們需要在模組中維護一個從識別符到原生視圖的映射。

儘管這個解決方案複雜，但它被用於 `RCTUIManager` 中，這是一個管理所有 React Native 視圖的內部 React Native 類別。

原生模組還可用於向 JS 公開現有的原生函式庫。[Geolocation 函式庫](https://github.com/michalchudziak/react-native-geolocation)就是這個概念的一個實際例子。

:::caution
所有原生模組共享相同的命名空間。在建立新模組時請注意名稱衝突。
:::

## 佈局計算流程

在整合原生與 React Native 時，我們還需要一種方法來統一兩種不同的佈局系統。本節涵蓋常見的佈局問題，並提供解決這些問題的機制簡要說明。

### 嵌入 React Native 中的原生元件佈局

這種情況在[這篇文章](native-components-ios#styles)中有詳細說明。總結來說，由於所有原生 React 視圖都是 `UIView` 的子類別，大多數樣式和尺寸屬性都會如預期般直接生效。

### 嵌入原生端中的 React Native 元件佈局

#### 固定尺寸的 React Native 內容

一般情境是當我們有一個固定尺寸的 React Native 應用程式，且該尺寸對原生端是已知的。特別是，全螢幕的 React Native 視圖就屬於這種情況。如果我們想要一個較小的根視圖，可以明確設定 RCTRootView 的框架。

舉例來說，若要讓一個 RN 應用程式高度為 200（邏輯）像素，寬度與宿主視圖同寬，我們可以這樣做：

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

當我們有一個固定尺寸的根視圖時，需要在 JS 端尊重其邊界。換句話說，我們需要確保 React Native 內容能被包含在固定尺寸的根視圖內。最簡單的方法是使用 Flexbox 佈局。如果使用絕對定位，且 React 元件在根視圖邊界外可見，將會與原生視圖重疊，導致某些功能表現異常。例如，'TouchableHighlight' 將不會在根視圖邊界外高亮觸摸區域。

動態更新根視圖尺寸（透過重新設定其 frame 屬性）是完全可行的。React Native 會自動處理內容的佈局調整。

#### 彈性尺寸的 React Native 內容

某些情況下，我們需要渲染初始尺寸未知的內容。假設尺寸將由 JS 動態決定，我們有兩種解決方案：

1. 可用 `ScrollView` 元件包裹 React Native 視圖，確保內容始終可存取且不會與原生視圖重疊。
2. React Native 允許在 JS 中計算 RN 應用的尺寸，並傳遞給宿主 `RCTRootView` 的所有者。所有者需負責重新佈局子視圖以維持 UI 一致性，這透過 `RCTRootView` 的彈性模式實現。

`RCTRootView` 支援 4 種尺寸彈性模式：

```objectivec title='RCTRootView.h'
typedef NS_ENUM(NSInteger, RCTRootViewSizeFlexibility) {
  RCTRootViewSizeFlexibilityNone = 0,
  RCTRootViewSizeFlexibilityWidth,
  RCTRootViewSizeFlexibilityHeight,
  RCTRootViewSizeFlexibilityWidthAndHeight,
};
```

預設值 `RCTRootViewSizeFlexibilityNone` 使根視圖尺寸固定（仍可透過 `setFrame:` 更新）。其他三種模式能追蹤 React Native 內容的尺寸變化。例如，設定為 `RCTRootViewSizeFlexibilityHeight` 時，React Native 會測量內容高度並將資訊回傳給 `RCTRootView` 的委派。委派可執行任意操作（包括調整根視圖 frame 以適應內容），且僅在內容尺寸變化時被呼叫。

:::caution
若同時在 JS 和原生端設定彈性尺寸會導致未定義行為。例如：請勿在頂層 React 元件使用 `flexbox` 彈性寬度的同時，對宿主 `RCTRootView` 啟用 `RCTRootViewSizeFlexibilityWidth`。
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

範例中的 `FlexibleSizeExampleView` 視圖包含一個根視圖。我們建立並初始化根視圖後設定委派，由委派處理尺寸更新。將根視圖的尺寸彈性設為 `RCTRootViewSizeFlexibilityHeight` 後，每當 React Native 內容高度變化時，`rootViewDidChangeIntrinsicSize:` 方法會被呼叫。最後設定根視圖的寬度與位置（注意高度設定此時無效，因其已改由 RN 主導）。

完整範例程式碼請參閱[此處](https://github.com/facebook/react-native/blob/main/packages/rn-tester/RNTester/NativeExampleViews/FlexibleSizeExampleView.mm)。

動態變更根視圖的尺寸彈性模式是可行的。變更後會觸發佈局重新計算，內容尺寸確定後將呼叫委派的 `rootViewDidChangeIntrinsicSize:` 方法。

:::note
React Native 的佈局計算在獨立線程執行，而原生 UI 更新在主線程進行。這可能導致原生與 React Native 間暫時性 UI 不一致。此為已知問題，團隊正致力於同步不同來源的 UI 更新。
:::

:::note
React Native 在根視圖成為其他視圖的子視圖之前，不會執行任何佈局計算。
如果您希望在知道尺寸前隱藏 React Native 視圖，可先將根視圖添加為子視圖並設為初始隱藏狀態（使用 `UIView` 的 `hidden` 屬性）。然後在委派方法中變更其可見性。
:::