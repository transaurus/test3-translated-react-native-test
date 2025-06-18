---
id: linking
title: Linking
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

`Linking` 提供了一個通用介面來處理傳入和傳出的應用程式連結。

每個連結 (URL) 都有一個 URL Scheme，某些網站以 `https://` 或 `http://` 為前綴，其中的 `http` 就是 URL Scheme。我們簡稱其為 scheme。

除了 `https`，您可能也熟悉 `mailto` scheme。當您開啟一個帶有 mailto scheme 的連結時，您的作業系統會開啟已安裝的郵件應用程式。同樣地，也有用於撥打電話和發送簡訊的 scheme。更多關於[內建 URL](#built-in-url-schemes) scheme 的資訊請參閱下文。

就像使用 mailto scheme 一樣，也可以透過自訂 URL scheme 連結到其他應用程式。例如，當您從 Slack 收到一封 **Magic Link** 電子郵件時，**Launch Slack** 按鈕是一個錨點標籤，其 href 看起來像這樣：`slack://secret/magic-login/other-secret`。與 Slack 類似，您可以告訴作業系統您想要處理一個自訂 scheme。當 Slack 應用程式開啟時，它會接收到用於開啟它的 URL。這通常被稱為深度連結。更多關於如何[獲取深度連結](#get-the-deep-link)到您的應用程式中的資訊請參閱下文。

自訂 URL scheme 並不是在行動裝置上開啟應用程式的唯一方式。例如，如果您想透過電子郵件發送一個在行動裝置上開啟的連結，使用自訂 URL scheme 並不理想，因為使用者可能在桌面上開啟電子郵件，而連結將無法運作。相反，您應該使用標準的 `https` 連結，例如 `https://www.myapp.io/records/1234546`。在行動裝置上，這些連結可以配置為開啟您的應用程式。在 Android 上，此功能稱為 **Deep Links**，而在 iOS 上則稱為 **Universal Links**。

### 內建 URL Schemes

如引言所述，每個平台上都有一些用於核心功能的 URL scheme。以下是一個非詳盡的清單，但涵蓋了最常用的 scheme。

| Scheme           | Description                                | iOS | Android |
| ---------------- | ------------------------------------------ | --- | ------- |
| `mailto`         | Open mail app, eg: mailto: support@expo.io | ✅  | ✅      |
| `tel`            | Open phone app, eg: tel:+123456789         | ✅  | ✅      |
| `sms`            | Open SMS app, eg: sms:+123456789           | ✅  | ✅      |
| `https` / `http` | Open web browser app, eg: https://expo.io  | ✅  | ✅      |

### 啟用深度連結

<div className="banner-native-code-required">
  <h3>Projects with Native Code Only</h3>
  <p>The following section only applies to projects with native code exposed. If you are using the managed Expo workflow, see the guide on <a href="https://docs.expo.dev/guides/linking/">Linking</a> in the Expo documentation for the appropriate alternative.</p>
</div>

如果您想在應用程式中啟用深度連結，請閱讀以下指南：

<Tabs groupId="syntax" queryString defaultValue={constants.defaultPlatform} values={constants.platforms}>
<TabItem value="android">

> For instructions on how to add support for deep linking on Android, refer to [Enabling Deep Links for App Content - Add Intent Filters for Your Deep Links](https://developer.android.com/training/app-indexing/deep-linking.html#adding-filters).

If you wish to receive the intent in an existing instance of MainActivity, you may set the `launchMode` of MainActivity to `singleTask` in `AndroidManifest.xml`. See [`<activity>`](https://developer.android.com/guide/topics/manifest/activity-element.html) documentation for more information.

```xml
<activity
  android:name=".MainActivity"
  android:launchMode="singleTask">
```

</TabItem>
<TabItem value="ios">

> **NOTE:** On iOS, you'll need to add the `LinkingIOS` folder into your header search paths as described in step 3 [here](linking-libraries-ios#step-3). If you also want to listen to incoming app links during your app's execution, you'll need to add the following lines to your `*AppDelegate.m`:

```objectivec
// iOS 9.x or newer
#import <React/RCTLinkingManager.h>

- (BOOL)application:(UIApplication *)application
   openURL:(NSURL *)url
   options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
  return [RCTLinkingManager application:application openURL:url options:options];
}
```

If you're targeting iOS 8.x or older, you can use the following code instead:

```objectivec
// iOS 8.x or older
#import <React/RCTLinkingManager.h>

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url
  sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
  return [RCTLinkingManager application:application openURL:url
                      sourceApplication:sourceApplication annotation:annotation];
}
```

If your app is using [Universal Links](https://developer.apple.com/ios/universal-links/), you'll need to add the following code as well:

```objectivec
- (BOOL)application:(UIApplication *)application continueUserActivity:(nonnull NSUserActivity *)userActivity
 restorationHandler:(nonnull void (^)(NSArray<id<UIUserActivityRestoring>> * _Nullable))restorationHandler
{
 return [RCTLinkingManager application:application
                  continueUserActivity:userActivity
                    restorationHandler:restorationHandler];
}
```

</TabItem>
</Tabs>

### 處理深度連結

有兩種方式可以處理開啟您應用程式的 URL。

#### 1. 如果應用程式已經開啟，應用程式會被置於前景並觸發一個 Linking 'url' 事件

您可以使用 `Linking.addEventListener('url', callback)` 來處理這些事件 — 它會呼叫 `callback({url})` 並傳入連結的 URL

#### 2. 如果應用程式尚未開啟，它會被開啟並且 URL 會作為 initialURL 傳入

您可以使用 `Linking.getInitialURL()` 來處理這些事件 — 它會回傳一個解析為 URL 的 Promise（如果有 URL 的話）。

---

## 範例

### 開啟連結和深度連結 (Universal Links)

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Linking%20Example&supportedPlatforms=ios,android&ext=js
import React, {useCallback} from 'react';
import {Alert, Button, Linking, StyleSheet, View} from 'react-native';

const supportedURL = 'https://google.com';

const unsupportedURL = 'slack://open?team=123456';

const OpenURLButton = ({url, children}) => {
  const handlePress = useCallback(async () => {
    // Checking if the link is supported for links with custom URL scheme.
    const supported = await Linking.canOpenURL(url);

    if (supported) {
      // Opening the link with some app, if the URL scheme is "http" the web link should be opened
      // by some browser in the mobile
      await Linking.openURL(url);
    } else {
      Alert.alert(`Don't know how to open this URL: ${url}`);
    }
  }, [url]);

  return <Button title={children} onPress={handlePress} />;
};

const App = () => {
  return (
    <View style={styles.container}>
      <OpenURLButton url={supportedURL}>Open Supported URL</OpenURLButton>
      <OpenURLButton url={unsupportedURL}>Open Unsupported URL</OpenURLButton>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Linking%20Example&supportedPlatforms=ios,android&ext=tsx
import React, {useCallback} from 'react';
import {Alert, Button, Linking, StyleSheet, View} from 'react-native';

const supportedURL = 'https://google.com';

const unsupportedURL = 'slack://open?team=123456';

type OpenURLButtonProps = {
  url: string;
  children: string;
};

const OpenURLButton = ({url, children}: OpenURLButtonProps) => {
  const handlePress = useCallback(async () => {
    // Checking if the link is supported for links with custom URL scheme.
    const supported = await Linking.canOpenURL(url);

    if (supported) {
      // Opening the link with some app, if the URL scheme is "http" the web link should be opened
      // by some browser in the mobile
      await Linking.openURL(url);
    } else {
      Alert.alert(`Don't know how to open this URL: ${url}`);
    }
  }, [url]);

  return <Button title={children} onPress={handlePress} />;
};

const App = () => {
  return (
    <View style={styles.container}>
      <OpenURLButton url={supportedURL}>Open Supported URL</OpenURLButton>
      <OpenURLButton url={unsupportedURL}>Open Unsupported URL</OpenURLButton>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
</Tabs>

### 開啟自訂設定

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Linking%20Example&supportedPlatforms=ios,android&ext=js
import React, {useCallback} from 'react';
import {Button, Linking, StyleSheet, View} from 'react-native';

const OpenSettingsButton = ({children}) => {
  const handlePress = useCallback(async () => {
    // Open the custom settings if the app has one
    await Linking.openSettings();
  }, []);

  return <Button title={children} onPress={handlePress} />;
};

const App = () => {
  return (
    <View style={styles.container}>
      <OpenSettingsButton>Open Settings</OpenSettingsButton>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Linking%20Example&supportedPlatforms=ios,android&ext=tsx
import React, {useCallback} from 'react';
import {Button, Linking, StyleSheet, View} from 'react-native';

type OpenSettingsButtonProps = {
  children: string;
};

const OpenSettingsButton = ({children}: OpenSettingsButtonProps) => {
  const handlePress = useCallback(async () => {
    // Open the custom settings if the app has one
    await Linking.openSettings();
  }, []);

  return <Button title={children} onPress={handlePress} />;
};

const App = () => {
  return (
    <View style={styles.container}>
      <OpenSettingsButton>Open Settings</OpenSettingsButton>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
</Tabs>

### 獲取深度連結

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Linking%20Example&supportedPlatforms=ios,android&ext=js
import React, {useState, useEffect} from 'react';
import {Linking, StyleSheet, Text, View} from 'react-native';

const useInitialURL = () => {
  const [url, setUrl] = useState(null);
  const [processing, setProcessing] = useState(true);

  useEffect(() => {
    const getUrlAsync = async () => {
      // Get the deep link used to open the app
      const initialUrl = await Linking.getInitialURL();

      // The setTimeout is just for testing purpose
      setTimeout(() => {
        setUrl(initialUrl);
        setProcessing(false);
      }, 1000);
    };

    getUrlAsync();
  }, []);

  return {url, processing};
};

const App = () => {
  const {url: initialUrl, processing} = useInitialURL();

  return (
    <View style={styles.container}>
      <Text>
        {processing
          ? 'Processing the initial url from a deep link'
          : `The deep link is: ${initialUrl || 'None'}`}
      </Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Linking%20Example&supportedPlatforms=ios,android&ext=tsx
import React, {useState, useEffect} from 'react';
import {Linking, StyleSheet, Text, View} from 'react-native';

const useInitialURL = () => {
  const [url, setUrl] = useState<string | null>(null);
  const [processing, setProcessing] = useState(true);

  useEffect(() => {
    const getUrlAsync = async () => {
      // Get the deep link used to open the app
      const initialUrl = await Linking.getInitialURL();

      // The setTimeout is just for testing purpose
      setTimeout(() => {
        setUrl(initialUrl);
        setProcessing(false);
      }, 1000);
    };

    getUrlAsync();
  }, []);

  return {url, processing};
};

const App = () => {
  const {url: initialUrl, processing} = useInitialURL();

  return (
    <View style={styles.container}>
      <Text>
        {processing
          ? 'Processing the initial url from a deep link'
          : `The deep link is: ${initialUrl || 'None'}`}
      </Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
</Tabs>

### 發送 Intents (Android)

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Linking%20Example&supportedPlatforms=android&ext=js
import React, {useCallback} from 'react';
import {Alert, Button, Linking, StyleSheet, View} from 'react-native';

const SendIntentButton = ({action, extras, children}) => {
  const handlePress = useCallback(async () => {
    try {
      await Linking.sendIntent(action, extras);
    } catch (e) {
      Alert.alert(e.message);
    }
  }, [action, extras]);

  return <Button title={children} onPress={handlePress} />;
};

const App = () => {
  return (
    <View style={styles.container}>
      <SendIntentButton action="android.intent.action.POWER_USAGE_SUMMARY">
        Power Usage Summary
      </SendIntentButton>
      <SendIntentButton
        action="android.settings.APP_NOTIFICATION_SETTINGS"
        extras={[
          {
            key: 'android.provider.extra.APP_PACKAGE',
            value: 'com.facebook.katana',
          },
        ]}>
        App Notification Settings
      </SendIntentButton>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Linking%20Example&ext=tsx
import React, {useCallback} from 'react';
import {Alert, Button, Linking, StyleSheet, View} from 'react-native';

type SendIntentButtonProps = {
  action: string;
  children: string;
  extras?: Array<{
    key: string;
    value: string | number | boolean;
  }>;
};

const SendIntentButton = ({
  action,
  extras,
  children,
}: SendIntentButtonProps) => {
  const handlePress = useCallback(async () => {
    try {
      await Linking.sendIntent(action, extras);
    } catch (e: any) {
      Alert.alert(e.message);
    }
  }, [action, extras]);

  return <Button title={children} onPress={handlePress} />;
};

const App = () => {
  return (
    <View style={styles.container}>
      <SendIntentButton action="android.intent.action.POWER_USAGE_SUMMARY">
        Power Usage Summary
      </SendIntentButton>
      <SendIntentButton
        action="android.settings.APP_NOTIFICATION_SETTINGS"
        extras={[
          {
            key: 'android.provider.extra.APP_PACKAGE',
            value: 'com.facebook.katana',
          },
        ]}>
        App Notification Settings
      </SendIntentButton>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default App;
```

</TabItem>
</Tabs>

# 參考

## 方法

### `addEventListener()`

```tsx
static addEventListener(
  type: 'url',
  handler: (event: {url: string}) => void,
): EmitterSubscription;
```

透過監聽 `url` 事件類型並提供處理程序，來為 Linking 變更添加處理程序。

---

### `canOpenURL()`

```tsx
static canOpenURL(url: string): Promise<boolean>;
```

判斷已安裝的應用程式是否能處理指定的 URL。

此方法會返回一個 `Promise` 物件。當系統判斷出該 URL 是否能被處理時，Promise 會被解析，且第一個參數會表明該 URL 是否能被開啟。

在 Android 上，若無法檢查該 URL 是否能被開啟，或是當目標為 Android 11 (SDK 30) 時未在 `AndroidManifest.xml` 中指定相關的 intent 查詢，Promise 會被拒絕。同樣地，在 iOS 上，若未在 `Info.plist` 的 `LSApplicationQueriesSchemes` 鍵中加入特定 scheme（見下方），Promise 也會被拒絕。

**參數：**

| Name                                                     | Type   | Description      |
| -------------------------------------------------------- | ------ | ---------------- |
| url <div className="label basic required">Required</div> | string | The URL to open. |

> 對於網頁 URL，必須正確設定通訊協定（`"http://"`、`"https://"`）！

> 此方法在 iOS 9+ 上有其限制。根據 [Apple 官方文件](https://developer.apple.com/documentation/uikit/uiapplication/1622952-canopenurl)：
>
> - 若你的應用程式是針對較早版本的 iOS 所連結，但在 iOS 9.0 或更新版本上運行，則此方法最多可呼叫 50 次。達到此限制後，後續呼叫將一律解析為 `false`。若使用者重新安裝或升級應用程式，iOS 會重置此限制。
>
> 自 iOS 9 起，你的應用程式還需在 `Info.plist` 中提供 `LSApplicationQueriesSchemes` 鍵，否則 `canOpenURL()` 將一律解析為 `false`。

> 當目標為 Android 11 (SDK 30) 時，你必須在 `AndroidManifest.xml` 中指定你想要處理的 schemes 的 intents。常見的 intents 列表可參考[此處](https://developer.android.com/guide/components/intents-common)。
>
> 例如，要處理 `https` schemes，需在 manifest 中加入以下內容：
>
> ```
> <manifest ...>
>     <queries>
>         <intent>
>             <action android:name="android.intent.action.VIEW" />
>             <data android:scheme="https"/>
>         </intent>
>     </queries>
> </manifest>
> ```

---

### `getInitialURL()`

```tsx
static getInitialURL(): Promise<string | null>;
```

若應用程式的啟動是由應用程式連結觸發，則會返回該連結的 URL，否則返回 `null`。

> 要在 Android 上支援深度連結，請參考 https://developer.android.com/training/app-indexing/deep-linking.html#handling-intents

> 當遠端 JS 除錯功能啟用時，`getInitialURL` 可能會返回 `null`。請停用除錯器以確保能正確傳遞。

---

### `openSettings()`

```tsx
static openSettings(): Promise<void>;
```

開啟設定應用程式並顯示該應用程式的自訂設定（若有）。

---

### `openURL()`

```tsx
static openURL(url: string): Promise<any>;
```

嘗試使用任何已安裝的應用程式開啟指定的 `url`。

你可以使用其他 URL，例如位置（如 Android 上的 `"geo:37.484847,-122.148386"` 或 iOS 上的 `"https://maps.apple.com/?ll=37.484847,-122.148386"`）、聯絡人，或任何可透過已安裝應用程式開啟的 URL。

此方法會返回一個 `Promise` 物件。若使用者確認開啟對話框或 URL 自動開啟，Promise 會被解析。若使用者取消開啟對話框或沒有已註冊的應用程式可處理該 URL，Promise 會被拒絕。

**參數：**

| Name                                                     | Type   | Description      |
| -------------------------------------------------------- | ------ | ---------------- |
| url <div className="label basic required">Required</div> | string | The URL to open. |

> 若系統不知道如何開啟指定的 URL，此方法將會失敗。若你傳入的是非 http(s) 的 URL，最好先檢查 `canOpenURL()`。

> 對於網頁 URL，必須正確設定通訊協定（`"http://"`、`"https://"`）！

> 此方法在模擬器中的行為可能有所不同，例如 iOS 模擬器無法處理 `"tel:"` 連結，因為無法存取撥號應用程式。

---

### `sendIntent()` <div class="label android">Android</div>

```tsx
static sendIntent(
  action: string,
  extras?: Array<{key: string; value: string | number | boolean}>,
): Promise<void>;
```

啟動一個帶有附加資料的 Android intent。

**參數：**

| Name                                                        | Type                                                       |
| ----------------------------------------------------------- | ---------------------------------------------------------- |
| action <div className="label basic required">Required</div> | string                                                     |
| extras                                                      | `Array<{key: string, value: string ｜ number ｜ boolean}>` |