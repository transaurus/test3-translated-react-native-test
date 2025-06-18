import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 Android 應用的關鍵在於：

1. 設定 React Native 依賴項與目錄結構
2. 使用 JavaScript 開發 React Native 元件
3. 在 Android 應用中添加 `ReactRootView`，該視圖將作為 React Native 元件的容器
4. 啟動 React Native 伺服器並運行原生應用
5. 驗證應用的 React Native 功能是否正常運作

## 必要條件

請遵循[設定開發環境指南](set-up-your-environment)來配置 Android 版 React Native 應用的開發環境

### 1. 設定目錄結構

為確保流程順暢，請為整合專案新建資料夾，並將現有 Android 專案複製到 `/android` 子資料夾中

### 2. 安裝 JavaScript 依賴項

進入專案根目錄，創建包含以下內容的 `package.json` 檔案：

```
{
  "name": "MyReactNativeApp",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "react-native start"
  }
}
```

接著安裝 `react` 和 `react-native` 套件。開啟終端機或命令提示字元，導航至含 `package.json` 的目錄並執行：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install react-native
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add react-native
```

</TabItem>
</Tabs>

這將顯示類似以下的訊息（請向上滾動安裝命令輸出以查看）：

> 警告："`react-native@0.70.5`" 有未滿足的 peer 依賴項 "`react@18.1.0`"

這表示我們還需要安裝 React：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install react@version_printed_above
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add react@version_printed_above
```

</TabItem>
</Tabs>

安裝過程會創建新的 `/node_modules` 資料夾，該資料夾儲存專案所需的所有 JavaScript 依賴項

請將 `node_modules/` 加入你的 `.gitignore` 檔案

## 將 React Native 加入應用

### 配置 Gradle

React Native 使用 React Native Gradle 插件來配置依賴項與專案設定

首先修改 `settings.gradle` 檔案，加入這行：

```groovy
includeBuild('../node_modules/@react-native/gradle-plugin')
```

接著在頂層 `build.gradle` 中加入這行：

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

這確保 React Native Gradle 插件能在專案中使用
最後在應用模組的 `build.gradle` 檔案（位於應用資料夾內的另一個 `build.gradle`）中加入這些設定：

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

這些依賴項可從 `mavenCentral()` 取得，請確認已在 `repositories{}` 區塊中定義

:::info
我們刻意不指定這些 `implementation` 依賴項的版本，因為 React Native Gradle 插件會自動處理。若未使用該插件，則需手動指定版本
:::

### 啟用原生模組自動連結

要使用[自動連結](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)功能，需在多處進行配置。首先在 `settings.gradle` 中加入：

```gradle
apply from: file("../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesSettingsGradle(settings)
```

接著在 `app/build.gradle` 最底部加入：

```gradle
apply from: file("../../node_modules/@react-native-community/cli-platform-android/native_modules.gradle"); applyNativeModulesAppBuildGradle(project)
```

### 配置權限

確認 `AndroidManifest.xml` 中已啟用網路權限：

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

如需存取 `DevSettingsActivity`，請在 `AndroidManifest.xml` 中加入：

```xml
<activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
```

這僅在開發模式下從開發伺服器重新載入 JavaScript 時使用，因此如果需要，您可以在正式版本中移除這部分。

### 明文流量 (API 等級 28+)

> 從 Android 9 (API 等級 28) 開始，預設禁用明文流量；這會阻止您的應用程式連接到 [Metro 打包器][metro]。以下變更允許在除錯版本中使用明文流量。

#### 1. 將 `usesCleartextTraffic` 選項套用至您的除錯版 `AndroidManifest.xml`

```xml
<!-- ... -->
<application
  android:usesCleartextTraffic="true" tools:targetApi="28" >
  <!-- ... -->
</application>
<!-- ... -->
```

正式版本不需要此設定。

要了解更多關於網路安全設定和明文流量政策的資訊，[請參閱此連結](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted)。

### 程式碼整合

現在我們將實際修改原生 Android 應用程式以整合 React Native。

#### React Native 元件

我們將編寫的第一段程式碼是用於整合到我們應用程式中的新「高分」畫面的實際 React Native 程式碼。

##### 1. 建立 `index.js` 檔案

首先，在您的 React Native 專案根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且始終是必需的。它可以是一個小檔案，`require` 其他屬於您的 React Native 元件或應用程式的檔案，或者它可以包含所需的所有程式碼。在我們的案例中，我們將把所有內容放在 `index.js` 中。

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
const styles = StyleSheet.create({
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

##### 3. 設定開發錯誤覆蓋層的權限

如果您的應用程式目標是 Android `API 等級 23` 或更高，請確保您已為開發版本啟用 `android.permission.SYSTEM_ALERT_WINDOW` 權限。您可以使用 `Settings.canDrawOverlays(this);` 來檢查這一點。這在開發版本中是必需的，因為 React Native 開發錯誤必須顯示在所有其他視窗之上。由於 API 等級 23 (Android M) 引入的新權限系統，使用者需要批准它。這可以透過在您的 Activity 的 `onCreate()` 方法中新增以下程式碼來實現。

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

讓我們新增一些原生程式碼以啟動 React Native 運行時並告訴它渲染我們的 JS 元件。為此，我們將建立一個 `Activity`，該 Activity 建立一個 `ReactRootView`，在其中啟動一個 React 應用程式並將其設為主要內容視圖。

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

如果您使用的是 Android Studio，請使用 `Alt + Enter` 在您的 MyReactActivity 類別中新增所有缺少的導入。請注意使用您的套件的 `BuildConfig`，而不是 `facebook` 套件中的那個。

我們需要將 `MyReactActivity` 的主題設為 `Theme.AppCompat.Light.NoActionBar`，因為部分 React Native UI 元件依賴此主題。

```xml
<activity
  android:name=".MyReactActivity"
  android:label="@string/app_name"
  android:theme="@style/Theme.AppCompat.Light.NoActionBar">
</activity>
```

> 單一 `ReactInstanceManager` 可被多個 activity 或 fragment 共享。建議建立自訂的 `ReactFragment` 或 `ReactActivity`，並透過單例模式持有 `ReactInstanceManager`。當需要時（例如將 `ReactInstanceManager` 與生命週期綁定），直接使用該單例提供的實例。

接著，我們需要將部分 activity 生命週期回調傳遞給 `ReactInstanceManager` 和 `ReactRootView`：

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

同時需將返回按鈕事件傳遞給 React Native：

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

這允許 JavaScript 控制硬體返回按鈕的行為（例如實現導航邏輯）。若 JavaScript 未處理返回事件，則會調用 `invokeDefaultOnBackPressed` 方法，預設行為是關閉當前 `Activity`。

最後需設定開發者選單觸發方式。預設透過搖動裝置觸發，但在模擬器中可改為透過硬體選單按鈕觸發（Android Studio 模擬器請使用 <kbd>Ctrl</kbd> + <kbd>M</kbd>）：

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

至此，您的 activity 已準備好執行 JavaScript 程式碼。

### 測試整合結果

已完成 React Native 與現有應用整合的基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來建置 `index.bundle` 套件，並啟動本地伺服器提供該套件。

##### 1. 啟動打包伺服器

執行應用前需先啟動開發伺服器。在 React Native 專案根目錄執行以下指令：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm start
```

</TabItem>
<TabItem value="yarn">

```shell
yarn start
```

</TabItem>
</Tabs>

##### 2. 執行應用

像往常一樣建置並執行您的 Android 應用。

當進入應用中整合 React 的 activity 時，應會從開發伺服器載入 JavaScript 程式碼並顯示：

![螢幕截圖](/docs/assets/EmbeddedAppAndroid.png)

### 在 Android Studio 建立正式版

您也可以使用 Android Studio 建立正式版！流程與原本建置原生 Android 應用正式版完全相同。

若按照前述方式使用 React Native Gradle 插件，從 Android Studio 執行應用時所有功能應能正常運作。

若未使用 React Native Gradle 插件，每次建置正式版前需額外執行以下指令來建立 React Native 套件（該套件會與原生應用一起打包）：

```shell
$ npx react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/com/your-company-name/app-package-name/src/main/assets/index.android.bundle --assets-dest android/com/your-company-name/app-package-name/src/main/res/
```

> 請注意替換正確路徑，若 assets 資料夾不存在請先建立。

現在像往常一樣透過 Android Studio 建立原生應用的正式版即可！

### 後續步驟

現在您可以繼續常規開發流程。請參考我們的[除錯指南](debugging)和[部署文件](running-on-device)來深入瞭解 React Native 開發。

[metro]: https://metrobundler.dev/