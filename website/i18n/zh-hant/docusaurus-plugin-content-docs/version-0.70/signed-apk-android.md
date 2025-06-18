---
id: signed-apk-android
title: Publishing to Google Play Store
---

Android 要求所有應用程式在安裝前都必須使用憑證進行數位簽署。若要透過 [Google Play 商店](https://play.google.com/store) 發佈您的 Android 應用程式，必須使用發布金鑰進行簽署，且後續所有更新都需使用同一金鑰。自 2017 年起，Google Play 可透過 [App Signing by Google Play](https://developer.android.com/studio/publish/app-signing#app-signing-google-play) 功能自動管理發布簽署。但在將應用程式二進位檔上傳至 Google Play 前，仍需使用上傳金鑰進行簽署。Android 開發者文件中的 [Signing Your Applications](https://developer.android.com/tools/publishing/app-signing.html) 頁面詳細說明了此主題。本指南將簡要概述流程，並列出打包 JavaScript bundle 所需的步驟。

:::info
若您使用 Expo，請閱讀 Expo 的 [Deploying to App Stores](https://docs.expo.dev/distribution/app-stores/) 指南，了解如何為 Google Play 商店建構並提交應用程式。本指南適用於任何 React Native 應用程式，可自動化部署流程。
:::

## 產生上傳金鑰

您可以使用 `keytool` 產生私密簽署金鑰。

### Windows

在 Windows 上，必須以管理員身份從 `C:\Program Files\Java\jdkx.x.x_x\bin` 執行 `keytool`。

keytool -genkeypair -v -storetype PKCS12 -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

此命令會提示您輸入金鑰庫和金鑰的密碼，以及金鑰的 Distinguished Name 欄位。接著會產生名為 `my-upload-key.keystore` 的金鑰庫檔案。

金鑰庫包含單一金鑰，有效期為 10000 天。別名是後續簽署應用程式時使用的名稱，請務必記下此別名。

### macOS

在 macOS 上，若不確定 JDK bin 資料夾的位置，可執行以下命令查詢：

/usr/libexec/java_home

此命令會輸出 JDK 的目錄，類似以下路徑：

/Library/Java/JavaVirtualMachines/jdkX.X.X_XXX.jdk/Contents/Home

使用 `cd /your/jdk/path` 命令切換至該目錄，並以 sudo 權限執行 keytool 命令，如下所示。

sudo keytool -genkey -v -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000

:::caution
請妥善保管金鑰庫檔案。若遺失上傳金鑰或金鑰遭洩露，應 [遵循這些指示](https://support.google.com/googleplay/android-developer/answer/7384423#reset) 處理。
:::

## 設定 Gradle 變數

1. 將 `my-upload-key.keystore` 檔案置於專案資料夾的 `android/app` 目錄下。
2. 編輯 `~/.gradle/gradle.properties` 或 `android/gradle.properties` 檔案，並加入以下內容（將 `*****` 替換為正確的金鑰庫密碼、別名和金鑰密碼）：

```
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

這些將成為全域 Gradle 變數，後續可在 Gradle 設定中用於簽署應用程式。

:::note[關於使用 git 的注意事項]
將上述 Gradle 變數儲存在 `~/.gradle/gradle.properties` 而非 `android/gradle.properties` 中，可避免其被提交至 git。您可能需要在使用者家目錄中建立 `~/.gradle/gradle.properties` 檔案後，才能新增變數。
:::

:::note[安全性注意事項]
若您不傾向以明文儲存密碼，且使用 macOS 系統，可選擇[將憑證儲存在 Keychain Access 應用程式](https://pilloxa.gitlab.io/posts/safer-passwords-in-gradle/)。如此便可省略 `~/.gradle/gradle.properties` 檔案中的最後兩行設定。
:::

## 為 Gradle 設定檔添加簽署配置

最後需完成的配置步驟是設定正式版建構使用上傳金鑰簽署。編輯專案目錄中的 `android/app/build.gradle` 檔案，添加簽署配置：

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

## 產生正式版 AAB 檔案

在終端機執行以下指令：

```shell
cd android
./gradlew bundleRelease
```

Gradle 的 `bundleRelease` 會將應用程式運作所需的所有 JavaScript 程式碼打包至 AAB ([Android App Bundle](https://developer.android.com/guide/app-bundle))。若需調整 JavaScript 套件或繪圖資源的打包方式（例如變更預設檔案/資料夾名稱或專案結構），請參閱 `android/app/build.gradle` 檔案以更新對應設定。

:::note
請確認 `gradle.properties` 未包含 `org.gradle.configureondemand=true` 參數，否則正式版建構會略過將 JS 與資源打包至應用程式二進位檔的步驟。
:::

產生的 AAB 檔案位於 `android/app/build/outputs/bundle/release/app-release.aab`，可直接上傳至 Google Play。

Google Play 需在 Play 管理後台為應用程式設定「Google Play 應用程式簽署」功能才能接受 AAB 格式。若您要更新未採用此機制的既有應用程式，請參閱我們的[遷移指南](#migrating-old-android-react-native-apps-to-use-app-signing-by-google-play)了解如何調整配置。

## 測試應用程式正式版

將正式版上傳至 Play 商店前，請務必徹底測試。先解除安裝裝置上所有舊版應用程式，再於專案根目錄執行以下指令進行安裝：

```shell
npx react-native run-android --variant=release
```

注意：僅有當您完成上述簽署設定後，才能使用 `--variant release` 參數。

此時可終止所有運行中的 bundler 程序，因為所有框架與 JavaScript 程式碼均已打包至 APK 資源中。

## 發布至其他商店

預設產生的 APK 會包含 x86 和 ARMv7a 兩種 CPU 架構的原生程式碼，這使得 APK 能相容絕大多數 Android 裝置。但此設計會導致裝置上存在未使用的原生程式碼，造成 APK 檔案不必要的膨脹。

您可透過修改 android/app/build.gradle 中的下列設定，為每種 CPU 架構分別產生 APK：

```diff
- ndk {
-   abiFilters "armeabi-v7a", "x86"
- }
- def enableSeparateBuildPerCPUArchitecture = false
+ def enableSeparateBuildPerCPUArchitecture = true
```

將這兩個檔案上傳至支援裝置定向的商店（如 [Google Play](https://developer.android.com/google/play/publishing/multiple-apks.html) 和 [Amazon AppStore](https://developer.amazon.com/docs/app-submission/device-filtering-and-compatibility.html)），使用者將自動取得對應的 APK。若要上傳至其他不支援單一應用程式多 APK 的商店（如 [APKFiles](https://www.apkfiles.com/)），請同時修改以下設定以產生包含雙 CPU 架構的通用 APK。

```diff
- universalApk false  // If true, also generate a universal APK
+ universalApk true  // If true, also generate a universal APK
```

## 啟用 Proguard 縮減 APK 體積（選用）

Proguard 工具能透過移除應用程式未使用的 React Native Java 位元碼（及其依賴庫）來輕微縮減 APK 大小。

:::caution[重要]
若您已啟用 Proguard，請務必徹底測試您的應用程式。Proguard 通常需要針對每個使用的原生函式庫進行特定配置。請參閱 `app/proguard-rules.pro`。
:::

要啟用 Proguard，請編輯 `android/app/build.gradle`：

```groovy
/**
 * Run Proguard to shrink the Java bytecode in release builds.
 */
def enableProguardInReleaseBuilds = true
```

## 遷移舊版 Android React Native 應用程式以使用 Google Play 應用程式簽署

若您正從舊版 React Native 遷移，您的應用程式可能尚未使用 Google Play 應用程式簽署功能。我們建議啟用此功能以享受自動應用程式拆分等優勢。遷移時需先[產生新的上傳金鑰](#generating-an-upload-key)，接著將 `android/app/build.gradle` 中的發佈簽署配置改為使用上傳金鑰（參見[在 Gradle 中添加簽署配置](#adding-signing-config-to-your-apps-gradle-config)章節）。完成後，請依照 [Google Play 說明網站指引](https://support.google.com/googleplay/android-developer/answer/7384423)將原始發佈金鑰提交至 Google Play。

## 預設權限

預設情況下，Android 應用程式會添加 `INTERNET` 權限，因絕大多數應用程式皆需使用。`SYSTEM_ALERT_WINDOW` 權限會在偵錯模式中加入至您的 Android APK，但正式環境中將被移除。