---
id: headless-js-android
title: Headless JS
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem';
import constants from '@site/core/TabsConstants';

Headless JS 是一種在應用程式處於背景時執行 JavaScript 任務的方式。例如可用於同步最新資料、處理推播通知或播放音樂。

## JS API

任務是一個在 `AppRegistry` 上註冊的異步函數，類似於註冊 React 應用程式：

```tsx
import {AppRegistry} from 'react-native';
AppRegistry.registerHeadlessTask('SomeTaskName', () =>
  require('SomeTaskName'),
);
```

接著在 `SomeTaskName.js` 中：

```tsx
module.exports = async taskData => {
  // do stuff
};
```

你可以在任務中執行任何操作，例如網路請求、計時器等，只要不涉及 UI 即可。一旦任務完成（即 Promise 被解析），React Native 將進入「暫停」模式（除非有其他任務正在執行，或有前景應用程式）。

## 平台 API

是的，這仍然需要一些原生程式碼，但相當精簡。你需要擴展 `HeadlessJsTaskService` 並覆寫 `getTaskConfig`，例如：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
package com.your_application_name;

import android.content.Intent;
import android.os.Bundle;
import com.facebook.react.HeadlessJsTaskService;
import com.facebook.react.bridge.Arguments;
import com.facebook.react.jstasks.HeadlessJsTaskConfig;
import javax.annotation.Nullable;

public class MyTaskService extends HeadlessJsTaskService {

  @Override
  protected @Nullable HeadlessJsTaskConfig getTaskConfig(Intent intent) {
    Bundle extras = intent.getExtras();
    if (extras != null) {
      return new HeadlessJsTaskConfig(
          "SomeTaskName",
          Arguments.fromBundle(extras),
          5000, // timeout in milliseconds for the task
          false // optional: defines whether or not the task is allowed in foreground. Default is false
        );
    }
    return null;
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
package com.your_application_name;

import android.content.Intent
import com.facebook.react.HeadlessJsTaskService
import com.facebook.react.bridge.Arguments
import com.facebook.react.jstasks.HeadlessJsTaskConfig

class MyTaskService : HeadlessJsTaskService() {
    override fun getTaskConfig(intent: Intent): HeadlessJsTaskConfig? {
        return intent.extras?.let {
            HeadlessJsTaskConfig(
                "SomeTaskName",
                Arguments.fromBundle(it),
                5000, // timeout for the task
                false // optional: defines whether or not the task is allowed in foreground.
                // Default is false
            )
        }
    }
}

```

</TabItem>
</Tabs>

然後將服務添加到 `AndroidManifest.xml` 文件的 `application` 標籤內：

```xml
<service android:name="com.example.MyTaskService" />
```

現在，每當你[啟動服務][0]時（例如作為定期任務或響應某些系統事件/廣播），JS 將啟動、執行你的任務，然後關閉。

範例：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
Intent service = new Intent(getApplicationContext(), MyTaskService.class);
Bundle bundle = new Bundle();

bundle.putString("foo", "bar");
service.putExtras(bundle);

getApplicationContext().startService(service);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
val service = Intent(applicationContext, MyTaskService::class.java)
val bundle = Bundle()

bundle.putString("foo", "bar")

service.putExtras(bundle)

applicationContext.startService(service)
```

</TabItem>
</Tabs>

## 重試

預設情況下，Headless JS 任務不會進行任何重試。若要實現重試，你需要建立一個 `HeadlessJsRetryPolicy` 並拋出特定的 `Error`。

`LinearCountingRetryPolicy` 是 `HeadlessJsRetryPolicy` 的一個實現，允許你指定最大重試次數和每次嘗試之間的固定延遲。如果這不符合你的需求，你可以實作自己的 `HeadlessJsRetryPolicy`。這些策略可以作為額外參數傳遞給 `HeadlessJsTaskConfig` 構造函數，例如：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
HeadlessJsRetryPolicy retryPolicy = new LinearCountingRetryPolicy(
  3, // Max number of retry attempts
  1000 // Delay between each retry attempt
);

return new HeadlessJsTaskConfig(
  'SomeTaskName',
  Arguments.fromBundle(extras),
  5000,
  false,
  retryPolicy
);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
val retryPolicy: HeadlessJsTaskRetryPolicy =
    LinearCountingRetryPolicy(
        3, // Max number of retry attempts
        1000 // Delay between each retry attempt
    )

return HeadlessJsTaskConfig("SomeTaskName", Arguments.fromBundle(extras), 5000, false, retryPolicy)
```

</TabItem>
</Tabs>

只有當拋出特定的 `Error` 時，才會進行重試嘗試。在 Headless JS 任務中，你可以導入該錯誤並在需要重試時拋出。

範例：

```tsx
import {HeadlessJsTaskError} from 'HeadlessJsTask';

module.exports = async taskData => {
  const condition = ...;
  if (!condition) {
    throw new HeadlessJsTaskError();
  }
};
```

如果你希望所有錯誤都觸發重試嘗試，則需要捕獲它們並拋出上述錯誤。

## 注意事項

- 預設情況下，若嘗試在應用程式處於前景時執行任務，應用程式會崩潰。這是為了防止開發者因在任務中執行大量工作而拖慢 UI。你可以傳遞第四個 `boolean` 參數來控制此行為。
- 如果從 `BroadcastReceiver` 啟動服務，請確保在從 `onReceive()` 返回之前調用 `HeadlessJsTaskService.acquireWakeLockNow()`。

## 使用範例

可以從 Java API 啟動服務。首先你需要決定何時啟動服務，並據此實作你的解決方案。以下是一個響應網路連接變更的範例。

以下幾行顯示了用於註冊廣播接收器的 Android 清單文件部分。

```xml
<receiver android:name=".NetworkChangeReceiver" >
  <intent-filter>
    <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
  </intent-filter>
</receiver>
```

廣播接收器隨後在 `onReceive` 函數中處理廣播的意圖。這是檢查應用程式是否處於前景的好地方。如果應用程式不在前景，我們可以準備啟動意圖，不帶資訊或使用 `putExtra` 捆綁額外資訊（請注意 bundle 只能處理可打包的值）。最後啟動服務並獲取 wakelock。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
import android.app.ActivityManager;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.net.ConnectivityManager;
import android.net.Network;
import android.net.NetworkCapabilities;
import android.net.NetworkInfo;
import android.os.Build;

import com.facebook.react.HeadlessJsTaskService;

public class NetworkChangeReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(final Context context, final Intent intent) {
        /**
         This part will be called every time network connection is changed
         e.g. Connected -> Not Connected
         **/
        if (!isAppOnForeground((context))) {
            /**
             We will start our service and send extra info about
             network connections
             **/
            boolean hasInternet = isNetworkAvailable(context);
            Intent serviceIntent = new Intent(context, MyTaskService.class);
            serviceIntent.putExtra("hasInternet", hasInternet);
            context.startService(serviceIntent);
            HeadlessJsTaskService.acquireWakeLockNow(context);
        }
    }

    private boolean isAppOnForeground(Context context) {
        /**
         We need to check if app is in foreground otherwise the app will crash.
         https://stackoverflow.com/questions/8489993/check-android-application-is-in-foreground-or-not
         **/
        ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
        List<ActivityManager.RunningAppProcessInfo> appProcesses =
                activityManager.getRunningAppProcesses();
        if (appProcesses == null) {
            return false;
        }
        final String packageName = context.getPackageName();
        for (ActivityManager.RunningAppProcessInfo appProcess : appProcesses) {
            if (appProcess.importance ==
                    ActivityManager.RunningAppProcessInfo.IMPORTANCE_FOREGROUND &&
                    appProcess.processName.equals(packageName)) {
                return true;
            }
        }
        return false;
    }

    public static boolean isNetworkAvailable(Context context) {
        ConnectivityManager cm = (ConnectivityManager)
                context.getSystemService(Context.CONNECTIVITY_SERVICE);

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            Network networkCapabilities = cm.getActiveNetwork();

            if(networkCapabilities == null) {
                return false;
            }

            NetworkCapabilities actNw = cm.getNetworkCapabilities(networkCapabilities);

            if(actNw == null) {
                return false;
            }

            if(actNw.hasTransport(NetworkCapabilities.TRANSPORT_WIFI) || actNw.hasTransport(NetworkCapabilities.TRANSPORT_CELLULAR) || actNw.hasTransport(NetworkCapabilities.TRANSPORT_ETHERNET)) {
                return true;
            }

            return false;
        }

        // deprecated in API level 29
        NetworkInfo netInfo = cm.getActiveNetworkInfo();
        return (netInfo != null && netInfo.isConnected());
    }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
import android.app.ActivityManager
import android.app.ActivityManager.RunningAppProcessInfo
import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.net.ConnectivityManager
import android.net.NetworkCapabilities
import android.os.Build
import com.facebook.react.HeadlessJsTaskService

class NetworkChangeReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent?) {
        /**
         * This part will be called every time network connection is changed e.g. Connected -> Not
         * Connected
         */
        if (!isAppOnForeground(context)) {
            /** We will start our service and send extra info about network connections */
            val hasInternet = isNetworkAvailable(context)
            val serviceIntent = Intent(context, MyTaskService::class.java)
            serviceIntent.putExtra("hasInternet", hasInternet)
            context.startService(serviceIntent)
            HeadlessJsTaskService.acquireWakeLockNow(context)
        }
    }

    private fun isAppOnForeground(context: Context): Boolean {
        /**
         * We need to check if app is in foreground otherwise the app will crash.
         * https://stackoverflow.com/questions/8489993/check-android-application-is-in-foreground-or-not
         */
        val activityManager = context.getSystemService(Context.ACTIVITY_SERVICE) as ActivityManager
        val appProcesses = activityManager.runningAppProcesses ?: return false
        val packageName: String = context.getPackageName()
        for (appProcess in appProcesses) {
            if (appProcess.importance == RunningAppProcessInfo.IMPORTANCE_FOREGROUND &&
                    appProcess.processName == packageName
            ) {
                return true
            }
        }
        return false
    }

    companion object {
        fun isNetworkAvailable(context: Context): Boolean {
            val cm = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
            var result = false

            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                val networkCapabilities = cm.activeNetwork ?: return false

                val actNw = cm.getNetworkCapabilities(networkCapabilities) ?: return false

                result =
                    when {
                        actNw.hasTransport(NetworkCapabilities.TRANSPORT_WIFI) -> true
                        actNw.hasTransport(NetworkCapabilities.TRANSPORT_CELLULAR) -> true
                        actNw.hasTransport(NetworkCapabilities.TRANSPORT_ETHERNET) -> true
                        else -> false
                    }

                return result
            } else {
                cm.run {
                    // deprecated in API level 29
                    cm.activeNetworkInfo?.run {
                        result =
                            when (type) {
                                ConnectivityManager.TYPE_WIFI -> true
                                ConnectivityManager.TYPE_MOBILE -> true
                                ConnectivityManager.TYPE_ETHERNET -> true
                                else -> false
                            }
                    }
                }
            }
            return result
        }
    }
}

```

</TabItem>
</Tabs>

[0]: https://developer.android.com/reference/android/content/Context.html#startService(android.content.Intent)