---
id: native-modules-ios
title: iOS Native Modules
---

import NativeDeprecated from '@site/versioned_docs/version-0.77/the-new-architecture/\_markdown_native_deprecation.mdx'
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<NativeDeprecated />

歡迎來到 iOS 原生模組開發。請先閱讀 [原生模組簡介](native-modules-intro) 了解原生模組的基本概念。

## 建立日曆原生模組

本指南將帶您建立一個名為 `CalendarModule` 的原生模組，該模組可讓您從 JavaScript 存取 Apple 的日曆 API。最終您將能透過 JavaScript 呼叫 `CalendarModule.createCalendarEvent('晚餐派對', '我家');` 來觸發建立日曆事件的原生方法。

### 設定環境

首先，請在 Xcode 中開啟 React Native 應用程式內的 iOS 專案。您可以在以下路徑找到 iOS 專案：

<figure>
  <img src="/docs/assets/native-modules-ios-open-project.png" width="500" alt="Image of opening up an iOS project within a React Native app inside of xCode." />
  <figcaption>Image of where you can find your iOS project</figcaption>
</figure>

我們建議使用 Xcode 來編寫原生程式碼。Xcode 專為 iOS 開發設計，能幫助您快速解決程式碼語法等基本錯誤。

### 建立自訂原生模組檔案

第一步是建立主要的自訂原生模組標頭檔與實作檔。請建立新檔案 `RCTCalendarModule.h`

<figure>
  <img src="/docs/assets/native-modules-ios-add-class.png" width="500" alt="Image of creating a class called  RCTCalendarModule.h." />
  <figcaption>Image of creating a custom native module file within the same folder as AppDelegate</figcaption>
</figure>

並加入以下內容：

```objectivec
//  RCTCalendarModule.h
#import <React/RCTBridgeModule.h>
@interface RCTCalendarModule : NSObject <RCTBridgeModule>
@end

```

您可以為原生模組選擇任何合適的名稱。此處命名為 `RCTCalendarModule` 是因為我們要建立日曆原生模組。由於 Objective-C 不像 Java 或 C++ 具有語言層級的命名空間支援，慣例是在類別名稱前加上前綴字串（例如應用程式名稱或基礎架構名稱的縮寫）。本例中的 RCT 代表 React。

如下所示，CalendarModule 類別需實作 `RCTBridgeModule` 協定。原生模組就是實作此協定的 Objective-C 類別。

接著開始實作原生模組。請在相同目錄下使用 Xcode 的 Cocoa Touch Class 建立對應的實作檔 `RCTCalendarModule.m`，並包含以下內容：

```objectivec
// RCTCalendarModule.m
#import "RCTCalendarModule.h"

@implementation RCTCalendarModule

// To export a module named RCTCalendarModule
RCT_EXPORT_MODULE();

@end

```

### 模組名稱

目前您的 `RCTCalendarModule.m` 原生模組僅包含 `RCT_EXPORT_MODULE` 宏，該宏會將原生模組類別匯出並註冊到 React Native。`RCT_EXPORT_MODULE` 宏還可接受一個可選參數，用於指定模組在 JavaScript 程式碼中的存取名稱。

請注意此參數不是字串常值。下方範例傳遞的是 `RCT_EXPORT_MODULE(CalendarModuleFoo)` 而非 `RCT_EXPORT_MODULE("CalendarModuleFoo")`。

```objectivec
// To export a module named CalendarModuleFoo
RCT_EXPORT_MODULE(CalendarModuleFoo);
```

之後即可透過以下方式在 JS 中存取該原生模組：

```tsx
const {CalendarModuleFoo} = ReactNative.NativeModules;
```

若未指定名稱，JavaScript 模組名稱將與 Objective-C 類別名稱相同（自動移除 "RCT" 或 "RK" 前綴）。

請參考下方範例，不帶參數呼叫 `RCT_EXPORT_MODULE`。如此模組將以 `CalendarModule` 名稱暴露給 React Native（即移除 RCT 前綴後的 Objective-C 類別名稱）。

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

之後即可透過以下方式在 JS 中存取該原生模組：

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 將原生方法匯出至 JavaScript

React Native 不會自動將原生模組中的方法暴露給 JavaScript，除非明確使用 `RCT_EXPORT_METHOD` 宏指令。透過該宏定義的方法都是非同步的，因此返回值類型始終為 void。若要將 `RCT_EXPORT_METHOD` 方法的結果傳回 JavaScript，可以使用回調函數或發送事件（後續會說明）。現在我們為 `CalendarModule` 原生模組建立一個原生方法，使用 `RCT_EXPORT_METHOD` 宏並命名為 `createCalendarEvent()`，暫時讓它接收名稱和位置兩個字串參數。參數類型的選項稍後會詳細說明。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> 請注意，除非你的方法依賴 RCT 參數轉換（參見下方的參數類型說明），否則 TurboModules 將不再需要 `RCT_EXPORT_METHOD` 宏。React Native 最終會移除 `RCT_EXPORT_MACRO`，因此我們不建議使用 `RCTConvert`。你可以在方法體內自行處理參數轉換。

在完善 `createCalendarEvent()` 方法的功能之前，先加入一個控制台日誌，以便確認該方法已從 React Native 應用的 JavaScript 端被調用。使用 React 提供的 `RCTLog` API。首先在文件頂部導入該標頭檔，然後添加日誌調用。

```objectivec
#import <React/RCTLog.h>
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
 RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);
}
```

### 同步方法

你可以使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD` 來創建同步的原生方法。

```objectivec
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getName)
{
return [[UIDevice currentDevice] name];
}
```

該方法的返回類型必須是物件類型（id），並且可序列化為 JSON。這意味著該方法只能返回 nil 或 JSON 值（例如 NSNumber、NSString、NSArray、NSDictionary）。

目前我們不建議使用同步方法，因為同步調用可能會嚴重影響性能，並在原生模組中引入線程相關的問題。此外，請注意，如果選擇使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`，你的應用將無法使用 Google Chrome 調試器。這是因為同步方法要求 JS 虛擬機與應用共享記憶體。對於 Google Chrome 調試器，React Native 運行在 Chrome 的 JS 虛擬機中，並通過 WebSockets 與移動設備進行異步通信。

### 測試已構建的內容

至此，你已經在 iOS 中為原生模組搭建了基本框架。現在可以通過 JavaScript 訪問該原生模組並調用其導出的方法來進行測試。

在應用中找到一個適合調用原生模組 `createCalendarEvent()` 方法的地方。以下是一個示例組件 `NewModuleButton`，你可以將其添加到應用中。你可以在 `NewModuleButton` 的 `onPress()` 函數中調用原生模組。

```tsx
import React from 'react';
import {Button} from 'react-native';

const NewModuleButton = () => {
  const onPress = () => {
    console.log('We will invoke the native module here!');
  };

  return (
    <Button
      title="Click to invoke your native module!"
      color="#841584"
      onPress={onPress}
    />
  );
};

export default NewModuleButton;
```

要從 JavaScript 訪問你的原生模組，首先需要從 React Native 導入 `NativeModules`：

```tsx
import {NativeModules} from 'react-native';
```

然後可以通過 `NativeModules` 訪問 `CalendarModule` 原生模組。

```tsx
const {CalendarModule} = NativeModules;
```

現在你已經可以訪問 CalendarModule 原生模組，可以調用你的原生方法 `createCalendarEvent()`。以下將其添加到 `NewModuleButton` 的 `onPress()` 方法中：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後一步是重新構建 React Native 應用，以便獲取包含新原生模組的最新原生代碼。在 react native 應用所在的命令行中運行以下命令：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios
```

</TabItem>
</Tabs>

### 迭代開發時的構建

當您按照這些指南進行操作並對原生模組進行迭代時，您需要對應用程式進行原生重建，以便從 JavaScript 存取最新的變更。這是因為您編寫的程式碼位於應用程式的原生部分。雖然 React Native 的 Metro 打包工具可以監視 JavaScript 的變更並即時重建 JS 套件，但它不會對原生程式碼執行此操作。因此，如果您想測試最新的原生變更，需要使用上述命令進行重建。

### 回顧✨

您現在應該能夠在 JavaScript 中呼叫原生模組的 `createCalendarEvent()` 方法。由於您在函式中使用了 `RCTLog`，您可以透過[在應用程式中啟用除錯模式](https://reactnative.dev/docs/debugging#chrome-developer-tools)並查看 Chrome 的 JS 控制台或行動應用除錯工具 Flipper 來確認原生方法是否被呼叫。每次呼叫原生模組方法時，您應該會看到 `RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);` 訊息。

<figure>
  <img src="/docs/assets/native-modules-ios-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of iOS logs in Flipper</figcaption>
</figure>

至此，您已經建立了一個 iOS 原生模組，並從 React Native 應用程式的 JavaScript 中呼叫了其方法。您可以繼續閱讀，了解更多關於原生模組方法接受的參數類型，以及如何在原生模組中設定回調和 Promise 的內容。

## 超越日曆原生模組

### 更好的原生模組匯出

像上面那樣從 `NativeModules` 中提取原生模組的匯入方式有些笨拙。

為了讓您的原生模組的使用者不必每次存取原生模組時都這樣做，您可以為模組建立一個 JavaScript 包裝器。建立一個名為 NativeCalendarModule.js 的新 JavaScript 檔案，內容如下：

```tsx
/**
* This exposes the native CalendarModule module as a JS module. This has a
* function 'createCalendarEvent' which takes the following parameters:

* 1. String name: A string representing the name of the event
* 2. String location: A string representing the location of the event
*/
import {NativeModules} from 'react-native';
const {CalendarModule} = NativeModules;
export default CalendarModule;
```

這個 JavaScript 檔案也是您添加任何 JavaScript 端功能的好地方。例如，如果您使用像 TypeScript 這樣的類型系統，您可以在這裡為原生模組添加類型註解。雖然 React Native 目前還不支援原生到 JS 的類型安全，但有了這些類型註解，您的所有 JS 程式碼都將是類型安全的。這些註解也將使您更容易在未來切換到類型安全的原生模組。以下是為日曆模組添加類型安全的範例：

```tsx
/**
 * This exposes the native CalendarModule module as a JS module. This has a
 * function 'createCalendarEvent' which takes the following parameters:
 *
 * 1. String name: A string representing the name of the event
 * 2. String location: A string representing the location of the event
 */
import {NativeModules} from 'react-native';
const {CalendarModule} = NativeModules;
interface CalendarInterface {
  createCalendarEvent(name: string, location: string): void;
}
export default CalendarModule as CalendarInterface;
```

在您的其他 JavaScript 檔案中，您可以像這樣存取原生模組並呼叫其方法：

```tsx
import NativeCalendarModule from './NativeCalendarModule';
NativeCalendarModule.createCalendarEvent('foo', 'bar');
```

> 注意，這假設您匯入 `CalendarModule` 的位置與 `NativeCalendarModule.js` 位於同一層級。請根據需要更新相對匯入路徑。

### 參數類型

當在 JavaScript 中呼叫原生模組方法時，React Native 會將參數從 JS 物件轉換為其 Objective-C/Swift 物件對應物。因此，例如，如果您的 Objective-C 原生模組方法接受一個 NSNumber，在 JS 中您需要使用數字呼叫該方法。React Native 會為您處理轉換。以下是原生模組方法支援的參數類型及其對應的 JavaScript 等價物的列表。

| Objective-C                                   | JavaScript         |
| --------------------------------------------- | ------------------ |
| NSString                                      | string, ?string    |
| BOOL                                          | boolean            |
| double                                        | number             |
| NSNumber                                      | ?number            |
| NSArray                                       | Array, ?Array      |
| NSDictionary                                  | Object, ?Object    |
| RCTResponseSenderBlock                        | Function (success) |
| RCTResponseSenderBlock, RCTResponseErrorBlock | Function (failure) |
| RCTPromiseResolveBlock, RCTPromiseRejectBlock | Promise            |

> 以下類型目前受支援，但在 TurboModules 中將不再支援。請避免使用它們。
>
> - Function (failure) -> RCTResponseErrorBlock
> - Number -> NSInteger
> - Number -> CGFloat
> - Number -> float

對於 iOS，您還可以使用 `RCTConvert` 類支援的任何參數類型編寫原生模組方法（有關支援的內容，請參閱 [RCTConvert](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTConvert.h)）。RCTConvert 輔助函式都接受一個 JSON 值作為輸入，並將其映射到原生的 Objective-C 類型或類別。

### 匯出常數

原生模組可以透過覆寫原生方法 `constantsToExport()` 來匯出常數。以下是覆寫 `constantsToExport()` 的範例，它返回一個字典，其中包含一個預設事件名稱屬性，您可以在 JavaScript 中這樣存取：

```objectivec
- (NSDictionary *)constantsToExport
{
 return @{ @"DEFAULT_EVENT_NAME": @"New Event" };
}
```

接著可以透過在 JavaScript 中呼叫原生模組的 `getConstants()` 方法來存取這個常數，如下所示：

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

技術上來說，直接從 `NativeModule` 物件存取 `constantsToExport()` 匯出的常數是可行的。但這在 TurboModules 中將不再支援，因此我們鼓勵開發社群改用上述方法，以避免未來不必要的遷移工作。

> 請注意，常數僅在初始化時匯出，因此若在執行階段變更 `constantsToExport()` 的值，並不會影響 JavaScript 環境。

在 iOS 上，若您覆寫了 `constantsToExport()` 方法，則應同時實作 `+ requiresMainQueueSetup`，以告知 React Native 您的模組是否需要在主執行緒初始化（在任何 JavaScript 程式碼執行前）。否則您會看到警告，提示未來您的模組可能會在背景執行緒初始化，除非您透過 `+ requiresMainQueueSetup:` 明確選擇退出。若您的模組不需要存取 UIKit，則應對 `+ requiresMainQueueSetup` 回傳 NO。

### 回呼函式

原生模組還支援一種特殊的參數類型——回呼函式（callback）。回呼函式用於在非同步方法中將資料從 Objective-C 傳遞到 JavaScript，也可用於從原生端非同步執行 JavaScript 程式碼。

在 iOS 中，回呼函式是透過 `RCTResponseSenderBlock` 類型實現的。以下範例將回呼參數 `myCallBack` 加入 `createCalendarEventMethod()`：

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

接著您可以在原生函式中呼叫回呼，並透過陣列傳遞任何想傳給 JavaScript 的結果。請注意，`RCTResponseSenderBlock` 僅接受一個參數——傳遞給 JavaScript 回呼的參數陣列。以下範例將回傳先前呼叫中建立的事件 ID。

> 需特別強調的是，回呼函式不會在原⽣函式完成後立即觸發——請記住通訊是非同步進行的。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
 NSInteger eventId = ...
 callback(@[@(eventId)]);

 RCTLogInfo(@"Pretending to create an event %@ at %@", title, location);
}

```

然後可以透過以下方式在 JavaScript 中存取這個方法：

```tsx
const onSubmit = () => {
  CalendarModule.createCalendarEvent(
    'Party',
    '04-12-2020',
    eventId => {
      console.log(`Created a new event with id ${eventId}`);
    },
  );
};
```

原生模組應僅呼叫其回呼函式一次。不過，它可以儲存回呼並稍後再呼叫。此模式常用於封裝需要委派（delegate）的 iOS API——參見 [`RCTAlertManager`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/CoreModules/RCTAlertManager.mm) 的範例。若回呼從未被呼叫，會導致部分記憶體洩漏。

回呼函式的錯誤處理有兩種方式。第一種是遵循 Node 的慣例，將傳遞給回呼陣列的第一個參數視為錯誤物件。

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
  NSNumber *eventId = [NSNumber numberWithInt:123];
  callback(@[[NSNull null], eventId]);
}
```

在 JavaScript 中，您可以檢查第一個參數來判斷是否有錯誤傳入：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEventCallback(
    'testName',
    'testLocation',
    (error, eventId) => {
      if (error) {
        console.error(`Error found! ${error}`);
      }
      console.log(`event id ${eventId} returned`);
    },
  );
};
```

另一種選擇是使用兩個獨立的回呼函式：onFailure 和 onSuccess。

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title
                  location:(NSString *)location
                  errorCallback: (RCTResponseSenderBlock)errorCallback
                  successCallback: (RCTResponseSenderBlock)successCallback)
{
  @try {
    NSNumber *eventId = [NSNumber numberWithInt:123];
    successCallback(@[eventId]);
  }

  @catch ( NSException *e ) {
    errorCallback(@[e]);
  }
}
```

然後在 JavaScript 中，您可以為錯誤和成功回應分別添加回呼：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEventCallback(
    'testName',
    'testLocation',
    error => {
      console.error(`Error found! ${error}`);
    },
    eventId => {
      console.log(`event id ${eventId} returned`);
    },
  );
};
```

若要將類錯誤物件傳遞給 JavaScript，請使用 [`RCTUtils.h`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTUtils.h) 中的 `RCTMakeError`。目前這僅會將 Error 形狀的字典傳給 JavaScript，但 React Native 的目標是未來能自動生成真正的 JavaScript Error 物件。您也可以提供 `RCTResponseErrorBlock` 參數，用於錯誤回呼並接受 `NSError *` 物件。請注意，此參數類型在 TurboModules 中將不受支援。

### Promise

原生模組也可以實現 Promise，這能簡化您的 JavaScript 程式碼，特別是在使用 ES2016 的 `async/await` 語法時。當原生模組方法的最後一個參數是 `RCTPromiseResolveBlock` 和 `RCTPromiseRejectBlock` 時，其對應的 JS 方法將回傳一個 JS Promise 物件。

將上述程式碼重構為使用 Promise 而非回呼函式的範例如下：

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                 location:(NSString *)location
                 resolver:(RCTPromiseResolveBlock)resolve
                 rejecter:(RCTPromiseRejectBlock)reject)
{
 NSInteger eventId = createCalendarEvent();
 if (eventId) {
    resolve(@(eventId));
  } else {
    reject(@"event_failure", @"no event id returned", nil);
  }
}

```

該方法的 JavaScript 對應部分會返回一個 Promise。這意味著您可以在 async 函數中使用 `await` 關鍵字來調用它並等待其結果：

```tsx
const onSubmit = async () => {
  try {
    const eventId = await CalendarModule.createCalendarEvent(
      'Party',
      'my house',
    );
    console.log(`Created a new event with id ${eventId}`);
  } catch (e) {
    console.error(e);
  }
};
```

### 向 JavaScript 發送事件

原生模組可以直接向 JavaScript 發送事件信號，而無需被直接調用。例如，您可能希望向 JavaScript 發出信號，提醒原生 iOS 日曆應用中的某個日曆事件即將發生。首選方法是繼承 `RCTEventEmitter`，實現 `supportedEvents` 並調用 `self sendEventWithName`：

更新您的頭文件類以導入 `RCTEventEmitter` 並繼承 `RCTEventEmitter`：

```objectivec
//  CalendarModule.h

#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>

@interface CalendarModule : RCTEventEmitter <RCTBridgeModule>
@end

```

JavaScript 代碼可以通過在您的模組周圍創建一個新的 `NativeEventEmitter` 實例來訂閱這些事件。

如果在沒有監聽器的情況下發送事件，您將收到警告。為了避免這種情況並優化模組的工作負載（例如通過取消訂閱上游通知或暫停後台任務），您可以在 `RCTEventEmitter` 子類中覆寫 `startObserving` 和 `stopObserving` 方法。

```objectivec
@implementation CalendarModule
{
  bool hasListeners;
}

// Will be called when this module's first listener is added.
-(void)startObserving {
    hasListeners = YES;
    // Set up any upstream listeners or background tasks as necessary
}

// Will be called when this module's last listener is removed, or on dealloc.
-(void)stopObserving {
    hasListeners = NO;
    // Remove upstream listeners, stop unnecessary background tasks
}

- (void)calendarEventReminderReceived:(NSNotification *)notification
{
  NSString *eventName = notification.userInfo[@"name"];
  if (hasListeners) {// Only send events if anyone is listening
    [self sendEventWithName:@"EventReminder" body:@{@"name": eventName}];
  }
}

```

### 線程處理

除非原生模組提供自己的方法隊列，否則不應對其被調用的線程做出任何假設。目前，如果原生模組未提供方法隊列，React Native 會為其創建一個單獨的 GCD 隊列並在那裡調用其方法。請注意，這是一個實現細節，可能會發生變化。如果要為原生模組顯式提供方法隊列，請在原生模組中覆寫 `(dispatch_queue_t) methodQueue` 方法。例如，如果需要使用僅限主線程的 iOS API，則應通過以下方式指定：

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

同樣，如果某個操作可能需要很長時間才能完成，原生模組可以指定自己的隊列來運行操作。再次強調，目前 React Native 會為您的原生模組提供單獨的方法隊列，但這是一個不應依賴的實現細節。如果不提供自己的方法隊列，將來您的原生模組的長時間運行操作可能會阻塞在其他不相關原生模組上執行的異步調用。例如，這裡的 `RCTAsyncLocalStorage` 模組創建了自己的隊列，因此 React 隊列不會被潛在的慢速磁盤訪問阻塞。

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

指定的 `methodQueue` 將由模組中的所有方法共享。如果只有一個方法是長時間運行的（或由於某些原因需要在與其他方法不同的隊列上運行），則可以在方法內部使用 `dispatch_async` 將該特定方法的代碼放在另一個隊列上執行，而不影響其他方法：

```objectivec
RCT_EXPORT_METHOD(doSomethingExpensive:(NSString *)param callback:(RCTResponseSenderBlock)callback)
{
 dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
   // Call long-running code on background thread
   ...
   // You can invoke callback from any thread/queue
   callback(@[...]);
 });
}

```

> 在模組之間共享調度隊列
>
> `methodQueue` 方法將在模組初始化時調用一次，然後由 React Native 保留，因此除非您希望在模組內部使用該隊列，否則無需自行保留對隊列的引用。但是，如果希望在多個模組之間共享同一個隊列，則需要確保為每個模組保留並返回相同的隊列實例。

### 依賴注入

React Native 會自動創建並初始化任何已註冊的原生模組。但是，您可能希望創建並初始化自己的模組實例，例如注入依賴項。

您可以通過創建一個實現 `RCTBridgeDelegate` 協議的類，使用該委託作為參數初始化一個 `RCTBridge`，然後使用初始化的橋接器初始化一個 `RCTRootView` 來實現這一點。

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### 導出 Swift

Swift 不支持宏，因此要在 React Native 中將原生模組及其方法暴露給 JavaScript 需要更多的設置。但其工作原理相對相同。假設您有一個相同的 `CalendarModule`，但作為 Swift 類：

```swift
// CalendarModule.swift

@objc(CalendarModule)
class CalendarModule: NSObject {

 @objc(addEvent:location:date:)
 func addEvent(_ name: String, location: String, date: NSNumber) -> Void {
   // Date is ready to use!
 }

 @objc
 func constantsToExport() -> [String: Any]! {
   return ["someKey": "someValue"]
 }

}
```

> 務必使用 `@objc` 修飾符，以確保類別和函式能正確導出至 Objective-C 運行環境。

接著建立一個私有實作檔案，用於向 React Native 註冊必要資訊：

```objectivec
// CalendarModuleBridge.m
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(CalendarModule, NSObject)

RCT_EXTERN_METHOD(addEvent:(NSString *)name location:(NSString *)location date:(nonnull NSNumber *)date)

@end
```

對於初次接觸 Swift 與 Objective-C 的開發者，當你在 iOS 專案中[混用這兩種語言](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)時，還需要一個額外的橋接檔案（稱為橋接標頭檔）來將 Objective-C 檔案暴露給 Swift。若透過 Xcode 的 `檔案>新增檔案` 選單將 Swift 檔案加入專案，Xcode 會提示建立此標頭檔。你需在此標頭檔中導入 `RCTBridgeModule.h`。

```objectivec
// CalendarModule-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

你也可以使用 `RCT_EXTERN_REMAP_MODULE` 和 `_RCT_EXTERN_REMAP_METHOD` 來調整導出模組或方法在 JavaScript 中的名稱。更多資訊請參閱 [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridgeModule.h)。

> 開發第三方模組的重要注意事項：僅 Xcode 9 及以上版本支援包含 Swift 的靜態函式庫。若要在模組的 iOS 靜態函式庫中使用 Swift，主應用專案必須包含 Swift 程式碼及自身的橋接標頭檔。若應用專案不含任何 Swift 程式碼，可暫時以一個空的 .swift 檔案和空白橋接標頭檔作為解決方案。

### 保留方法名稱

#### invalidate()

iOS 平台的原生模組可通過實作 `invalidate()` 方法來遵循 [RCTInvalidating](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTInvalidating.h) 協議。當原生橋接失效時（例如開發模式重新載入），[系統可能調用](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456)此方法。請視需求使用此機制執行原生模組所需的清理工作。