---
id: pushnotificationios
title: '🚧 PushNotificationIOS'
---

> **已棄用。** 請改用 [社群套件](https://github.com/react-native-push-notification/ios)。

<div className="banner-native-code-required">
  <h3>Projects with Native Code Only</h3>
  <p>The following section only applies to projects with native code exposed. If you are using the managed Expo workflow, see the guide on <a href="https://docs.expo.dev/versions/latest/sdk/notifications/">Notifications</a> in the Expo documentation for the appropriate alternative.</p>
</div>

處理應用程式的通知，包括排程和權限設定。

---

## 開始使用

要啟用推播通知，請先[透過 Apple 設定您的通知服務](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server)與後端系統。

接著，在專案中[啟用遠端通知功能](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/pushing_background_updates_to_your_app#2980038)。這將自動啟用必要的設定。

### 啟用 `register` 事件支援

在您的 `AppDelegate.m` 中加入：

```objectivec
#import <React/RCTPushNotificationManager.h>
```

然後實作以下方法以處理遠端通知註冊事件：

```objectivec
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
{
 // This will trigger 'register' events on PushNotificationIOS
 [RCTPushNotificationManager didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
}
- (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
{
 // This will trigger 'registrationError' events on PushNotificationIOS
 [RCTPushNotificationManager didFailToRegisterForRemoteNotificationsWithError:error];
}
```

### 處理通知

您需要在 `AppDelegate` 中實作 `UNUserNotificationCenterDelegate`：

```objectivec
#import <UserNotifications/UserNotifications.h>

@interface YourAppDelegate () <UNUserNotificationCenterDelegate>
@end
```

在應用程式啟動時設定委派：

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  ...
  UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
  center.delegate = self;

  return YES;
}
```

#### 前景通知

實作 `userNotificationCenter:willPresentNotification:withCompletionHandler:` 以處理應用程式在前景時收到的通知。使用 completionHandler 決定是否向使用者顯示通知，並相應地通知 `RCTPushNotificationManager`：

```objectivec
// Called when a notification is delivered to a foreground app.
- (void)userNotificationCenter:(UNUserNotificationCenter *)center
       willPresentNotification:(UNNotification *)notification
         withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
{
  // This will trigger 'notification' and 'localNotification' events on PushNotificationIOS
  [RCTPushNotificationManager didReceiveNotification:notification];
  // Decide if and how the notification will be shown to the user
  completionHandler(UNNotificationPresentationOptionNone);
}
```

#### 背景通知

實作 `userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:` 以處理使用者點擊通知的情況，通常用於使用者點擊背景通知開啟應用程式時。但若您已在 `userNotificationCenter:willPresentNotification:withCompletionHandler:` 中設定顯示前景通知，此方法也會在使用者點擊前景通知時被呼叫。此時，您應僅在其中一個回呼中通知 `RCTPushNotificationManager`。

若點擊通知導致應用程式啟動，請呼叫 `setInitialNotification:`。若通知先前未被 `userNotificationCenter:willPresentNotification:withCompletionHandler:` 處理，也請呼叫 `didReceiveNotification:`：

```objectivec
- (void)  userNotificationCenter:(UNUserNotificationCenter *)center
  didReceiveNotificationResponse:(UNNotificationResponse *)response
           withCompletionHandler:(void (^)(void))completionHandler
{
  // This condition passes if the notification was tapped to launch the app
  if ([response.actionIdentifier isEqualToString:UNNotificationDefaultActionIdentifier]) {
    // Allow the notification to be retrieved on the JS side using getInitialNotification()
    [RCTPushNotificationManager setInitialNotification:response.notification];
  }
  // This will trigger 'notification' and 'localNotification' events on PushNotificationIOS
  [RCTPushNotificationManager didReceiveNotification:response.notification];
  completionHandler();
}
```

---

# 參考資料

## 方法

### `presentLocalNotification()`

```tsx
static presentLocalNotification(details: PresentLocalNotificationDetails);
```

立即顯示本地通知。

**參數：**

| Name    | Type   | Required | Description |
| ------- | ------ | -------- | ----------- |
| details | object | Yes      | See below.  |

`details` 為包含以下屬性的物件：

- `alertTitle` : 通知警示的標題文字。
- `alertBody` : 通知警示的訊息內容。
- `userInfo` : 包含額外通知資料的物件（選填）。
- `category` : 此通知的類別，需設定才能使用可操作通知（選填）。例如包含回覆或按讚等額外動作的通知。
- `applicationIconBadgeNumber` : 顯示在應用程式圖示上的徽章數字。預設值為 0，表示不顯示徽章（選填）。
- `isSilent` : 若為 true，通知將無聲顯示（選填）。
- `soundName` : 觸發通知時播放的音效（選填）。
- `alertAction` : 已棄用。此為 iOS 舊版 UILocalNotification 所用。

---

### `scheduleLocalNotification()`

```tsx
static scheduleLocalNotification(details: ScheduleLocalNotificationDetails);
```

排定未來顯示的本地通知。

**參數：**

| Name    | Type   | Required | Description |
| ------- | ------ | -------- | ----------- |
| details | object | Yes      | See below.  |

`details` 為包含以下屬性的物件：

- `alertTitle` : 顯示為通知標題的文字。
- `alertBody` : 顯示在通知內容中的訊息。
- `fireDate` : 通知觸發的時間。排程通知可使用 `fireDate` 或 `fireIntervalSeconds`，其中 `fireDate` 優先級較高。
- `fireIntervalSeconds` : 從現在起多少秒後顯示通知。
- `userInfo` : 包含額外通知資料的物件（可選）。
- `category` : 此通知的分類，用於可操作通知（可選）。例如包含回覆或點讚等附加操作的通知。
- `applicationIconBadgeNumber` : 顯示在應用程式圖示上的徽章數字。預設值為 0，表示不顯示徽章（可選）。
- `isSilent` : 若為 true，通知將無聲顯示（可選）。
- `soundName` : 通知觸發時播放的聲音（可選）。
- `alertAction` : 已棄用。此為 iOS 舊版 UILocalNotification 所用。
- `repeatInterval` : 已棄用。請改用 `fireDate` 或 `fireIntervalSeconds`。

---

### `cancelAllLocalNotifications()`

```tsx
static cancelAllLocalNotifications();
```

取消所有已排程的本地通知。

---

### `removeAllDeliveredNotifications()`

```tsx
static removeAllDeliveredNotifications();
```

從通知中心移除所有已送達的通知。

---

### `getDeliveredNotifications()`

```tsx
static getDeliveredNotifications(callback: (notifications: Object[]) => void);
```

提供目前顯示在通知中心中的應用程式通知清單。

**參數：**

| Name     | Type     | Required | Description                                                  |
| -------- | -------- | -------- | ------------------------------------------------------------ |
| callback | function | Yes      | Function which receives an array of delivered notifications. |

已送達的通知是一個包含以下屬性的物件：

- `identifier` : 此通知的識別碼。
- `title` : 此通知的標題。
- `body` : 此通知的內容。
- `category` : 此通知的分類（可選）。
- `userInfo` : 包含額外通知資料的物件（可選）。
- `thread-id` : 此通知的執行緒識別碼（如果有）。

---

### `removeDeliveredNotifications()`

```tsx
static removeDeliveredNotifications(identifiers: string[]);
```

從通知中心移除指定的通知。

**參數：**

| Name        | Type  | Required | Description                        |
| ----------- | ----- | -------- | ---------------------------------- |
| identifiers | array | Yes      | Array of notification identifiers. |

---

### `setApplicationIconBadgeNumber()`

```tsx
static setApplicationIconBadgeNumber(num: number);
```

設定主畫面應用程式圖示的徽章數字。

**參數：**

| Name   | Type   | Required | Description                    |
| ------ | ------ | -------- | ------------------------------ |
| number | number | Yes      | Badge number for the app icon. |

---

### `getApplicationIconBadgeNumber()`

```tsx
static getApplicationIconBadgeNumber(callback: (num: number) => void);
```

取得主畫面應用程式圖示的當前徽章數字。

**參數：**

| Name     | Type     | Required | Description                                        |
| -------- | -------- | -------- | -------------------------------------------------- |
| callback | function | Yes      | Function which processes the current badge number. |

---

### `cancelLocalNotifications()`

```tsx
static cancelLocalNotifications(userInfo: Object);
```

取消所有符合提供 `userInfo` 欄位的已排程本地通知。

**參數：**

| Name     | Type   | Required | Description |
| -------- | ------ | -------- | ----------- |
| userInfo | object | No       |             |

---

### `getScheduledLocalNotifications()`

```tsx
static getScheduledLocalNotifications(
  callback: (notifications: ScheduleLocalNotificationDetails[]) => void,
);
```

取得當前已排程的本地通知清單。

**參數：**

| Name     | Type     | Required | Description                                                                  |
| -------- | -------- | -------- | ---------------------------------------------------------------------------- |
| callback | function | Yes      | Function which processes an array of objects describing local notifications. |

---

### `addEventListener()`

```tsx
static addEventListener(
  type: PushNotificationEventName,
  handler:
    | ((notification: PushNotification) => void)
    | ((deviceToken: string) => void)
    | ((error: {message: string; code: number; details: any}) => void),
);
```

為通知事件附加監聽器，包括本地通知、遠端通知及通知註冊結果。

**參數：**

| Name    | Type     | Required | Description                         |
| ------- | -------- | -------- | ----------------------------------- |
| type    | string   | Yes      | Event type to listen to. See below. |
| handler | function | Yes      | Listener.                           |

有效的事件類型包括：

- `notification`：當收到遠端通知時觸發。處理程序將以 `PushNotificationIOS` 的實例調用。此事件會處理在前景接收的通知，或從背景點擊開啟應用程式的通知。
- `localNotification`：當收到本地通知時觸發。處理程序將以 `PushNotificationIOS` 的實例調用。此事件會處理在前景接收的通知，或從背景點擊開啟應用程式的通知。
- `register`：當用戶成功註冊遠端通知時觸發。處理程序將以代表設備令牌的十六進制字符串調用。
- `registrationError`：當用戶註冊遠端通知失敗時觸發。通常由於 APNS 問題或設備是模擬器導致。處理程序將以 `{message: string, code: number, details: any}` 調用。

---

### `removeEventListener()`

```tsx
static removeEventListener(
  type: PushNotificationEventName,
);
```

移除事件監聽器。請在 `componentWillUnmount` 中調用此方法以避免記憶體洩漏。

**參數：**

| Name | Type   | Required | Description                                       |
| ---- | ------ | -------- | ------------------------------------------------- |
| type | string | Yes      | Event type. See `addEventListener()` for options. |

---

### `requestPermissions()`

```tsx
static requestPermissions(permissions?: PushNotificationPermissions[]);
```

向 iOS 請求通知權限，並向用戶顯示對話框。預設情況下，此方法會請求所有通知權限，但您可以選擇性地指定要請求的權限。支援的權限包括：

- `alert`
- `badge`
- `sound`

如果提供了一個映射對象給此方法，則僅會請求值為真值的權限。

此方法返回一個 Promise，當用戶接受或拒絕請求時，或權限先前已被拒絕時，該 Promise 會解析。Promise 解析後會返回請求完成後的權限狀態。

**參數：**

| Name        | Type  | Required | Description            |
| ----------- | ----- | -------- | ---------------------- |
| permissions | array | No       | alert, badge, or sound |

---

### `abandonPermissions()`

```tsx
static abandonPermissions();
```

取消註冊所有透過 Apple Push Notification 服務接收的遠端通知。

您應僅在極少數情況下調用此方法，例如當應用程式的新版本移除了對所有類型遠端通知的支援時。用戶可以透過設定應用程式暫時阻止應用程式接收遠端通知。透過此方法取消註冊的應用程式隨時可以重新註冊。

---

### `checkPermissions()`

```tsx
static checkPermissions(
  callback: (permissions: PushNotificationPermissions) => void,
);
```

檢查當前啟用了哪些推送權限。

**參數：**

| Name     | Type     | Required | Description |
| -------- | -------- | -------- | ----------- |
| callback | function | Yes      | See below.  |

`callback` 將以一個 `permissions` 對象調用：

- `alert: boolean`
- `badge: boolean`
- `sound: boolean`

---

### `getInitialNotification()`

```tsx
static getInitialNotification(): Promise<PushNotification | null>;
```

此方法返回一個 Promise。如果應用程式是透過點擊推送通知啟動的，則該 Promise 會解析為一個類型為 `PushNotificationIOS` 的對象，代表被點擊的通知。否則，它會解析為 `null`。

---

### `getAuthorizationStatus()`

```tsx
static getAuthorizationStatus(): Promise<number>;
```

此方法返回一個 Promise，解析為當前的通知授權狀態。可能的數值請參閱 [UNAuthorizationStatus](https://developer.apple.com/documentation/usernotifications/unauthorizationstatus?language=objc)。

---

### `finish()`

```tsx
finish(result: string);
```

此方法適用於透過 [`application:didReceiveRemoteNotification:fetchCompletionHandler:`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application?language=objc) 接收的遠端通知。但此方法已被 `UNUserNotificationCenterDelegate` 取代，若同時實作了 `application:didReceiveRemoteNotification:fetchCompletionHandler:` 和 `UNUserNotificationCenterDelegate` 的新處理程序，此方法將不再被呼叫。

若因某些原因仍需依賴 `application:didReceiveRemoteNotification:fetchCompletionHandler:`，則需在 iOS 端設定事件處理：

```objectivec
- (void)           application:(UIApplication *)application
  didReceiveRemoteNotification:(NSDictionary *)userInfo
        fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
{
  [RCTPushNotificationManager didReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
}
```

在 JS 端處理完通知後，呼叫 `finish()` 以執行原生完成處理程序。呼叫此區塊時，請傳入最能描述操作結果的 fetch 結果值。可能的數值清單請參見 `PushNotificationIOS.FetchResult`。

若使用 `application:didReceiveRemoteNotification:fetchCompletionHandler:`，則「必須」呼叫此處理程序，且應盡快執行。詳見[官方文件](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application?language=objc)。

---

### `getMessage()`

```tsx
getMessage(): string | Object;
```

為 `getAlert` 的別名，用於取得通知的主要訊息字串。

---

### `getSound()`

```tsx
getSound(): string;
```

從 `aps` 物件取得聲音字串。本地通知將回傳 `null`。

---

### `getCategory()`

```tsx
getCategory(): string;
```

從 `aps` 物件取得分類字串。

---

### `getAlert()`

```tsx
getAlert(): string | Object;
```

從 `aps` 物件取得通知的主要訊息。另請參見別名：`getMessage()`。

---

### `getContentAvailable()`

```tsx
getContentAvailable(): number;
```

從 `aps` 物件取得 content-available 數值。

---

### `getBadgeCount()`

```tsx
getBadgeCount(): number;
```

從 `aps` 物件取得徽章計數。

---

### `getData()`

```tsx
getData(): Object;
```

取得通知上的資料物件。

---

### `getThreadID()`

```tsx
getThreadID();
```

取得通知上的執行緒 ID。