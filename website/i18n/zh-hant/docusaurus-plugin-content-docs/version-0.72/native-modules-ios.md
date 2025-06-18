---
id: native-modules-ios
title: iOS Native Modules
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<NativeDeprecated />

歡迎使用 iOS 原生模組。請先閱讀 [原生模組簡介](native-modules-intro) 了解原生模組的基本概念。

## 建立日曆原生模組

本指南將帶您建立一個名為 `CalendarModule` 的原生模組，該模組可讓您從 JavaScript 存取 Apple 的日曆 API。最終您將能透過 JavaScript 呼叫 `CalendarModule.createCalendarEvent('晚餐派對', '我家');` 來觸發建立日曆事件的原生方法。

### 設定

首先，請在 Xcode 中開啟 React Native 應用程式的 iOS 專案。您可以在以下路徑找到 React Native 應用程式中的 iOS 專案：

<figure>
  <img src="/docs/assets/native-modules-ios-open-project.png" width="500" alt="Image of opening up an iOS project within a React Native app inside of xCode." />
  <figcaption>Image of where you can find your iOS project</figcaption>
</figure>

我們建議使用 Xcode 來編寫原生程式碼。Xcode 專為 iOS 開發設計，能幫助您快速解決程式碼語法等小錯誤。

### 建立自訂原生模組檔案

第一步是建立我們的主要自訂原生模組標頭檔與實作檔。請建立一個名為 `RCTCalendarModule.h` 的新檔案：

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

您可以為原生模組使用任何合適的名稱。由於您正在建立日曆原生模組，因此將類別命名為 `RCTCalendarModule`。由於 Objective-C 不像 Java 或 C++ 具有語言層級的命名空間支援，慣例是在類別名稱前加上子字串。這可以是應用程式名稱或基礎架構名稱的縮寫。本例中的 RCT 代表 React。

如下所示，CalendarModule 類別實現了 `RCTBridgeModule` 協議。原生模組是一個實現 `RCTBridgeModule` 協議的 Objective-C 類別。

接下來，我們開始實作原生模組。請在同一個資料夾中建立對應的實作檔 `RCTCalendarModule.m`，並包含以下內容：

```objectivec
// RCTCalendarModule.m
#import "RCTCalendarModule.h"

@implementation RCTCalendarModule

// To export a module named RCTCalendarModule
RCT_EXPORT_MODULE();

@end

```

### 模組名稱

目前，您的 `RCTCalendarModule.m` 原生模組僅包含 `RCT_EXPORT_MODULE` 宏，該宏會將原生模組類別匯出並註冊到 React Native。`RCT_EXPORT_MODULE` 宏還接受一個可選參數，用於指定模組在 JavaScript 程式碼中的可存取名稱。

此參數不是字串字面量。在下面的範例中，傳遞的是 `RCT_EXPORT_MODULE(CalendarModuleFoo)`，而不是 `RCT_EXPORT_MODULE("CalendarModuleFoo")`。

```objectivec
// To export a module named CalendarModuleFoo
RCT_EXPORT_MODULE(CalendarModuleFoo);
```

然後可以在 JavaScript 中這樣存取原生模組：

```tsx
const {CalendarModuleFoo} = ReactNative.NativeModules;
```

如果您不指定名稱，JavaScript 模組名稱將與 Objective-C 類別名稱相同，但會移除任何 "RCT" 或 "RK" 前綴。

讓我們按照下面的範例，不帶任何參數呼叫 `RCT_EXPORT_MODULE`。因此，模組將以 `CalendarModule` 的名稱暴露給 React Native，因為這是移除 RCT 後的 Objective-C 類別名稱。

```objectivec
// Without passing in a name this will export the native module name as the Objective-C class name with “RCT” removed
RCT_EXPORT_MODULE();
```

然後可以在 JavaScript 中這樣存取原生模組：

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 將原生方法匯出到 JavaScript

React Native 不會自動將原生模組中的方法暴露給 JavaScript，除非明確告知。這可以透過 `RCT_EXPORT_METHOD` 巨集來實現。使用 `RCT_EXPORT_METHOD` 編寫的方法都是非同步的，因此回傳類型始終為 void。若要將 `RCT_EXPORT_METHOD` 方法的結果傳遞給 JavaScript，可以使用回調函數或發送事件（後續會說明）。現在我們繼續為 `CalendarModule` 原生模組設置一個原生方法，使用 `RCT_EXPORT_METHOD` 巨集。將其命名為 `createCalendarEvent()`，目前先讓它接收名稱和位置兩個字串參數。參數類型的選項稍後會詳細說明。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
}
```

> 請注意，除非你的方法依賴於 RCT 參數轉換（參見下方的參數類型），否則 TurboModules 將不需要 `RCT_EXPORT_METHOD` 巨集。最終，React Native 會移除 `RCT_EXPORT_MACRO`，因此我們不建議使用 `RCTConvert`。相反，你可以在方法體內進行參數轉換。

在完善 `createCalendarEvent()` 方法的功能之前，先在方法中添加一個控制台日誌，以便確認它已從 JavaScript 調用。使用 React 的 `RCTLog` API。首先在文件頂部導入該標頭，然後添加日誌調用。

```objectivec
#import <React/RCTLog.h>
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)name location:(NSString *)location)
{
 RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);
}
```

### 同步方法

你可以使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD` 來創建一個同步的原生方法。

```objectivec
RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD(getName)
{
return [[UIDevice currentDevice] name];
}
```

該方法的回傳類型必須是物件類型（id）並且可序列化為 JSON。這意味著該方法只能回傳 nil 或 JSON 值（例如 NSNumber、NSString、NSArray、NSDictionary）。

目前，我們不建議使用同步方法，因為同步調用方法可能會導致嚴重的性能損失，並為你的原生模組引入與線程相關的錯誤。此外，請注意，如果你選擇使用 `RCT_EXPORT_BLOCKING_SYNCHRONOUS_METHOD`，你的應用將無法再使用 Google Chrome 調試器。這是因為同步方法要求 JS VM 與應用共享記憶體。對於 Google Chrome 調試器，React Native 運行在 Google Chrome 內的 JS VM 中，並通過 WebSockets 與移動設備進行非同步通信。

### 測試你構建的內容

現在你已經在 iOS 中為原生模組設置了基本框架。通過訪問原生模組並在 JavaScript 中調用其導出的方法來測試它。

在你的應用中找到一個地方，你希望添加對原生模組 `createCalendarEvent()` 方法的調用。以下是一個組件 `NewModuleButton` 的示例，你可以將其添加到你的應用中。你可以在 `NewModuleButton` 的 `onPress()` 函數中調用原生模組。

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

為了從 JavaScript 訪問你的原生模組，首先需要從 React Native 導入 `NativeModules`：

```tsx
import {NativeModules} from 'react-native';
```

然後你可以從 `NativeModules` 中訪問 `CalendarModule` 原生模組。

```tsx
const {CalendarModule} = NativeModules;
```

現在你已經可以使用 `CalendarModule` 原生模組，可以調用你的原生方法 `createCalendarEvent()`。以下是將其添加到 `NewModuleButton` 的 `onPress()` 方法中的示例：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後一步是重新構建 React Native 應用，以便獲得最新的原生代碼（包含你的新原生模組！）。在 React Native 應用所在的命令行中，運行以下命令：

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

### 迭代構建

當你依照這些指南進行原生模組開發並反覆修改時，需要重新編譯應用程式才能讓JavaScript端取得最新的變更。這是因為你所編寫的程式碼屬於應用的原生部分。雖然React Native的Metro打包器能監測JavaScript變更並即時重建JS套件包，但不會自動處理原生程式碼。因此若要測試最新的原生端修改，必須使用上述指令重新編譯。

### 重點回顧✨

你現在應該能在JavaScript中呼叫原生模組的`createCalendarEvent()`方法。由於函式中使用了`RCTLog`，可透過[啟用應用程式的除錯模式](https://reactnative.dev/docs/debugging#chrome-developer-tools)，在Chrome的JS控制台或行動端除錯工具Flipper中確認原生方法是否被觸發。每次呼叫該原生模組方法時，都應看到`RCTLogInfo(@"Pretending to create an event %@ at %@", name, location);`的日誌訊息。

<figure>
  <img src="/docs/assets/native-modules-ios-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of iOS logs in Flipper</figcaption>
</figure>

至此你已完成iOS原生模組的建立，並從React Native應用的JavaScript端成功呼叫其方法。後續可進一步了解原生模組方法的參數類型設定，以及如何在原生模組中配置回調函式與Promise等進階功能。

## 日曆原生模組的進階應用

### 優化原生模組導出方式

透過`NativeModules`提取原生模組的現行導入方式較為繁瑣。

為避免模組使用者每次存取時重複此操作，可為模組建立JavaScript封裝層。新建名為NativeCalendarModule.js的檔案，內容如下：

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

此JavaScript檔案同時也是添加JS端功能的理想位置。例如若使用TypeScript等類型系統，可在此為原生模組添加類型註解。雖然React Native尚未支援原生到JS的類型安全，但這些註解能確保所有JS程式碼具備類型安全性，也為未來轉換至類型安全的原生模組預作準備。以下是為日曆模組添加類型安全的範例：

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

> 注意此處假設導入`CalendarModule`的位置與`NativeCalendarModule.js`處於相同目錄層級，請根據實際情況調整相對路徑。

### 參數類型對應

當JavaScript呼叫原生模組方法時，React Native會將JS物件參數轉換為對應的Objective-C/Swift物件類型。例如若Objective-C原生模組方法接受NSNumber類型，在JS端需傳入數字，React Native會自動處理類型轉換。以下是原生模組方法支援的參數類型與JS對應關係表：

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

iOS端還可使用`RCTConvert`類支援的所有參數類型（詳見[RCTConvert](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTConvert.h)）。RCTConvert輔助函式皆接受JSON值作為輸入，並將其轉換為原生Objective-C類型或類別。

### 導出常數

原生模組可透過覆寫`constantsToExport()`方法來導出常數。以下範例覆寫該方法，返回包含預設事件名稱屬性的字典，在JavaScript端可如此存取：

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

技術上來說，直接從 `NativeModule` 物件存取透過 `constantsToExport()` 匯出的常數是可行的。但這在 TurboModules 中將不再支援，因此我們鼓勵開發社群改用上述方法，以避免未來不必要的遷移工作。

> 請注意，常數僅在初始化時匯出，因此若在執行階段變更 `constantsToExport()` 的值，不會影響 JavaScript 環境。

在 iOS 上，若您覆寫了 `constantsToExport()`，則應同時實作 `+ requiresMainQueueSetup` 來告知 React Native 您的模組是否需要先於任何 JavaScript 程式碼執行前在主執行緒初始化。否則您會看到警告，提示未來您的模組可能會在背景執行緒初始化，除非您明確透過 `+ requiresMainQueueSetup:` 選擇退出。若您的模組不需要存取 UIKit，則應對 `+ requiresMainQueueSetup` 回傳 NO。

### 回呼函式

原生模組還支援一種特殊的參數類型——回呼函式(callback)。回呼函式用於在非同步方法中將資料從 Objective-C 傳遞到 JavaScript，也可用於從原生端非同步執行 JavaScript。

在 iOS 中，回呼函式是透過 `RCTResponseSenderBlock` 類型實作的。以下範例將回呼參數 `myCallBack` 加入 `createCalendarEventMethod()`：

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title
                location:(NSString *)location
                myCallback:(RCTResponseSenderBlock)callback)

```

接著您可以在原生函式中呼叫回呼函式，並透過陣列傳遞任何想傳給 JavaScript 的結果。請注意，`RCTResponseSenderBlock` 僅接受一個參數——傳遞給 JavaScript 回呼函式的參數陣列。以下範例將回傳先前呼叫中建立的事件 ID。

> 需特別強調的是，回呼函式並非在原生函式完成後立即呼叫——請記住通訊是非同步的。

```objectivec
RCT_EXPORT_METHOD(createCalendarEvent:(NSString *)title location:(NSString *)location callback: (RCTResponseSenderBlock)callback)
{
 NSInteger eventId = ...
 callback(@[@(eventId)]);

 RCTLogInfo(@"Pretending to create an event %@ at %@", title, location);
}

```

接著可透過以下方式在 JavaScript 中存取此方法：

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

原生模組應僅呼叫其回呼函式一次。不過，它可以儲存回呼函式並稍後呼叫。此模式常用於封裝需要委派(delegate)的 iOS API——參見 [`RCTAlertManager`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/CoreModules/RCTAlertManager.mm) 範例。若回呼函式從未被呼叫，會導致部分記憶體洩漏。

使用回呼函式處理錯誤有兩種方法。第一種是遵循 Node 的慣例，將傳遞給回呼陣列的第一個參數視為錯誤物件。

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

接著在 JavaScript 中可為錯誤和成功回應分別添加回呼函式：

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

若您想將類錯誤物件傳遞給 JavaScript，請使用 [`RCTUtils.h`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTUtils.h) 中的 `RCTMakeError`。目前這僅會將 Error 形狀的字典傳給 JavaScript，但 React Native 的目標是在未來自動產生真正的 JavaScript Error 物件。您也可以提供 `RCTResponseErrorBlock` 參數，用於錯誤回呼並接受 `NSError *` 物件。請注意，此參數類型在 TurboModules 中將不受支援。

### Promise

原生模組也能履行(fullfill) Promise，這可簡化您的 JavaScript 程式碼，特別是在使用 ES2016 的 `async/await` 語法時。當原生模組方法的最後一個參數是 `RCTPromiseResolveBlock` 和 `RCTPromiseRejectBlock` 時，其對應的 JS 方法將回傳 JS Promise 物件。

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

這個方法的 JavaScript 對應部分會回傳一個 Promise。這意味著你可以在 async 函數中使用 `await` 關鍵字來呼叫它並等待其結果：

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

原生模組可以在不被直接呼叫的情況下向 JavaScript 發出事件信號。例如，你可能希望向 JavaScript 發出信號，提醒原生 iOS 日曆應用中的某個日曆事件即將發生。推薦的做法是繼承 `RCTEventEmitter`，實作 `supportedEvents` 並呼叫 `self sendEventWithName`：

更新你的標頭類別以導入 `RCTEventEmitter` 並繼承 `RCTEventEmitter`：

```objectivec
//  CalendarModule.h

#import <React/RCTBridgeModule.h>
#import <React/RCTEventEmitter.h>

@interface CalendarModule : RCTEventEmitter <RCTBridgeModule>
@end

```

JavaScript 代碼可以通過在你的模組周圍創建一個新的 `NativeEventEmitter` 實例來訂閱這些事件。

如果在沒有監聽器的情況下發出事件，你將會收到警告。為了避免這種情況，並優化模組的工作負載（例如通過取消訂閱上游通知或暫停背景任務），你可以在 `RCTEventEmitter` 子類中覆寫 `startObserving` 和 `stopObserving`。

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

除非原生模組提供自己的方法隊列，否則不應對其被呼叫的執行緒做出任何假設。目前，如果原生模組未提供方法隊列，React Native 會為其創建一個單獨的 GCD 隊列並在那裡呼叫其方法。請注意，這是一個實現細節，可能會發生變化。如果你想明確為原生模組提供方法隊列，可以在原生模組中覆寫 `(dispatch_queue_t) methodQueue` 方法。例如，如果需要使用僅限主執行緒的 iOS API，應通過以下方式指定：

```objectivec
- (dispatch_queue_t)methodQueue
{
  return dispatch_get_main_queue();
}
```

同樣地，如果某個操作可能需要很長時間才能完成，原生模組可以指定自己的隊列來執行操作。再次強調，目前 React Native 會為你的原生模組提供一個單獨的方法隊列，但這是一個你不應依賴的實現細節。如果你不提供自己的方法隊列，未來你的原生模組的長時間運行操作可能會阻塞在其他不相關原生模組上執行的異步呼叫。例如，這裡的 `RCTAsyncLocalStorage` 模組創建了自己的隊列，這樣 React 隊列就不會被潛在的慢速磁碟訪問阻塞。

```objectivec
- (dispatch_queue_t)methodQueue
{
 return dispatch_queue_create("com.facebook.React.AsyncLocalStorageQueue", DISPATCH_QUEUE_SERIAL);
}
```

指定的 `methodQueue` 將由模組中的所有方法共享。如果只有一個方法是長時間運行的（或由於某些原因需要在與其他方法不同的隊列上運行），你可以在方法內部使用 `dispatch_async` 將該特定方法的代碼放在另一個隊列上執行，而不影響其他方法：

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
> `methodQueue` 方法將在模組初始化時被呼叫一次，然後由 React Native 保留，因此除非你希望在模組內部使用它，否則無需自己保留隊列的引用。然而，如果你希望在多個模組之間共享同一個隊列，則需要確保為每個模組保留並返回相同的隊列實例。

### 依賴注入

React Native 會自動創建並初始化任何已註冊的原生模組。但是，你可能希望創建並初始化自己的模組實例，例如為了注入依賴項。

你可以通過創建一個實現 `RCTBridgeDelegate` 協議的類別，使用該委託作為參數初始化一個 `RCTBridge`，然後使用初始化後的橋接器初始化一個 `RCTRootView` 來實現這一點。

```objectivec
id<RCTBridgeDelegate> moduleInitialiser = [[classThatImplementsRCTBridgeDelegate alloc] init];

RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:moduleInitialiser launchOptions:nil];

RCTRootView *rootView = [[RCTRootView alloc]
                        initWithBridge:bridge
                            moduleName:kModuleName
                     initialProperties:nil];
```

### 導出 Swift

Swift 不支援巨集，因此將原生模組及其方法暴露給 React Native 中的 JavaScript 需要更多的設置。不過，其運作方式大致相同。假設你有相同的 `CalendarModule`，但作為一個 Swift 類別：

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

> 務必使用 `@objc` 修飾符，以確保類別和函式能正確導出至 Objective-C 運行環境。

接著建立一個私有實作檔案，用於向 React Native 註冊必要資訊：

```objectivec
// CalendarManagerBridge.m
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(CalendarManager, NSObject)

RCT_EXTERN_METHOD(addEvent:(NSString *)name location:(NSString *)location date:(nonnull NSNumber *)date)

@end
```

對於初次接觸 Swift 與 Objective-C 的開發者，當您在 iOS 專案中[混用這兩種語言](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)時，還需要一個額外的橋接檔案（稱為 bridging header）來將 Objective-C 檔案暴露給 Swift。若您透過 Xcode 的 `檔案>新增檔案` 選單將 Swift 檔案加入專案，Xcode 會主動提示建立此橋接標頭檔。您需在此標頭檔中引入 `RCTBridgeModule.h`。

```objectivec
// CalendarManager-Bridging-Header.h
#import <React/RCTBridgeModule.h>
```

您亦可使用 `RCT_EXTERN_REMAP_MODULE` 和 `_RCT_EXTERN_REMAP_METHOD` 來調整導出模組或方法在 JavaScript 中的名稱。詳情請參閱 [`RCTBridgeModule`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridgeModule.h)。

> 開發第三方模組的重要注意事項：僅 Xcode 9 及以上版本支援含 Swift 的靜態函式庫。若您在模組的 iOS 靜態函式庫中使用 Swift，主應用專案必須包含 Swift 程式碼及自身的橋接標頭檔才能成功建構 Xcode 專案。若應用專案不含任何 Swift 程式碼，可暫時以單一空白 .swift 檔案與空白橋接標頭檔作為解決方案。

### 保留方法名稱

#### invalidate()

iOS 平台的原生模組可透過實作 `invalidate()` 方法來遵循 [RCTInvalidating](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTInvalidating.h) 協議。當原生橋接失效時（例如：開發模式重新載入），[系統可能調用此方法](https://github.com/facebook/react-native/blob/0.62-stable/ReactCommon/turbomodule/core/platform/ios/RCTTurboModuleManager.mm#L456)。請視需求運用此機制為您的原生模組執行必要的清理工作。