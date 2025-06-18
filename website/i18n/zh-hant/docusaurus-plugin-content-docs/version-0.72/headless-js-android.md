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

你可以在任務中執行任何操作（如網路請求、計時器等），只要不涉及 UI。當任務完成（即 Promise 被解析）後，React Native 將進入「暫停」模式（除非有其他任務正在執行或應用處於前台）。

## 平台 API

這仍需要少量原生程式碼。你需要擴展 `HeadlessJsTaskService` 並覆寫 `getTaskConfig`，例如：

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

接著將服務加入 `AndroidManifest.xml` 檔案：

```xml
<service android:name="com.example.MyTaskService" />
```

現在，每當你[啟動服務][0]（例如作為定期任務或響應系統事件/廣播時），JS 環境會啟動、執行任務後關閉。

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

## 重試機制

預設情況下，Headless JS 任務不會自動重試。若要實現重試，需建立 `HeadlessJsRetryPolicy` 並拋出特定 `Error`。

`LinearCountingRetryPolicy` 是 `HeadlessJsRetryPolicy` 的實作，允許指定最大重試次數與固定重試間隔。若不符合需求，可自行實作策略。這些策略可透過 `HeadlessJsTaskConfig` 建構函式的額外參數傳入，例如：

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

僅當拋出特定 `Error` 時才會觸發重試。在 Headless JS 任務中，可導入該錯誤並在需要重試時拋出。

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

若希望所有錯誤都觸發重試，需捕獲錯誤並拋出上述特定錯誤。

## 注意事項

- 預設情況下，若應用處於前台時嘗試執行任務，應用會崩潰。此設計是為防止開發者因在任務中執行大量工作而拖慢 UI。可透過第四個 `boolean` 參數控制此行為。
- 若從 `BroadcastReceiver` 啟動服務，請確保在 `onReceive()` 返回前呼叫 `HeadlessJsTaskService.acquireWakeLockNow()`。

## 使用範例

可從 Java API 啟動服務。首先需決定服務啟動時機並實作對應邏輯。以下範例展示如何響應網路連接變化：

以下程式碼展示 Android 清單檔案中註冊廣播接收器的部分：

```xml
<receiver android:name=".NetworkChangeReceiver" >
  <intent-filter>
    <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
  </intent-filter>
</receiver>
```

廣播接收器在 `onReceive` 函數中處理廣播的 Intent。此處是檢查應用是否處於前台的理想位置。若應用不在前台，可準備要啟動的 Intent（可透過 `putExtra` 綁定附加資訊，注意 Bundle 僅能處理 Parcelable 值）。最後啟動服務並取得喚醒鎖。

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
         http://stackoverflow.com/questions/8489993/check-android-application-is-in-foreground-or-not
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
         * http://stackoverflow.com/questions/8489993/check-android-application-is-in-foreground-or-not
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