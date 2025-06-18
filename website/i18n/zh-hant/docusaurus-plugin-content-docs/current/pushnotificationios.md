---
id: pushnotificationios
title: '🚧 PushNotificationIOS'
---

> **已棄用。** 請改用 [社群套件](https://github.com/react-native-push-notification/ios)。

<div className="banner-native-code-required">
  <h3>Projects with Native Code Only</h3>
  <p>The following section only applies to projects with native code exposed. If you are using the managed Expo workflow, see the guide on <a href="https://docs.expo.dev/versions/latest/sdk/notifications/">Notifications</a> in the Expo documentation for the appropriate alternative.</p>
</div>

處理應用程式的通知，包括排程和權限管理。

---

## 開始使用

要啟用推播通知，請先[向 Apple 設定您的通知服務](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server)並配置您的伺服器端系統。

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

實作 `userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:` 以處理使用者點擊通知的情況，通常用於使用者點擊背景通知開啟應用程式時。但如果您在前景通知處理方法中設定要顯示通知，則此方法也會在使用者點擊前景通知時被呼叫。在這種情況下，您應僅在其中一個回調中通知 `RCTPushNotificationManager`。

若點擊通知導致應用程式啟動，請呼叫 `setInitialNotification:`。如果通知未被 `userNotificationCenter:willPresentNotification:withCompletionHandler:` 處理過，也需呼叫 `didReceiveNotification:`：

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

# 參考文件

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
- `category` : 此通知的類別，用於可操作通知（選填）。例如包含回覆或按讚等額外操作的通知。
- `applicationIconBadgeNumber` : 顯示在應用程式圖示上的徽章數字。預設值為 0，表示不顯示徽章（選填）。
- `isSilent` : 若為 true，通知將無聲顯示（選填）。
- `soundName` : 通知觸發時播放的音效（選填）。
- `alertAction` : 已棄用。此屬性用於 iOS 舊版 UILocalNotification。

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

- `alertTitle` : 顯示為通知警示標題的文字。
- `alertBody` : 顯示在通知警示中的訊息內容。
- `fireDate` : 通知觸發的具體時間。排程通知可使用 `fireDate` 或 `fireIntervalSeconds`，其中 `fireDate` 優先級較高。
- `fireIntervalSeconds` : 從現在起多少秒後顯示通知。
- `userInfo` : 包含額外通知資料的物件（選填）。
- `category` : 此通知的分類，需用於可操作通知（選填）。例如包含回覆或點讚等附加操作的通知。
- `applicationIconBadgeNumber` : 顯示在應用程式圖示上的徽章數字。預設值為 0，表示不顯示徽章（選填）。
- `isSilent` : 若為 true，通知將以靜音方式顯示（選填）。
- `soundName` : 通知觸發時播放的音效（選填）。
- `alertAction` : 已棄用。此參數用於 iOS 舊版 UILocalNotification。
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

提供當前顯示在通知中心內的應用程式通知清單。

**參數：**

| Name     | Type     | Required | Description                                                  |
| -------- | -------- | -------- | ------------------------------------------------------------ |
| callback | function | Yes      | Function which receives an array of delivered notifications. |

已送達通知為包含以下屬性的物件：

- `identifier` : 此通知的唯一識別碼。
- `title` : 此通知的標題。
- `body` : 此通知的內容。
- `category` : 此通知的分類（選填）。
- `userInfo` : 包含額外通知資料的物件（選填）。
- `thread-id` : 此通知的執行緒識別碼（若存在）。

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

取得主畫面應用程式圖示當前的徽章數字。

**參數：**

| Name     | Type     | Required | Description                                        |
| -------- | -------- | -------- | -------------------------------------------------- |
| callback | function | Yes      | Function which processes the current badge number. |

---

### `cancelLocalNotifications()`

```tsx
static cancelLocalNotifications(userInfo: Object);
```

取消所有符合提供之 `userInfo` 欄位條件的已排程本地通知。

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

- `notification`：當收到遠端通知時觸發。處理程序將以 `PushNotificationIOS` 的實例調用。此事件會處理在前景收到的通知或從背景點擊開啟應用程式的通知。
- `localNotification`：當收到本地通知時觸發。處理程序將以 `PushNotificationIOS` 的實例調用。此事件會處理在前景收到的通知或從背景點擊開啟應用程式的通知。
- `register`：當用戶成功註冊遠端通知時觸發。處理程序將以代表 deviceToken 的十六進制字符串調用。
- `registrationError`：當用戶註冊遠端通知失敗時觸發。通常由於 APNS 問題或設備是模擬器而發生。處理程序將以 `{message: string, code: number, details: any}` 調用。

---

### `removeEventListener()`

```tsx
static removeEventListener(
  type: PushNotificationEventName,
);
```

移除事件監聽器。在 `componentWillUnmount` 中執行此操作以防止記憶體洩漏。

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

如果提供了一個映射（map）給此方法，則只有值為真（truthy）的權限會被請求。

此方法返回一個 Promise，當用戶接受或拒絕請求時，或權限先前已被拒絕時，該 Promise 會解析。Promise 解析為請求完成後的權限狀態。

**參數：**

| Name        | Type  | Required | Description            |
| ----------- | ----- | -------- | ---------------------- |
| permissions | array | No       | alert, badge, or sound |

---

### `abandonPermissions()`

```tsx
static abandonPermissions();
```

取消註冊所有通過 Apple Push Notification 服務接收的遠端通知。

您應該僅在極少數情況下調用此方法，例如當應用程式的新版本移除對所有類型遠端通知的支援時。用戶可以通過「設定」應用暫時阻止應用接收遠端通知。通過此方法取消註冊的應用程式隨時可以重新註冊。

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

`callback` 將以一個 `permissions` 物件調用：

- `alert: boolean`
- `badge: boolean`
- `sound: boolean`

---

### `getInitialNotification()`

```tsx
static getInitialNotification(): Promise<PushNotification | null>;
```

此方法返回一個 Promise。如果應用程式是由推送通知啟動的，則此 Promise 解析為被點擊通知的 `PushNotificationIOS` 類型物件。否則，解析為 `null`。

---

### `getAuthorizationStatus()`

```tsx
static getAuthorizationStatus(): Promise<number>;
```

此方法返回一個 Promise，解析為當前通知授權狀態。可能的參見 [UNAuthorizationStatus](https://developer.apple.com/documentation/usernotifications/unauthorizationstatus?language=objc)。

---

### `finish()`

```tsx
finish(result: string);
```

This method is available for remote notifications that have been received via [`application:didReceiveRemoteNotification:fetchCompletionHandler:`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application?language=objc). However, this is superseded by `UNUserNotificationCenterDelegate` and will no longer be invoked if both `application:didReceiveRemoteNotification:fetchCompletionHandler:` and the newer handlers from `UNUserNotificationCenterDelegate` are implemented.

If for some reason you're still relying on `application:didReceiveRemoteNotification:fetchCompletionHandler:`, you'll need to set up event handling on the iOS side:

```objectivec
- (void)           application:(UIApplication *)application
  didReceiveRemoteNotification:(NSDictionary *)userInfo
        fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
{
  [RCTPushNotificationManager didReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
}
```

Call `finish()` to execute the native completion handlers once you're done handling the notification on the JS side. When calling this block, pass in the fetch result value that best describes the results of your operation. For a list of possible values, see `PushNotificationIOS.FetchResult`.

If you're using `application:didReceiveRemoteNotification:fetchCompletionHandler:`, you _must_ call this handler and should do so as soon as possible. See the [official documentation](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application?language=objc) for more details.

---

### `getMessage()`

```tsx
getMessage(): string | Object;
```

An alias for `getAlert` to get the notification's main message string.

---

### `getSound()`

```tsx
getSound(): string;
```

Gets the sound string from the `aps` object. This will be `null` for local notifications.

---

### `getCategory()`

```tsx
getCategory(): string;
```

Gets the category string from the `aps` object.

---

### `getAlert()`

```tsx
getAlert(): string | Object;
```

Gets the notification's main message from the `aps` object. Also see the alias: `getMessage()`.

---

### `getContentAvailable()`

```tsx
getContentAvailable(): number;
```

Gets the content-available number from the `aps` object.

---

### `getBadgeCount()`

```tsx
getBadgeCount(): number;
```

Gets the badge count number from the `aps` object.

---

### `getData()`

```tsx
getData(): Object;
```

Gets the data object on the notification.

---

### `getThreadID()`

```tsx
getThreadID();
```

Gets the thread ID on the notification.