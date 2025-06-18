---
id: communication-ios
title: Communication between native and React Native
---

在[整合既有應用程式指南](integration-with-existing-apps)與[原生UI元件指南](native-components-ios)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通管道。其他指南已提及部分實現方法，本文將統整現有技術方案。

## 簡介

React Native的設計靈感源自React，因此資訊流的基本概念相似。React中的資料流是單向的，我們維護一個元件層級結構，每個元件僅依賴其父元件與自身內部狀態。這透過屬性(properties)實現：資料以自上而下的方式從父元件傳遞給子元件。若祖先元件需依賴後代元件的狀態，則應向下傳遞回調函數供後代元件更新祖先狀態。

此概念同樣適用於React Native。只要在框架內構建應用程式，我們就能透過屬性和回調驅動應用。但當混合使用React Native與原生元件時，需要特定的跨語言機制來實現彼此間的資訊傳遞。

## 屬性

屬性是最直接的跨元件通訊方式。因此我們需要實現雙向屬性傳遞：從原生到React Native，以及從React Native到原生。

### 從原生端傳遞屬性至React Native

要在原生元件中嵌入React Native視圖，我們使用`RCTRootView`。`RCTRootView`是承載React Native應用的`UIView`，同時提供原生端與宿主應用間的介面。

`RCTRootView`的初始化方法允許傳遞任意屬性至React Native應用。`initialProperties`參數必須是`NSDictionary`實例，該字典會在內部轉換為頂層JS元件可引用的JSON物件。

```objectivec
NSArray *imageList = @[@"http://foo.com/bar1.png",
                       @"http://foo.com/bar2.png"];

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

`RCTRootView`還提供可讀寫屬性`appProperties`。設置後，React Native應用會以新屬性重新渲染。僅當新屬性與先前不同時才會執行更新。

```objectivec
NSArray *imageList = @[@"http://foo.com/bar3.png",
                       @"http://foo.com/bar4.png"];

rootView.appProperties = @{@"images" : imageList};
```

隨時更新屬性皆可，但更新操作必須在主執行緒進行。讀取操作則可在任何執行緒執行。

:::note
目前存在已知問題：若在橋接器啟動期間設置appProperties，變更可能會遺失。詳見https://github.com/facebook/react-native/issues/20115。
:::

無法僅更新部分屬性，建議自行建立封裝層實現此功能。

### 從React Native傳遞屬性至原生端

[此文章](native-components-ios#properties)詳細說明原生元件屬性的暴露方法。簡言之，在自訂原生元件中使用`RCT_CUSTOM_VIEW_PROPERTY`宏導出屬性，即可在React Native中像普通元件般使用這些屬性。

### 屬性的限制

跨語言屬性的主要缺點是不支援回調函數，這使得我們無法實現自下而上的資料綁定。例如當需要透過JS操作移除原生父視圖中的RN子視圖時，僅憑屬性無法實現，因為資訊需自下而上傳遞。

雖然存在跨語言回調的變通方案([參見此處](native-modules-ios#callbacks))，但這些回調並非萬能解方。主要問題在於它們不適合作為屬性傳遞，該機制實際是用於從JS觸發原生操作後，在JS端處理操作結果。

## 其他跨語言互動方式（事件與原生模組）

如前一章所述，使用屬性存在一些限制。有時屬性不足以驅動應用程式的邏輯，我們需要更靈活的解決方案。本章涵蓋 React Native 中可用的其他通訊技術，這些技術既可用於內部通訊（RN 中 JS 與原生層之間的溝通），也可用於外部通訊（RN 與應用程式中「純原生」部分之間的溝通）。

React Native 允許您執行跨語言函式呼叫。您可以從 JS 執行自訂原生程式碼，反之亦然。遺憾的是，根據我們所處的端別，實現相同目標的方式有所不同。對於原生端，我們使用事件機制來排程在 JS 中執行處理函式；而對於 React Native 端，我們直接呼叫原生模組匯出的方法。

### 從原生端呼叫 React Native 函式（事件）

事件在[這篇文章](native-components-ios#events)中有詳細說明。請注意，使用事件無法保證執行時間，因為事件是在獨立執行緒上處理的。

事件功能強大，因為它們允許我們無需持有元件參照即可修改 React Native 元件。但使用時需注意以下陷阱：

- 由於事件可從任何地方發送，可能導致專案中出現義大利麵條式依賴關係。
- 事件共享命名空間，可能發生名稱衝突。這些衝突無法靜態檢測，難以除錯。
- 若使用多個相同 React Native 元件實例，並需從事件角度區分它們，可能需要引入識別碼並隨事件傳遞（可使用原生視圖的 `reactTag` 作為識別碼）。

將原生元件嵌入 React Native 時的常見模式，是讓原生元件的 RCTViewManager 成為視圖的委派，透過橋接將事件發送回 JavaScript。這能將相關事件呼叫集中管理。

### 從 React Native 呼叫原生函式（原生模組）

原生模組是可在 JS 中使用的 Objective-C 類別。通常每個模組在 JS 橋接中會建立一個實例。它們可以向 React Native 匯出任意函式和常數，詳見[這篇文章](native-modules-ios#content)。

原生模組是單例的特性限制了其在嵌入情境中的應用。假設我們有一個嵌入原生視圖的 React Native 元件，需要更新原生父視圖。使用原生模組機制時，我們需匯出一個不僅接收預期參數，還需包含父原生視圖識別碼的函式。該識別碼用於取得父視圖參照以進行更新，這意味著我們需在模組中維護從識別碼到原生視圖的映射。

雖然此解決方案複雜，但 React Native 內部管理所有視圖的 `RCTUIManager` 類別正是採用這種方式。

原生模組也可用於向 JS 公開現有原生函式庫，[Geolocation 函式庫](https://github.com/michalchudziak/react-native-geolocation)就是此概念的實例。

:::caution
所有原生模組共享相同命名空間，建立新模組時需注意名稱衝突問題。
:::

## 版面計算流程

整合原生與 React Native 時，我們還需要統一兩種不同版面系統的方法。本節涵蓋常見版面問題，並簡述解決機制。

### Layout of a native component embedded in React Native

此情境在[這篇文章](native-components-ios#styles)中有說明。簡言之，由於所有原生 React 視圖都是 `UIView` 的子類別，多數樣式和尺寸屬性都能如預期運作。

### 嵌入原生端的 React Native 元件版面

#### 固定尺寸的 React Native 內容

典型情境是當 React Native 應用程式具有固定尺寸且原生端已知時。特別是全螢幕 React Native 視圖就屬於此類。若要建立較小的根視圖，可明確設定 RCTRootView 的 frame 屬性。

舉例來說，若要讓一個 React Native 應用程式高度為 200（邏輯）像素，並與宿主視圖同寬，我們可以這樣做：

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

當我們有一個固定大小的根視圖時，需要在 JavaScript 端尊重其邊界。換句話說，我們需要確保 React Native 內容能被限制在固定大小的根視圖內。最簡單的方法是使用 Flexbox 佈局。如果使用絕對定位，且 React 元件在根視圖邊界外可見，將會與原生視圖重疊，導致某些功能出現非預期行為。例如，'TouchableHighlight' 將不會在根視圖邊界外高亮觸摸區域。

動態更新根視圖的大小是完全可以的，只需重新設定其 frame 屬性。React Native 會自動處理內容的佈局。

#### 彈性大小的 React Native 內容

某些情況下，我們希望渲染初始大小未知的內容。假設大小將由 JavaScript 動態決定，我們有兩種解決方案：

1. 可以將 React Native 視圖包裹在 `ScrollView` 元件中。這能確保內容始終可見且不會與原生視圖重疊。
2. React Native 允許在 JavaScript 中計算應用程式大小，並將結果提供給宿主 `RCTRootView` 的所有者。所有者需負責重新佈局子視圖以保持 UI 一致性。這通過 `RCTRootView` 的彈性模式實現。

`RCTRootView` 支援四種不同的尺寸彈性模式：

```objectivec title='RCTRootView.h'
typedef NS_ENUM(NSInteger, RCTRootViewSizeFlexibility) {
  RCTRootViewSizeFlexibilityNone = 0,
  RCTRootViewSizeFlexibilityWidth,
  RCTRootViewSizeFlexibilityHeight,
  RCTRootViewSizeFlexibilityWidthAndHeight,
};
```

預設值 `RCTRootViewSizeFlexibilityNone` 會固定根視圖大小（但仍可透過 `setFrame:` 更新）。其他三種模式允許追蹤 React Native 內容的尺寸變化。例如，將模式設為 `RCTRootViewSizeFlexibilityHeight` 會讓 React Native 測量內容高度並將資訊回傳給 `RCTRootView` 的委派。委派方法中可執行任何操作（包括調整根視圖 frame 以適應內容），且僅在內容尺寸變化時觸發。

:::caution
若在 JavaScript 和原生端同時啟用某個維度的彈性佈局，會導致未定義行為。例如：不應在頂層 React 元件使用 `flexbox` 彈性寬度的同時，對宿主 `RCTRootView` 啟用 `RCTRootViewSizeFlexibilityWidth` 模式。
:::

以下是一個範例：

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

此範例中，`FlexibleSizeExampleView` 視圖持有根視圖。我們建立並初始化根視圖後設定委派，委派將處理尺寸更新。接著將根視圖的尺寸彈性設為 `RCTRootViewSizeFlexibilityHeight`，這意味著每當 React Native 內容高度變化時，`rootViewDidChangeIntrinsicSize:` 方法會被呼叫。最後設定根視圖的寬度與位置（注意高度設定在此無效，因為高度已交由 React Native 控制）。

完整範例程式碼可參閱[此處](https://github.com/facebook/react-native/blob/0.71-stable/packages/rn-tester/RNTester/NativeExampleViews/FlexibleSizeExampleView.m)。

動態變更根視圖的彈性模式是可行的。變更後會觸發佈局重新計算，一旦內容尺寸確定，委派的 `rootViewDidChangeIntrinsicSize:` 方法將被呼叫。

:::note
React Native 的佈局計算在獨立線程執行，而原生 UI 更新則在主線程進行。
這可能導致原生與 React Native 之間暫時性的 UI 不一致。這是已知問題，我們的團隊正在努力同步來自不同來源的 UI 更新。
:::

:::note
React Native 在根視圖成為其他視圖的子視圖之前，不會執行任何佈局計算。
如果你想在知道尺寸前隱藏 React Native 視圖，可先將根視圖添加為子視圖並設為初始隱藏（使用 `UIView` 的 `hidden` 屬性），然後在委派方法中更改其可見性。
:::