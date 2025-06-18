---
id: native-modules-ios
title: iOS Native Modules
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<NativeDeprecated />

歡迎來到 iOS 原生模組開發。請先閱讀 [原生模組簡介](native-modules-intro) 了解原生模組的基本概念。

## 建立日曆原生模組

本指南將帶您建立一個名為 `CalendarModule` 的原生模組，讓您能從 JavaScript 存取 Apple 的日曆 API。最終您將能在 JavaScript 中呼叫 `CalendarModule.createCalendarEvent('晚餐派對', '我家');` 來觸發建立日曆事件的原生方法。

### 環境設定

首先，請在 Xcode 中開啟您 React Native 應用程式的 iOS 專案。React Native 應用中的 iOS 專案路徑如下：

<figure>
  <img src="/docs/assets/native-modules-ios-open-project.png" width="500" alt="Image of opening up an iOS project within a React Native app inside of xCode." />
  <figcaption>Image of where you can find your iOS project</figcaption>
</figure>

我們建議使用 Xcode 來編寫原生程式碼。Xcode 專為 iOS 開發設計，能幫助您快速解決程式碼語法等小錯誤。

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

您可以使用任何符合模組功能的名稱。此處命名為 `RCTCalendarModule` 是因為要建立日曆原生模組。由於 Objective-C 不像 Java 或 C++ 有語言層級的命名空間支援，慣例是在類別名稱前加上前綴字串（例如應用程式名稱或基礎架構名稱的縮寫）。本例中的 RCT 代表 React。

如下所示，CalendarModule 類別實作了 `RCTBridgeModule` 協定。原生模組就是實作 `RCTBridgeModule` 協定的 Objective-C 類別。

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

目前您的 `RCTCalendarModule.m` 原生模組僅包含 `RCT_EXPORT_MODULE` 巨集，該巨集會將原生模組類別匯出並註冊到 React Native。`RCT_EXPORT_MODULE` 巨集也可接受一個可選參數，用於指定模組在 JavaScript 中的存取名稱。

請注意此參數不是字串常值。下方範例中傳遞的是 `RCT_EXPORT_MODULE(CalendarModuleFoo)` 而非 `RCT_EXPORT_MODULE("CalendarModuleFoo")`。

```objectivec
// To export a module named CalendarModuleFoo
RCT_EXPORT_MODULE(CalendarModuleFoo);
```

接著就能透過以下方式在 JS 中存取該原生模組：

```tsx
const {CalendarModuleFoo} = ReactNative.NativeModules;
```

若未指定名稱，JavaScript 模組名稱會與 Objective-C 類別名稱相同（自動移除 "RCT" 或 "RK" 前綴）。

請參考下方範例，不帶參數呼叫 `RCT_EXPORT_MODULE`。如此模組將以 `CalendarModule` 名稱暴露給 React Native（這是移除 RCT 前綴後的 Objective-C 類別名稱）。

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

接著就能透過以下方式在 JS 中存取該原生模組：

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 將原生方法匯出至 JavaScript

React Native 不會自動將原生模組中的方法暴露給 JavaScript，除非明確使用 `RCT_EXPORT_METHOD` 宏指令宣告。透過 `RCT_EXPORT_METHOD` 編寫的方法都是非同步的，因此返回值類型永遠為 void。若要將 `RCT_EXPORT_METHOD` 方法的結果傳回 JavaScript，可以使用回調函數或發送事件（後續會說明）。現在我們為 `CalendarModule` 原生模組建立一個使用 `RCT_EXPORT_METHOD` 宏指令的原生方法，命名為 `createCalendarEvent()`，暫時讓它接收名稱和位置兩個字串參數，參數類型的選項稍後會詳細說明。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> 請注意，在 TurboModules 中除非方法依賴 RCT 參數轉換（參見下方參數類型說明），否則不需要使用 `RCT_EXPORT_METHOD` 宏指令。React Native 最終將移除 `RCT_EXPORT_MACRO`，因此我們不建議使用 `RCTConvert`，而應在方法體內自行處理參數轉換。

在完善 `createCalendarEvent()` 方法的具體功能前，先在方法內添加一個 console log，以便確認該方法已從 React Native 應用的 JavaScript 端被調用。這裡使用 React 提供的 `RCTLog` API，需先在文件頂部導入該標頭檔，然後添加 log 調用。

```objectivec
#import <React/RCTLog.h>
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
 RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);
}
```

### 同步方法

可使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD` 創建同步原生方法。

```objectivec
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getName)
{
return [[UIDevice currentDevice] name];
}
```

該方法的返回類型必須為對象類型（id）且可序列化為 JSON，這意味著只能返回 nil 或 JSON 兼容值（如 NSNumber、NSString、NSArray、NSDictionary）。

目前我們不建議使用同步方法，因為同步調用可能導致嚴重的性能損耗並引發線程相關問題。此外需注意，若使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`，應用程式將無法再使用 Google Chrome 調試器。這是因為同步方法要求 JS 虛擬機與應用共享記憶體，而 Google Chrome 調試器中 React Native 運行於 Chrome 的 JS 虛擬機，並通過 WebSockets 與移動設備異步通信。

### 測試現有成果

至此你已完成 iOS 原生模組的基礎架構，現在可以通過 JavaScript 訪問該原生模組並調用其導出方法進行測試。

在應用中選擇合適位置添加對原生模組 `createCalendarEvent()` 方法的調用。以下是一個可添加到應用中的 `NewModuleButton` 組件範例，可在其 `onPress()` 函數中調用原生模組。

```tsx
import React from 'react';
import {NativeModules, Button} from 'react-native';

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

接著可通過 `NativeModules` 獲取 `CalendarModule` 原生模組。

```tsx
const {CalendarModule} = NativeModules;
```

現在已可訪問 CalendarModule 原生模組，即可調用原生方法 `createCalendarEvent()`。以下將其添加到 `NewModuleButton` 的 `onPress()` 方法中：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後一步是重新構建 React Native 應用，使最新原生代碼（包含新創建的原生模組）生效。在 react native 應用所在的命令行中執行以下指令：

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

### 迭代開發時的構建流程

As you work through these guides and iterate on your native module, you will need to do a native rebuild of your application to access your most recent changes from JavaScript. This is because the code that you are writing sits within the native part of your application. While React Native’s metro bundler can watch for changes in JavaScript and rebuild JS bundle on the fly for you, it will not do so for native code. So if you want to test your latest native changes you need to rebuild by using the above command.

### Recap✨

You should now be able to invoke your `createCalendarEvent()` method on your native module in JavaScript. Since you are using `RCTLog` in the function, you can confirm your native method is being invoked by [enabling debug mode in your app](https://reactnative.dev/docs/debugging#chrome-developer-tools) and looking at the JS console in Chrome or the mobile app debugger Flipper. You should see your `RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);` message each time you invoke the native module method.

<figure>
  <img src="/docs/assets/native-modules-ios-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of iOS logs in Flipper</figcaption>
</figure>

At this point you have created an iOS native module and invoked a method on it from JavaScript in your React Native application. You can read on to learn more about things like what argument types your native module method takes and how to setup callbacks and promises within your native module.

## Beyond a Calendar Native Module

### Better Native Module Export

Importing your native module by pulling it off of `NativeModules` like above is a bit clunky.

To save consumers of your native module from needing to do that each time they want to access your native module, you can create a JavaScript wrapper for the module. Create a new JavaScript file named NativeCalendarModule.js with the following content:

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

This JavaScript file also becomes a good location for you to add any JavaScript side functionality. For example, if you use a type system like TypeScript you can add type annotations for your native module here. While React Native does not yet support Native to JS type safety, with these type annotations, all your JS code will be type safe. These annotations will also make it easier for you to switch to type-safe native modules down the line. Below is an example of adding type safety to the Calendar Module:

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

In your other JavaScript files you can access the native module and invoke its method like this:

```tsx
import NativeCalendarModule from './NativeCalendarModule';
NativeCalendarModule.createCalendarEvent('foo', 'bar');
```

> Note this assumes that the place you are importing `CalendarModule` is in the same hierarchy as `NativeCalendarModule.js`. Please update the relative import as necessary.

### Argument Types

When a native module method is invoked in JavaScript, React Native converts the arguments from JS objects to their Objective-C/Swift object analogues. So for example, if your Objective-C Native Module method accepts a NSNumber, in JS you need to call the method with a number. React Native will handle the conversion for you. Below is a list of the argument types supported for native module methods and the JavaScript equivalents they map to.

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

> The following types are currently supported but will not be supported in TurboModules. Please avoid using them.
>
> - Function (failure) -> RCTResponseErrorBlock
> - Number -> NSInteger
> - Number -> CGFloat
> - Number -> float

For iOS, you can also write native module methods with any argument type that is supported by the `RCTConvert` class (see [RCTConvert](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTConvert.h) for details about what is supported). The RCTConvert helper functions all accept a JSON value as input and map it to a native Objective-C type or class.

### Exporting Constants

A native module can export constants by overriding the native method `constantsToExport()`. Below `constantsToExport()` is overridden, and returns a Dictionary that contains a default event name property you can access in JavaScript like so:

```objectivec
- (NSDictionary *)constantsToExport
{
 return @{ @"DEFAULT_EVENT_NAME": @"New Event" };
}
```

The constant can then be accessed by invoking `getConstants()` on the native module in JS like so:

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

Technically, it is possible to access constants exported in `constantsToExport()` directly off the `NativeModule` object. This will no longer be supported with TurboModules, so we encourage the community to switch to the above approach to avoid necessary migration down the line.

> Note that the constants are exported only at initialization time, so if you change `constantsToExport()` values at runtime it won't affect the JavaScript environment.

For iOS, if you override `constantsToExport()` then you should also implement `+ requiresMainQueueSetup` to let React Native know if your module needs to be initialized on the main thread, before any JavaScript code executes. Otherwise you will see a warning that in the future your module may be initialized on a background thread unless you explicitly opt out with `+ requiresMainQueueSetup:`. If your module does not require access to UIKit, then you should respond to `+ requiresMainQueueSetup` with NO.

### Callbacks

Native modules also support a unique kind of argument - a callback. Callbacks are used to pass data from Objective-C to JavaScript for asynchronous methods. They can also be used to asynchronously execute JS from the native side.

For iOS, callbacks are implemented using the type `RCTResponseSenderBlock`. Below the callback parameter `myCallBack` is added to the `createCalendarEventMethod()`:

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

You can then invoke the callback in your native function, providing whatever result you want to pass to JavaScript in an array. Note that `RCTResponseSenderBlock` accepts only one argument - an array of parameters to pass to the JavaScript callback. Below you will pass back the ID of an event created in an earlier call.

> It is important to highlight that the callback is not invoked immediately after the native function completes—remember the communication is asynchronous.

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
 NSInteger eventId = ...
 callback(@[@(eventId)]);

 RCTLogInfo(@"Pretending to create an event %@ at %@", title, location);
}

```

This method could then be accessed in JavaScript using the following:

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

A native module is supposed to invoke its callback only once. It can, however, store the callback and invoke it later. This pattern is often used to wrap iOS APIs that require delegates— see [`RCTAlertManager`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/CoreModules/RCTAlertManager.mm) for an example. If the callback is never invoked, some memory is leaked.

There are two approaches to error handling with callbacks. The first is to follow Node’s convention and treat the first argument passed to the callback array as an error object.

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
  NSNumber *eventId = [NSNumber numberWithInt:123];
  callback(@[[NSNull null], eventId]);
}
```

In JavaScript, you can then check the first argument to see if an error was passed through:

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

Another option is to use two separate callbacks: onFailure and onSuccess.

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

Then in JavaScript you can add a separate callback for error and success responses:

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

If you want to pass error-like objects to JavaScript, use `RCTMakeError` from [`RCTUtils.h.`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTUtils.h) Right now this only passes an Error-shaped dictionary to JavaScript, but React Native aims to automatically generate real JavaScript Error objects in the future. You can also provide a `RCTResponseErrorBlock` argument, which is used for error callbacks and accepts an `NSError \* object`. Please note that this argument type will not be supported with TurboModules.

### Promises

Native modules can also fulfill a promise, which can simplify your JavaScript, especially when using ES2016's `async/await` syntax. When the last parameter of a native module method is a `RCTPromiseResolveBlock` and `RCTPromiseRejectBlock`, its corresponding JS method will return a JS Promise object.

Refactoring the above code to use a promise instead of callbacks looks like this:

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

該方法的 JavaScript 對應部分會回傳一個 Promise。這意味著您可以在 async 函數中使用 `await` 關鍵字來呼叫它並等待其結果：

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

原生模組可以在不被直接呼叫的情況下向 JavaScript 發出事件信號。例如，您可能希望向 JavaScript 發出信號，提醒原生 iOS 日曆應用中的某個日曆事件即將發生。推薦的做法是繼承 `RCTEventEmitter`，實現 `supportedEvents` 並呼叫 `self sendEventWithName`：

更新您的標頭類別以導入 `RCTEventEmitter` 並繼承 `RCTEventEmitter`：

```objectivec
//  CalendarModule.h

#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>

@interface CalendarModule : RCTEventEmitter <RCTBridgeModule>
@end

```

JavaScript 代碼可以通過在您的模組周圍創建一個新的 `NativeEventEmitter` 實例來訂閱這些事件。

如果在沒有監聽器的情況下發出事件，您將收到警告。為了避免這種情況並優化模組的工作負載（例如通過取消訂閱上游通知或暫停後台任務），您可以在 `RCTEventEmitter` 子類中覆寫 `startObserving` 和 `stopObserving`。

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

除非原生模組提供自己的方法隊列，否則不應對其被呼叫的線程做出任何假設。目前，如果原生模組未提供方法隊列，React Native 會為其創建一個單獨的 GCD 隊列並在那裡呼叫其方法。請注意，這是一個實現細節，可能會發生變化。如果您想為原生模組明確提供一個方法隊列，請在原生模組中覆寫 `(dispatch_queue_t) methodQueue` 方法。例如，如果需要使用僅限主線程的 iOS API，則應通過以下方式指定：

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

同樣，如果某個操作可能需要很長時間才能完成，原生模組可以指定自己的隊列來運行操作。再次強調，目前 React Native 會為您的原生模組提供一個單獨的方法隊列，但這是一個您不應依賴的實現細節。如果您不提供自己的方法隊列，未來您的原生模組的長時間運行操作可能會阻塞在其他不相關原生模組上執行的異步呼叫。例如，這裡的 `RCTAsyncLocalStorage` 模組創建了自己的隊列，這樣 React 隊列就不會被潛在的慢速磁盤訪問阻塞。

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

指定的 `methodQueue` 將由模組中的所有方法共享。如果只有一個方法是長時間運行的（或由於某種原因需要在與其他方法不同的隊列上運行），您可以在方法內部使用 `dispatch_async` 將該特定方法的代碼放在另一個隊列上執行，而不影響其他方法：

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
> `methodQueue` 方法將在模組初始化時被呼叫一次，然後由 React Native 保留，因此除非您希望在模組內部使用該隊列，否則無需自行保留對隊列的引用。但是，如果您希望在多個模組之間共享同一個隊列，則需要確保為每個模組保留並返回相同的隊列實例。

### 依賴注入

React Native 會自動創建並初始化任何已註冊的原生模組。但是，您可能希望創建並初始化自己的模組實例，例如為了注入依賴項。

您可以通過創建一個實現 `RCTBridgeDelegate` 協議的類別，使用該委託作為參數初始化一個 `RCTBridge`，然後使用初始化的橋接器初始化一個 `RCTRootView` 來實現這一點。

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### 導出 Swift

Swift 不支持宏，因此將原生模組及其方法暴露給 React Native 中的 JavaScript 需要更多的設置。不過，其工作原理相對相同。假設您有一個相同的 `CalendarModule`，但作為 Swift 類別：

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

若您不熟悉 Swift 與 Objective-C，請注意在 iOS 專案中[混用這兩種語言](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)時，需額外建立橋接標頭檔（bridging header）將 Objective-C 檔案暴露給 Swift。若透過 Xcode 的 `檔案>新增檔案` 選單添加 Swift 檔案，Xcode 會提示建立此標頭檔。您需在此標頭檔中引入 `RCTBridgeModule.h`。

```objectivec
// CalendarModule-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

您亦可使用 `RCT_EXTERN_REMAP_MODULE` 和 `_RCT_EXTERN_REMAP_METHOD` 來調整模組或方法在 JavaScript 中的命名。詳見 [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridgeModule.h)。

> 第三方模組開發注意事項：僅 Xcode 9 及以上版本支援含 Swift 的靜態函式庫。若模組中的 iOS 靜態函式庫使用 Swift，主應用專案必須包含 Swift 程式碼與橋接標頭檔。若專案無 Swift 程式碼，可暫時以空白 .swift 檔案與空橋接標頭檔因應。

### 保留方法名稱

#### invalidate()

iOS 端的原生模組可透過實作 `invalidate()` 方法來遵循 [RCTInvalidating](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTInvalidating.h) 協議。當原生橋接失效時（例如開發模式重新載入），[系統可能呼叫](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456)此方法。請依需求利用此機制執行模組所需的清理工作。