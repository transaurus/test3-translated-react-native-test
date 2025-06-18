---
id: pushnotificationios
title: '🚧 PushNotificationIOS'
---

> **Deprecated.** Use the [community package](https://github.com/react-native-push-notification/ios) instead.

<div className="banner-native-code-required">
  <h3>Projects with Native Code Only</h3>
  <p>The following section only applies to projects with native code exposed. If you are using the managed Expo workflow, see the guide on <a href="https://docs.expo.dev/versions/latest/sdk/notifications/">Notifications</a> in the Expo documentation for the appropriate alternative.</p>
</div>

Handle notifications for your app, including scheduling and permissions.

---

## Getting Started

To enable push notifications, [configure your notifications with Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server) and your server-side system.

Then, [enable remote notifications](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/pushing_background_updates_to_your_app#2980038) in your project. This will automatically enable the required settings.

### Enable support for `register` events

In your `AppDelegate.m`, add:

```objectivec
#import <React/RCTPushNotificationManager.h>
```

Then implement the following in order to handle remote notification registration events:

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

### Handle notifications

You'll need to implement `UNUserNotificationCenterDelegate` in your `AppDelegate`:

```objectivec
#import <UserNotifications/UserNotifications.h>

@interface YourAppDelegate () <UNUserNotificationCenterDelegate>
@end
```

Set the delegate on app launch:

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  ...
  UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
  center.delegate = self;

  return YES;
}
```

#### Foreground notifications

Implement `userNotificationCenter:willPresentNotification:withCompletionHandler:` to handle notifications that arrive when the app is in the foreground. Use the completionHandler to determine if the notification will be shown to the user and notify `RCTPushNotificationManager` accordingly:

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

#### Background notifications

Implement `userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:` to handle when a notification is tapped, typically called for background notifications which the user taps to open the app. However, if you had set foreground notifications to be shown in `userNotificationCenter:willPresentNotification:withCompletionHandler:`, this method will also be invoked on foreground notifications when tapped. In this case, you should only notify `RCTPushNotificationManager` in one of these callbacks.

If the tapped notification resulted in app launch, call `setInitialNotification:`. If the notification was not previously handled by `userNotificationCenter:willPresentNotification:withCompletionHandler:`, call `didReceiveNotification:` as well:

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

# Reference

## Methods

### `presentLocalNotification()`

```tsx
static presentLocalNotification(details: PresentLocalNotificationDetails);
```

Schedules a local notification for immediate presentation.

**Parameters:**

| Name    | Type   | Required | Description |
| ------- | ------ | -------- | ----------- |
| details | object | Yes      | See below.  |

`details` is an object containing:

- `alertTitle` : The text displayed as the title of the notification alert.
- `alertBody` : The message displayed in the notification alert.
- `userInfo` : An object containing additional notification data (optional).
- `category` : The category of this notification, required for actionable notifications (optional). e.g. notifications with additional actions such as Reply or Like.
- `applicationIconBadgeNumber` The number to display as the app's icon badge. The default value of this property is 0, which means that no badge is displayed (optional).
- `isSilent` : If true, the notification will appear without sound (optional).
- `soundName` : The sound played when the notification is fired (optional).
- `alertAction` : DEPRECATED. This was used for iOS's legacy UILocalNotification.

---

### `scheduleLocalNotification()`

```tsx
static scheduleLocalNotification(details: ScheduleLocalNotificationDetails);
```

Schedules a local notification for future presentation.

**Parameters:**

| Name    | Type   | Required | Description |
| ------- | ------ | -------- | ----------- |
| details | object | Yes      | See below.  |

`details` is an object containing:

- `alertTitle` : 顯示為通知警示標題的文字。
- `alertBody` : 顯示在通知警示中的訊息內容。
- `fireDate` : 通知觸發的具體時間。排程通知時可使用 `fireDate` 或 `fireIntervalSeconds`，其中 `fireDate` 優先級更高。
- `fireIntervalSeconds` : 從當前時間算起，多少秒後顯示通知。
- `userInfo` : 包含額外通知資料的物件（可選）。
- `category` : 此通知的分類，用於可操作通知（可選）。例如包含「回覆」或「讚」等附加動作的通知。
- `applicationIconBadgeNumber` : 顯示在應用程式圖示上的徽章數字。預設值為 0，表示不顯示徽章（可選）。
- `isSilent` : 若為 true，通知將以靜音方式顯示（可選）。
- `soundName` : 通知觸發時播放的音效（可選）。
- `alertAction` : 已棄用。此屬性用於 iOS 舊版 UILocalNotification。
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

取得當前顯示在通知中心內的應用程式通知清單。

**參數：**

| Name     | Type     | Required | Description                                                  |
| -------- | -------- | -------- | ------------------------------------------------------------ |
| callback | function | Yes      | Function which receives an array of delivered notifications. |

已送達的通知為包含以下屬性的物件：

- `identifier` : 此通知的唯一識別碼。
- `title` : 此通知的標題。
- `body` : 此通知的內容正文。
- `category` : 此通知的分類（可選）。
- `userInfo` : 包含額外通知資料的物件（可選）。
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

設定主畫面應用程式圖示上顯示的徽章數字。

**參數：**

| Name   | Type   | Required | Description                    |
| ------ | ------ | -------- | ------------------------------ |
| number | number | Yes      | Badge number for the app icon. |

---

### `getApplicationIconBadgeNumber()`

```tsx
static getApplicationIconBadgeNumber(callback: (num: number) => void);
```

取得主畫面應用程式圖示上當前的徽章數字。

**參數：**

| Name     | Type     | Required | Description                                        |
| -------- | -------- | -------- | -------------------------------------------------- |
| callback | function | Yes      | Function which processes the current badge number. |

---

### `cancelLocalNotifications()`

```tsx
static cancelLocalNotifications(userInfo: Object);
```

取消所有符合指定 `userInfo` 條件的已排程本地通知。

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

**Parameters:**

| Name    | Type     | Required | Description                         |
| ------- | -------- | -------- | ----------------------------------- |
| type    | string   | Yes      | Event type to listen to. See below. |
| handler | function | Yes      | Listener.                           |

Valid events types include:

- `notification` : Fired when a remote notification is received. The handler will be invoked with an instance of `PushNotificationIOS`. This will handle notifications that arrive in the foreground or were tapped to open the app from the background.
- `localNotification` : Fired when a local notification is received. The handler will be invoked with an instance of `PushNotificationIOS`. This will handle notifications that arrive in the foreground or were tapped to open the app from the background.
- `register`: Fired when the user registers successfully for remote notifications. The handler will be invoked with a hex string representing the deviceToken.
- `registrationError`: Fired when the user fails to register for remote notifications. Typically occurs due to APNS issues or if the device is a simulator. The handler will be invoked with `{message: string, code: number, details: any}`.

---

### `removeEventListener()`

```tsx
static removeEventListener(
  type: PushNotificationEventName,
);
```

Removes the event listener. Do this in `componentWillUnmount` to prevent memory leaks.

**Parameters:**

| Name | Type   | Required | Description                                       |
| ---- | ------ | -------- | ------------------------------------------------- |
| type | string | Yes      | Event type. See `addEventListener()` for options. |

---

### `requestPermissions()`

```tsx
static requestPermissions(permissions?: PushNotificationPermissions[]);
```

Requests notification permissions from iOS, prompting the user with a dialog box. By default, this will request all notification permissions, but you can optionally specify which permissions to request. The following permissions are supported:

- `alert`
- `badge`
- `sound`

If a map is provided to the method, only the permissions with truthy values will be requested.

This method returns a promise that will resolve when the user accepts or rejects the request, or if the permissions were previously rejected. The promise resolves to the state of the permissions after the request has been completed.

**Parameters:**

| Name        | Type  | Required | Description            |
| ----------- | ----- | -------- | ---------------------- |
| permissions | array | No       | alert, badge, or sound |

---

### `abandonPermissions()`

```tsx
static abandonPermissions();
```

Unregister for all remote notifications received via Apple Push Notification service.

You should call this method in rare circumstances only, such as when a new version of the app removes support for all types of remote notifications. Users can temporarily prevent apps from receiving remote notifications through the Settings app. Apps unregistered through this method can always re-register.

---

### `checkPermissions()`

```tsx
static checkPermissions(
  callback: (permissions: PushNotificationPermissions) => void,
);
```

Check which push permissions are currently enabled.

**Parameters:**

| Name     | Type     | Required | Description |
| -------- | -------- | -------- | ----------- |
| callback | function | Yes      | See below.  |

`callback` will be invoked with a `permissions` object:

- `alert: boolean`
- `badge: boolean`
- `sound: boolean`

---

### `getInitialNotification()`

```tsx
static getInitialNotification(): Promise<PushNotification | null>;
```

This method returns a promise. If the app was launched by a push notification, this promise resolves to an object of type `PushNotificationIOS` for the notification that was tapped. Otherwise, it resolves to `null`.

---

### `getAuthorizationStatus()`

```tsx
static getAuthorizationStatus(): Promise<number>;
```

This method returns a promise that resolves to the current notification authorization status. See [UNAuthorizationStatus](https://developer.apple.com/documentation/usernotifications/unauthorizationstatus?language=objc) for possible values.

---

### `finish()`

```tsx
finish(result: string);
```

此方法適用於透過 [`application:didReceiveRemoteNotification:fetchCompletionHandler:`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application?language=objc) 接收的遠端通知。然而，此方法已被 `UNUserNotificationCenterDelegate` 取代，若同時實作了 `application:didReceiveRemoteNotification:fetchCompletionHandler:` 和 `UNUserNotificationCenterDelegate` 的新處理程序，此方法將不再被呼叫。

若因某些原因仍需依賴 `application:didReceiveRemoteNotification:fetchCompletionHandler:`，您需在 iOS 端設定事件處理：

```objectivec
- (void)           application:(UIApplication *)application
  didReceiveRemoteNotification:(NSDictionary *)userInfo
        fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
{
  [RCTPushNotificationManager didReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
}
```

在 JS 端處理完通知後，呼叫 `finish()` 以執行原生完成處理程序。呼叫此區塊時，請傳入最能描述操作結果的 fetch result 值。可能的數值清單請參見 `PushNotificationIOS.FetchResult`。

若使用 `application:didReceiveRemoteNotification:fetchCompletionHandler:`，您「必須」呼叫此處理程序，且應盡快執行。詳見 [官方文件](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application?language=objc)。

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

從 `aps` 物件取得聲音字串。本地通知此值為 `null`。

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

從 `aps` 物件取得徽章計數數值。

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