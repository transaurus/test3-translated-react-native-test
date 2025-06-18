---
id: native-modules-ios
title: iOS Native Modules
---

import NativeDeprecated from '@site/versioned_docs/version-0.79/the-new-architecture/\_markdown_native_deprecation.mdx'
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<NativeDeprecated />

歡迎來到 iOS 原生模組開發。請先閱讀 [原生模組介紹](native-modules-intro) 了解原生模組的基本概念。

## 建立日曆原生模組

在本指南中，您將建立一個名為 `CalendarModule` 的原生模組，用於從 JavaScript 存取 Apple 的日曆 API。最終您將能夠在 JavaScript 中呼叫 `CalendarModule.createCalendarEvent('晚餐派對', '我家');` 來觸發建立日曆事件的原生方法。

### 設定

首先，請在 Xcode 中開啟您 React Native 應用程式的 iOS 專案。React Native 應用程式的 iOS 專案通常位於以下路徑：

<figure>
  <img src="/docs/assets/native-modules-ios-open-project.png" width="500" alt="Image of opening up an iOS project within a React Native app inside of xCode." />
  <figcaption>Image of where you can find your iOS project</figcaption>
</figure>

我們建議使用 Xcode 來編寫原生程式碼。Xcode 專為 iOS 開發設計，能幫助您快速解決程式碼語法等小錯誤。

### 建立自訂原生模組檔案

第一步是建立我們的主要自訂原生模組標頭檔與實作檔。請建立一個新檔案 `RCTCalendarModule.h`

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

您可以為原生模組使用任何合適的名稱。由於我們正在建立日曆原生模組，因此將類別命名為 `RCTCalendarModule`。由於 Objective-C 不像 Java 或 C++ 具有語言層級的命名空間支援，慣例是在類別名稱前加上前綴字串。這可以是您應用程式名稱或基礎架構名稱的縮寫。本例中的 RCT 代表 React。

如下所示，CalendarModule 類別實現了 `RCTBridgeModule` 協議。原生模組就是實現 `RCTBridgeModule` 協議的 Objective-C 類別。

接下來，我們開始實作原生模組。請在 Xcode 中使用 Cocoa Touch Class 在相同資料夾中建立對應的實作檔 `RCTCalendarModule.m`，並包含以下內容：

```objectivec
// RCTCalendarModule.m
#import "RCTCalendarModule.h"

@implementation RCTCalendarModule

// To export a module named RCTCalendarModule
RCT_EXPORT_MODULE();

@end

```

### 模組名稱

目前您的 `RCTCalendarModule.m` 原生模組僅包含 `RCT_EXPORT_MODULE` 宏，該宏會將原生模組類別匯出並註冊到 React Native。`RCT_EXPORT_MODULE` 宏還可接受一個可選參數，用於指定模組在 JavaScript 程式碼中的可存取名稱。

請注意此參數不是字串常值。在下面的範例中傳遞的是 `RCT_EXPORT_MODULE(CalendarModuleFoo)`，而非 `RCT_EXPORT_MODULE("CalendarModuleFoo")`。

```objectivec
// To export a module named CalendarModuleFoo
RCT_EXPORT_MODULE(CalendarModuleFoo);
```

這樣原生模組就可以在 JS 中這樣存取：

```tsx
const {CalendarModuleFoo} = ReactNative.NativeModules;
```

若未指定名稱，JavaScript 模組名稱將與 Objective-C 類別名稱相同，但會移除任何 "RCT" 或 "RK" 前綴。

讓我們遵循以下範例，不帶任何參數呼叫 `RCT_EXPORT_MODULE`。如此一來，模組將以 `CalendarModule` 名稱暴露給 React Native，因為這是移除 RCT 後的 Objective-C 類別名稱。

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

這樣原生模組就可以在 JS 中這樣存取：

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 將原生方法匯出至 JavaScript

React Native 不會自動將原生模組中的方法暴露給 JavaScript，除非明確使用 `RCT_EXPORT_METHOD` 宏指令宣告。透過 `RCT_EXPORT_METHOD` 編寫的方法都是非同步的，因此回傳類型永遠是 void。若要將 `RCT_EXPORT_METHOD` 方法的結果傳回 JavaScript，可以使用回調函數或發送事件（後續會說明）。現在我們使用 `RCT_EXPORT_METHOD` 宏指令為 `CalendarModule` 原生模組建立一個原生方法。將其命名為 `createCalendarEvent()`，暫時先接收名稱和地點兩個字串參數，參數類型的選項稍後會詳細說明。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> 請注意，在 TurboModules 中除非方法依賴 RCT 參數轉換（參見下方參數類型說明），否則不需要使用 `RCT_EXPORT_METHOD` 宏指令。React Native 最終將移除 `RCT_EXPORT_MACRO`，因此我們不建議使用 `RCTConvert`。替代方案是在方法體內自行處理參數轉換。

在實作 `createCalendarEvent()` 方法的功能之前，先在其中加入 console log 以便確認該方法已從 React Native 應用的 JavaScript 端被調用。使用 React 提供的 `RCTLog` API。首先在檔案頂部引入該標頭檔，然後加入 log 呼叫。

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

此方法的回傳類型必須是物件類型（id）且可序列化為 JSON。這意味著該方法只能回傳 nil 或 JSON 值（例如 NSNumber、NSString、NSArray、NSDictionary）。

目前我們不建議使用同步方法，因為同步調用可能導致嚴重的效能損耗，並為原生模組引入線程相關的錯誤。此外請注意，若選擇使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`，你的應用將無法再使用 Google Chrome 調試器。這是因為同步方法要求 JS 虛擬機與應用共享記憶體。而在 Google Chrome 調試器中，React Native 運行於 Chrome 的 JS 虛擬機內，並透過 WebSockets 與移動設備進行非同步通訊。

### 測試現有成果

至此你已完成 iOS 原生模組的基礎架構。現在可以透過 JavaScript 訪問原生模組並調用其導出的方法來進行測試。

在應用中找到適合調用原生模組 `createCalendarEvent()` 方法的位置。以下是一個可添加到應用中的元件範例 `NewModuleButton`，你可以在該元件的 `onPress()` 函數中調用原生模組。

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

要從 JavaScript 訪問原生模組，首先需要從 React Native 導入 `NativeModules`：

```tsx
import {NativeModules} from 'react-native';
```

接著就能透過 `NativeModules` 取得 `CalendarModule` 原生模組。

```tsx
const {CalendarModule} = NativeModules;
```

現在有了可用的 CalendarModule 原生模組，即可調用你的原生方法 `createCalendarEvent()`。以下將其加入 `NewModuleButton` 的 `onPress()` 方法中：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後一步是重新建置 React Native 應用，讓最新的原生代碼（包含你的新原生模組！）生效。在 react native 應用所在的命令行中執行以下指令：

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

### 迭代開發時的建置流程

當您依照這些指南進行原生模組開發並反覆修改時，需要重新建置原生應用程式才能讓JavaScript端取得最新變更。這是因為您編寫的程式碼屬於應用程式的原生部分。雖然React Native的Metro打包工具可以監控JavaScript變更並即時重建JS套件，但不會自動處理原生程式碼。因此若要測試最新的原生修改，必須使用上述指令手動重建。

### 重點回顧✨

您現在應該能從JavaScript端呼叫原生模組的`createCalendarEvent()`方法。由於函式中使用了`RCTLog`，可透過[啟用應用程式的除錯模式](https://reactnative.dev/docs/debugging#chrome-developer-tools)，在Chrome的JS控制台或行動端除錯工具Flipper中確認原生方法是否被觸發。每次呼叫原生模組方法時，都應看到`RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);`的日誌訊息。

<figure>
  <img src="/docs/assets/native-modules-ios-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of iOS logs in Flipper</figcaption>
</figure>

至此您已完成iOS原生模組的建立，並從React Native應用的JavaScript端成功呼叫其方法。後續可進一步了解原生模組方法的參數類型設定，以及如何在模組中配置回調函式(callbacks)與Promise等進階功能。

## 日曆原生模組的進階應用

### 優化模組導出方式

透過`NativeModules`提取原生模組的現行方式稍顯笨拙。

為避免模組使用者每次都要重複此操作，可建立JavaScript封裝層。新建名為NativeCalendarModule.js的檔案，內容如下：

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

此JavaScript檔案也是添加JS端功能的理想位置。例如若使用TypeScript等類型系統，可在此為原生模組添加類型註解。雖然React Native尚未支援原生到JS的類型安全，但這些註解能確保所有JS程式碼具備類型安全，也為未來轉換至類型安全的原生模組預作準備。以下是為日曆模組添加類型安全的範例：

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

在其他JavaScript檔案中，可透過以下方式存取原生模組並呼叫其方法：

```tsx
import NativeCalendarModule from './NativeCalendarModule';
NativeCalendarModule.createCalendarEvent('foo', 'bar');
```

> 注意：此範例假設導入`CalendarModule`的位置與`NativeCalendarModule.js`處於相同目錄層級，請根據實際情況調整相對路徑。

### 參數類型

當JavaScript呼叫原生模組方法時，React Native會將JS物件參數轉換為對應的Objective-C/Swift物件類型。例如若Objective-C原生模組方法接收NSNumber，JS端需傳入數字類型，React Native會自動處理轉換。以下是原生模組方法支援的參數類型與JS對照表：

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

> 以下類型目前雖受支援，但TurboModules將不再相容，建議避免使用：
>
> - Function (failure) -> RCTResponseErrorBlock
> - Number -> NSInteger
> - Number -> CGFloat
> - Number -> float

iOS端還可使用`RCTConvert`類支援的任何參數類型編寫原生模組方法（詳見[RCTConvert](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTConvert.h)）。RCTConvert輔助函式皆接受JSON值作為輸入，並將其映射至原生Objective-C類型或類別。

### 導出常數

原生模組可透過覆寫`constantsToExport()`方法導出常數。以下範例覆寫該方法，返回包含預設事件名稱屬性的字典，JS端可如此存取：

```objectivec
- (NSDictionary *)constantsToExport
{
 return @{ @"DEFAULT_EVENT_NAME": @"New Event" };
}
```

接著可以透過在 JavaScript 中呼叫原生模組的 `getConstants()` 方法來存取這個常數：

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

技術上來說，可以直接從 `NativeModule` 物件存取透過 `constantsToExport()` 匯出的常數。但這在 TurboModules 中將不再支援，因此我們鼓勵開發社群改用上述方法，以避免未來不必要的遷移工作。

> 請注意，常數僅在初始化時匯出，因此若在執行階段變更 `constantsToExport()` 的值，不會影響 JavaScript 環境。

在 iOS 上，若您覆寫了 `constantsToExport()`，則應同時實作 `+ requiresMainQueueSetup` 來告知 React Native 您的模組是否需要先於任何 JavaScript 程式碼執行前在主執行緒初始化。否則您會看到警告，提示未來您的模組可能會在背景執行緒初始化，除非您明確透過 `+ requiresMainQueueSetup:` 選擇退出。若您的模組不需要存取 UIKit，則應對 `+ requiresMainQueueSetup` 回傳 NO。

### 回呼函式

原生模組還支援一種特殊的參數類型——回呼函式(callback)。回呼函式用於在非同步方法中將資料從 Objective-C 傳遞到 JavaScript，也可用於從原生端非同步執行 JavaScript。

在 iOS 中，回呼函式是透過 `RCTResponseSenderBlock` 類型實作的。以下範例在 `createCalendarEventMethod()` 中新增了回呼參數 `myCallBack`：

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

接著您可以在原生函式中呼叫回呼，並透過陣列傳遞任何想提供給 JavaScript 的結果。請注意，`RCTResponseSenderBlock` 僅接受一個參數——傳遞給 JavaScript 回呼的參數陣列。以下範例將回傳先前呼叫中建立的事件 ID。

> 需特別強調的是，回呼函式並非在原生函式完成後立即呼叫——請記住通訊是非同步的。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
 NSInteger eventId = ...
 callback(@[@(eventId)]);

 RCTLogInfo(@"Pretending to create an event %@ at %@", title, location);
}

```

然後可以透過以下方式在 JavaScript 中存取此方法：

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

原生模組應僅呼叫其回呼函式一次。不過，它可以儲存回呼並稍後再呼叫。此模式常用於封裝需要委派(delegate)的 iOS API——請參閱 [`RCTAlertManager`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/CoreModules/RCTAlertManager.mm) 的範例。若回呼從未被呼叫，會導致部分記憶體洩漏。

使用回呼函式時有兩種錯誤處理方式。第一種是遵循 Node 的慣例，將傳遞給回呼陣列的第一個參數視為錯誤物件。

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
  NSNumber *eventId = [NSNumber numberWithInt:123];
  callback(@[[NSNull null], eventId]);
}
```

在 JavaScript 中，您可以檢查第一個參數來判斷是否有錯誤被傳遞：

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

若您想將類似錯誤的物件傳遞給 JavaScript，請使用 [`RCTUtils.h`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTUtils.h) 中的 `RCTMakeError`。目前這僅會將 Error 形狀的字典傳遞給 JavaScript，但 React Native 的目標是在未來自動產生真正的 JavaScript Error 物件。您也可以提供 `RCTResponseErrorBlock` 參數，用於錯誤回呼並接受 `NSError *` 物件。請注意，此參數類型在 TurboModules 中將不受支援。

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

原生模組可以直接向 JavaScript 發送事件信號，而無需被直接調用。例如，您可能希望向 JavaScript 發出信號，提醒原生 iOS 日曆應用中的某個日曆事件即將發生。推薦的做法是繼承 `RCTEventEmitter`，實現 `supportedEvents` 並呼叫 `self sendEventWithName`：

更新您的標頭類別以導入 `RCTEventEmitter` 並繼承 `RCTEventEmitter`：

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

除非原生模組提供自己的方法隊列，否則不應對其被調用的線程做出任何假設。目前，如果原生模組未提供方法隊列，React Native 會為其創建一個單獨的 GCD 隊列並在那裡調用其方法。請注意，這是一個實現細節，可能會發生變化。如果您想為原生模組明確提供一個方法隊列，請在原生模組中覆寫 `(dispatch_queue_t) methodQueue` 方法。例如，如果需要使用僅限主線程的 iOS API，應通過以下方式指定：

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

同樣，如果某個操作可能需要較長時間完成，原生模組可以指定自己的隊列來執行操作。再次強調，目前 React Native 會為您的原生模組提供一個單獨的方法隊列，但這是一個不應依賴的實現細節。如果您不提供自己的方法隊列，將來您的原生模組的長時間運行操作可能會阻塞在其他不相關原生模組上執行的異步調用。例如，這裡的 `RCTAsyncLocalStorage` 模組創建了自己的隊列，以避免 React 隊列被潛在的慢速磁盤訪問阻塞。

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

指定的 `methodQueue` 將由模組中的所有方法共享。如果只有一個方法是長時間運行的（或由於某些原因需要與其他方法在不同的隊列上運行），您可以在方法內部使用 `dispatch_async` 將該特定方法的代碼放在另一個隊列上執行，而不影響其他方法：

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
> `methodQueue` 方法會在模組初始化時被調用一次，然後由 React Native 保留，因此除非您希望在模組內部使用該隊列，否則無需自行保留對隊列的引用。然而，如果您希望在多個模組之間共享同一個隊列，則需要確保為每個模組保留並返回相同的隊列實例。

### 依賴注入

React Native 會自動創建並初始化所有已註冊的原生模組。但您可能希望創建並初始化自己的模組實例，以便注入依賴項等。

您可以通過創建一個實現 `RCTBridgeDelegate` 協議的類別，使用該委託作為參數初始化一個 `RCTBridge`，然後用初始化後的橋接器來初始化一個 `RCTRootView` 來實現這一點。

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### 導出 Swift

Swift 不支援宏，因此在 React Native 中將原生模組及其方法暴露給 JavaScript 需要更多的設置。但其運作方式基本相同。假設您有一個相同的 `CalendarModule`，但作為 Swift 類別：

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

對於初次接觸 Swift 與 Objective-C 的開發者，當您在 iOS 專案中[混用這兩種語言](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)時，還需要一個額外的橋接標頭檔（bridging header）來將 Objective-C 檔案暴露給 Swift。若您透過 Xcode 的 `檔案>新增檔案` 選單將 Swift 檔案加入專案，Xcode 會主動提示建立此標頭檔。您需在此標頭檔中導入 `RCTBridgeModule.h`。

```objectivec
// CalendarModule-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

您亦可使用 `RCT_EXTERN_REMAP_MODULE` 和 `_RCT_EXTERN_REMAP_METHOD` 來調整導出模組或方法在 JavaScript 中的名稱。更多資訊請參閱 [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridgeModule.h)。

> 開發第三方模組時的重要注意事項：僅 Xcode 9 及以上版本支援含 Swift 的靜態函式庫。若您在模組包含的 iOS 靜態函式庫中使用 Swift，主應用程式專案必須包含 Swift 程式碼及橋接標頭檔才能成功建置 Xcode 專案。若應用程式專案不含任何 Swift 程式碼，可暫時以單一空白 .swift 檔案與空白橋接標頭檔作為解決方案。

### 保留方法名稱

#### invalidate()

iOS 平台的原生模組可透過實作 `invalidate()` 方法來遵循 [RCTInvalidating](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTInvalidating.h) 協議。當原生橋接失效時（例如：開發模式重新載入），[系統可能調用](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456)此方法。請視需求使用此機制為您的原生模組執行必要的清理工作。