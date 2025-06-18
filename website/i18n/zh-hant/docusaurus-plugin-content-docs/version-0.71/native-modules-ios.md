---
id: native-modules-ios
title: iOS Native Modules
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

歡迎使用 iOS 原生模組。請先閱讀 [原生模組簡介](native-modules-intro) 了解什麼是原生模組。

## 建立日曆原生模組

在接下來的指南中，您將建立一個名為 `CalendarModule` 的原生模組，該模組將允許您從 JavaScript 存取 Apple 的日曆 API。最終您將能夠從 JavaScript 調用 `CalendarModule.createCalendarEvent('晚餐派對', '我家');`，從而觸發建立日曆事件的原生方法。

### 設定

首先，請在 Xcode 中開啟您的 React Native 應用程式內的 iOS 專案。您可以在以下路徑找到 React Native 應用程式中的 iOS 專案：

<figure>
  <img src="/docs/assets/native-modules-ios-open-project.png" width="500" alt="Image of opening up an iOS project within a React Native app inside of xCode." />
  <figcaption>Image of where you can find your iOS project</figcaption>
</figure>

我們建議使用 Xcode 來編寫您的原生程式碼。Xcode 專為 iOS 開發而設計，使用它將幫助您快速解決諸如程式碼語法等小錯誤。

### 建立自訂原生模組檔案

第一步是建立我們的主要自訂原生模組標頭檔和實作檔。建立一個名為 `RCTCalendarModule.h` 的新檔案：

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

您可以使用任何適合您正在建立的原生模組的名稱。由於您正在建立一個日曆原生模組，因此將類別命名為 `RCTCalendarModule`。由於 Objective-C 不像 Java 或 C++ 那樣有語言層級的命名空間支援，慣例是在類別名稱前加上一個子字串。這可以是您應用程式名稱的縮寫或您的基礎設施名稱。在此範例中，RCT 指的是 React。

如下所示，CalendarModule 類別實現了 `RCTBridgeModule` 協議。原生模組是一個實現了 `RCTBridgeModule` 協議的 Objective-C 類別。

接下來，讓我們開始實作原生模組。在同一個資料夾中建立對應的實作檔 `RCTCalendarModule.m`，並包含以下內容：

```objectivec
// RCTCalendarModule.m
#import "RCTCalendarModule.h"

@implementation RCTCalendarModule

// To export a module named RCTCalendarModule
RCT_EXPORT_MODULE();

@end

```

### 模組名稱

目前，您的 `RCTCalendarModule.m` 原生模組僅包含一個 `RCT_EXPORT_MODULE` 宏，該宏將原生模組類別導出並註冊到 React Native。`RCT_EXPORT_MODULE` 宏還可以接受一個可選參數，該參數指定模組在 JavaScript 程式碼中可存取的名稱。

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

讓我們遵循下面的範例，不帶任何參數調用 `RCT_EXPORT_MODULE`。因此，模組將使用名稱 `CalendarModule` 暴露給 React Native，因為這是 Objective-C 類別名稱，並移除了 RCT。

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

然後可以在 JS 中這樣存取原生模組：

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 將原生方法導出到 JavaScript

React Native 不會自動將原生模組中的方法暴露給 JavaScript，除非明確告知。這可以通過使用 `RCT_EXPORT_METHOD` 宏來實現。在 `RCT_EXPORT_METHOD` 宏中編寫的方法是異步的，因此返回類型始終為 void。要將 `RCT_EXPORT_METHOD` 方法的結果傳遞給 JavaScript，可以使用回調或發送事件（稍後會介紹）。現在，讓我們使用 `RCT_EXPORT_METHOD` 宏為我們的 `CalendarModule` 原生模組設置一個原生方法。將其命名為 `createCalendarEvent()`，並暫時讓它接受名稱和位置作為字符串參數。參數類型的選項將在稍後介紹。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> 請注意，除非您的方法依賴於 RCT 參數轉換（參見下面的參數類型），否則 `RCT_EXPORT_METHOD` 宏在 TurboModules 中將不再需要。最終，React Native 將移除 `RCT_EXPORT_MACRO`，因此我們不建議使用 `RCTConvert`。相反，您可以在方法體內進行參數轉換。

在構建 `createCalendarEvent()` 方法的具體功能之前，先在方法中添加一個控制台日誌，以便確認它已從 JavaScript 調用到您的 React Native 應用程序中。使用 React 的 `RCTLog` API。讓我們在文件的頂部導入該頭文件，然後添加日誌調用。

```objectivec
#import <React/RCTLog.h>
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
 RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);
}
```

### 同步方法

您可以使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD` 來創建一個同步的原生方法。

```objectivec
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getName)
{
return [[UIDevice currentDevice] name];
}
```

該方法的返回類型必須是對象類型（id）並且應該可序列化為 JSON。這意味著該方法只能返回 nil 或 JSON 值（例如 NSNumber、NSString、NSArray、NSDictionary）。

目前，我們不建議使用同步方法，因為同步調用方法可能會對性能造成嚴重影響，並在您的原生模組中引入與線程相關的錯誤。此外，請注意，如果您選擇使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`，您的應用程序將無法再使用 Google Chrome 調試器。這是因為同步方法要求 JS VM 與應用程序共享內存。對於 Google Chrome 調試器，React Native 在 Google Chrome 的 JS VM 中運行，並通過 WebSockets 與移動設備進行異步通信。

### 測試您構建的內容

此時，您已經在 iOS 中為您的原生模組設置了基本的框架。通過訪問原生模組並在 JavaScript 中調用其導出的方法來測試它。

在您的應用程序中找到一個您希望添加調用原生模組 `createCalendarEvent()` 方法的地方。下面是一個組件 `NewModuleButton` 的示例，您可以將其添加到您的應用程序中。您可以在 `NewModuleButton` 的 `onPress()` 函數中調用原生模組。

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

為了從 JavaScript 訪問您的原生模組，您需要首先從 React Native 導入 `NativeModules`：

```tsx
import {NativeModules} from 'react-native';
```

然後，您可以從 `NativeModules` 中訪問 `CalendarModule` 原生模組。

```tsx
const {CalendarModule} = NativeModules;
```

現在您已經可以使用 `CalendarModule` 原生模組，您可以調用您的原生方法 `createCalendarEvent()`。下面將其添加到 `NewModuleButton` 的 `onPress()` 方法中：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後一步是重新構建 React Native 應用程序，以便您可以獲得最新的原生代碼（包含您的新原生模組！）。在您的命令行中，位於 react native 應用程序的目錄下，運行以下命令：

```shell
npx react-native run-ios
```

### 迭代構建

當你依照這些指南進行開發並迭代你的原生模組時，你需要重新原生編譯應用程式才能從 JavaScript 存取最新的變更。這是因為你所編寫的程式碼位於應用程式的原生部分。雖然 React Native 的 Metro 打包工具可以監控 JavaScript 的變更並即時重新打包 JS 套件，但它不會對原生程式碼這麼做。因此，如果你想測試最新的原生變更，必須使用 `npx react-native run-ios` 指令重新編譯。

### 重點回顧 ✨

你現在應該能夠在 JavaScript 中呼叫原生模組的 `createCalendarEvent()` 方法。由於你在函式中使用了 `RCTLog`，可以透過[在應用程式中啟用除錯模式](https://reactnative.dev/docs/debugging#chrome-developer-tools)來確認原生方法是否被呼叫，並在 Chrome 的 JS 控制台或行動應用除錯工具 Flipper 中查看。每次呼叫原生模組方法時，你都應該會看到 `RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);` 的訊息。

<figure>
  <img src="/docs/assets/native-modules-ios-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of iOS logs in Flipper</figcaption>
</figure>

至此，你已經建立了一個 iOS 原生模組，並從 React Native 應用程式的 JavaScript 中呼叫了它的方法。你可以繼續閱讀，了解更多關於原生模組方法接受的參數類型，以及如何在原生模組中設置回調和 Promise 的內容。

## 超越日曆原生模組

### 更好的原生模組匯出方式

像上面那樣從 `NativeModules` 中提取你的原生模組來匯入，有點笨拙。

為了讓你的原生模組的使用者不必每次存取原生模組時都這麼做，你可以為模組建立一個 JavaScript 包裝器。建立一個名為 NativeCalendarModule.js 的新 JavaScript 檔案，內容如下：

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

這個 JavaScript 檔案也是你添加任何 JavaScript 端功能的好地方。例如，如果你使用像 TypeScript 這樣的類型系統，可以在這裡為你的原生模組添加類型註解。雖然 React Native 目前還不支援原生到 JS 的類型安全，但有了這些類型註解，你所有的 JS 程式碼都將是類型安全的。這些註解也會讓你更容易在未來切換到類型安全的原生模組。以下是一個為日曆模組添加類型安全的範例：

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

在你的其他 JavaScript 檔案中，可以這樣存取原生模組並呼叫其方法：

```tsx
import NativeCalendarModule from './NativeCalendarModule';
NativeCalendarModule.createCalendarEvent('foo', 'bar');
```

> 注意：這假設你匯入 `CalendarModule` 的位置與 `NativeCalendarModule.js` 位於相同的層級。請根據需要更新相對匯入路徑。

### 參數類型

當在 JavaScript 中呼叫原生模組方法時，React Native 會將參數從 JS 物件轉換為對應的 Objective-C/Swift 物件。因此，舉例來說，如果你的 Objective-C 原生模組方法接受一個 NSNumber，在 JS 中你需要用數字來呼叫該方法。React Native 會為你處理轉換。以下是原生模組方法支援的參數類型及其對應的 JavaScript 等價物的列表。

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

對於 iOS，你也可以使用 `RCTConvert` 類別支援的任何參數類型來編寫原生模組方法（詳見 [RCTConvert](https://github.com/facebook/react-native/blob/0.71-stable/React/Base/RCTConvert.h) 以了解支援的內容）。RCTConvert 輔助函數都接受一個 JSON 值作為輸入，並將其映射到原生的 Objective-C 類型或類別。

### 匯出常數

原生模組可以透過覆寫原生方法 `constantsToExport()` 來匯出常數。以下覆寫了 `constantsToExport()`，並返回一個字典，其中包含你可以在 JavaScript 中存取的預設事件名稱屬性，如下所示：

```objectivec
- (NSDictionary *)constantsToExport
{
 return @{ @"DEFAULT_EVENT_NAME": @"New Event" };
}
```

接著可以透過在JS中調用原生模組的`getConstants()`來存取該常數：

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

技術上來說，直接從`NativeModule`物件存取`constantsToExport()`導出的常數是可行的。但這在TurboModules中將不再支援，因此我們鼓勵社群採用上述方法，以避免未來不必要的遷移工作。

> 請注意，常數僅在初始化時導出，若在運行時更改`constantsToExport()`的值，不會影響JavaScript環境。

在iOS端，若覆寫了`constantsToExport()`，則應同時實作`+ requiresMainQueueSetup`來告知React Native您的模組是否需要先於任何JavaScript程式碼執行前，在主線程初始化。否則您會看到警告，提示未來您的模組可能在背景線程初始化，除非您透過`+ requiresMainQueueSetup:`明確選擇退出。若您的模組無需存取UIKit，則應對`+ requiresMainQueueSetup`回傳NO。

### 回調函數

原生模組還支援一種特殊的參數類型——回調函數(callback)。回調函數用於在非同步方法中將數據從Objective-C傳遞至JavaScript，也可用於從原生端非同步執行JS。

在iOS中，回調函數透過類型`RCTResponseSenderBlock`實作。以下為`createCalendarEventMethod()`添加回調參數`myCallBack`的範例：

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

接著您可以在原生函數中調用回調，透過陣列傳遞任何想回傳至JavaScript的結果。請注意`RCTResponseSenderBlock`僅接受一個參數——傳遞給JS回調的參數陣列。以下範例將回傳先前調用中所建立事件的ID。

> 需特別強調的是，回調函數不會在原生日函數完成後立即觸發——請記住這種通訊是非同步的。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
 NSInteger eventId = ...
 callback(@[@(eventId)]);

 RCTLogInfo(@"Pretending to create an event %@ at %@", title, location);
}

```

此方法隨後可透過以下方式在JavaScript中存取：

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

原生模組應僅調用其回調函數一次。不過，它可以儲存回調並延後調用。此模式常用於封裝需要委派(delegate)的iOS API——參見[`RCTAlertManager`](https://github.com/facebook/react-native/blob/3a11f0536ea65b87dc0f006665f16a87cfa14b5e/React/CoreModules/RCTAlertManager.mm)範例。若回調從未被調用，會導致部分記憶體洩漏。

回調函數的錯誤處理有兩種方式。第一種是遵循Node慣例，將傳遞給回調陣列的第一個參數視為錯誤物件。

```objectivec
RCT_EXPORT_METHOD(createCalendarEventCallback:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
  NSNumber *eventId = [NSNumber numberWithInt:123];
  callback(@[[NSNull null], eventId]);
}
```

在JavaScript中，您可以檢查第一個參數來判斷是否傳遞了錯誤：

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

另一種選項是使用兩個獨立回調：onFailure與onSuccess。

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

接著在JavaScript中可為錯誤與成功響應分別添加回調：

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

若要將類錯誤物件傳遞至JavaScript，請使用[`RCTUtils.h`](https://github.com/facebook/react-native/blob/0.71-stable/React/Base/RCTUtils.h)中的`RCTMakeError`。目前這僅會傳遞Error形狀的字典至JavaScript，但React Native目標是未來能自動生成真正的JavaScript Error物件。您也可提供`RCTResponseErrorBlock`參數，該參數用於錯誤回調並接受`NSError *`物件。請注意此參數類型在TurboModules中將不受支援。

### Promise

原生模組也能實現Promise，這可簡化您的JavaScript程式碼，特別是在使用ES2016的`async/await`語法時。當原生模組方法的最後一個參數是`RCTPromiseResolveBlock`與`RCTPromiseRejectBlock`時，其對應的JS方法將回傳JS Promise物件。

將上述程式碼重構為使用 Promise 而非回調函數的範例如下：

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

該方法在 JavaScript 端的對應實現會回傳一個 Promise。這意味著您可以在 async 函數中使用 `await` 關鍵字來呼叫它並等待其結果：

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

原生模組能夠不經直接呼叫就向 JavaScript 發出事件信號。例如，您可能希望向 JavaScript 發出提醒，表示來自原生 iOS 日曆應用程式的某個日曆事件即將發生。推薦的做法是繼承 `RCTEventEmitter`，實現 `supportedEvents` 並呼叫 `self sendEventWithName`：

更新您的標頭類別以導入 `RCTEventEmitter` 並繼承 `RCTEventEmitter`：

```objectivec
//  CalendarModule.h

#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>

@interface CalendarModule : RCTEventEmitter <RCTBridgeModule>
@end

```

JavaScript 程式碼可以透過圍繞您的模組創建新的 `NativeEventEmitter` 實例來訂閱這些事件。

如果在沒有監聽器的情況下發出事件，您將收到警告。為避免這種情況並優化模組的工作負載（例如通過取消訂閱上游通知或暫停背景任務），您可以在 `RCTEventEmitter` 子類中覆寫 `startObserving` 和 `stopObserving` 方法。

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
  if (hasListeners) {// Only send events if anyone is listening
    [self sendEventWithName:@"EventReminder" body:@{@"name": eventName}];
  }
}

```

### 執行緒處理

除非原生模組提供自己的方法佇列，否則它不應對自己被呼叫的執行緒做出任何假設。目前，如果原生模組未提供方法佇列，React Native 會為其創建一個獨立的 GCD 佇列並在那裡呼叫其方法。請注意，這是一個實現細節，可能會發生變化。如果您想為原生模組明確提供方法佇列，請在原生模組中覆寫 `(dispatch_queue_t) methodQueue` 方法。例如，如果需要使用僅限主執行緒的 iOS API，則應通過以下方式指定：

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

同樣地，如果某個操作可能需要很長時間才能完成，原生模組可以指定自己的佇列來執行操作。再次強調，目前 React Native 會為您的原生模組提供獨立的方法佇列，但這是一個您不應依賴的實現細節。如果您不提供自己的方法佇列，未來您的原生模組的長時間運行操作可能會阻塞在其他不相關原生模組上執行的非同步呼叫。例如，這裡的 `RCTAsyncLocalStorage` 模組創建了自己的佇列，以免 React 佇列被潛在的慢速磁碟存取所阻塞。

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

指定的 `methodQueue` 將由模組中的所有方法共享。如果只有一個方法需要長時間運行（或出於某些原因需要在不同佇列上運行），您可以在該方法內部使用 `dispatch_async` 將特定方法的程式碼放在另一個佇列上執行，而不影響其他方法：

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
> `methodQueue` 方法將在模組初始化時被呼叫一次，然後由 React Native 保留，因此除非您希望在模組內部使用該佇列，否則無需自行保留對佇列的引用。然而，如果您希望在多個模組間共享同一個佇列，則需要確保為每個模組保留並返回相同的佇列實例。

### 依賴注入

React Native 會自動創建並初始化任何已註冊的原生模組。但您可能希望創建並初始化自己的模組實例，以便注入依賴項等。

您可以通過創建一個實現 `RCTBridgeDelegate` 協議的類別，使用該委託作為參數初始化 `RCTBridge`，然後使用初始化後的橋接器初始化 `RCTRootView` 來實現這一點。

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### 導出 Swift

Swift 不支援巨集，因此要將原生模組及其方法暴露給 React Native 中的 JavaScript 需要更多設置。但其運作方式大致相同。假設您有一個相同的 `CalendarModule`，但作為 Swift 類別：

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

> 務必使用 `@objc` 修飾符以確保類別和函式能正確導出至 Objective-C 運行環境。

接著建立一個私有實作檔案，用於向 React Native 註冊必要資訊：

```objectivec
// CalendarManagerBridge.m
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(CalendarManager, NSObject)

RCT_EXTERN_METHOD(addEvent:(NSString *)name location:(NSString *)location date:(nonnull NSNumber *)date)

@end
```

對於剛接觸 Swift 和 Objective-C 的開發者，當您在 iOS 專案中[混合使用這兩種語言](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)時，還需要一個橋接頭文件（bridging header）來將 Objective-C 檔案暴露給 Swift。若您透過 Xcode 的 `檔案>新增檔案` 選單添加 Swift 檔案，Xcode 會提示建立此頭文件。您需要在該頭文件中導入 `RCTBridgeModule.h`。

```objectivec
// CalendarManager-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

您也可以使用 `RCT_EXTERN_REMAP_MODULE` 和 `_RCT_EXTERN_REMAP_METHOD` 來調整導出模組或方法在 JavaScript 中的名稱。更多資訊請參閱 [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/0.71-stable/React/Base/RCTBridgeModule.h)。

> 開發第三方模組時的重要注意事項：僅 Xcode 9 及以上版本支援包含 Swift 的靜態函式庫。若您在模組的 iOS 靜態函式庫中使用 Swift，主應用專案必須包含 Swift 程式碼和橋接頭文件才能成功建置 Xcode 專案。若您的應用專案原本不含任何 Swift 程式碼，可暫時加入一個空的 .swift 檔案和空白橋接頭文件作為解決方案。

### 保留方法名稱

#### invalidate()

原生模組可透過實作 `invalidate()` 方法來遵循 iOS 的 [RCTInvalidating](https://github.com/facebook/react-native/blob/0.62-stable/React/Base/RCTInvalidating.h) 協議。當原生橋接失效時（例如：開發模式重新載入），[系統可能調用](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456)此方法。請視需求使用此機制為您的原生模組執行必要的清理工作。