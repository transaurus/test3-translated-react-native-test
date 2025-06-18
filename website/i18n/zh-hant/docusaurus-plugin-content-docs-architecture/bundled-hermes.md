---
id: bundled-hermes
title: Bundled Hermes
---

本頁面概述了 **Hermes 與 React Native 的建置方式**。

若您需要關於如何在應用程式中使用 Hermes 的指引，請參閱另一頁面：[使用 Hermes](/docs/hermes)

:::caution
請注意，本頁面為技術深度解析，主要針對在 Hermes 或 React Native 之上開發擴充功能的用戶。一般 React Native 使用者無需深入了解 React Native 與 Hermes 的互動機制。
:::

## 何謂「捆綁式 Hermes」

自 React Native 0.69.0 起，每個 React Native 版本都將 **同步建置** 對應的 Hermes 版本。我們稱此發佈模式為 **捆綁式 Hermes**。

從 0.69 開始，您將始終獲得一個與 React Native 版本同步建置並測試過的 JavaScript 引擎。

## 為何轉向「捆綁式 Hermes」

過去，React Native 與 Hermes 採用 **兩套獨立的發佈流程** 與版本號機制。這種雙軌制導致開源生態系產生混淆，難以判斷特定 Hermes 版本是否相容特定 React Native 版本（例如需知曉 Hermes 0.11.0 僅相容 React Native 0.68.0 等）。

Hermes 與 React Native 共用 JSI 程式碼（[Hermes 端](https://github.com/facebook/hermes/tree/main/API/jsi/jsi) 與 [React Native 端](https://github.com/facebook/react-native/tree/main/packages/react-native/ReactCommon/jsi/jsi)）。若兩者的 JSI 版本不同步，建置出的 Hermes 將無法與 React Native 相容。詳見 [ABI 相容性問題說明](https://github.com/react-native-community/discussions-and-proposals/issues/257)。

為解決此問題，我們擴展了 React Native 發佈流程，使其能下載並建置 Hermes，並確保建置 Hermes 時僅使用單一 JSI 版本。

透過此機制，我們可在發佈 React Native 版本時同步發佈對應的 Hermes 版本，並確保該 Hermes 引擎與當次 React Native 版本 **完全相容**。我們將此 Hermes 版本與 React Native 版本共同發佈，故稱之為 _捆綁式 Hermes_。

## 對應用程式開發者的影響

如前言所述，若您為應用程式開發者，此變更 **不會直接影響** 您的開發流程。

以下段落將說明我們在底層進行的調整及其設計考量，以保持透明度。

### iOS 使用者

在 iOS 平台，我們已變更您所使用的 `hermes-engine` 來源。

在 React Native 0.69 之前，使用者需下載 CocoaPods 套件（參見 [podspec 檔案](https://github.com/CocoaPods/Specs/blob/master/Specs/5/d/0/hermes-engine/0.11.0/hermes-engine.podspec.json)）。

自 React Native 0.69 起，使用者將改用 `react-native` NPM 套件中 `sdks/hermes-engine/hermes-engine.podspec` 檔案定義的 podspec。該 podspec 依賴於我們在 React Native 發佈流程中上傳至 Maven 與 React Native GitHub Release 的 Hermes 預建壓縮檔（參見 [此版本附件](https://github.com/facebook/react-native/releases/tag/v0.70.4)）。

### Android 使用者

在 Android 平台，我們將以下列方式更新預設範本中的 [`android/app/build.gradle`](https://github.com/facebook/react-native/blob/main/template/android/app/build.gradle) 檔案：

```diff
dependencies {
    // ...

    if (enableHermes) {
+       implementation("com.facebook.react:hermes-engine:+") {
+           exclude group:'com.facebook.fbjni'
+       }
-       def hermesPath = "../../node_modules/hermes-engine/android/";
-       debugImplementation files(hermesPath + "hermes-debug.aar")
-       releaseImplementation files(hermesPath + "hermes-release.aar")
    } else {
        implementation jscFlavor
    }
}
```

在 React Native 0.69 之前，使用者會從 `hermes-engine` NPM 套件中取得 `hermes-debug.aar` 和 `hermes-release.aar`。

在 React Native 0.69 中，使用者將從 `react-native` NPM 套件的 `android/com/facebook/react/hermes-engine/` 資料夾內取得 Android 多變體構件。
請注意，我們將在未來的 React Native 版本中[完全移除](https://github.com/facebook/react-native/blob/c418bf4c8fe8bf97273e3a64211eaa38d836e0a0/package.json#L105)對 `hermes-engine` 的依賴。

#### 採用新架構的 Android 使用者

由於我們原生程式碼建置設定的特性（即我們如何使用 NDK），採用新架構的使用者將**從原始碼建置 Hermes**。

這使得新架構使用者的 React Native 和 Hermes 建置機制保持一致（他們將從原始碼建置這兩個框架）。
這意味著這類 Android 使用者在首次建置時可能會遇到建置時間增加的狀況。

您可以在以下頁面找到優化建置時間並減少對建置影響的指引：[加速建置階段](/docs/next/build-speed)。

#### 在 Windows 上採用新架構的 Android 使用者

在 Windows 機器上使用新架構建置 React Native 應用程式的使用者，需要遵循以下額外步驟以確保建置正常運作：

- 確保[環境已正確配置](https://reactnative.dev/docs/environment-setup)，包含 Android SDK 和 node。
- 使用 Chocolatey 安裝 [cmake](https://community.chocolatey.org/packages/cmake)
- 安裝以下任一項：
  - [Visual Studio 2022 建置工具](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022)。
  - [Visual Studio 22 社群版](https://visualstudio.microsoft.com/vs/community/) - 僅選擇 C++ 桌面開發即可。
- 確保[Visual Studio 命令提示字元](https://docs.microsoft.com/en-us/visualstudio/ide/reference/command-prompt-powershell?view=vs-2022)已正確配置。這是必要的，因為正確的 C++ 編譯器環境變數會在這些命令提示字元中配置。
- 在 Visual Studio 命令提示字元中執行 `npx react-native run-android` 來運行應用程式。

### 使用者是否仍可使用其他引擎？

是的，使用者可以自由啟用/停用 Hermes（在 Android 上使用 `enableHermes` 變數，在 iOS 上使用 `hermes_enabled`）。
「捆綁式 Hermes」的變更僅會影響 **Hermes 的建置和捆綁方式**。

從 React Native 0.70 開始，`enableHermes`/`hermes_enabled` 的預設值為 `true`。

## 這將如何影響貢獻者和擴充功能開發者

如果您是 React Native 的貢獻者，或正在基於 React Native 或 Hermes 開發擴充功能，請繼續閱讀以了解捆綁式 Hermes 的運作方式。

### 捆綁式 Hermes 在底層是如何運作的？

此機制依賴於從 `facebook/hermes` 儲存庫**下載原始碼壓縮檔**到 `facebook/react-native` 儲存庫中。我們對其他原生依賴項（Folly、Glog 等）也有類似的機制，並將 Hermes 調整為遵循相同的設定。

當從 `main` 分支建置 React Native 時，我們將取得 `facebook/hermes` 的 `main` 分支壓縮檔，並將其作為 React Native 建置過程的一部分進行建置。

當從發行分支（例如 `0.69-stable`）建置 React Native 時，我們將改為使用 Hermes 儲存庫中的**標籤**來**同步兩個儲存庫的程式碼**。所使用的特定標籤名稱將儲存在 React Native 發行分支的 `sdks/.hermesversion` 檔案中（例如，這是 [0.69 發行分支上的檔案](https://github.com/facebook/react-native/blob/0.69-stable/sdks/.hermesversion)）。

某種程度上，您可以將這種方法視為類似於 **git 子模組**。

如果您基於 Hermes 進行開發，可以依賴這些標籤來理解建置 React Native 時所使用的 Hermes 版本，因為標籤名稱中會標明 React Native 的版本（例如 `hermes-2022-05-20-RNv0.69.0-ee8941b8874132b8f83e4486b63ed5c19fc3f111`）。

#### Android 實作細節

為了在 Android 上實現這一點，我們在 React Native 的 `/ReactAndroid/hermes-engine` 中新增了一個建置任務，負責建置 Hermes 並打包以供使用（[更多背景請參閱此處](https://github.com/facebook/react-native/pull/33396)）。

您現在可以透過以下指令觸發 Hermes 引擎的建置：

```bash
// Build a debug version of Hermes
./gradlew :ReactAndroid:hermes-engine:assembleDebug
// Build a release version of Hermes
./gradlew :ReactAndroid:hermes-engine:assembleRelease
```

（此指令適用於 React Native 的 `main` 分支）。

您無需在機器上安裝額外工具（如 `cmake`、`ninja` 或 `python3`），因為我們已將建置配置為使用 NDK 版本的這些工具。

在 Gradle 消費端，我們也對消費邏輯進行了小幅改進：從 `releaseImplementation` 和 `debugImplementation` 改為使用 `implementation`。這是可行的，因為新版 `hermes-engine` Android 套件具備**變體感知**能力，能正確將引擎的 debug 版本與應用程式的 debug 版本匹配。您無需在此進行任何自訂配置（即使您使用 `staging` 或其他建置類型/風味）。

然而，這使得模板中必須加入以下這行程式碼：

```
exclude group:'com.facebook.fbjni'
```

這是必要的，因為 React Native 是透過非 prefab 的方式（即解壓 `.aar` 並提取 `.so` 檔案）來使用 `fbjni`。而 Hermes-engine 和其他函式庫則是使用 prefab 來消費 fbjni。我們正在研究[解決此問題](https://github.com/facebook/react-native/pull/33397)，未來將使 Hermes 的導入只需一行程式碼。

#### iOS 實作細節

iOS 的實作依賴於位於以下路徑的一系列腳本：

- [`/scripts/hermes`](https://github.com/facebook/react-native/tree/main/scripts/hermes)。這些腳本包含下載 Hermes tarball、解壓縮及配置 iOS 建置的邏輯。當 `hermes_enabled` 設為 `true` 時，它們會在 `pod install` 階段被呼叫。
- [`/sdks/hermes-engine`](https://github.com/facebook/react-native/tree/main/sdks/hermes-engine)。這些腳本包含實際建置 Hermes 的邏輯。它們是從 `facebook/hermes` 倉庫複製並調整而來，以確保能在 React Native 中正常運作。具體而言，`utils` 資料夾中的腳本負責為所有 Mac 平台建置 Hermes。

Hermes 會作為 CircleCI 上 `build_hermes_macos` 任務的一部分進行建置。該任務會產生一個 tarball 作為產物，當使用已發布的 React Native 版本時，此 tarball 將由 `hermes-engine` podspec 下載（[這裡是 React Native 0.69 在 `build_hermes_macos` 中建立產物的範例](https://app.circleci.com/pipelines/github/facebook/react-native/13679/workflows/5172f8e4-6b02-4ccb-ab97-7cb954911fae/jobs/258701/artifacts)）。

##### 預先建置的 Hermes

如果當前使用的 React Native 版本沒有預先建置的產物（例如您可能正在使用 React Native 的 `main` 分支），則需要從原始碼建置 Hermes。首先，在 `pod install` 期間會為 macOS 建置 Hermes 編譯器 `hermesc`，接著 Hermes 本身會透過 `build-hermes-xcode.sh` 腳本作為 Xcode 建置流程的一部分進行建置。

##### 從原始碼建置 Hermes

當使用 React Native 的 `main` 分支時，Hermes 總是從原始碼建構。若您使用的是穩定版 React Native，可以透過在 CocoaPods 使用時設定環境變數 `CI=true` 來強制從原始碼建構 Hermes：`CI=true pod install`。

##### 除錯符號

預先建構的 Hermes 成品預設不包含除錯符號 (dSYMs)。我們計劃在未來為每個版本提供這些除錯符號。在此之前，若您需要 Hermes 的除錯符號，必須從原始碼建構 Hermes。建置目錄中會生成 `hermes.framework.dSYM` 檔案，與各 Hermes 框架並存。

### 我擔心這項變更會影響到我

我們想強調，這本質上是關於 Hermes 建置位置及兩套件庫間程式碼同步方式的組織性變更。此變更對使用者應完全透明。

過去，我們會為特定 React Native 版本發布 Hermes 版本（例如 [`v0.11.0 for RN0.68.x`](https://github.com/facebook/hermes/releases/tag/v0.11.0)）。

採用「捆綁式 Hermes」後，您可改為依賴標籤來識別特定 React Native 版本發布時所使用的 Hermes 版本。