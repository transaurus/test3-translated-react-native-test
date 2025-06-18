---
id: native-modules-ios
title: iOS Native Modules
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

歡迎來到 iOS 原生模組開發。請先閱讀 [原生模組簡介](native-modules-intro) 了解原生模組的基本概念。

## 建立日曆原生模組

本指南將帶您建立一個名為 `CalendarModule` 的原生模組，讓您能透過 JavaScript 存取蘋果的日曆 API。最終您將能在 JavaScript 中呼叫 `CalendarModule.createCalendarEvent('晚餐派對', '我家');` 來觸發建立日曆事件的原生方法。

### 環境設定

首先，請用 Xcode 開啟 React Native 應用中的 iOS 專案。您可以在以下路徑找到 iOS 專案：

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

您可以為原生模組選擇任何合適的名稱。此處命名為 `RCTCalendarModule` 是因為要建立日曆原生模組。由於 Objective-C 不像 Java 或 C++ 有語言層級的命名空間支援，慣例是在類別名稱前加上前綴字串（例如應用程式名稱或基礎架構名稱的縮寫）。本例中的 RCT 代表 React。

如下所示，CalendarModule 類別實現了 `RCTBridgeModule` 協議。原生模組就是實現 `RCTBridgeModule` 協議的 Objective-C 類別。

接著開始實作原生模組。請在同個資料夾建立對應的實作檔 `RCTCalendarModule.m`，並包含以下內容：

```objectivec
// RCTCalendarModule.m
#import "RCTCalendarModule.h"

@implementation RCTCalendarModule

// To export a module named RCTCalendarModule
RCT_EXPORT_MODULE();

@end

```

### 模組名稱

目前您的 `RCTCalendarModule.m` 原生模組僅包含 `RCT_EXPORT_MODULE` 宏，該宏會將原生模組類別導出並註冊到 React Native。`RCT_EXPORT_MODULE` 宏還可接受一個可選參數，用於指定模組在 JavaScript 中的存取名稱。

注意此參數不是字串字面量。下方範例中傳遞的是 `RCT_EXPORT_MODULE(CalendarModuleFoo)`，而非 `RCT_EXPORT_MODULE("CalendarModuleFoo")`。

```objectivec
// To export a module named CalendarModuleFoo
RCT_EXPORT_MODULE(CalendarModuleFoo);
```

接著就能在 JavaScript 中這樣存取該原生模組：

```jsx
const {CalendarModuleFoo} = ReactNative.NativeModules;
```

若未指定名稱，JavaScript 模組名稱會與 Objective-C 類別名稱相同（但會移除 "RCT" 或 "RK" 前綴）。

讓我們遵循下方範例，不帶參數呼叫 `RCT_EXPORT_MODULE`。結果模組會以 `CalendarModule` 名稱暴露給 React Native，因為這是移除 RCT 前綴後的 Objective-C 類別名稱。

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

接著就能在 JavaScript 中這樣存取該原生模組：

```jsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 將原生方法導出至 JavaScript

React Native will not expose any methods in a native module to JavaScript unless explicitly told to. This can be done using the `RCT_EXPORT_METHOD` macro. Methods written in the `RCT_EXPORT_METHOD` macro are asynchronous and the return type is therefore always void. In order to pass a result from a `RCT_EXPORT_METHOD` method to JavaScript you can use callbacks or emit events (covered below). Let’s go ahead and set up a native method for our `CalendarModule` native module using the `RCT_EXPORT_METHOD` macro. Call it `createCalendarEvent()` and for now have it take in name and location arguments as strings. Argument type options will be covered shortly.

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> Please note that the `RCT_EXPORT_METHOD` macro will not be necessary with TurboModules unless your method relies on RCT argument conversion (see argument types below). Ultimately, React Native will remove `RCT_EXPORT_MACRO,` so we discourage people from using `RCTConvert`. Instead, you can do the argument conversion within the method body.

Before you build out the `createCalendarEvent()` method’s functionality, add a console log in the method so you can confirm it has been invoked from JavaScript in your React Native application. Use the `RCTLog` APIs from React. Let’s import that header at the top of your file and then add the log call.

```objectivec
#import <React/RCTLog.h>
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
 RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);
}
```

### Synchronous Methods

You can use the `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD` to create a synchronous native method.

```objectivec
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getName)
{
return [[UIDevice currentDevice] name];
}
```

The return type of this method must be of object type (id) and should be serializable to JSON. This means that the hook can only return nil or JSON values (e.g. NSNumber, NSString, NSArray, NSDictionary).

At the moment, we do not recommend using synchronous methods, since calling methods synchronously can have strong performance penalties and introduce threading-related bugs to your native modules. Additionally, please note that if you choose to use `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`, your app can no longer use the Google Chrome debugger. This is because synchronous methods require the JS VM to share memory with the app. For the Google Chrome debugger, React Native runs inside the JS VM in Google Chrome, and communicates asynchronously with the mobile devices via WebSockets.

### Test What You Have Built

At this point you have set up the basic scaffolding for your native module in iOS. Test that out by accessing the native module and invoking it’s exported method in JavaScript.

Find a place in your application where you would like to add a call to the native module’s `createCalendarEvent()` method. Below is an example of a component, `NewModuleButton` you can add in your app. You can invoke the native module inside `NewModuleButton`'s `onPress()` function.

```jsx
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

In order to access your native module from JavaScript you need to first import `NativeModules` from React Native:

```jsx
import {NativeModules} from 'react-native';
```

You can then access the `CalendarModule` native module off of `NativeModules`.

```jsx
const {CalendarModule} = NativeModules;
```

Now that you have the CalendarModule native module available, you can invoke your native method `createCalendarEvent()`. Below it is added to the `onPress()` method in `NewModuleButton`:

```jsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

The final step is to rebuild the React Native app so that you can have the latest native code (with your new native module!) available. In your command line, where the react native application is located, run the following :

```shell
npx react-native run-ios
```

### Building as You Iterate

當您按照這些指南進行原生模組的迭代開發時，需要重新建置應用程式才能從 JavaScript 存取最新的變更。這是因為您編寫的程式碼位於應用程式的原生部分。雖然 React Native 的 Metro 打包工具可以監控 JavaScript 的變更並即時重建 JS 套件，但不會對原生程式碼進行相同操作。因此若要測試最新的原生變更，必須使用 `npx react-native run-ios` 指令重新建置。

### 重點回顧 ✨

您現在應該能從 JavaScript 呼叫原生模組的 `createCalendarEvent()` 方法。由於函式中使用了 `RCTLog`，可透過[啟用應用程式的除錯模式](https://reactnative.dev/docs/debugging#chrome-developer-tools)並查看 Chrome 的 JS 控制台或行動應用除錯工具 Flipper 來確認原生方法是否被呼叫。每次呼叫原生模組方法時，都應看到 `RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);` 的日誌訊息。

<figure>
  <img src="/docs/assets/native-modules-ios-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of iOS logs in Flipper</figcaption>
</figure>

至此您已建立 iOS 原生模組，並從 React Native 應用的 JavaScript 端呼叫其方法。後續可進一步了解原生模組方法的參數類型設定，以及如何在原生模組中配置回調函式與 Promise。

## 日曆原生模組的進階應用

### 優化原生模組匯出方式

透過 `NativeModules` 提取原生模組的現行方式較為繁瑣。

為避免使用者每次存取原生模組時都需重複此操作，可為模組建立 JavaScript 封裝層。新建名為 NativeCalendarModule.js 的 JavaScript 檔案，內容如下：

```jsx
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

此 JavaScript 檔案也是添加 JavaScript 端功能的理想位置。例如若使用 TypeScript 等類型系統，可在此為原生模組添加類型註解。雖然 React Native 尚未支援原生到 JS 的類型安全，但這些註解能確保所有 JS 程式碼具備類型安全，也利於未來轉換至類型安全的原生模組。以下是為日曆模組添加類型安全的範例：

```jsx
/**
* This exposes the native CalendarModule module as a JS module. This has a
* function 'createCalendarEvent' which takes the following parameters:
*
* 1. String name: A string representing the name of the event
* 2. String location: A string representing the location of the event
*/
import { NativeModules } from 'react-native';
const { CalendarModule } = NativeModules;
interface CalendarInterface {
   createCalendarEvent(name: string, location: string): void;
}
export default CalendarModule as CalendarInterface;
```

在其他 JavaScript 檔案中可透過以下方式存取原生模組並呼叫其方法：

```jsx
import NativeCalendarModule from './NativeCalendarModule';
NativeCalendarModule.createCalendarEvent('foo', 'bar');
```

> 注意：此處假設導入 `CalendarModule` 的位置與 `NativeCalendarModule.js` 處於相同目錄層級，請根據實際情況調整相對路徑。

### 參數類型

當 JavaScript 呼叫原生模組方法時，React Native 會將參數從 JS 物件轉換為對應的 Objective-C/Swift 物件。例如若 Objective-C 原生模組方法接受 NSNumber，在 JS 中需使用數字呼叫該方法，React Native 會自動處理轉換。以下是原生模組方法支援的參數類型及其對應的 JavaScript 等效類型清單：

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

> 以下類型目前雖受支援，但 TurboModules 將不再相容，請避免使用：
>
> - Function (failure) -> RCTResponseErrorBlock
> - Number -> NSInteger
> - Number -> CGFloat
> - Number -> float

在 iOS 端，您還可使用任何 `RCTConvert` 類別支援的參數類型編寫原生模組方法（詳見 [RCTConvert](https://github.com/facebook/react-native/blob/0.70-stable/React/Base/RCTConvert.h)）。RCTConvert 輔助函式皆接受 JSON 值作為輸入，並將其映射至原生 Objective-C 類型或類別。

### 匯出常數

原生模組可透過覆寫 `constantsToExport()` 方法來匯出常數。以下範例覆寫該方法，回傳包含預設事件名稱屬性的字典，您可在 JavaScript 中如此存取：

```objectivec
- (NSDictionary *)constantsToExport
{
 return @{ @"DEFAULT_EVENT_NAME": @"New Event" };
}
```

接著可以透過在 JavaScript 中呼叫原生模組的 `getConstants()` 方法來存取這個常數：

```objectivec
const { DEFAULT_EVENT_NAME } = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

技術上來說，可以直接從 `NativeModule` 物件存取透過 `constantsToExport()` 匯出的常數。但這項功能將在 TurboModules 中不再支援，因此我們鼓勵開發社群改用上述方法，以避免未來不必要的遷移工作。

> 請注意，常數僅在初始化時匯出，因此若在執行時期變更 `constantsToExport()` 的值，並不會影響 JavaScript 環境。

在 iOS 上，若您覆寫了 `constantsToExport()` 方法，則應同時實作 `+ requiresMainQueueSetup` 來告知 React Native 您的模組是否需要先於任何 JavaScript 程式碼執行前，在主執行緒上初始化。否則您會看到警告，提示未來您的模組可能會在背景執行緒初始化，除非您明確透過 `+ requiresMainQueueSetup:` 選擇退出。若您的模組不需要存取 UIKit，則應對 `+ requiresMainQueueSetup` 回傳 NO。

### 回呼函式

原生模組還支援一種特殊的參數類型——回呼函式(callback)。回呼函式用於在非同步方法中將資料從 Objective-C 傳遞到 JavaScript，也可用於從原生端非同步執行 JavaScript 程式碼。

在 iOS 上，回呼函式是透過 `RCTResponseSenderBlock` 類型實現的。以下範例在 `createCalendarEventMethod()` 中加入了回呼參數 `myCallBack`：

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

接著您可以在原生函式中呼叫這個回呼，並透過陣列傳遞任何想送至 JavaScript 的結果。請注意 `RCTResponseSenderBlock` 只接受一個參數——傳給 JavaScript 回呼的參數陣列。以下範例將回傳先前呼叫中建立的事件 ID。

> 需特別強調的是，回呼函式不會在原生函式完成後立即被呼叫——請記住這種通訊是非同步的。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
 NSInteger eventId = ...
 callback(@[@(eventId)]);

 RCTLogInfo(@"Pretending to create an event %@ at %@", title, location);
}

```

這個方法接著可以在 JavaScript 中透過以下方式呼叫：

```jsx
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

原生模組應該只呼叫其回呼函式一次。不過，它可以儲存回呼並稍後再呼叫。這種模式常用於封裝需要委派(delegate)的 iOS API——可參考 [`RCTAlertManager`](https://github.com/facebook/react-native/blob/3a11f0536ea65b87dc0f006665f16a87cfa14b5e/React/CoreModules/RCTAlertManager.mm) 的實作範例。若回呼從未被呼叫，會導致部分記憶體洩漏。

使用回呼函式時有兩種錯誤處理方式。第一種是遵循 Node 的慣例，將傳給回呼陣列的第一個參數視為錯誤物件。

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
  NSNumber *eventId = [NSNumber numberWithInt:123];
  callback(@[[NSNull null], eventId]);
}
```

在 JavaScript 中，您可以檢查第一個參數來判斷是否有錯誤被傳回：

```jsx
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

接著在 JavaScript 中可以分別為錯誤和成功回應加入回呼：

```jsx
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

若要將錯誤類物件傳遞給 JavaScript，請使用 [`RCTUtils.h`](https://github.com/facebook/react-native/blob/0.70-stable/React/Base/RCTUtils.h) 中的 `RCTMakeError`。目前這只會將一個 Error 形狀的字典傳給 JavaScript，但 React Native 的目標是在未來自動產生真正的 JavaScript Error 物件。您也可以提供 `RCTResponseErrorBlock` 參數，這用於錯誤回呼並接受 `NSError *` 物件。請注意此參數類型將不會在 TurboModules 中獲得支援。

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

此方法的 JavaScript 對應版本會回傳一個 Promise。這意味著您可以在非同步函式中使用 `await` 關鍵字來呼叫它並等待其結果：

```jsx
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

原生模組可以直接不經呼叫就向 JavaScript 發出事件信號。例如，您可能希望向 JavaScript 發出信號，提醒原生 iOS 日曆應用中的某個日曆事件即將發生。推薦的做法是繼承 `RCTEventEmitter`，實作 `supportedEvents` 並呼叫 `self sendEventWithName`：

更新您的標頭類別以導入 `RCTEventEmitter` 並繼承 `RCTEventEmitter`：

```objectivec
//  CalendarModule.h

#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>

@interface CalendarModule : RCTEventEmitter <RCTBridgeModule>
@end

```

JavaScript 程式碼可以透過在您的模組周圍建立一個新的 `NativeEventEmitter` 實例來訂閱這些事件。

如果您在沒有監聽器的情況下發出事件，將會收到警告。為了避免這種情況並優化模組的工作負載（例如取消訂閱上游通知或暫停背景任務），您可以在 `RCTEventEmitter` 子類別中覆寫 `startObserving` 和 `stopObserving` 方法。

```objectivec
@implementation CalendarManager
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
  if (hasListeners) { // Only send events if anyone is listening
    [self sendEventWithName:@"EventReminder" body:@{@"name": eventName}];
  }
}

```

### 執行緒處理

除非原生模組提供自己的方法佇列，否則不應對其被呼叫的執行緒做出任何假設。目前，如果原生模組未提供方法佇列，React Native 會為其建立一個獨立的 GCD 佇列並在其中呼叫其方法。請注意，這是一個實作細節，可能會變更。如果您想明確為原生模組提供方法佇列，請在原生模組中覆寫 `(dispatch_queue_t) methodQueue` 方法。例如，如果需要使用僅限主執行緒的 iOS API，應透過以下方式指定：

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

同樣地，如果某個操作可能需要較長時間完成，原生模組可以指定自己的佇列來執行操作。再次強調，目前 React Native 會為您的原生模組提供獨立的方法佇列，但這是一個您不應依賴的實作細節。如果您不提供自己的方法佇列，未來您的原生模組的長時間運行操作可能會阻塞在其他不相關原生模組上執行的非同步呼叫。例如，這裡的 `RCTAsyncLocalStorage` 模組建立了自己的佇列，以避免 React 佇列被潛在的慢速磁碟存取阻塞。

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

指定的 `methodQueue` 將由模組中的所有方法共享。如果只有一個方法是長時間運行的（或出於某些原因需要在不同佇列上執行），您可以在方法內部使用 `dispatch_async` 將該特定方法的程式碼放在另一個佇列上執行，而不影響其他方法：

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

> 在模組間共享調度佇列
>
> `methodQueue` 方法會在模組初始化時被呼叫一次，然後由 React Native 保留，因此除非您希望在模組內部使用該佇列，否則無需自行保留佇列的參考。然而，如果您希望在多個模組間共享同一個佇列，則需要確保為每個模組保留並回傳相同的佇列實例。

### 依賴注入

React Native 會自動建立並初始化所有已註冊的原生模組。但您可能希望建立並初始化自己的模組實例，例如為了注入依賴項。

您可以透過建立一個實作 `RCTBridgeDelegate` 協定的類別，使用該委派作為參數初始化一個 `RCTBridge`，然後用初始化後的橋接器來初始化 `RCTRootView` 來實現這一點。

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### 匯出 Swift

Swift 不支援巨集，因此要將原生模組及其方法暴露給 React Native 中的 JavaScript 需要更多設定。但其運作方式大致相同。假設您有一個相同的 `CalendarModule`，但作為 Swift 類別：

```swift
// CalendarManager.swift

@objc(CalendarManager)
class CalendarManager: NSObject {

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

> It is important to use the `@objc` modifiers to ensure the class and functions are exported properly to the Objective-C runtime.

Then create a private implementation file that will register the required information with React Native:

```objectivec
// CalendarManagerBridge.m
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(CalendarManager, NSObject)

RCT_EXTERN_METHOD(addEvent:(NSString *)name location:(NSString *)location date:(nonnull NSNumber *)date)

@end
```

For those of you new to Swift and Objective-C, whenever you [mix the two languages in an iOS project](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html), you will also need an additional bridging file, known as a bridging header, to expose the Objective-C files to Swift. Xcode will offer to create this header file for you if you add your Swift file to your app through the Xcode `File>New File` menu option. You will need to import `RCTBridgeModule.h` in this header file.

```objectivec
// CalendarManager-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

You can also use `RCT_EXTERN_REMAP_MODULE` and `_RCT_EXTERN_REMAP_METHOD` to alter the JavaScript name of the module or methods you are exporting. For more information see [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/0.70-stable/React/Base/RCTBridgeModule.h).

> Important when making third party modules: Static libraries with Swift are only supported in Xcode 9 and later. In order for the Xcode project to build when you use Swift in the iOS static library you include in the module, your main app project must contain Swift code and a bridging header itself. If your app project does not contain any Swift code, a workaround can be a single empty .swift file and an empty bridging header.

### Reserved Method Names

#### invalidate()

Native modules can conform to the [RCTInvalidating](https://github.com/facebook/react-native/blob/0.62-stable/React/Base/RCTInvalidating.h) protocol on iOS by implementing the `invalidate()` method. This method [can be invoked](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456) when the native bridge is invalidated (ie: on devmode reload). Please use this mechanism as necessary to do the required cleanup for your native module.