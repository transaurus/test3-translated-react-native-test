---
id: pushnotificationios
title: '🚧 PushNotificationIOS'
---

> **已棄用。** 請改用 [社群套件](https://reactnative.directory/?search=push+notification) 之一。

<div className="banner-native-code-required">
  <h3>Projects with Native Code Only</h3>
  <p>The following section only applies to projects with native code exposed. If you are using the managed Expo workflow, see the guide on <a href="https://docs.expo.dev/versions/latest/sdk/notifications/">Notifications</a> in the Expo documentation for the appropriate alternative.</p>
</div>

處理應用程式的推播通知，包括權限處理和圖示徽章數字。

要開始使用，請先[向 Apple 設定您的通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)以及您的伺服器端系統。

React Native 版本 0.60.0 或更高：

- 0.60.0 版本的自動連結功能會為您處理連結！

React Native 版本低於 0.60.0：

將 PushNotificationIOS 函式庫加入您的 Podfile：./ios/Podfile

- CocoaPods：

  - 將 PushNotificationIOS 函式庫加入您的 Podfile：./ios/Podfile

    ```ruby
    target 'myAwesomeApp' do
      # Pods for myAwesomeApp
      pod 'React-RCTPushNotification', :path => '../node_modules/react-native/Libraries/PushNotificationIOS'
    end
    ```

- [手動連結](linking-libraries-ios.md#manual-linking) PushNotificationIOS 函式庫：
  - 將以下內容加入您的專案：`node_modules/react-native/Libraries/PushNotificationIOS/RCTPushNotification.xcodeproj`
  - 將以下內容加入 `Link Binary With Libraries`：`libRCTPushNotification.a`

最後，為了啟用對 `notification` 和 `register` 事件的支援，您需要擴充您的 AppDelegate。

在您的 `AppDelegate.m` 頂部：

`#import <React/RCTPushNotificationManager.h>`

然後在您的 AppDelegate 實作中加入以下內容：

```objectivec
 // Required to register for notifications
 - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
 {
  [RCTPushNotificationManager didRegisterUserNotificationSettings:notificationSettings];
 }
 // Required for the register event.
 - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
 {
  [RCTPushNotificationManager didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
 }
 // Required for the notification event. You must call the completion handler after handling the remote notification.
 - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
                                                        fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
 {
   [RCTPushNotificationManager didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
 }
 // Required for the registrationError event.
 - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
 {
  [RCTPushNotificationManager didFailToRegisterForRemoteNotificationsWithError:error];
 }
 // Required for the localNotification event.
 - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
 {
  [RCTPushNotificationManager didReceiveLocalNotification:notification];
 }
```

要在前景顯示通知（從 iOS 10 開始可用），請加入以下幾行：

在您的 `AppDelegate.m` 頂部：

`#import <UserNotifications/UserNotifications.h>`

然後在您的 AppDelegate 實作中加入以下內容：

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  ...
  // Define UNUserNotificationCenter
  UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
  center.delegate = self;

  return YES;
}

//Called when a notification is delivered to a foreground app.
-(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
{
  completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
}
```

接著啟用背景模式/遠端通知以正確使用遠端通知。最簡單的方法是透過專案設定。導航至 Targets -> 您的應用程式 -> Capabilities -> Background Modes 並勾選 Remote notifications。這將自動啟用所需的設定。

---

# 參考

## 方法

### `presentLocalNotification()`

```jsx
PushNotificationIOS.presentLocalNotification(details);
```

排程本地通知以立即顯示。

**參數：**

| Name    | Type   | Required | Description |
| ------- | ------ | -------- | ----------- |
| details | object | Yes      | See below.  |

details 是一個包含以下內容的物件：

- `alertBody` : 通知警示中顯示的訊息。
- `alertAction` : 可操作通知下方顯示的「動作」。預設為「view」。請注意，Apple 從 iOS 10 開始不再顯示此內容。
- `alertTitle` : 通知警示中顯示的標題文字。
- `soundName` : 通知觸發時播放的聲音（可選）。
- `isSilent` : 如果為 true，通知將無聲顯示（可選）。
- `category` : 此通知的類別，用於可操作通知（可選）。
- `userInfo` : 包含額外通知資料的物件（可選）。
- `applicationIconBadgeNumber` : 顯示在應用程式圖示上的徽章數字。此屬性的預設值為 0，表示不顯示徽章（可選）。

---

### `scheduleLocalNotification()`

```jsx
PushNotificationIOS.scheduleLocalNotification(details);
```

排程本地通知以供未來顯示。

**參數：**

| Name    | Type   | Required | Description |
| ------- | ------ | -------- | ----------- |
| details | object | Yes      | See below.  |

details 是一個包含以下屬性的物件：

- `fireDate` : 系統應傳送通知的日期和時間。
- `alertTitle` : 顯示為通知警示標題的文字。
- `alertBody` : 顯示在通知警示中的訊息。
- `alertAction` : 顯示在可操作通知下方的「動作」。預設為「檢視」。注意：Apple 在 iOS 10 及以上版本不再顯示此項目。
- `soundName` : 觸發通知時播放的聲音（可選）。
- `isSilent` : 若為 true，通知將無聲顯示（可選）。
- `category` : 此通知的類別，為可操作通知的必要項目（可選）。
- `userInfo` : 包含額外通知資料的物件（可選）。
- `applicationIconBadgeNumber` : 顯示在應用程式圖示上的徽章數字。設為 0 可移除徽章（可選）。
- `repeatInterval` : 以字串形式指定的重複間隔。可能值：`minute`、`hour`、`day`、`week`、`month`、`year`（可選）。

---

### `cancelAllLocalNotifications()`

```jsx
PushNotificationIOS.cancelAllLocalNotifications();
```

取消所有已排程的本地通知

---

### `removeAllDeliveredNotifications()`

```jsx
PushNotificationIOS.removeAllDeliveredNotifications();
```

從通知中心移除所有已傳送的通知

---

### `getDeliveredNotifications()`

```jsx
PushNotificationIOS.getDeliveredNotifications(callback);
```

提供目前仍顯示在通知中心內的應用程式通知清單

**參數：**

| Name     | Type     | Required | Description                                                 |
| -------- | -------- | -------- | ----------------------------------------------------------- |
| callback | function | Yes      | Function which receive an array of delivered notifications. |

已傳送的通知是一個包含以下屬性的物件：

- `identifier` : 此通知的識別符。
- `title` : 此通知的標題。
- `body` : 此通知的內容。
- `category` : 此通知的類別（可選）。
- `userInfo` : 包含額外通知資料的物件（可選）。
- `thread-id` : 此通知的執行緒識別符（若存在）。

---

### `removeDeliveredNotifications()`

```jsx
PushNotificationIOS.removeDeliveredNotifications(identifiers);
```

從通知中心移除指定的通知

**參數：**

| Name        | Type  | Required | Description                        |
| ----------- | ----- | -------- | ---------------------------------- |
| identifiers | array | Yes      | Array of notification identifiers. |

---

### `setApplicationIconBadgeNumber()`

```jsx
PushNotificationIOS.setApplicationIconBadgeNumber(number);
```

設定主畫面應用程式圖示上的徽章數字

**參數：**

| Name   | Type   | Required | Description                    |
| ------ | ------ | -------- | ------------------------------ |
| number | number | Yes      | Badge number for the app icon. |

---

### `getApplicationIconBadgeNumber()`

```jsx
PushNotificationIOS.getApplicationIconBadgeNumber(callback);
```

取得主畫面應用程式圖示上當前的徽章數字

**參數：**

| Name     | Type     | Required | Description                                              |
| -------- | -------- | -------- | -------------------------------------------------------- |
| callback | function | Yes      | A function that will be passed the current badge number. |

---

### `cancelLocalNotifications()`

```jsx
PushNotificationIOS.cancelLocalNotifications(userInfo);
```

取消本地通知。

可選地限制取消的通知範圍，僅取消那些 `userInfo` 欄位與 `userInfo` 參數對應欄位相符的通知。

**參數：**

| Name     | Type   | Required | Description |
| -------- | ------ | -------- | ----------- |
| userInfo | object | No       |             |

---

### `getScheduledLocalNotifications()`

```jsx
PushNotificationIOS.getScheduledLocalNotifications(callback);
```

取得當前已排程的本地通知。

**參數：**

| Name     | Type     | Required | Description                                                                        |
| -------- | -------- | -------- | ---------------------------------------------------------------------------------- |
| callback | function | Yes      | A function that will be passed an array of objects describing local notifications. |

---

### `addEventListener()`

```jsx
PushNotificationIOS.addEventListener(type, handler);
```

在應用程式於前景或背景運行時，為遠端或本地通知事件附加監聽器。

**參數：**

| Name    | Type     | Required | Description |
| ------- | -------- | -------- | ----------- |
| type    | string   | Yes      | Event type. |
| handler | function | Yes      | Listener.   |

有效事件包括：

- `notification`：當收到遠端通知時觸發。處理程序將以 `PushNotificationIOS` 的實例調用。
- `localNotification`：當收到本地通知時觸發。處理程序將以 `PushNotificationIOS` 的實例調用。
- `register`：當用戶註冊遠端通知時觸發。處理程序將以代表設備令牌的十六進制字符串調用。
- `registrationError`：當用戶註冊遠端通知失敗時觸發。通常發生在 APNS 出現問題或設備是模擬器時。處理程序將以 `{message: string, code: number, details: any}` 調用。

---

### `removeEventListener()`

```jsx
PushNotificationIOS.removeEventListener(type, handler);
```

移除事件監聽器。在 `componentWillUnmount` 中執行此操作以防止記憶體洩漏。

**參數：**

| Name    | Type     | Required | Description |
| ------- | -------- | -------- | ----------- |
| type    | string   | Yes      | Event type. |
| handler | function | Yes      | Listener.   |

---

### `requestPermissions()`

```jsx
PushNotificationIOS.requestPermissions([permissions]);
```

向 iOS 請求通知權限，彈出用戶對話框。默認情況下，它會請求所有通知權限，但可以通過傳遞請求權限的映射來請求其中的一部分。支援的權限包括：

- `alert`
- `badge`
- `sound`

如果向方法提供了映射，則只會請求值為真值的權限。

此方法返回一個 Promise，當用戶接受、拒絕或權限先前已被拒絕時，該 Promise 會解析。Promise 解析為權限的當前狀態。

**參數：**

| Name        | Type  | Required | Description            |
| ----------- | ----- | -------- | ---------------------- |
| permissions | array | No       | alert, badge, or sound |

---

### `abandonPermissions()`

```jsx
PushNotificationIOS.abandonPermissions();
```

取消註冊通過 Apple Push Notification 服務接收的所有遠端通知。

您應該僅在極少數情況下調用此方法，例如當應用程式的新版本移除對所有類型遠端通知的支援時。用戶可以通過設置應用程式的通知部分暫時阻止應用程式接收遠端通知。通過此方法取消註冊的應用程式始終可以重新註冊。

---

### `checkPermissions()`

```jsx
PushNotificationIOS.checkPermissions(callback);
```

查看當前啟用的推送權限。

**參數：**

| Name     | Type     | Required | Description |
| -------- | -------- | -------- | ----------- |
| callback | function | Yes      | See below.  |

`callback` 將以 `permissions` 對象調用：

- `alert: boolean`
- `badge: boolean`
- `sound: boolean`

---

### `getInitialNotification()`

```jsx
PushNotificationIOS.getInitialNotification();
```

此方法返回一個 Promise。如果應用程式是由推送通知啟動的，則此 Promise 解析為類型為 `PushNotificationIOS` 的對象。否則，解析為 `null`。

---

### `constructor()`

```jsx
constructor(nativeNotif);
```

您永遠不需要自己實例化 `PushNotificationIOS`。監聽 `notification` 事件並調用 `getInitialNotification` 就足夠了。

---

### `finish()`

```jsx
finish(fetchResult);
```

此方法適用於通過以下方式接收的遠端通知：`application:didReceiveRemoteNotification:fetchCompletionHandler:` https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application?language=objc

呼叫此方法以在遠端通知處理完成時執行。呼叫此區塊時，需傳入最能描述操作結果的擷取結果值。您「必須」呼叫此處理程序，並應盡快執行。有關可能值的列表，請參閱 `PushNotificationIOS.FetchResult`。

若未呼叫此方法，您的背景遠端通知可能會被節流，詳細資訊請參閱上述文件連結。

---

### `getMessage()`

```jsx
getMessage();
```

`getAlert` 的別名，用於取得通知的主要訊息字串

---

### `getSound()`

```jsx
getSound();
```

從 `aps` 物件中取得聲音字串

---

### `getCategory()`

```jsx
getCategory();
```

從 `aps` 物件中取得分類字串

---

### `getAlert()`

```jsx
getAlert();
```

從 `aps` 物件中取得通知的主要訊息

---

### `getContentAvailable()`

```jsx
getContentAvailable();
```

從 `aps` 物件中取得 content-available 數值

---

### `getBadgeCount()`

```jsx
getBadgeCount();
```

從 `aps` 物件中取得徽章計數

---

### `getData()`

```jsx
getData();
```

取得通知上的資料物件

---

### `getThreadID()`

```jsx
getThreadID();
```

取得通知上的執行緒 ID