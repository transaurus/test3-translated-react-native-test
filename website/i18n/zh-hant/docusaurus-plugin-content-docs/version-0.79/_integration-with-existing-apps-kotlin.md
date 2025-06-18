import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 Android 應用的關鍵在於：

1. 建立正確的目錄結構
2. 安裝必要的 NPM 相依套件
3. 在 Gradle 配置中加入 React Native
4. 為首個 React Native 畫面編寫 TypeScript 程式碼
5. 使用 ReactActivity 將 React Native 與 Android 程式碼整合
6. 執行打包工具並查看應用運作情況來測試整合結果

## 使用社群範本

遵循本指南時，建議您參考 [React Native 社群範本](https://github.com/react-native-community/template/)。該範本包含一個**最小化的 Android 應用**，可幫助您理解如何將 React Native 整合至現有 Android 應用中。

## 必要條件

請先依照[設定開發環境](set-up-your-environment)指南與[不使用框架的 React Native](getting-started-without-a-framework)指南，配置您的 Android 版 React Native 應用開發環境。
本指南同時假設您已熟悉 Android 開發基礎知識，例如建立 Activity 與編輯 `AndroidManifest.xml` 檔案。

## 1. 設定目錄結構

為確保流程順暢，請為整合式 React Native 專案建立新資料夾，然後將**現有 Android 專案**移至 `/android` 子資料夾中。

## 2. 安裝 NPM 相依套件

進入根目錄並執行以下指令：

```shell
curl -O https://raw.githubusercontent.com/react-native-community/template/refs/heads/0.75-stable/template/package.json
```

此操作會將[社群範本中的 package.json 檔案](https://github.com/react-native-community/template/blob/0.75-stable/template/package.json)複製到您的專案中。

接著執行以下指令安裝 NPM 套件：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install
```

</TabItem>
<TabItem value="yarn">

```shell
yarn install
```

</TabItem>
</Tabs>

安裝程序會建立新的 `node_modules` 資料夾，此資料夾儲存了建置專案所需的所有 JavaScript 相依套件。

請將 `node_modules/` 加入您的 `.gitignore` 檔案（可參考[社群預設範本](https://github.com/react-native-community/template/blob/0.75-stable/template/_gitignore)）。

## 3. 將 React Native 加入應用

### 配置 Gradle

React Native 使用 React Native Gradle 插件來配置相依套件與專案設定。

首先編輯 `settings.gradle` 檔案並加入以下內容（參照[社群範本建議](https://github.com/react-native-community/template/blob/0.77-stable/template/android/settings.gradle)）：

```groovy
// Configures the React Native Gradle Settings plugin used for autolinking
pluginManagement { includeBuild("../node_modules/@react-native/gradle-plugin") }
plugins { id("com.facebook.react.settings") }
extensions.configure(com.facebook.react.ReactSettingsExtension){ ex -> ex.autolinkLibrariesFromCommand() }
// If using .gradle.kts files:
// extensions.configure<com.facebook.react.ReactSettingsExtension> { autolinkLibrariesFromCommand() }
includeBuild("../node_modules/@react-native/gradle-plugin")

// Include your existing Gradle modules here.
// include(":app")
```

接著開啟頂層的 `build.gradle` 並加入這行（參照[社群範本建議](https://github.com/react-native-community/template/blob/0.77-stable/template/android/build.gradle)）：

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

此操作確保 React Native Gradle 插件 (RNGP) 可在專案中使用。
最後在應用程式的 `build.gradle` 檔案中加入以下內容（此為不同的 `build.gradle` 檔案，通常位於 `app` 資料夾內 - 可參考[社群範本檔案](https://github.com/react-native-community/template/blob/0.77-stable/template/android/app/build.gradle)）：

```diff
apply plugin: "com.android.application"
+apply plugin: "com.facebook.react"

repositories {
    mavenCentral()
}

dependencies {
    // Other dependencies here
+   // Note: we intentionally don't specify the version number here as RNGP will take care of it.
+   // If you don't use the RNGP, you'll have to specify version manually.
+   implementation("com.facebook.react:react-android")
+   implementation("com.facebook.react:hermes-android")
}

+react {
+   // Needed to enable Autolinking - https://github.com/react-native-community/cli/blob/master/docs/autolinking.md
+   autolinkLibrariesWithApp()
+}
```

最後開啟應用程式的 `gradle.properties` 檔案並加入以下內容（參照[社群範本檔案](https://github.com/react-native-community/template/blob/0.77-stable/template/android/gradle.properties)）：

```diff
+reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
+newArchEnabled=true
+hermesEnabled=true
```

### 配置清單檔案

首先確認您的 `AndroidManifest.xml` 已包含網路權限：

```diff
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

+   <uses-permission android:name="android.permission.INTERNET" />

    <application
      android:name=".MainApplication">
    </application>
</manifest>
```

接著您需要在 **debug** 版的 `AndroidManifest.xml` 中啟用 [明文傳輸](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted)：

```diff
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
+       android:usesCleartextTraffic="true"
+       tools:targetApi="28"
    />
</manifest>
```

如同往常，這裡提供社群模板中的 AndroidManifest.xml 檔案作為參考：[main](https://github.com/react-native-community/template/blob/0.77-stable/template/android/app/src/main/AndroidManifest.xml) 和 [debug](https://github.com/react-native-community/template/blob/0.77-stable/template/android/app/src/debug/AndroidManifest.xml)

這是必要的，因為您的應用程式將透過 HTTP 與本地打包工具 [Metro][https://metrobundler.dev/] 進行通訊。

請確保僅在 **debug** 版清單中添加此設定。

## 4. 撰寫 TypeScript 程式碼

現在我們將實際修改原生 Android 應用程式以整合 React Native。

我們要編寫的第一段程式碼是用於新畫面的實際 React Native 程式碼，該畫面將被整合到我們的應用程式中。

### 建立 `index.js` 檔案

首先，在您的 React Native 專案根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且始終是必需的。它可以是一個小檔案，用於 `import` 其他屬於您的 React Native 元件或應用程式的檔案，也可以包含所需的所有程式碼。

我們的 index.js 應如下所示（這裡提供 [社群模板檔案作為參考](https://github.com/react-native-community/template/blob/0.77-stable/template/index.js)）：

```js
import {AppRegistry} from 'react-native';
import App from './App';

AppRegistry.registerComponent('HelloWorld', () => App);
```

### 建立 `App.tsx` 檔案

讓我們建立一個 `App.tsx` 檔案。這是一個 [TypeScript](https://www.typescriptlang.org/) 檔案，可以包含 [JSX](<https://en.wikipedia.org/wiki/JSX_(JavaScript)>) 表達式。它包含了我們將整合到 Android 應用程式中的根 React Native 元件（[連結](https://github.com/react-native-community/template/blob/0.77-stable/template/App.tsx)）：

```tsx
import React from 'react';
import {
  SafeAreaView,
  ScrollView,
  StatusBar,
  StyleSheet,
  Text,
  useColorScheme,
  View,
} from 'react-native';

import {
  Colors,
  DebugInstructions,
  Header,
  ReloadInstructions,
} from 'react-native/Libraries/NewAppScreen';

function App(): React.JSX.Element {
  const isDarkMode = useColorScheme() === 'dark';

  const backgroundStyle = {
    backgroundColor: isDarkMode ? Colors.darker : Colors.lighter,
  };

  return (
    <SafeAreaView style={backgroundStyle}>
      <StatusBar
        barStyle={isDarkMode ? 'light-content' : 'dark-content'}
        backgroundColor={backgroundStyle.backgroundColor}
      />
      <ScrollView
        contentInsetAdjustmentBehavior="automatic"
        style={backgroundStyle}>
        <Header />
        <View
          style={{
            backgroundColor: isDarkMode
              ? Colors.black
              : Colors.white,
            padding: 24,
          }}>
          <Text style={styles.title}>Step One</Text>
          <Text>
            Edit <Text style={styles.bold}>App.tsx</Text> to
            change this screen and see your edits.
          </Text>
          <Text style={styles.title}>See your changes</Text>
          <ReloadInstructions />
          <Text style={styles.title}>Debug</Text>
          <DebugInstructions />
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  title: {
    fontSize: 24,
    fontWeight: '600',
  },
  bold: {
    fontWeight: '700',
  },
});

export default App;
```

這裡提供 [社群模板檔案作為參考](https://github.com/react-native-community/template/blob/0.77-stable/template/App.tsx)

## 5. 與您的 Android 程式碼整合

我們現在需要添加一些原生程式碼，以啟動 React Native 運行時並告訴它渲染我們的 React 元件。

### 更新您的 Application 類別

首先，我們需要更新您的 `Application` 類別以正確初始化 React Native，如下所示：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>

<TabItem value="java">

```diff
package <your-package-here>;

import android.app.Application;
+import com.facebook.react.PackageList;
+import com.facebook.react.ReactApplication;
+import com.facebook.react.ReactHost;
+import com.facebook.react.ReactNativeHost;
+import com.facebook.react.ReactPackage;
+import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint;
+import com.facebook.react.defaults.DefaultReactHost;
+import com.facebook.react.defaults.DefaultReactNativeHost;
+import com.facebook.soloader.SoLoader;
+import com.facebook.react.soloader.OpenSourceMergedSoMapping
+import java.util.List;

-class MainApplication extends Application {
+class MainApplication extends Application implements ReactApplication {
+ @Override
+ public ReactNativeHost getReactNativeHost() {
+   return new DefaultReactNativeHost(this) {
+     @Override
+     protected List<ReactPackage> getPackages() { return new PackageList(this).getPackages(); }
+     @Override
+     protected String getJSMainModuleName() { return "index"; }
+     @Override
+     public boolean getUseDeveloperSupport() { return BuildConfig.DEBUG; }
+     @Override
+     protected boolean isNewArchEnabled() { return BuildConfig.IS_NEW_ARCHITECTURE_ENABLED; }
+     @Override
+     protected Boolean isHermesEnabled() { return BuildConfig.IS_HERMES_ENABLED; }
+   };
+ }

+ @Override
+ public ReactHost getReactHost() {
+   return DefaultReactHost.getDefaultReactHost(getApplicationContext(), getReactNativeHost());
+ }

  @Override
  public void onCreate() {
    super.onCreate();
+   SoLoader.init(this, OpenSourceMergedSoMapping);
+   if (BuildConfig.IS_NEW_ARCHITECTURE_ENABLED) {
+     DefaultNewArchitectureEntryPoint.load();
+   }
  }
}
```

</TabItem>

<TabItem value="kotlin">

```diff
// package <your-package-here>

import android.app.Application
+import com.facebook.react.PackageList
+import com.facebook.react.ReactApplication
+import com.facebook.react.ReactHost
+import com.facebook.react.ReactNativeHost
+import com.facebook.react.ReactPackage
+import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint.load
+import com.facebook.react.defaults.DefaultReactHost.getDefaultReactHost
+import com.facebook.react.defaults.DefaultReactNativeHost
+import com.facebook.soloader.SoLoader
+import com.facebook.react.soloader.OpenSourceMergedSoMapping

-class MainApplication : Application() {
+class MainApplication : Application(), ReactApplication {

+ override val reactNativeHost: ReactNativeHost =
+      object : DefaultReactNativeHost(this) {
+        override fun getPackages(): List<ReactPackage> = PackageList(this).packages
+        override fun getJSMainModuleName(): String = "index"
+        override fun getUseDeveloperSupport(): Boolean = BuildConfig.DEBUG
+        override val isNewArchEnabled: Boolean = BuildConfig.IS_NEW_ARCHITECTURE_ENABLED
+        override val isHermesEnabled: Boolean = BuildConfig.IS_HERMES_ENABLED
+      }

+ override val reactHost: ReactHost
+   get() = getDefaultReactHost(applicationContext, reactNativeHost)

  override fun onCreate() {
    super.onCreate()
+   SoLoader.init(this, OpenSourceMergedSoMapping)
+   if (BuildConfig.IS_NEW_ARCHITECTURE_ENABLED) {
+     load()
+   }
  }
}
```

</TabItem>
</Tabs>

如同往常，這裡提供 [MainApplication.kt 社群模板檔案作為參考](https://github.com/react-native-community/template/blob/0.77-stable/template/android/app/src/main/java/com/helloworld/MainApplication.kt)

#### 建立 `ReactActivity`

最後，我們需要建立一個新的 `Activity`，該 Activity 將擴展 `ReactActivity` 並託管 React Native 程式碼。此 Activity 將負責啟動 React Native 運行時並渲染 React 元件。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>

<TabItem value="java">

```java
// package <your-package-here>;

import com.facebook.react.ReactActivity;
import com.facebook.react.ReactActivityDelegate;
import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint;
import com.facebook.react.defaults.DefaultReactActivityDelegate;

public class MyReactActivity extends ReactActivity {

    @Override
    protected String getMainComponentName() {
        return "HelloWorld";
    }

    @Override
    protected ReactActivityDelegate createReactActivityDelegate() {
        return new DefaultReactActivityDelegate(this, getMainComponentName(), DefaultNewArchitectureEntryPoint.getFabricEnabled());
    }
}
```

</TabItem>

<TabItem value="kotlin">

```kotlin
// package <your-package-here>

import com.facebook.react.ReactActivity
import com.facebook.react.ReactActivityDelegate
import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint.fabricEnabled
import com.facebook.react.defaults.DefaultReactActivityDelegate

class MyReactActivity : ReactActivity() {

    override fun getMainComponentName(): String = "HelloWorld"

    override fun createReactActivityDelegate(): ReactActivityDelegate =
        DefaultReactActivityDelegate(this, mainComponentName, fabricEnabled)
}
```

</TabItem>
</Tabs>

如同往常，這裡提供 [MainActivity.kt 社群模板檔案作為參考](https://github.com/react-native-community/template/blob/0.77-stable/template/android/app/src/main/java/com/helloworld/MainApplication.kt)

每當您建立一個新的 Activity 時，都需要將其添加到您的 `AndroidManifest.xml` 檔案中。您還需要將 `MyReactActivity` 的主題設置為 `Theme.AppCompat.Light.NoActionBar`（或任何非 ActionBar 的主題），否則您的應用程式將在 React Native 畫面上方渲染一個 ActionBar：

```diff
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
      android:name=".MainApplication">

+     <activity
+       android:name=".MyReactActivity"
+       android:label="@string/app_name"
+       android:theme="@style/Theme.AppCompat.Light.NoActionBar">
+     </activity>
    </application>
</manifest>
```

現在您的 Activity 已準備好執行一些 JavaScript 程式碼。

## 6. 測試整合結果

您已完成整合 React Native 與應用程式的所有基本步驟。現在我們將啟動 [Metro 打包工具](https://metrobundler.dev/)，將您的 TypeScript 應用程式代碼打包成 bundle。Metro 的 HTTP 伺服器會將 bundle 從開發環境的 `localhost` 共享至模擬器或實體裝置，這使得[熱重載](https://reactnative.dev/blog/2016/03/24/introducing-hot-reloading)成為可能。

首先，您需要在專案根目錄下創建一個 `metro.config.js` 文件，內容如下：

```js
const {getDefaultConfig} = require('@react-native/metro-config');
module.exports = getDefaultConfig(__dirname);
```

您可以參考社群模板中的 [metro.config.js 文件](https://github.com/react-native-community/template/blob/0.77-stable/template/metro.config.js)作為範例。

配置文件就位後，即可運行打包工具。在專案根目錄下執行以下命令：

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

現在如常建置並運行您的 Android 應用程式。

當您進入應用中由 React 驅動的 Activity 時，它應會從開發伺服器加載 JavaScript 代碼並顯示：

<center><img src="/docs/assets/EmbeddedAppAndroidVideo.gif" width="300" /></center>

### 在 Android Studio 中創建正式版建置

您也可以使用 Android Studio 創建正式版建置！這與為既有原生 Android 應用創建正式版的流程同樣快速。

React Native Gradle 插件會負責將 JS 代碼打包進您的 APK/App Bundle 中。

若未使用 Android Studio，可通過以下命令創建正式版：

```
cd android
# For a Release APK
./gradlew :app:assembleRelease
# For a Release AAB
./gradlew :app:bundleRelease
```

### 接下來？

至此，您可以如常繼續開發應用程式。請參考我們的[除錯指南](debugging)和[部署文檔](running-on-device)，了解更多關於 React Native 開發的相關資訊。