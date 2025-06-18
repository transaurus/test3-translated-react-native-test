---
id: integration-with-android-fragment
title: Integration with an Android Fragment
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

[與現有應用整合](https://reactnative.dev/docs/integration-with-existing-apps)指南詳細說明了如何將全螢幕的React Native應用作為Activity整合到現有的Android應用中。若要在現有應用中以Fragments形式使用React Native元件，則需要進行一些額外設定。這樣做的好處是能讓原生應用在Activity中同時整合React Native元件與原生fragments。

### 1. 將React Native加入您的應用

按照[與現有應用整合](https://reactnative.dev/docs/integration-with-existing-apps)指南操作，直到「程式碼整合」章節。繼續完成該章節的步驟1：建立`index.android.js`檔案，以及步驟2：加入您的React Native程式碼。

### 2. 將您的應用與React Native Fragment整合

您可以將React Native元件渲染到Fragment中，而非全螢幕的React Native Activity。該元件可稱為「畫面」或「fragment」，其運作方式與Android fragment相同，可能包含子元件。這些元件可放置在`/fragments`資料夾中，而用於組成fragment的子元件則可放在`/components`資料夾。

您需要在主要的Application Java/Kotlin類別中實作`ReactApplication`介面。如果是從Android Studio建立的新專案且使用預設activity，則需建立新類別（例如`MyReactApplication.java`或`MyReactApplication.kt`）。若是現有類別，您可以在`AndroidManifest.xml`檔案中找到這個主類別。在`<application />`標籤下，您會看到`android:name`屬性，例如`android:name=".MyReactApplication"`。此值就是您要實作並提供必要方法的類別。

請確認您的主要Application類別實作了ReactApplication：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
class MyReactApplication: Application(), ReactApplication {...}
```

</TabItem>
<TabItem value="java">

```java
public class MyReactApplication extends Application implements ReactApplication {...}
```

</TabItem>
</Tabs>

覆寫必要的方法`getUseDeveloperSupport`、`getPackages`和`getReactNativeHost`：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
class MyReactApplication : Application(), ReactApplication {
    override fun onCreate() {
        super.onCreate()
        SoLoader.init(this, false)
    }
    private val reactNativeHost =
        object : DefaultReactNativeHost(this) {
            override fun getUseDeveloperSupport() = BuildConfig.DEBUG
            override fun getPackages(): List<ReactPackage> {
                val packages = PackageList(this).getPackages().toMutableList()
                // Packages that cannot be autolinked yet can be added manually here
                return packages
            }
        }
    override fun getReactNativeHost(): ReactNativeHost = reactNativeHost
}
```

</TabItem>
<TabItem value="java">

```java
public class MyReactApplication extends Application implements ReactApplication {
    @Override
    public void onCreate() {
        super.onCreate();
        SoLoader.init(this, false);
    }

    private final ReactNativeHost mReactNativeHost = new DefaultReactNativeHost(this) {
        @Override
        public boolean getUseDeveloperSupport() {
            return BuildConfig.DEBUG;
        }

        protected List<ReactPackage> getPackages() {
            List<ReactPackage> packages = new PackageList(this).getPackages();
            // Packages that cannot be autolinked yet can be added manually here
            return packages;
        }
    };

    @Override
    public ReactNativeHost getReactNativeHost() {
        return mReactNativeHost;
    }
}
```

</TabItem>
</Tabs>

如果您使用Android Studio，可使用Alt + Enter來加入類別中所有缺少的import。或者，以下是需要手動加入的必要import：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
import android.app.Application

import com.facebook.react.PackageList
import com.facebook.react.ReactApplication
import com.facebook.react.ReactNativeHost
import com.facebook.react.ReactPackage
import com.facebook.react.defaults.DefaultReactNativeHost
import com.facebook.soloader.SoLoader
```

</TabItem>
<TabItem value="java">

```java
import android.app.Application;

import com.facebook.react.PackageList;
import com.facebook.react.ReactApplication;
import com.facebook.react.ReactNativeHost;
import com.facebook.react.ReactPackage;
import com.facebook.react.defaults.DefaultReactNativeHost;
import com.facebook.soloader.SoLoader;

import java.util.List;
```

</TabItem>
</Tabs>

執行「Sync Project files with Gradle」操作。

### 步驟3. 為React Native Fragment加入FrameLayout

現在您要將React Native Fragment加入Activity中。對於新專案，此Activity會是`MainActivity`，但它可以是任何Activity，且隨著您在應用中整合更多React Native元件，可以將更多fragments加入其他Activities。

首先將React Native Fragment加入Activity的layout中。例如`res/layouts`資料夾中的`main_activity.xml`。

加入一個具有id、寬度和高度的`<FrameLayout>`。這是您將尋找並渲染React Native Fragment的layout。

```xml
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/reactNativeFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### 步驟4. 將React Native Fragment加入FrameLayout

要將React Native Fragment加入layout中，您需要有一個Activity。如前所述，在新專案中這會是`MainActivity`。在此Activity中加入一個按鈕和事件監聽器。點擊按鈕時，您將渲染React Native Fragment。

修改Activity layout以加入按鈕：

```xml
<Button
    android:layout_margin="10dp"
    android:id="@+id/button"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Show react fragment" />
```

現在在Activity類別（例如`MainActivity.java`或`MainActivity.kt`）中，您需要為按鈕加入`OnClickListener`，實例化`ReactFragment`並將其加入frame layout。

在Activity頂部加入按鈕欄位：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
private lateinit var button: Button
```

</TabItem>
<TabItem value="java">

```java
private Button mButton;
```

</TabItem>
</Tabs>

更新Activity的`onCreate`方法如下：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
override fun onCreate(savedInstanceState: Bundle) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.main_activity)
    button = findViewById<Button>(R.id.button)
    button.setOnClickListener {
        val reactNativeFragment = ReactFragment.Builder()
                .setComponentName("HelloWorld")
                .setLaunchOptions(getLaunchOptions("test message"))
                .build()
        getSupportFragmentManager()
                .beginTransaction()
                .add(R.id.reactNativeFragment, reactNativeFragment)
                .commit()
    }
}
```

</TabItem>
<TabItem value="java">

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main_activity);

    mButton = findViewById(R.id.button);
    mButton.setOnClickListener(new View.OnClickListener() {
        public void onClick(View v) {
            Fragment reactNativeFragment = new ReactFragment.Builder()
                    .setComponentName("HelloWorld")
                    .setLaunchOptions(getLaunchOptions("test message"))
                    .build();

            getSupportFragmentManager()
                    .beginTransaction()
                    .add(R.id.reactNativeFragment, reactNativeFragment)
                    .commit();

        }
    });
}
```

</TabItem>
</Tabs>

上述程式碼中 `Fragment reactNativeFragment = new ReactFragment.Builder()` 會建立 ReactFragment，而 `getSupportFragmentManager().beginTransaction().add()` 則會將該 Fragment 加入至 Frame Layout 中。

若您使用的是 React Native 的入門套件，請將 "HelloWorld" 字串替換為您 `index.js` 或 `index.android.js` 檔案中的字串（即 AppRegistry.registerComponent() 方法的第一個參數）。

加入 `getLaunchOptions` 方法，該方法可讓您將 props 傳遞至您的元件。此為選用項目，若您不需要傳遞任何 props，可移除 `setLaunchOptions`。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
private fun getLaunchOptions(message: String) = Bundle().apply {
    putString("message", message)
}

```

</TabItem>
<TabItem value="java">

```java
private Bundle getLaunchOptions(String message) {
    Bundle initialProperties = new Bundle();
    initialProperties.putString("message", message);
    return initialProperties;
}
```

</TabItem>
</Tabs>

在您的 Activity 類別中加入所有缺少的 imports。請注意使用您套件的 BuildConfig，而非來自 facebook 套件的！或者，以下是需手動加入的必要 imports：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
import android.app.Application

import com.facebook.react.ReactApplication
import com.facebook.react.ReactNativeHost
import com.facebook.react.ReactPackage
import com.facebook.react.shell.MainReactPackage
import com.facebook.soloader.SoLoader

```

</TabItem>
<TabItem value="java">

```java
import android.app.Application;

import com.facebook.react.ReactApplication;
import com.facebook.react.ReactNativeHost;
import com.facebook.react.ReactPackage;
import com.facebook.react.shell.MainReactPackage;
import com.facebook.soloader.SoLoader;
```

</TabItem>
</Tabs>

執行「Sync Project files with Gradle」操作。

### 步驟 5. 測試您的整合

請確保執行 `yarn` 以安裝您的 react-native 相依套件，並執行 `yarn native` 以啟動 metro bundler。在 Android Studio 中執行您的 Android 應用程式，它應會從開發伺服器載入 JavaScript 程式碼，並在 Activity 中的 React Native Fragment 顯示。

### 步驟 6. 額外設定 - 原生模組

您可能需要從您的 react 元件呼叫現有的 Java/Kotlin 程式碼。原生模組可讓您呼叫原生程式碼並在您的原生應用程式中執行方法。請按照此處的設定 [native-modules-android](/docs/0.75/native-modules-android)