---
title: How to Build from Source
---

若您想開發新功能/修復錯誤、測試尚未發布的最新功能，或維護包含無法合併至核心程式碼的修補程式的自訂分支，您需要從原始碼建構 React Native。

## Android 平台

### 必要條件

要從原始碼建構，您需要安裝 Android SDK。若您已遵循[設定開發環境](/docs/environment-setup)指南，應已完成相關設定。

無需額外安裝 NDK 或 CMake 等工具特定版本，Android SDK 會**自動下載**建構所需的所有項目。

### 將專案指向 nightly 版本

要使用 React Native 的最新修復與功能，可透過以下指令將專案更新至 nightly 版本：

```
yarn add react-native@nightly
```

這會將您的專案更新為每日發布、包含最新變更的 React Native nightly 版本。

### 將專案設定為從原始碼建構

無論是穩定版或 nightly 版，您通常會使用**預編譯**成品。若想改為從原始碼建構以直接測試框架修改，需按以下方式編輯 `android/settings.gradle` 檔案：

```diff
  // ...
  include ':app'
  includeBuild('../node_modules/@react-native/gradle-plugin')
  
+ includeBuild('../node_modules/react-native') {
+     dependencySubstitution {
+         substitute(module("com.facebook.react:react-android")).using(project(":packages:react-native:ReactAndroid"))
+         substitute(module("com.facebook.react:react-native")).using(project(":packages:react-native:ReactAndroid"))
+         substitute(module("com.facebook.react:hermes-android")).using(project(":packages:react-native:ReactAndroid:hermes-engine"))
+         substitute(module("com.facebook.react:hermes-engine")).using(project(":packages:react-native:ReactAndroid:hermes-engine"))
+     }
+ }
```

### 補充說明

從原始碼建構可能耗時較長，特別是首次建構時需下載約 200 MB 成品並編譯原生程式碼。

每次從儲存庫更新 `react-native` 版本時，建構目錄可能被刪除並重新下載所有檔案。
若要避免此情況，可透過編輯 `~/.gradle/init.gradle` 檔案變更建構目錄路徑：

```groovy
gradle.projectsLoaded {
    rootProject.allprojects {
        buildDir = "/path/to/build/directory/${rootProject.name}/${project.name}"
    }
}
```

## 實施依據

建議的 React Native 開發方式是始終更新至最新版本。我們對舊版提供的支援[詳見版本支援政策](https://github.com/reactwg/react-native-releases/#releases-support-policy)。

從原始碼建構的方式應僅用於提交 PR 前進行端到端測試，不建議長期使用。特別是分叉 React Native 或設定為始終從原始碼建構，將導致專案更難更新，整體開發體驗也會較差。