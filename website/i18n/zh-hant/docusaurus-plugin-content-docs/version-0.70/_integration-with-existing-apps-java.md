## 核心概念

將 React Native 元件整合至 Android 應用的關鍵在於：

1. 設定 React Native 相依套件與目錄結構。
2. 使用 JavaScript 開發 React Native 元件。
3. 在 Android 應用中添加 `ReactRootView`，此視圖將作為 React Native 元件的容器。
4. 啟動 React Native 伺服器並運行原生應用。
5. 驗證應用中的 React Native 功能是否如預期運作。

## 必要條件

請依照[環境設定指南](environment-setup)中的 React Native CLI 快速入門，配置開發環境以建構 Android 版 React Native 應用。

### 1. 設定目錄結構

為確保流程順暢，請為整合的 React Native 專案建立新資料夾，然後將現有 Android 專案複製到 `/android` 子資料夾中。

### 2. 安裝 JavaScript 相依套件

進入專案的根目錄，建立包含以下內容的新 `package.json` 檔案：

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

接著，請確認已[安裝 yarn 套件管理工具](https://yarnpkg.com/lang/en/docs/install/)。

安裝 `react` 和 `react-native` 套件。開啟終端機或命令提示字元，導航至包含 `package.json` 檔案的目錄並執行：

```shell
$ yarn add react-native
```

這將顯示類似以下的訊息（請在 yarn 輸出中向上滾動查看）：

> 警告："react-native@0.52.2" 有未滿足的 peer 相依套件 "react@16.2.0"。

這是正常的，表示我們還需要安裝 React：

```shell
$ yarn add react@version_printed_above
```

Yarn 已建立新的 `/node_modules` 資料夾，此資料夾儲存了建構專案所需的所有 JavaScript 相依套件。

將 `node_modules/` 加入你的 `.gitignore` 檔案。

## 將 React Native 加入應用

### 配置 maven

在應用的 `build.gradle` 檔案中添加 React Native 和 JSC 的相依套件：

```gradle
dependencies {
    implementation "com.android.support:appcompat-v7:27.1.1"
    ...
    implementation "com.facebook.react:react-native:+" // From node_modules
    implementation "org.webkit:android-jsc:+"
}
```

> 若需確保原生建構始終使用特定 React Native 版本，請將 `+` 替換為從 `npm` 下載的實際 React Native 版本號。

在頂層 `build.gradle` 的 "allprojects" 區塊中，於其他 maven 儲存庫之前添加本地 React Native 和 JSC 的 maven 目錄條目：

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

為使用[自動連結](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)功能，需在多處進行配置。首先在 `settings.gradle` 中添加以下條目：

```gradle
apply from: file("../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesSettingsGradle(settings)
```

接著在 `app/build.gradle` 的最底部添加以下條目：

```gradle
apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
```

### 配置權限

接著，確認 `AndroidManifest.xml` 中包含網路權限：

<uses-permission android:name="android.permission.INTERNET" />

如需存取 `DevSettingsActivity`，請在 `AndroidManifest.xml` 中添加：

<activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />

此設定僅在開發模式下用於從開發伺服器重新載入 JavaScript，因此可根據需求在正式版建構中移除。

### 明文流量 (API 等級 28+)

> 從 Android 9（API 級別 28）開始，預設禁用明文傳輸；這會阻止您的應用程式連接到 [Metro 打包器][metro]。以下變更允許在除錯版本中使用明文傳輸。

#### 1. 將 `usesCleartextTraffic` 選項應用於您的除錯版 `AndroidManifest.xml`

```xml
<!-- ... -->
<application
  android:usesCleartextTraffic="true" tools:targetApi="28" >
  <!-- ... -->
</application>
<!-- ... -->
```

此設定在正式版本中不需要。

要了解更多關於網路安全配置和明文傳輸政策的資訊，[請參閱此連結](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted)。

### 程式碼整合

現在我們將實際修改原生 Android 應用程式以整合 React Native。

#### React Native 元件

我們將編寫的第一段程式碼是用於整合到我們應用程式中的新「高分」畫面的實際 React Native 程式碼。

##### 1. 建立 `index.js` 檔案

首先，在您的 React Native 專案根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，並且總是必需的。它可以是一個小檔案，`require` 其他屬於您的 React Native 元件或應用程式的檔案，或者它可以包含所有需要的程式碼。在我們的例子中，我們將把所有內容放在 `index.js` 中。

##### 2. 新增您的 React Native 程式碼

在您的 `index.js` 中，建立您的元件。在我們的範例中，我們將在一個帶有樣式的 `<View>` 中新增一個 `<Text>` 元件：

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

##### 3. 為開發錯誤覆蓋配置權限

如果您的應用程式目標是 Android `API 級別 23` 或更高，請確保您已為開發版本啟用 `android.permission.SYSTEM_ALERT_WINDOW` 權限。您可以使用 `Settings.canDrawOverlays(this);` 來檢查這一點。這在開發版本中是必需的，因為 React Native 開發錯誤必須顯示在所有其他視窗之上。由於 API 級別 23（Android M）中引入的新權限系統，用戶需要批准它。這可以通過在您的 Activity 的 `onCreate()` 方法中新增以下程式碼來實現。

```java
private final int OVERLAY_PERMISSION_REQ_CODE = 1;  // Choose any value

...

if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    if (!Settings.canDrawOverlays(this)) {
        Intent intent = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION,
                                   Uri.parse("package:" + getPackageName()));
        startActivityForResult(intent, OVERLAY_PERMISSION_REQ_CODE);
    }
}
```

最後，必須覆寫 `onActivityResult()` 方法（如下面的程式碼所示）以處理權限接受或拒絕的情況，以確保一致的用戶體驗。此外，對於使用 `startActivityForResult` 的整合原生模組，我們需要將結果傳遞給我們的 `ReactInstanceManager` 實例的 `onActivityResult` 方法。

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == OVERLAY_PERMISSION_REQ_CODE) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            if (!Settings.canDrawOverlays(this)) {
                // SYSTEM_ALERT_WINDOW permission not granted
            }
        }
    }
    mReactInstanceManager.onActivityResult( this, requestCode, resultCode, data );
}
```

#### 魔法：`ReactRootView`

讓我們新增一些原生程式碼來啟動 React Native 運行時並告訴它渲染我們的 JS 元件。為此，我們將建立一個 `Activity`，它建立一個 `ReactRootView`，在其中啟動一個 React 應用程式，並將其設為主要內容視圖。

> 如果您的目標是 Android 版本 &lt;5，請使用 `com.android.support:appcompat` 套件中的 `AppCompatActivity` 類別，而不是 `Activity`。

```java
public class MyReactActivity extends Activity implements DefaultHardwareBackBtnHandler {
    private ReactRootView mReactRootView;
    private ReactInstanceManager mReactInstanceManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        SoLoader.init(this, false);

        mReactRootView = new ReactRootView(this);
        List<ReactPackage> packages = new PackageList(getApplication()).getPackages();
        // Packages that cannot be autolinked yet can be added manually here, for example:
        // packages.add(new MyReactNativePackage());
        // Remember to include them in `settings.gradle` and `app/build.gradle` too.

        mReactInstanceManager = ReactInstanceManager.builder()
                .setApplication(getApplication())
                .setCurrentActivity(this)
                .setBundleAssetName("index.android.bundle")
                .setJSMainModulePath("index")
                .addPackages(packages)
                .setUseDeveloperSupport(BuildConfig.DEBUG)
                .setInitialLifecycleState(LifecycleState.RESUMED)
                .build();
        // The string here (e.g. "MyReactNativeApp") has to match
        // the string in AppRegistry.registerComponent() in index.js
        mReactRootView.startReactApplication(mReactInstanceManager, "MyReactNativeApp", null);

        setContentView(mReactRootView);
    }

    @Override
    public void invokeDefaultOnBackPressed() {
        super.onBackPressed();
    }
}
```

> 如果您使用的是 React Native 的入門套件，請將 "HelloWorld" 字串替換為您的 index.js 檔案中的字串（這是 `AppRegistry.registerComponent()` 方法的第一個參數）。

執行「Sync Project files with Gradle」操作。

如果您使用的是 Android Studio，請使用 `Alt + Enter` 來新增您的 MyReactActivity 類別中所有缺少的導入。請注意使用您的套件的 `BuildConfig`，而不是 `facebook` 套件的。

我們需要將 `MyReactActivity` 的主題設為 `Theme.AppCompat.Light.NoActionBar`，因為一些 React Native UI 元件依賴於此主題。

```xml
<activity
  android:name=".MyReactActivity"
  android:label="@string/app_name"
  android:theme="@style/Theme.AppCompat.Light.NoActionBar">
</activity>
```

> 一個 `ReactInstanceManager` 可以被多個活動(Activity)和/或片段(Fragment)共享。您需要創建自己的 `ReactFragment` 或 `ReactActivity`，並使用一個單例 _持有者_ 來保存 `ReactInstanceManager`。當您需要 `ReactInstanceManager` 時（例如，將 `ReactInstanceManager` 掛鉤到這些活動或片件的生命週期中），請使用該單例提供的實例。

接下來，我們需要將一些活動生命週期回調傳遞給 `ReactInstanceManager` 和 `ReactRootView`：

```java
@Override
protected void onPause() {
    super.onPause();

    if (mReactInstanceManager != null) {
        mReactInstanceManager.onHostPause(this);
    }
}

@Override
protected void onResume() {
    super.onResume();

    if (mReactInstanceManager != null) {
        mReactInstanceManager.onHostResume(this, this);
    }
}

@Override
protected void onDestroy() {
    super.onDestroy();

    if (mReactInstanceManager != null) {
        mReactInstanceManager.onHostDestroy(this);
    }
    if (mReactRootView != null) {
        mReactRootView.unmountReactApplication();
    }
}
```

我們還需要將返回按鈕事件傳遞給 React Native：

```java
@Override
 public void onBackPressed() {
    if (mReactInstanceManager != null) {
        mReactInstanceManager.onBackPressed();
    } else {
        super.onBackPressed();
    }
}
```

這允許 JavaScript 控制當用戶按下硬體返回按鈕時的行為（例如實現導航功能）。當 JavaScript 未處理返回按鈕事件時，將調用您的 `invokeDefaultOnBackPressed` 方法。默認情況下，這會結束您的 `Activity`。

最後，我們需要掛載開發者選單。默認情況下，這通過（猛烈）搖動設備來激活，但在模擬器中不太實用。因此我們設置當您按下硬體選單按鈕時顯示（如果使用 Android Studio 模擬器，請使用 `Ctrl + M`）：

```java
@Override
public boolean onKeyUp(int keyCode, KeyEvent event) {
    if (keyCode == KeyEvent.KEYCODE_MENU && mReactInstanceManager != null) {
        mReactInstanceManager.showDevOptionsDialog();
        return true;
    }
    return super.onKeyUp(keyCode, event);
}
```

現在您的活動已準備好運行一些 JavaScript 代碼。

### 測試您的整合

您現在已完成將 React Native 整合到現有應用程序中的所有基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來構建 `index.bundle` 包，並啟動本地主機上的伺服器來提供它。

##### 1. 運行打包工具

要運行您的應用程序，首先需要啟動開發伺服器。為此，請在 React Native 專案的根目錄中運行以下命令：

```shell
$ yarn start
```

##### 2. 運行應用程序

現在像平常一樣構建並運行您的 Android 應用程序。

當您進入應用程序中由 React 驅動的活動時，它應該會從開發伺服器加載 JavaScript 代碼並顯示：

![螢幕截圖](/docs/assets/EmbeddedAppAndroid.png)

### 在 Android Studio 中創建發布版本

您也可以使用 Android Studio 創建發布版本！這與創建現有原生 Android 應用程序的發布版本一樣快捷。在每次創建發布版本之前，您需要執行一個額外步驟。您需要執行以下命令來創建 React Native 包，該包將包含在您的原生 Android 應用程序中：

```shell
$ npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/com/your-company-name/app-package-name/src/main/assets/index.android.bundle --assets-dest android/com/your-company-name/app-package-name/src/main/res/
```

> 別忘記替換路徑為正確的路徑，並在不存在時創建 assets 文件夾。

現在，像往常一樣在 Android Studio 中創建您的原生應用程序的發布版本，您應該就可以開始了！

### 接下來呢？

此時您可以像往常一樣繼續開發您的應用程序。參考我們的[調試指南](debugging)和[部署指南](running-on-device)以了解更多關於使用 React Native 的資訊。

[metro]: https://metrobundler.dev/