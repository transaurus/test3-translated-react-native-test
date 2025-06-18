## 核心概念

將 React Native 元件整合至 Android 應用的關鍵在於：

1. 設定 React Native 依賴項與目錄結構。
2. 使用 JavaScript 開發 React Native 元件。
3. 在 Android 應用中添加 `ReactRootView`，此視圖將作為 React Native 元件的容器。
4. 啟動 React Native 伺服器並運行原生應用。
5. 驗證應用中的 React Native 功能是否如預期運作。

## 必要條件

請依照[環境設定指南](environment-setup)中的 React Native CLI 快速入門，配置您的開發環境以建構 Android 版 React Native 應用。

### 1. 設定目錄結構

為確保流程順暢，請為整合的 React Native 專案建立新資料夾，然後將現有的 Android 專案複製到 `/android` 子資料夾中。

### 2. 安裝 JavaScript 依賴項

進入專案的根目錄，建立一個新的 `package.json` 檔案，內容如下：

```
{
  "name": "MyReactNativeApp",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "yarn react-native start"
  }
}
```

接著，請確認您已[安裝 yarn 套件管理工具](https://yarnpkg.com/lang/en/docs/install/)。

安裝 `react` 和 `react-native` 套件。開啟終端機或命令提示字元，導航至包含 `package.json` 檔案的目錄並執行：

```shell
$ yarn add react-native
```

這將輸出類似以下的訊息（請在 yarn 輸出中向上滾動查看）：

> 警告："react-native@0.52.2" 有未滿足的同儕依賴項 "react@16.2.0"。

這是正常的，表示我們還需要安裝 React：

```shell
$ yarn add react@version_printed_above
```

Yarn 已建立新的 `/node_modules` 資料夾，此資料夾儲存了專案建構所需的所有 JavaScript 依賴項。

請將 `node_modules/` 加入您的 `.gitignore` 檔案。

## 將 React Native 加入您的應用

### 配置 maven

在應用的 `build.gradle` 檔案中添加 React Native 和 JSC 依賴項：

```gradle
dependencies {
    implementation "com.android.support:appcompat-v7:27.1.1"
    ...
    implementation "com.facebook.react:react-native:+" // From node_modules
    implementation "org.webkit:android-jsc:+"
}
```

> 若需確保原生建構始終使用特定 React Native 版本，請將 `+` 替換為您從 `npm` 下載的實際 React Native 版本號。

在頂層 `settings.gradle` 的「dependencyResolutionManagement」區塊中，添加本地 React Native 和 JSC maven 目錄的條目，並確保其位於其他 maven 儲存庫之上：

```gradle
dependencyResolutionManagement {
    ...
    repositories {
        ...
        maven {
            url "$rootDir/../node_modules/react-native/android"
        }
        maven {
            url("$rootDir/../node_modules/jsc-android/dist")
        }
    }
}
```

> 若專案在頂層 `build.gradle` 中配置了依賴儲存庫，請務必將條目添加至「allprojects」區塊，並置於其他 maven 儲存庫之上：

```gradle
allprojects {
    repositories {
        maven {
            // All of React Native (JS, Android binaries) is installed from npm
            url "$rootDir/../node_modules/react-native/android"
        }
        maven {
            // Android JSC is installed from npm
            url("$rootDir/../node_modules/jsc-android/dist")
        }
        ...
    }
    ...
}
```

> 請確認路徑正確！在 Android Studio 中執行 Gradle 同步後，不應出現「Failed to resolve: com.facebook.react:react-native:0.x.x」錯誤。

### 啟用原生模組自動連結

為使用[自動連結](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)功能，需在幾處進行配置。首先在 `settings.gradle` 中添加以下條目：

```gradle
apply from: file("../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesSettingsGradle(settings)
```

接著在 `app/build.gradle` 的最底部添加以下條目：

```gradle
apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
```

### 配置權限

接下來，請確保您的 `AndroidManifest.xml` 中包含網路權限：

<uses-permission android:name="android.permission.INTERNET" />

如需存取 `DevSettingsActivity`，請在 `AndroidManifest.xml` 中添加：

<activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />

This is only used in dev mode when reloading JavaScript from the development server, so you can strip this in release builds if you need to.

### Cleartext Traffic (API level 28+)

> Starting with Android 9 (API level 28), cleartext traffic is disabled by default; this prevents your application from connecting to the [Metro bundler][metro]. The changes below allow cleartext traffic in debug builds.

#### 1. Apply the `usesCleartextTraffic` option to your Debug `AndroidManifest.xml`

```xml
<!-- ... -->
<application
  android:usesCleartextTraffic="true" tools:targetApi="28" >
  <!-- ... -->
</application>
<!-- ... -->
```

This is not required for Release builds.

To learn more about Network Security Config and the cleartext traffic policy [see this link](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted).

### Code integration

Now we will actually modify the native Android application to integrate React Native.

#### The React Native component

The first bit of code we will write is the actual React Native code for the new "High Score" screen that will be integrated into our application.

##### 1. Create a `index.js` file

First, create an empty `index.js` file in the root of your React Native project.

`index.js` is the starting point for React Native applications, and it is always required. It can be a small file that `require`s other file that are part of your React Native component or application, or it can contain all the code that is needed for it. In our case, we will put everything in `index.js`.

##### 2. Add your React Native code

In your `index.js`, create your component. In our sample here, we will add a `<Text>` component within a styled `<View>`:

```jsx
import React from 'react';
import {AppRegistry, StyleSheet, Text, View} from 'react-native';

const HelloWorld = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.hello}>Hello, World</Text>
    </View>
  );
};
var styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  hello: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
});

AppRegistry.registerComponent(
  'MyReactNativeApp',
  () => HelloWorld,
);
```

##### 3. Configure permissions for development error overlay

If your app is targeting the Android `API level 23` or greater, make sure you have the permission `android.permission.SYSTEM_ALERT_WINDOW` enabled for the development build. You can check this with `Settings.canDrawOverlays(this)`. This is required in dev builds because React Native development errors must be displayed above all the other windows. Due to the new permissions system introduced in the API level 23 (Android M), the user needs to approve it. This can be achieved by adding the following code to your Activity's in `onCreate()` method.

```kotlin
companion object {
    const val OVERLAY_PERMISSION_REQ_CODE = 1  // Choose any value
}

...

if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    if(!Settings.canDrawOverlays(this)) {
        val intent = Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION,
                                    Uri.parse("package: $packageName"))
        startActivityForResult(intent, OVERLAY_PERMISSION_REQ_CODE);
    }
}
```

Finally, the `onActivityResult()` method (as shown in the code below) has to be overridden to handle the permission Accepted or Denied cases for consistent UX. Also, for integrating Native Modules which use `startActivityForResult`, we need to pass the result to the `onActivityResult` method of our `ReactInstanceManager` instance.

```kotlin
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    if (requestCode == OVERLAY_PERMISSION_REQ_CODE) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            if (!Settings.canDrawOverlays(this)) {
                // SYSTEM_ALERT_WINDOW permission not granted
            }
        }
    }
    reactInstanceManager?.onActivityResult(this, requestCode, resultCode, data)
}
```

#### The Magic: `ReactRootView`

Let's add some native code in order to start the React Native runtime and tell it to render our JS component. To do this, we're going to create an `Activity` that creates a `ReactRootView`, starts a React application inside it and sets it as the main content view.

> If you are targeting Android version &lt;5, use the `AppCompatActivity` class from the `com.android.support:appcompat` package instead of `Activity`.

```kotlin
class MyReactActivity : Activity(), DefaultHardwareBackBtnHandler {
    private lateinit var reactRootView: ReactRootView
    private lateinit var reactInstanceManager: ReactInstanceManager
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        SoLoader.init(this, false)
        reactRootView = ReactRootView(this)
        val packages: List<ReactPackage> = PackageList(application).packages
        // Packages that cannot be autolinked yet can be added manually here, for example:
        // packages.add(MyReactNativePackage())
        // Remember to include them in `settings.gradle` and `app/build.gradle` too.
        reactInstanceManager = ReactInstanceManager.builder()
            .setApplication(application)
            .setCurrentActivity(this)
            .setBundleAssetName("index.android.bundle")
            .setJSMainModulePath("index")
            .addPackages(packages)
            .setUseDeveloperSupport(BuildConfig.DEBUG)
            .setInitialLifecycleState(LifecycleState.RESUMED)
            .build()
        // The string here (e.g. "MyReactNativeApp") has to match
        // the string in AppRegistry.registerComponent() in index.js
        reactRootView?.startReactApplication(reactInstanceManager, "MyReactNativeApp", null)
        setContentView(reactRootView)
    }

    override fun invokeDefaultOnBackPressed() {
        super.onBackPressed()
    }
}
```

> If you are using a starter kit for React Native, replace the "HelloWorld" string with the one in your index.js file (it’s the first argument to the `AppRegistry.registerComponent()` method).

Perform a “Sync Project files with Gradle” operation.

若您使用 Android Studio，請使用 `Alt + Enter` 為您的 MyReactActivity 類別補齊所有缺失的導入。請注意使用您套件的 `BuildConfig`，而非 `facebook` 套件中的版本。

我們需將 `MyReactActivity` 的主題設為 `Theme.AppCompat.Light.NoActionBar`，因部分 React Native UI 元件依賴此主題。

```xml
<activity
  android:name=".MyReactActivity"
  android:label="@string/app_name"
  android:theme="@style/Theme.AppCompat.Light.NoActionBar">
</activity>
```

> 一個 `ReactInstanceManager` 可被多個活動(Activity)或片段(Fragment)共享。建議建立自己的 `ReactFragment` 或 `ReactActivity`，並透過單例持有者保存 `ReactInstanceManager`。當需要時（例如將 `ReactInstanceManager` 掛載到這些活動的生命週期），使用該單例提供的實例。

接著，我們需將部分活動生命週期回調傳遞給 `ReactInstanceManager` 和 `ReactRootView`：

```kotlin
override fun onPause() {
    super.onPause()
    reactInstanceManager.onHostPause(this)
}

override fun onResume() {
    super.onResume()
    reactInstanceManager.onHostResume(this, this)
}

override fun onDestroy() {
    super.onDestroy()
    reactInstanceManager.onHostDestroy(this)
    reactRootView.unmountReactApplication()
}
```

我們還需將返回按鈕事件傳遞給 React Native：

```kotlin
override fun onBackPressed() {
    reactInstanceManager.onBackPressed()
    super.onBackPressed()
}
```

這允許 JavaScript 控制使用者按下實體返回鍵時的行為（例如實現導航）。若 JavaScript 未處理返回按鈕事件，則會調用您的 `invokeDefaultOnBackPressed` 方法。預設情況下，這將結束您的 `Activity`。

最後，我們需掛載開發者選單。預設透過搖晃裝置觸發，但在模擬器中不實用。因此改為按下實體選單鍵時顯示（Android Studio 模擬器請使用 `Ctrl + M`）：

```kotlin
override fun onKeyUp(keyCode: Int, event: KeyEvent?): Boolean {
    if (keyCode == KeyEvent.KEYCODE_MENU && reactInstanceManager != null) {
        reactInstanceManager.showDevOptionsDialog()
        return true
    }
    return super.onKeyUp(keyCode, event)
}
```

現在您的活動已準備好執行 JavaScript 程式碼。

### 測試整合結果

您已完成整合 React Native 與現有應用程式的基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來建構 `index.bundle` 套件，並運行本地伺服器來提供它。

##### 1. 執行打包伺服器

要運行應用程式，需先啟動開發伺服器。請在 React Native 專案根目錄執行以下指令：

```shell
$ yarn start
```

##### 2. 運行應用程式

現在如常建置並運行您的 Android 應用程式。

當進入應用內 React 驅動的活動時，應會從開發伺服器載入 JavaScript 程式碼並顯示：

![螢幕截圖](/docs/assets/EmbeddedAppAndroid.png)

### 在 Android Studio 建立正式版建置

您也可使用 Android Studio 建立正式版建置！步驟與原本建置原生 Android 應用程式相同。每次正式建置前需多執行一個步驟：建立 React Native 套件包並將其包含在原生應用中：

```shell
$ npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/com/your-company-name/app-package-name/src/main/assets/index.android.bundle --assets-dest android/com/your-company-name/app-package-name/src/main/res/
```

> 請注意替換正確路徑，若 assets 資料夾不存在請先建立。

現在如常透過 Android Studio 建立原生應用的正式版建置即可！

### 後續步驟

至此您可繼續常規開發。參考我們的[除錯指南](debugging)與[部署文件](running-on-device)以深入瞭解 React Native 開發。

[metro]: https://metrobundler.dev/