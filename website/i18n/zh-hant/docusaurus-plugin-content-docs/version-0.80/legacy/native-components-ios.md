---
id: native-components-ios
title: iOS Native UI Components
---

import NativeDeprecated from '@site/versioned_docs/version-0.80/the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

現有大量原生UI元件可供最新應用程式使用——有些是平台內建元件，其他則來自第三方函式庫，甚至可能是您過往開發的專屬元件。React Native已封裝了部分核心平台元件（如`ScrollView`和`TextInput`），但並未涵蓋所有元件，特別是您曾為舊應用程式開發的自訂元件。幸運的是，我們能將這些現有元件封裝整合至React Native應用程式中。

與原生模組指南類似，本進階指南假設您具備iOS開發基礎知識。我們將透過實作React Native核心庫中現有`MapView`元件的子集，示範如何建構原生UI元件。

## iOS MapView範例

假設我們需要在應用程式中加入互動式地圖——與其從頭開發，不如直接使用[`MKMapView`](https://developer.apple.com/library/prerelease/mac/documentation/MapKit/Reference/MKMapView_Class/index.html)，只需讓它能透過JavaScript呼叫即可。

原生視圖由`RCTViewManager`的子類別建立與管理。這些子類別功能類似視圖控制器，但實質上是單例模式——橋接器只會為每個管理器建立一個實例。它們將原生視圖暴露給`RCTUIManager`，由後者委派設置與更新視圖屬性。`RCTViewManager`通常也作為視圖的委託對象，透過橋接器將事件回傳至JavaScript。

要暴露視圖您需要：

- 繼承`RCTViewManager`建立元件管理器
- 添加`RCT_EXPORT_MODULE()`標記宏
- 實作`-(UIView *)view`方法

```objectivec title='RNTMapManager.m'
#import <MapKit/MapKit.h>

#import <React/RCTViewManager.h>

@interface RNTMapManager : RCTViewManager
@end

@implementation RNTMapManager

RCT_EXPORT_MODULE(RNTMap)

- (UIView *)view
{
  return [[MKMapView alloc] init];
}

@end
```

:::note
請勿嘗試在透過`-view`方法暴露的`UIView`實例上設置`frame`或`backgroundColor`屬性。
React Native會覆寫自訂類別設置的值以匹配JavaScript元件的佈局屬性。
若需此類精細控制，建議將目標`UIView`實例包裹在另一個`UIView`中並返回包裹後的實例。
詳見[Issue 2948](https://github.com/facebook/react-native/issues/2948)。
:::

:::info
上述範例中，我們使用`RNT`作為類別前綴。前綴用於避免與其他框架命名衝突。
Apple框架使用雙字母前綴，React Native使用`RCT`前綴。建議您在自己的類別中使用非`RCT`的三字母前綴。
:::

接著您需要少量JavaScript程式碼使其成為可用的React元件：

```tsx {3} title="MapView.tsx"
import {requireNativeComponent} from 'react-native';

export default requireNativeComponent('RNTMap');
```

`requireNativeComponent`函式會自動將`RNTMap`解析為`RNTMapManager`，並導出原生視圖供JavaScript使用。

```tsx title="MyApp.tsx"
import MapView from './MapView.tsx';

export default function MyApp() {
  return <MapView style={{flex: 1}} />;
}
```

:::note
渲染時請記得拉伸視圖，否則您將只看到空白畫面。
:::

現在這已是功能完整的原生地圖視圖元件，支援雙指縮放等原生手勢操作。但目前尚無法透過JavaScript控制它。

## 屬性

增強元件可用性的第一步是橋接部分原生屬性。假設我們要禁用縮放功能並指定可見區域。禁用縮放是布林值屬性，只需添加這行程式碼：

```objectivec title='RNTMapManager.m'
RCT_EXPORT_VIEW_PROPERTY(zoomEnabled, BOOL)
```

請注意，我們明確指定類型為 `BOOL`——React Native 在橋接通信時會使用 `RCTConvert` 來轉換各種不同的數據類型，錯誤的值會顯示方便的「RedBox」錯誤，讓您立即知道問題所在。當情況如此簡單時，整個實現過程都由這個宏自動處理完成。

現在要實際禁用縮放功能，我們在 JavaScript 中設置屬性：

```tsx {4} title="MyApp.tsx"
import MapView from './MapView.tsx';

export default function MyApp() {
  return <MapView zoomEnabled={false} style={{flex: 1}} />;
}
```

為了記錄我們的 `MapView` 組件的屬性（及其接受的數值），我們將添加一個包裝組件，並使用 TypeScript 來描述接口：

```tsx {6-9} title="MapView.tsx"
import {requireNativeComponent} from 'react-native';

const RNTMap = requireNativeComponent('RNTMap');

export default function MapView(props: {
  /**
   * Whether the user may use pinch gestures to zoom in and out.
   */
  zoomEnabled?: boolean;
}) {
  return <RNTMap {...props} />;
}
```

現在我們有了一個文檔完善的包裝組件可供使用。

接下來，讓我們添加更複雜的 `region` 屬性。我們首先添加原生代碼：

```objectivec title='RNTMapManager.m'
RCT_CUSTOM_VIEW_PROPERTY(region, MKCoordinateRegion, MKMapView)
{
  [view setRegion:json ? [RCTConvert MKCoordinateRegion:json] : defaultView.region animated:YES];
}
```

好的，這比之前處理的 `BOOL` 情況要複雜一些。現在我們有一個需要轉換函數的 `MKCoordinateRegion` 類型，並且我們有自定義代碼，以便在從 JS 設置區域時視圖會動畫過渡。在我們提供的函數體內，`json` 指的是從 JS 傳遞過來的原始值。還有一個 `view` 變量讓我們可以訪問管理器的視圖實例，以及一個 `defaultView`，用於在 JS 發送空值時將屬性重置為默認值。

您可以為您的視圖編寫任何轉換函數——這裡是通過 `RCTConvert` 的擴展來實現 `MKCoordinateRegion` 的轉換。它使用了 ReactNative 已有的 `RCTConvert+CoreLocation` 擴展：

```objectivec title='RNTMapManager.m'
#import "RCTConvert+Mapkit.h"
```

```objectivec title='RCTConvert+Mapkit.h'
#import <MapKit/MapKit.h>
#import <React/RCTConvert.h>
#import <CoreLocation/CoreLocation.h>
#import <React/RCTConvert+CoreLocation.h>

@interface RCTConvert (Mapkit)

+ (MKCoordinateSpan)MKCoordinateSpan:(id)json;
+ (MKCoordinateRegion)MKCoordinateRegion:(id)json;

@end

@implementation RCTConvert(MapKit)

+ (MKCoordinateSpan)MKCoordinateSpan:(id)json
{
  json = [self NSDictionary:json];
  return (MKCoordinateSpan){
    [self CLLocationDegrees:json[@"latitudeDelta"]],
    [self CLLocationDegrees:json[@"longitudeDelta"]]
  };
}

+ (MKCoordinateRegion)MKCoordinateRegion:(id)json
{
  return (MKCoordinateRegion){
    [self CLLocationCoordinate2D:json],
    [self MKCoordinateSpan:json]
  };
}

@end
```

這些轉換函數設計用於安全處理 JS 可能拋出的任何 JSON，當遇到缺失鍵或其他開發者錯誤時，會顯示「RedBox」錯誤並返回標準的初始化值。

為了完成對 `region` 屬性的支持，我們可以用 TypeScript 來記錄它：

```tsx {6-25} title="MapView.tsx"
import {requireNativeComponent} from 'react-native';

const RNTMap = requireNativeComponent('RNTMap');

export default function MapView(props: {
  /**
   * The region to be displayed by the map.
   *
   * The region is defined by the center coordinates and the span of
   * coordinates to display.
   */
  region?: {
    /**
     * Coordinates for the center of the map.
     */
    latitude: number;
    longitude: number;

    /**
     * Distance between the minimum and the maximum latitude/longitude
     * to be displayed.
     */
    latitudeDelta: number;
    longitudeDelta: number;
  };
  /**
   * Whether the user may use pinch gestures to zoom in and out.
   */
  zoomEnabled?: boolean;
}) {
  return <RNTMap {...props} />;
}
```

現在我們可以為 `MapView` 提供 `region` 屬性：

```tsx {4-9,12} title="MyApp.tsx"
import MapView from './MapView.tsx';

export default function MyApp() {
  const region = {
    latitude: 37.48,
    longitude: -122.16,
    latitudeDelta: 0.1,
    longitudeDelta: 0.1,
  };
  return (
    <MapView
      region={region}
      zoomEnabled={false}
      style={{flex: 1}}
    />
  );
}
```

## 事件處理

現在我們有一個可以從 JS 自由控制的原生地圖組件，但我們如何處理來自用戶的事件，例如捏合縮放或平移以改變可見區域？

到目前為止，我們只從管理器的 `-(UIView *)view` 方法返回了一個 `MKMapView` 實例。我們無法向 `MKMapView` 添加新屬性，因此我們必須從 `MKMapView` 創建一個新的子類來作為我們的視圖使用。然後我們可以在這個子類上添加一個 `onRegionChange` 回調：

```objectivec title='RNTMapView.h'
#import <MapKit/MapKit.h>

#import <React/RCTComponent.h>

@interface RNTMapView: MKMapView

@property (nonatomic, copy) RCTBubblingEventBlock onRegionChange;

@end
```

```objectivec title='RNTMapView.m'
#import "RNTMapView.h"

@implementation RNTMapView

@end
```

請注意，所有的 `RCTBubblingEventBlock` 都必須以 `on` 為前綴。接下來，在 `RNTMapManager` 上聲明一個事件處理屬性，使其成為所有暴露視圖的代理，並通過從原生視圖調用事件處理塊來將事件轉發到 JS。

```objectivec {9,17,31-48} title='RNTMapManager.m'
#import <MapKit/MapKit.h>
#import <React/RCTViewManager.h>

#import "RNTMapView.h"
#import "RCTConvert+Mapkit.h"

@interface RNTMapManager : RCTViewManager <MKMapViewDelegate>
@end

@implementation RNTMapManager

RCT_EXPORT_MODULE()

RCT_EXPORT_VIEW_PROPERTY(zoomEnabled, BOOL)
RCT_EXPORT_VIEW_PROPERTY(onRegionChange, RCTBubblingEventBlock)

RCT_CUSTOM_VIEW_PROPERTY(region, MKCoordinateRegion, MKMapView)
{
  [view setRegion:json ? [RCTConvert MKCoordinateRegion:json] : defaultView.region animated:YES];
}

- (UIView *)view
{
  RNTMapView *map = [RNTMapView new];
  map.delegate = self;
  return map;
}

#pragma mark MKMapViewDelegate

- (void)mapView:(RNTMapView *)mapView regionDidChangeAnimated:(BOOL)animated
{
  if (!mapView.onRegionChange) {
    return;
  }

  MKCoordinateRegion region = mapView.region;
  mapView.onRegionChange(@{
    @"region": @{
      @"latitude": @(region.center.latitude),
      @"longitude": @(region.center.longitude),
      @"latitudeDelta": @(region.span.latitudeDelta),
      @"longitudeDelta": @(region.span.longitudeDelta),
    }
  });
}
@end
```

在代理方法 `-mapView:regionDidChangeAnimated:` 中，事件處理塊會在相應的視圖上被調用，並傳遞區域數據。調用 `onRegionChange` 事件處理塊會導致在 JavaScript 中調用相同的回調屬性。這個回調會以原始事件的形式被調用，我們通常在包裝組件中處理它以簡化 API：

```tsx {3-10,14-17,19} title="MapView.tsx"
// ...

type RegionChangeEvent = {
  nativeEvent: {
    latitude: number;
    longitude: number;
    latitudeDelta: number;
    longitudeDelta: number;
  };
};

export default function MapView(props: {
  // ...
  /**
   * Callback that is called continuously when the user is dragging the map.
   */
  onRegionChange: (event: RegionChangeEvent) => unknown;
}) {
  return <RNTMap {...props} onRegionChange={onRegionChange} />;
}
```

```tsx {6-9,14} title="MyApp.tsx"
import MapView from './MapView.tsx';

export default function MyApp() {
  // ...

  const onRegionChange = useCallback(event => {
    const {region} = event.nativeEvent;
    // Do something with `region.latitude`, etc.
  });

  return (
    <MapView
      // ...
      onRegionChange={onRegionChange}
    />
  );
}
```

## 處理多個原生視圖

一個 React Native 視圖在視圖樹中可以擁有多個子視圖，例如：

```tsx
<View>
  <MyNativeView />
  <MyNativeView />
  <Button />
</View>
```

在這個例子中，類 `MyNativeView` 是 `NativeComponent` 的包裝器，並公開了一些方法，這些方法將在 iOS 平台上被調用。`MyNativeView` 定義在 `MyNativeView.ios.js` 中，並包含 `NativeComponent` 的代理方法。

當使用者與元件互動時，例如點擊按鈕，`MyNativeView` 的 `backgroundColor` 會隨之改變。此時 `UIManager` 無法判斷應處理哪個 `MyNativeView` 或變更哪個元件的 `backgroundColor`。以下提供解決方案：

```tsx
<View>
  <MyNativeView ref={this.myNativeReference} />
  <MyNativeView ref={this.myNativeReference2} />
  <Button
    onPress={() => {
      this.myNativeReference.callNativeMethod();
    }}
  />
</View>
```

現在上述元件已持有特定 `MyNativeView` 的參照，這讓我們能操作具體的 `MyNativeView` 實例。按鈕因此可控制哪個 `MyNativeView` 應變更其 `backgroundColor`。此範例假設 `callNativeMethod` 會改變背景色：

```tsx title="MyNativeView.ios.tsx"
class MyNativeView extends React.Component {
  callNativeMethod = () => {
    UIManager.dispatchViewManagerCommand(
      ReactNative.findNodeHandle(this),
      UIManager.getViewManagerConfig('RNCMyNativeView').Commands
        .callNativeMethod,
      [],
    );
  };

  render() {
    return <NativeComponent ref={NATIVE_COMPONENT_REF} />;
  }
}
```

`callNativeMethod` 是我們透過 `MyNativeView` 暴露的自訂 iOS 方法，例如用於變更背景色。該方法需使用 `UIManager.dispatchViewManagerCommand`，此指令需要三個參數：

- `(nonnull NSNumber \*)reactTag`  -  React 視圖的識別碼
- `commandID:(NSInteger)commandID`  -  需呼叫的原生方法 ID
- `commandArgs:(NSArray<id> \*)commandArgs`  -  可從 JS 傳遞至原生的方法參數

```objectivec title='RNCMyNativeViewManager.m'
#import <React/RCTViewManager.h>
#import <React/RCTUIManager.h>
#import <React/RCTLog.h>

RCT_EXPORT_METHOD(callNativeMethod:(nonnull NSNumber*) reactTag) {
    [self.bridge.uiManager addUIBlock:^(RCTUIManager *uiManager, NSDictionary<NSNumber *,UIView *> *viewRegistry) {
        NativeView *view = viewRegistry[reactTag];
        if (!view || ![view isKindOfClass:[NativeView class]]) {
            RCTLogError(@"Cannot find NativeView with tag #%@", reactTag);
            return;
        }
        [view callNativeMethod];
    }];

}
```

此處 `callNativeMethod` 定義於 `RNCMyNativeViewManager.m` 檔案，僅接收 `(nonnull NSNumber*) reactTag` 單一參數。此匯出函式會透過包含 `viewRegistry` 參數的 `addUIBlock` 找到對應視圖，並根據 `reactTag` 回傳元件實例，從而確保方法能正確作用於目標元件。

## 樣式處理

由於所有原生 React 視圖皆繼承自 `UIView`，多數樣式屬性會如預期生效。但某些元件（如固定尺寸的 `UIDatePicker`）需要預設樣式——這對佈局演算法至關重要，同時也需保留覆寫預設樣式的能力。`DatePickerIOS` 的實作方式是將原生元件包裹於具彈性樣式的額外視圖中，並對內部原生元件套用由原生常數生成的固定樣式：

```tsx title="DatePickerIOS.ios.tsx"
import {UIManager} from 'react-native';
const RCTDatePickerIOSConsts = UIManager.RCTDatePicker.Constants;
...
  render: function() {
    return (
      <View style={this.props.style}>
        <RCTDatePickerIOS
          ref={DATEPICKER}
          style={styles.rkDatePickerIOS}
          ...
        />
      </View>
    );
  }
});

const styles = StyleSheet.create({
  rkDatePickerIOS: {
    height: RCTDatePickerIOSConsts.ComponentHeight,
    width: RCTDatePickerIOSConsts.ComponentWidth,
  },
});
```

`RCTDatePickerIOSConsts` 常數是從原生端匯出的，其實作方式是擷取原生元件的實際框架尺寸：

```objectivec title='RCTDatePickerManager.m'
- (NSDictionary *)constantsToExport
{
  UIDatePicker *dp = [[UIDatePicker alloc] init];
  [dp layoutIfNeeded];

  return @{
    @"ComponentHeight": @(CGRectGetHeight(dp.frame)),
    @"ComponentWidth": @(CGRectGetWidth(dp.frame)),
    @"DatePickerModes": @{
      @"time": @(UIDatePickerModeTime),
      @"date": @(UIDatePickerModeDate),
      @"datetime": @(UIDatePickerModeDateAndTime),
    }
  };
}
```

本指南涵蓋了自訂原生元件橋接的多個面向，但仍有更多進階考量（例如插入與佈局子視圖的客製鉤子）。若需深入探索，請參考[已實作元件的原始碼](https://github.com/facebook/react-native/tree/main/packages/react-native/React/Views)。