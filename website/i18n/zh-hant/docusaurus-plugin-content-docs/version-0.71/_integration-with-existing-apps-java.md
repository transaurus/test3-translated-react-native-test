## 核心概念

將 React Native 元件整合至 Android 應用的關鍵在於：

1. 設定 React Native 依賴項與目錄結構。
2. 使用 JavaScript 開發 React Native 元件。
3. 在 Android 應用中添加 `ReactRootView`，此視圖將作為 React Native 元件的容器。
4. 啟動 React Native 伺服器並運行原生應用。
5. 驗證應用的 React Native 部分是否如預期運作。

## 必要條件

請遵循[環境設定指南](environment-setup)中的 React Native CLI 快速入門，為建構 Android 版 React Native 應用配置開發環境。

### 1. 設定目錄結構

為確保流程順暢，請為整合的 React Native 專案建立新資料夾，然後將現有的 Android 專案複製到 `/android` 子資料夾中。

### 2. 安裝 JavaScript 依賴項

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

這將輸出類似以下的訊息（請在 yarn 輸出中向上滾動查看）：

> 警告："react-native@0.70.5" 有未滿足的對等依賴 "react@18.1.0"

這是正常的，表示我們還需要安裝 React：

```shell
$ yarn add react@version_printed_above
```

Yarn 已建立新的 `/node_modules` 資料夾，此資料夾儲存建構專案所需的所有 JavaScript 依賴項。

請將 `node_modules/` 加入你的 `.gitignore` 檔案。

## 將 React Native 加入你的應用

### 配置 Gradle

React Native 使用 React Native Gradle 插件來配置依賴項與專案設定。

首先，請編輯你的 `settings.gradle` 檔案，加入這一行：

```groovy
includeBuild('../node_modules/react-native-gradle-plugin')
```

接著開啟頂層的 `build.gradle` 並包含這一行：

```diff
buildscript {
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath("com.android.tools.build:gradle:7.3.1")
+       classpath("com.facebook.react:react-native-gradle-plugin")
    }
}
```

這確保了 React Native Gradle 插件可在專案中使用。
最後，在你的應用 `build.gradle` 檔案（位於應用資料夾內的另一個 `build.gradle` 檔案）中加入以下內容：

```diff
apply plugin: "com.android.application"
+apply plugin: "com.facebook.react"

repositories {
    mavenCentral()
}

dependencies {
    // Other dependencies here
+   implementation "com.facebook.react:react-android"
+   implementation "com.facebook.react:hermes-android"
}
```

這些依賴項可在 `mavenCentral()` 取得，請確認已在 `repositories{}` 區塊中定義。

:::info
我們刻意不為這些 `implementation` 依賴項指定版本，因為 React Native Gradle 插件會自動處理。若未使用 React Native Gradle 插件，則需手動指定版本。
:::

### 啟用原生模組自動連結

要使用[自動連結](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)的功能，需在幾個地方進行設定。首先在 `settings.gradle` 中加入以下條目：

```gradle
apply from: file("../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesSettingsGradle(settings)
```

接著在 `app/build.gradle` 的最底部加入以下條目：

```gradle
apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
```

### 配置權限

接下來，請確認你的 `AndroidManifest.xml` 中包含網路權限：

<uses-permission android:name="android.permission.INTERNET" />

若需存取 `DevSettingsActivity`，請在 `AndroidManifest.xml` 中加入：

<activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />

此設定僅在開發模式下用於從開發伺服器重新載入 JavaScript，因此若有必要可在正式版建置中移除。

### 明文傳輸 (API 等級 28+)

> 從 Android 9 (API 等級 28) 開始，預設禁用明文傳輸；這會阻止應用程式連接到 [Metro 打包器][metro]。以下變更允許在除錯版建置中使用明文傳輸。

#### 1. 在除錯版 `AndroidManifest.xml` 中啟用 `usesCleartextTraffic`

```xml
<!-- ... -->
<application
  android:usesCleartextTraffic="true" tools:targetApi="28" >
  <!-- ... -->
</application>
<!-- ... -->
```

正式版建置無需此設定。

進一步了解網路安全設定與明文傳輸政策，請參閱[此連結](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted)。

### 程式碼整合

現在我們將實際修改原生 Android 應用程式以整合 React Native。

#### React Native 元件

我們將編寫的第一段程式碼是用於整合到應用程式中的新「高分」畫面的實際 React Native 程式碼。

##### 1. 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且為必要檔案。它可以是一個小檔案，用於 `require` 其他屬於 React Native 元件或應用程式的檔案，也可以包含所有需要的程式碼。在本例中，我們會將所有內容放在 `index.js`。

##### 2. 加入 React Native 程式碼

在 `index.js` 中建立您的元件。在此範例中，我們將在一個帶樣式的 `<View>` 中加入 `<Text>` 元件：

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

##### 3. 設定開發錯誤覆蓋層權限

若您的應用程式目標為 Android `API 等級 23` 或更高，請確保開發版建置已啟用 `android.permission.SYSTEM_ALERT_WINDOW` 權限。您可使用 `Settings.canDrawOverlays(this);` 檢查此權限。開發版建置需要此權限，因為 React Native 開發錯誤必須顯示在所有其他視窗之上。由於 API 等級 23 (Android M) 引入的新權限系統，使用者需手動核准。可透過在 Activity 的 `onCreate()` 方法中加入以下程式碼實現：

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

最後，需覆寫 `onActivityResult()` 方法（如下方程式碼所示）以處理權限核准或拒絕的情況，確保一致的用戶體驗。此外，對於使用 `startActivityForResult` 的原生模組整合，我們需將結果傳遞給 `ReactInstanceManager` 實例的 `onActivityResult` 方法。

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

#### 魔法所在：`ReactRootView`

讓我們加入一些原生程式碼來啟動 React Native 運行時環境，並告訴它渲染我們的 JS 元件。為此，我們將建立一個 `Activity`，該 Activity 會建立一個 `ReactRootView`，在其中啟動 React 應用程式並將其設為主要內容視圖。

> 若目標為 Android 版本 &lt;5，請使用 `com.android.support:appcompat` 套件中的 `AppCompatActivity` 類別，而非 `Activity`。

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

> 若您使用 React Native 入門套件，請將 "HelloWorld" 字串替換為您 index.js 檔案中的字串（這是 `AppRegistry.registerComponent()` 方法的第一個參數）。

執行「Sync Project files with Gradle」操作。

若使用 Android Studio，請使用 `Alt + Enter` 為你的 MyReactActivity 類別自動補齊所有缺失的導入。注意要使用你自己套件的 `BuildConfig`，而非 `facebook` 套件中的版本。

我們需將 `MyReactActivity` 的主題設為 `Theme.AppCompat.Light.NoActionBar`，因為部分 React Native UI 元件依賴此主題。

```xml
<activity
  android:name=".MyReactActivity"
  android:label="@string/app_name"
  android:theme="@style/Theme.AppCompat.Light.NoActionBar">
</activity>
```

> 單一 `ReactInstanceManager` 可被多個活動(Activity)或片段(Fragment)共享。建議建立自訂的 `ReactFragment` 或 `ReactActivity`，並透過單例模式持有 `ReactInstanceManager`。當需要時（例如將生命週期鉤子與這些活動/片段綁定），使用該單例提供的實例。

接著需將部分活動生命週期回調傳遞給 `ReactInstanceManager` 和 `ReactRootView`：

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

還需將返回按鈕事件傳遞至 React Native：

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

這允許 JavaScript 控制硬體返回按鈕的行為（例如實現導航）。若 JavaScript 未處理返回事件，則會調用 `invokeDefaultOnBackPressed` 方法，預設情況下會結束當前活動(Activity)。

最後需連接開發者選單。預設通過搖動設備觸發（但在模擬器中不實用），因此改為按下硬體選單按鈕觸發（Android Studio 模擬器請用 `Ctrl + M`）：

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

現在你的活動已準備好執行 JavaScript 程式碼。

### 測試整合結果

至此已完成 React Native 與現有應用的基礎整合。現在我們將啟動 [Metro 打包工具][metro] 來建構 `index.bundle` 套件，並運行本地伺服器提供該套件。

##### 1. 啟動打包伺服器

在 React Native 專案根目錄執行以下命令以啟動開發伺服器：

```shell
$ yarn start
```

##### 2. 運行應用程式

照常建置並運行你的 Android 應用程式。

當進入應用內 React 驅動的活動時，應會從開發伺服器載入 JavaScript 程式碼並顯示：

![螢幕截圖](/docs/assets/EmbeddedAppAndroid.png)

### 在 Android Studio 建立正式版建構

如同建構既有原生 Android 應用程式，你可直接使用 Android Studio 建立正式版建構。

若按照前述方式使用 React Native Gradle 插件，從 Android Studio 運行應用程式時所有功能應能正常運作。

若未使用該插件，每次正式建構前需額外執行以下命令來產生 React Native 套件（該套件將被包含在原生應用中）：

```shell
$ npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/com/your-company-name/app-package-name/src/main/assets/index.android.bundle --assets-dest android/com/your-company-name/app-package-name/src/main/res/
```

> 請注意替換正確路徑，若 assets 資料夾不存在需手動建立。

現在可如往常透過 Android Studio 建立正式版建構，一切準備就緒！

### 後續步驟

現在你可繼續常規開發流程。參考我們的[除錯指南](debugging)與[部署文件](running-on-device)以深入瞭解 React Native 開發。

[metro]: https://metrobundler.dev/