---
id: signed-apk-android
title: Publishing to Google Play Store
---

Android要求所有應用程式在安裝前都必須經過數位憑證簽署。若想透過[Google Play商店](https://play.google.com/store)分發您的Android應用程式，必須使用發布金鑰進行簽署，且後續所有更新都需使用同一金鑰。自2017年起，Google Play可透過[App Signing by Google Play](https://developer.android.com/studio/publish/app-signing#app-signing-google-play)功能自動管理簽署流程。但在上傳應用程式二進位檔至Google Play前，仍需使用上傳金鑰進行簽署。Android開發者文件中的[Signing Your Applications](https://developer.android.com/tools/publishing/app-signing.html)頁面詳細說明了此主題。本指南將簡要介紹流程，並列出封裝JavaScript套件所需的步驟。

:::info
若您使用Expo，請參閱Expo的[部署至應用商店](https://docs.expo.dev/distribution/app-stores/)指南，了解如何為Google Play商店建構並提交應用程式。本指南適用於任何React Native應用程式，可自動化部署流程。
:::

## 產生上傳金鑰

您可以使用`keytool`產生私密簽署金鑰。

### Windows

在Windows上，必須以管理員身分從`C:\Program Files\Java\jdkx.x.x_x\bin`執行`keytool`。

keytool -genkeypair -v -storetype PKCS12 -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

此命令會提示您輸入金鑰庫和金鑰的密碼，以及金鑰的辨別名稱欄位。接著會產生名為`my-upload-key.keystore`的金鑰庫檔案。

金鑰庫包含單一金鑰，有效期為10000天。別名是後續簽署應用程式時使用的名稱，請務必記下此別名。

### macOS

在macOS上，若不確定JDK的bin目錄位置，可執行以下命令查詢：

/usr/libexec/java_home

該命令會輸出JDK的目錄路徑，格式類似：

/Library/Java/JavaVirtualMachines/jdkX.X.X_XXX.jdk/Contents/Home

使用`cd /your/jdk/path`命令切換至該目錄，並以sudo權限執行如下所示的keytool命令。

sudo keytool -genkey -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

:::caution
請妥善保管金鑰庫檔案。若遺失上傳金鑰或金鑰遭洩露，應[遵循這些指示](https://support.google.com/googleplay/android-developer/answer/7384423#reset)處理。
:::

## 設定Gradle變數

1. 將`my-upload-key.keystore`檔案置於專案目錄下的`android/app`資料夾中。
2. 編輯`~/.gradle/gradle.properties`或`android/gradle.properties`檔案，加入以下內容（請將`*****`替換為正確的金鑰庫密碼、別名和金鑰密碼）：

```
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

這些將成為全域Gradle變數，後續可在Gradle配置中用於簽署應用程式。

:::note[關於git使用的注意事項]
將上述Gradle變數儲存在`~/.gradle/gradle.properties`而非`android/gradle.properties`中，可避免這些變數被提交至git版本控制。您可能需要在用戶家目錄中先建立`~/.gradle/gradle.properties`檔案，才能添加這些變數。
:::

:::note[關於安全性的注意事項]
若您不傾向以明文儲存密碼，且使用 macOS 系統，可選擇[將憑證儲存在 Keychain Access 應用程式](https://pilloxa.gitlab.io/posts/safer-passwords-in-gradle/)。如此便可省略 `~/.gradle/gradle.properties` 檔案中的最後兩行設定。
:::

## 為應用程式的 Gradle 設定添加簽署配置

最後需完成的配置步驟是設定 release 版本使用上傳金鑰進行簽署。編輯專案目錄中的 `android/app/build.gradle` 檔案，添加簽署配置：

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

## 產生 release 版 AAB

在終端機執行以下指令：

```shell
cd android
./gradlew bundleRelease
```

Gradle 的 `bundleRelease` 會將應用程式運作所需的所有 JavaScript 打包至 AAB ([Android App Bundle](https://developer.android.com/guide/app-bundle))。若需調整 JavaScript 套件或 drawable 資源的打包方式（例如變更預設檔案/資料夾名稱或專案結構），請參閱 `android/app/build.gradle` 檔案以更新對應設定。

:::note
請確認 `gradle.properties` 未包含 `org.gradle.configureondemand=true` 設定，否則會導致 release 版本跳過將 JS 與資源打包至應用程式二進位檔的步驟。
:::

產生的 AAB 檔案位於 `android/app/build/outputs/bundle/release/app-release.aab`，可直接上傳至 Google Play。

Google Play 需在 Play 控制台為應用程式設定「Google Play 應用程式簽署」才能接受 AAB 格式。若您要更新未使用此功能的現有應用程式，請參閱我們的[遷移章節](#migrating-old-android-react-native-apps-to-use-app-signing-by-google-play)了解如何變更配置。

## 測試應用程式的 release 版本

將 release 版本上傳至 Play 商店前，請務必徹底測試。先解除安裝裝置上所有舊版應用程式，再於專案根目錄執行以下指令進行安裝：

```shell
npx react-native run-android --mode=release
```

請注意，僅有當您完成上述簽署設定後，才能使用 `--mode release` 參數。

您可以終止所有正在執行的 bundler 程序，因為所有框架與 JavaScript 程式碼都已打包至 APK 的 assets 中。

## 發布至其他商店

預設生成的 APK 包含 x86 和 ARMv7a CPU 架構的原生程式碼，這使得 APK 能相容於絕大多數 Android 裝置。但此做法的缺點是裝置上會存在未使用的原生程式碼，導致 APK 體積不必要地增大。

您可以透過修改 android/app/build.gradle 中的下列設定，為每種 CPU 架構分別產生 APK：

```diff
- ndk {
-   abiFilters "armeabi-v7a", "x86"
- }
- def enableSeparateBuildPerCPUArchitecture = false
+ def enableSeparateBuildPerCPUArchitecture = true
```

將這兩個檔案上傳至支援裝置定向的商店（如 [Google Play](https://developer.android.com/google/play/publishing/multiple-apks.html) 和 [Amazon AppStore](https://developer.amazon.com/docs/app-submission/device-filtering-and-compatibility.html)），使用者將自動取得對應的 APK。若需上傳至其他不支援單一應用程式多 APK 的商店（如 [APKFiles](https://www.apkfiles.com/)），請同時修改以下設定以產生包含雙 CPU 架構的通用 APK。

```diff
- universalApk false  // If true, also generate a universal APK
+ universalApk true  // If true, also generate a universal APK
```

## 啟用 Proguard 縮減 APK 體積（選用）

Proguard 工具能透過移除應用程式未使用的 React Native Java 位元碼（及其依賴項），小幅縮減 APK 體積。

:::caution[重要]
若您已啟用 Proguard，請務必徹底測試您的應用程式。Proguard 通常需要針對您使用的每個原生函式庫進行特定配置。請參閱 `app/proguard-rules.pro`。
:::

要啟用 Proguard，請編輯 `android/app/build.gradle`：

```groovy
/**
 * Run Proguard to shrink the Java bytecode in release builds.
 */
def enableProguardInReleaseBuilds = true
```

## 將舊版 Android React Native 應用程式遷移至使用 Google Play 應用程式簽署

若您正從舊版 React Native 遷移，您的應用程式可能尚未使用 Google Play 應用程式簽署功能。我們建議您啟用此功能以利用自動應用程式拆分等優勢。要從舊版簽署方式遷移，您需先[產生新的上傳金鑰](#generating-an-upload-key)，然後在 `android/app/build.gradle` 中將發佈簽署配置替換為使用上傳金鑰而非發佈金鑰（參見[將簽署配置加入 gradle](#adding-signing-config-to-your-apps-gradle-config) 章節）。完成後，請依照 [Google Play 說明網站指引](https://support.google.com/googleplay/android-developer/answer/7384423)將原始發佈金鑰傳送至 Google Play。

## 預設權限

預設情況下，您的 Android 應用程式會加入 `INTERNET` 權限，因絕大多數應用程式都會使用它。`SYSTEM_ALERT_WINDOW` 權限會在偵錯模式中加入至您的 Android APK，但在正式環境中會被移除。