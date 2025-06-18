---
id: native-modules-ios
title: iOS Native Modules
---

import NativeDeprecated from '../the-new-architecture/\_markdown_native_deprecation.mdx'
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<NativeDeprecated />

歡迎使用 iOS 原生模組。請先閱讀 [原生模組簡介](native-modules-intro) 了解什麼是原生模組。

## 建立日曆原生模組

在接下來的指南中，您將建立一個名為 `CalendarModule` 的原生模組，該模組將允許您從 JavaScript 存取 Apple 的日曆 API。最終您將能夠從 JavaScript 調用 `CalendarModule.createCalendarEvent('晚餐派對', '我家');`，從而觸發建立日曆事件的原生方法。

### 設定

首先，在 Xcode 中開啟您的 React Native 應用程式內的 iOS 專案。您可以在以下路徑找到 React Native 應用程式中的 iOS 專案：

<figure>
  <img src="/docs/assets/native-modules-ios-open-project.png" width="500" alt="Image of opening up an iOS project within a React Native app inside of xCode." />
  <figcaption>Image of where you can find your iOS project</figcaption>
</figure>

我們建議使用 Xcode 來編寫您的原生程式碼。Xcode 專為 iOS 開發而設計，使用它將幫助您快速解決較小的錯誤，例如程式碼語法。

### 建立自訂原生模組檔案

第一步是建立我們的主要自訂原生模組標頭檔和實作檔案。建立一個名為 `RCTCalendarModule.h` 的新檔案

<figure>
  <img src="/docs/assets/native-modules-ios-add-class.png" width="500" alt="Image of creating a class called  RCTCalendarModule.h." />
  <figcaption>Image of creating a custom native module file within the same folder as AppDelegate</figcaption>
</figure>

並在其中加入以下內容：

```objectivec
//  RCTCalendarModule.h
#import <React/RCTBridgeModule.h>
@interface RCTCalendarModule : NSObject <RCTBridgeModule>
@end

```

您可以使用任何適合您正在建立的原生模組的名稱。由於您正在建立一個日曆原生模組，因此將類別命名為 `RCTCalendarModule`。由於 ObjC 不像 Java 或 C++ 那樣有語言層級的命名空間支援，慣例是在類別名稱前加上一個子字串。這可以是您的應用程式名稱或基礎架構名稱的縮寫。在此範例中，RCT 指的是 React。

如下所示，CalendarModule 類別實現了 `RCTBridgeModule` 協定。原生模組是一個實現了 `RCTBridgeModule` 協定的 Objective-C 類別。

接下來，讓我們開始實作原生模組。在相同的資料夾中使用 Xcode 的 cocoa touch class 建立對應的實作檔案 `RCTCalendarModule.m`，並包含以下內容：

```objectivec
// RCTCalendarModule.m
#import "RCTCalendarModule.h"

@implementation RCTCalendarModule

// To export a module named RCTCalendarModule
RCT_EXPORT_MODULE();

@end

```

### 模組名稱

目前，您的 `RCTCalendarModule.m` 原生模組僅包含一個 `RCT_EXPORT_MODULE` 宏，該宏將原生模組類別匯出並註冊到 React Native。`RCT_EXPORT_MODULE` 宏還可以接受一個可選參數，該參數指定模組在 JavaScript 程式碼中可存取的名稱。

此參數不是字串字面量。在下面的範例中，傳遞的是 `RCT_EXPORT_MODULE(CalendarModuleFoo)`，而不是 `RCT_EXPORT_MODULE("CalendarModuleFoo")`。

```objectivec
// To export a module named CalendarModuleFoo
RCT_EXPORT_MODULE(CalendarModuleFoo);
```

然後可以在 JS 中這樣存取原生模組：

```tsx
const {CalendarModuleFoo} = ReactNative.NativeModules;
```

如果您不指定名稱，JavaScript 模組名稱將與 Objective-C 類別名稱匹配，並移除任何 "RCT" 或 "RK" 前綴。

讓我們按照下面的範例，不帶任何參數調用 `RCT_EXPORT_MODULE`。因此，該模組將使用名稱 `CalendarModule` 暴露給 React Native，因為這是 Objective-C 類別名稱，並移除了 RCT。

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

然後可以在 JS 中這樣存取原生模組：

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 將原生方法匯出到 JavaScript

React Native 不會自動將原生模組中的方法暴露給 JavaScript，除非明確使用 `RCT_EXPORT_METHOD` 宏指令宣告。透過 `RCT_EXPORT_METHOD` 編寫的方法都是非同步的，因此回傳類型永遠是 void。若要將 `RCT_EXPORT_METHOD` 方法的結果傳遞給 JavaScript，可以使用回調函數或發送事件（後續會說明）。現在我們使用 `RCT_EXPORT_METHOD` 宏指令為 `CalendarModule` 原生模組建立一個原生方法，命名為 `createCalendarEvent()`，暫時讓它接收字串類型的名稱和位置參數。參數類型的選項稍後會詳細說明。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> 請注意，除非你的方法依賴 RCT 參數轉換（參見下方的參數類型說明），否則 TurboModules 將不再需要 `RCT_EXPORT_METHOD` 宏指令。React Native 最終會移除 `RCT_EXPORT_MACRO`，因此我們不建議使用 `RCTConvert`，而應在方法體內自行處理參數轉換。

在完善 `createCalendarEvent()` 方法的功能之前，先在方法內添加一個控制台日誌，以便確認它已從 React Native 應用的 JavaScript 端被調用。使用 React 提供的 `RCTLog` API，記得在檔案頂部導入該標頭檔，然後添加日誌調用。

```objectivec
#import <React/RCTLog.h>
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
 RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);
}
```

### 同步方法

你可以使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD` 來建立同步的原生方法。

```objectivec
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getName)
{
return [[UIDevice currentDevice] name];
}
```

此方法的回傳類型必須是物件類型（id）且可序列化為 JSON，這意味著它只能回傳 nil 或 JSON 兼容的值（例如 NSNumber、NSString、NSArray、NSDictionary）。

目前我們不建議使用同步方法，因為同步調用可能導致嚴重的效能損耗並引發線程相關的錯誤。此外，請注意若使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`，你的應用將無法再使用 Google Chrome 調試器。這是因為同步方法要求 JS 虛擬機與應用共享記憶體，而 Google Chrome 調試器中，React Native 運行於 Chrome 的 JS 虛擬機內，並通過 WebSockets 與移動設備異步通信。

### 測試已建構的功能

至此你已完成 iOS 原生模組的基礎架構，現在可以透過 JavaScript 訪問該模組並調用其導出的方法來測試。

在應用中找到適合調用原生模組 `createCalendarEvent()` 方法的位置。以下是一個可添加到應用中的元件範例 `NewModuleButton`，你可以在其 `onPress()` 函數中調用原生模組。

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

要從 JavaScript 訪問原生模組，需先從 React Native 導入 `NativeModules`：

```tsx
import {NativeModules} from 'react-native';
```

接著即可透過 `NativeModules` 取得 `CalendarModule` 原生模組。

```tsx
const {CalendarModule} = NativeModules;
```

現在有了可用的 CalendarModule 原生模組，便能調用原生方法 `createCalendarEvent()`。以下將其添加到 `NewModuleButton` 的 `onPress()` 方法中：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後一步是重新建構 React Native 應用，以確保最新的原生代碼（包含你的新原生模組）生效。在 React Native 應用所在的命令行中執行以下指令：

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

### 迭代開發時的建構流程

當你按照這些指南進行開發並迭代原生模組時，需要重新原生編譯應用程式才能從 JavaScript 獲取最新的變更。這是因為你所編寫的程式碼位於應用程式的原生部分。雖然 React Native 的 Metro 打包工具可以監控 JavaScript 的變更並即時重新打包 JS 套件，但不會對原生程式碼這麼做。因此，如果你想測試最新的原生變更，就需要使用上述指令重新編譯。

### 回顧✨

你現在應該能夠在 JavaScript 中呼叫原生模組的 `createCalendarEvent()` 方法。由於你在函式中使用了 `RCTLog`，可以透過[在應用程式中啟用除錯模式](https://reactnative.dev/docs/debugging#chrome-developer-tools)並查看 Chrome 的 JS 控制台或行動應用除錯工具 Flipper 來確認原生方法是否被呼叫。每次呼叫原生模組方法時，你都應該會看到 `RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);` 的訊息。

<figure>
  <img src="/docs/assets/native-modules-ios-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of iOS logs in Flipper</figcaption>
</figure>

至此，你已經建立了一個 iOS 原生模組，並從 React Native 應用程式的 JavaScript 中呼叫了它的方法。你可以繼續閱讀，了解更多關於原生模組方法接受的參數類型，以及如何在原生模組中設定回調和 Promise 的內容。

## 超越日曆原生模組

### 更好的原生模組導出方式

像上面那樣從 `NativeModules` 中提取原生模組的方式有點笨拙。

為了讓使用你的原生模組的人不必每次都這樣做，你可以為模組建立一個 JavaScript 封裝。建立一個名為 NativeCalendarModule.js 的新 JavaScript 檔案，內容如下：

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

這個 JavaScript 檔案也成為你添加任何 JavaScript 端功能的好地方。例如，如果你使用像 TypeScript 這樣的類型系統，可以在這裡為你的原生模組添加類型註解。雖然 React Native 目前還不支援原生到 JS 的類型安全，但有了這些類型註解，所有的 JS 程式碼都將是類型安全的。這些註解也會讓你在未來更容易切換到類型安全的原生模組。以下是為日曆模組添加類型安全的範例：

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

在其他 JavaScript 檔案中，你可以這樣存取原生模組並呼叫其方法：

```tsx
import NativeCalendarModule from './NativeCalendarModule';
NativeCalendarModule.createCalendarEvent('foo', 'bar');
```

> 注意，這假設你導入 `CalendarModule` 的位置與 `NativeCalendarModule.js` 位於同一層級。請根據需要更新相對導入路徑。

### 參數類型

當在 JavaScript 中呼叫原生模組方法時，React Native 會將參數從 JS 物件轉換為對應的 Objective-C/Swift 物件。因此，例如，如果你的 Objective-C 原生模組方法接受一個 NSNumber，在 JS 中你需要用數字呼叫該方法。React Native 會為你處理轉換。以下是原生模組方法支援的參數類型及其對應的 JavaScript 等效類型的列表。

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

對於 iOS，你也可以使用 `RCTConvert` 類支援的任何參數類型來編寫原生模組方法（詳見 [RCTConvert](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTConvert.h) 以了解支援的內容）。RCTConvert 輔助函數都接受一個 JSON 值作為輸入，並將其映射到原生的 Objective-C 類型或類別。

### 導出常數

原生模組可以透過覆寫原生方法 `constantsToExport()` 來導出常數。以下是覆寫 `constantsToExport()` 的範例，它返回一個字典，其中包含你可以在 JavaScript 中存取的預設事件名稱屬性：

```objectivec
- (NSDictionary *)constantsToExport
{
 return @{ @"DEFAULT_EVENT_NAME": @"New Event" };
}
```

接著可以透過在 JavaScript 中呼叫原生模組的 `getConstants()` 來存取這個常數，如下所示：

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

技術上來說，可以直接從 `NativeModule` 物件存取透過 `constantsToExport()` 匯出的常數。但這在 TurboModules 中將不再支援，因此我們鼓勵開發社群改用上述方法，以避免未來不必要的遷移工作。

> 請注意，常數僅在初始化時匯出，因此如果在執行階段變更 `constantsToExport()` 的值，不會影響 JavaScript 環境。

在 iOS 上，如果您覆寫了 `constantsToExport()`，則還應該實作 `+ requiresMainQueueSetup` 來告知 React Native 您的模組是否需要在主執行緒上初始化（在任何 JavaScript 程式碼執行之前）。否則您會看到警告，提示未來您的模組可能會在背景執行緒初始化，除非您明確透過 `+ requiresMainQueueSetup:` 選擇退出。如果您的模組不需要存取 UIKit，則應對 `+ requiresMainQueueSetup` 回傳 NO。

### 回呼函式

原生模組還支援一種特殊的參數類型——回呼函式（callback）。回呼函式用於在非同步方法中將資料從 Objective-C 傳遞到 JavaScript，也可以用於從原生端非同步執行 JavaScript。

在 iOS 中，回呼函式是透過 `RCTResponseSenderBlock` 類型實作的。以下範例將回呼參數 `myCallBack` 加入 `createCalendarEventMethod()`：

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

接著您可以在原生函式中呼叫這個回呼，並透過陣列傳遞任何想要傳給 JavaScript 的結果。請注意，`RCTResponseSenderBlock` 只接受一個參數——傳遞給 JavaScript 回呼的參數陣列。以下範例將傳回先前呼叫中建立的事件 ID。

> 需要特別強調的是，回呼函式不會在原⽣函式完成後立即被呼叫——請記住通訊是非同步的。

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

原生模組應該只呼叫其回呼函式一次。不過，它可以儲存回呼並稍後再呼叫。這種模式常用於封裝需要委派（delegate）的 iOS API——請參閱 [`RCTAlertManager`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/CoreModules/RCTAlertManager.mm) 的範例。如果回呼從未被呼叫，會導致一些記憶體洩漏。

使用回呼函式時有兩種錯誤處理方式。第一種是遵循 Node 的慣例，將傳遞給回呼陣列的第一個參數視為錯誤物件。

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
  NSNumber *eventId = [NSNumber numberWithInt:123];
  callback(@[[NSNull null], eventId]);
}
```

然後在 JavaScript 中，您可以檢查第一個參數來判斷是否有錯誤被傳遞：

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

如果您想將類似錯誤的物件傳遞給 JavaScript，請使用 [`RCTUtils.h`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTUtils.h) 中的 `RCTMakeError`。目前這只會將一個 Error 形狀的字典傳遞給 JavaScript，但 React Native 的目標是在未來自動生成真正的 JavaScript Error 物件。您也可以提供 `RCTResponseErrorBlock` 參數，它用於錯誤回呼並接受一個 `NSError *` 物件。請注意，這種參數類型在 TurboModules 中將不受支援。

### Promise

原生模組也可以實現 Promise，這可以簡化您的 JavaScript 程式碼，特別是在使用 ES2016 的 `async/await` 語法時。當原生模組方法的最後一個參數是 `RCTPromiseResolveBlock` 和 `RCTPromiseRejectBlock` 時，其對應的 JS 方法將回傳一個 JS Promise 物件。

將上述程式碼重構為使用 Promise 而非回呼函式，看起來會像這樣：

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

The JavaScript counterpart of this method returns a Promise. This means you can use the `await` keyword within an async function to call it and wait for its result:

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

### Sending Events to JavaScript

Native modules can signal events to JavaScript without being invoked directly. For example, you might want to signal to JavaScript a reminder that a calendar event from the native iOS calendar app will occur soon. The preferred way to do this is to subclass `RCTEventEmitter`, implement `supportedEvents` and call self `sendEventWithName`:

Update your header class to import `RCTEventEmitter` and subclass `RCTEventEmitter`:

```objectivec
//  CalendarModule.h

#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>

@interface CalendarModule : RCTEventEmitter <RCTBridgeModule>
@end

```

JavaScript code can subscribe to these events by creating a new `NativeEventEmitter` instance around your module.

You will receive a warning if you expend resources unnecessarily by emitting an event while there are no listeners. To avoid this, and to optimize your module's workload (e.g. by unsubscribing from upstream notifications or pausing background tasks), you can override `startObserving` and `stopObserving` in your `RCTEventEmitter` subclass.

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

### Threading

Unless the native module provides its own method queue, it shouldn't make any assumptions about what thread it's being called on. Currently, if a native module doesn't provide a method queue, React Native will create a separate GCD queue for it and invoke its methods there. Please note that this is an implementation detail and might change. If you want to explicitly provide a method queue for a native module, override the `(dispatch_queue_t) methodQueue` method in the native module. For example, if it needs to use a main-thread-only iOS API, it should specify this via:

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

Similarly, if an operation may take a long time to complete, the native module can specify its own queue to run operations on. Again, currently React Native will provide a separate method queue for your native module, but this is an implementation detail you should not rely on. If you don't provide your own method queue, in the future, your native module's long running operations may end up blocking async calls being executed on other unrelated native modules. The `RCTAsyncLocalStorage` module here, for example, creates its own queue so the React queue isn't blocked waiting on potentially slow disk access.

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

The specified `methodQueue` will be shared by all of the methods in your module. If only one of your methods is long-running (or needs to be run on a different queue than the others for some reason), you can use `dispatch_async` inside the method to perform that particular method's code on another queue, without affecting the others:

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

> Sharing dispatch queues between modules
>
> The `methodQueue` method will be called once when the module is initialized, and then retained by React Native, so there is no need to keep a reference to the queue yourself, unless you wish to make use of it within your module. However, if you wish to share the same queue between multiple modules then you will need to ensure that you retain and return the same queue instance for each of them.

### Dependency Injection

React Native will create and initialize any registered native modules automatically. However, you may wish to create and initialize your own module instances to, for example, inject dependencies.

You can do this by creating a class that implements the `RCTBridgeDelegate` Protocol, initializing an `RCTBridge` with the delegate as an argument and initialising a `RCTRootView` with the initialized bridge.

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### Exporting Swift

Swift doesn't have support for macros, so exposing native modules and their methods to JavaScript inside React Native requires a bit more setup. However, it works relatively the same. Let's say you have the same `CalendarModule` but as a Swift class:

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

> 務必使用 `@objc` 修飾符，以確保類別和函式能正確匯出至 Objective-C 運行環境。

接著建立一個私有實作檔案，用於向 React Native 註冊必要資訊：

```objectivec
// CalendarModuleBridge.m
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(CalendarModule, NSObject)

RCT_EXTERN_METHOD(addEvent:(NSString *)name location:(NSString *)location date:(nonnull NSNumber *)date)

@end
```

若您不熟悉 Swift 與 Objective-C，請注意在 iOS 專案中[混合使用這兩種語言](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)時，需透過橋接標頭檔案（bridging header）將 Objective-C 檔案暴露給 Swift。若透過 Xcode 的 `檔案>新增檔案` 選單添加 Swift 檔案，Xcode 會提示建立此標頭檔。您需在此標頭檔中引入 `RCTBridgeModule.h`。

```objectivec
// CalendarModule-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

您亦可使用 `RCT_EXTERN_REMAP_MODULE` 和 `_RCT_EXTERN_REMAP_METHOD` 來調整模組或方法在 JavaScript 中的命名。詳情請參閱 [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridgeModule.h)。

> 開發第三方模組的重要注意事項：僅 Xcode 9 及以上版本支援含 Swift 的靜態函式庫。若模組中的 iOS 靜態函式庫使用 Swift，主應用專案必須包含 Swift 程式碼與橋接標頭檔。若專案無任何 Swift 程式碼，可暫時以單一空白 .swift 檔案與空白橋接標頭檔作為解決方案。

### 保留方法名稱

#### invalidate()

iOS 上的原生模組可通過實作 `invalidate()` 方法來遵循 [RCTInvalidating](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTInvalidating.h) 協議。當原生橋接失效時（例如開發模式重新載入），[此方法可能被呼叫](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456)。請視需求使用此機制執行原生模組所需的清理工作。