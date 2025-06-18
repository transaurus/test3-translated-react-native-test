---
id: signed-apk-android
title: Publishing to Google Play Store
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

Android 要求所有應用程式在安裝前都必須使用憑證進行數位簽署。若想透過 [Google Play 商店](https://play.google.com/store) 發佈 Android 應用程式，必須使用發布金鑰簽署，且後續更新也需使用同一金鑰。自 2017 年起，Google Play 可透過 [Google Play 應用程式簽署](https://developer.android.com/studio/publish/app-signing#app-signing-google-play) 功能自動管理簽署流程。但上傳至 Google Play 前，應用程式二進位檔仍需使用上傳金鑰簽署。Android 開發者文件中的 [簽署您的應用程式](https://developer.android.com/tools/publishing/app-signing.html) 頁面詳細說明了此主題。本指南簡要概述流程，並列出封裝 JavaScript bundle 所需的步驟。

:::info
若您使用 Expo，請參閱 Expo 的 [部署至應用商店](https://docs.expo.dev/distribution/app-stores/) 指南，了解如何建置並提交應用程式至 Google Play 商店。本指南適用於任何 React Native 應用程式，可自動化部署流程。
:::

## 產生上傳金鑰

您可以使用 `keytool` 產生私密簽署金鑰。

### Windows

在 Windows 上，必須以管理員身分從 `C:\Program Files\Java\jdkx.x.x_x\bin` 執行 `keytool`。

```shell
keytool -genkeypair -v -storetype PKCS12 -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

此命令會提示您輸入金鑰庫和金鑰的密碼，以及金鑰的辨別名稱欄位。接著會產生名為 `my-upload-key.keystore` 的金鑰庫檔案。

金鑰庫包含單一金鑰，有效期為 10000 天。別名是後續簽署應用程式時使用的名稱，請務必記下此別名。

### macOS

在 macOS 上，若不確定 JDK bin 資料夾的位置，可執行以下命令查詢：

```shell
/usr/libexec/java_home
```

命令會輸出 JDK 的目錄，類似以下路徑：

```shell
/Library/Java/JavaVirtualMachines/jdkX.X.X_XXX.jdk/Contents/Home
```

使用 `cd /your/jdk/path` 命令切換至該目錄，並以 sudo 權限執行如下所示的 keytool 命令。

```shell
sudo keytool -genkey -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

:::caution
請妥善保管金鑰庫檔案。若遺失上傳金鑰或金鑰遭洩露，應 [遵循這些指示](https://support.google.com/googleplay/android-developer/answer/7384423#reset) 處理。
:::

## 設定 Gradle 變數

1. 將 `my-upload-key.keystore` 檔案置於專案目錄的 `android/app` 資料夾下。
2. 編輯 `~/.gradle/gradle.properties` 或 `android/gradle.properties` 檔案，加入以下內容（將 `*****` 替換為正確的金鑰庫密碼、別名和金鑰密碼）：

```
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

這些將成為全域 Gradle 變數，後續可在 Gradle 配置中用於簽署應用程式。

:::note[關於使用 git 的注意事項]
將上述 Gradle 變數儲存在 `~/.gradle/gradle.properties` 而非 `android/gradle.properties` 可避免其被提交至 git。您可能需在使用者主目錄中建立 `~/.gradle/gradle.properties` 檔案後才能新增變數。
:::

:::note[關於安全性的注意事項]
若不願以明文儲存密碼，且使用 macOS，可 [將憑證儲存在 Keychain Access 應用程式](https://pilloxa.gitlab.io/posts/safer-passwords-in-gradle/) 中。如此便可省略 `~/.gradle/gradle.properties` 中的最後兩行。
:::

## 將簽署配置加入應用程式的 Gradle 配置

The last configuration step that needs to be done is to setup release builds to be signed using upload key. Edit the file `android/app/build.gradle` in your project folder, and add the signing config,

```groovy
...
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```

## Generating the release AAB

Run the following command in a terminal:

```shell
npx react-native build-android --mode=release
```

This command uses Gradle's `bundleRelease` under the hood that bundles all the JavaScript needed to run your app into the AAB ([Android App Bundle](https://developer.android.com/guide/app-bundle)). If you need to change the way the JavaScript bundle and/or drawable resources are bundled (e.g. if you changed the default file/folder names or the general structure of the project), have a look at `android/app/build.gradle` to see how you can update it to reflect these changes.

:::note
Make sure `gradle.properties` does not include `org.gradle.configureondemand=true` as that will make the release build skip bundling JS and assets into the app binary.
:::

The generated AAB can be found under `android/app/build/outputs/bundle/release/app-release.aab`, and is ready to be uploaded to Google Play.

In order for Google Play to accept AAB format the App Signing by Google Play needs to be configured for your application on the Google Play Console. If you are updating an existing app that doesn't use App Signing by Google Play, please check our [migration section](#migrating-old-android-react-native-apps-to-use-app-signing-by-google-play) to learn how to perform that configuration change.

## Testing the release build of your app

Before uploading the release build to the Play Store, make sure you test it thoroughly. First uninstall any previous version of the app you already have installed. Install it on the device using the following command in the project root:

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android -- --mode="release"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android --mode release
```

</TabItem>
</Tabs>

Note that `--mode release` is only available if you've set up signing as described above.

You can terminate any running bundler instances, since all your framework and JavaScript code is bundled in the APK's assets.

## Publishing to other stores

By default, the generated APK has the native code for both `x86`, `x86_64`, `ARMv7a` and `ARM64-v8a` CPU architectures. This makes it easier to share APKs that run on almost all Android devices. However, this has the downside that there will be some unused native code on any device, leading to unnecessarily bigger APKs.

You can create an APK for each CPU by adding the following line in your `android/app/build.gradle` file:

```diff
android {

    splits {
        abi {
            reset()
            enable true
            universalApk false
            include "armeabi-v7a", "arm64-v8a", "x86", "x86_64"
        }
    }

}
```

Upload these files to markets which support device targeting, such as [Amazon AppStore](https://developer.amazon.com/docs/app-submission/device-filtering-and-compatibility.html) or [F-Droid](https://f-droid.org/en/), and the users will automatically get the appropriate APK. If you want to upload to other markets, such as [APKFiles](https://www.apkfiles.com/), which do not support multiple APKs for a single app, change the `universalApk false` line to `true` to create the default universal APK with binaries for both CPUs.

Please note that you will also have to configure distinct version codes, as [suggested in this page](https://developer.android.com/studio/build/configure-apk-splits#configure-APK-versions) from the official Android documentation.

## Enabling Proguard to reduce the size of the APK (optional)

Proguard is a tool that can slightly reduce the size of the APK. It does this by stripping parts of the React Native Java bytecode (and its dependencies) that your app is not using.

:::caution[Important]
Make sure to thoroughly test your app if you've enabled Proguard. Proguard often requires configuration specific to each native library you're using. See `app/proguard-rules.pro`.
:::

要啟用 Proguard，請編輯 `android/app/build.gradle`：

```groovy
/**
 * Run Proguard to shrink the Java bytecode in release builds.
 */
def enableProguardInReleaseBuilds = true
```

## 將舊版 Android React Native 應用遷移至使用 Google Play 應用簽署

如果您正在從舊版 React Native 遷移，您的應用可能尚未使用 Google Play 應用簽署功能。我們建議您啟用此功能以利用自動應用拆分等優勢。要從舊版簽署方式遷移，您需要先[生成新的上傳金鑰](#generating-an-upload-key)，然後將 `android/app/build.gradle` 中的發布簽署配置替換為使用上傳金鑰而非發布金鑰（參見[將簽署配置添加到應用的 Gradle 配置](#adding-signing-config-to-your-apps-gradle-config)一節）。完成後，請按照 [Google Play 幫助網站上的說明](https://support.google.com/googleplay/android-developer/answer/7384423)將原始發布金鑰發送給 Google Play。

## 默認權限

默認情況下，您的 Android 應用會添加 `INTERNET` 權限，因為幾乎所有應用都會使用它。在調試模式下，`SYSTEM_ALERT_WINDOW` 權限會添加到您的 Android APK 中，但在生產環境中會被移除。