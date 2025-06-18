---
id: build-speed
title: Speeding up your Build phase
---

建構 React Native 應用程式可能**相當耗時**，會佔用開發者數分鐘的時間。隨著專案規模擴大，特別是在擁有多位 React Native 開發者的大型組織中，這個問題會更加明顯。

為緩解效能衝擊，本頁面提供數項**提升建構速度**的建議方案。

:::info

請注意，這些建議屬於進階功能，需要對原生建構工具運作方式有一定程度的理解。

:::

## 開發期間僅建構單一 ABI（僅限 Android）

本地建構 Android 應用程式時，預設會建構所有 4 種[應用程式二進位介面 (ABI)](https://developer.android.com/ndk/guides/abis)：`armeabi-v7a`、`arm64-v8a`、`x86` 及 `x86_64`。

但若僅在本地建構並於模擬器或實體裝置測試，實際上無需建構全部 ABI。

此舉可將**原生建構時間**縮短約 75%。

若使用 React Native CLI，可在 `run-android` 指令加入 `--active-arch-only` 旗標。此旗標會從運行中的模擬器或連接的手機自動偵測正確的 ABI。確認此機制運作正常時，控制台會顯示如 `info Detected architectures arm64-v8a` 的訊息。

```
$ yarn react-native run-android --active-arch-only

[ ... ]
info Running jetifier to migrate libraries to AndroidX. You can disable it using "--no-jetifier" flag.
Jetifier found 1037 file(s) to forward-jetify. Using 32 workers...
info JS server already running.
info Detected architectures arm64-v8a
info Installing the app...
```

此機制依賴 `reactNativeArchitectures` Gradle 屬性。

因此，若直接透過命令列使用 Gradle 建構（不透過 CLI），可透過以下方式指定要建構的 ABI：

```
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

此方式適用於在 CI 環境建構 Android 應用程式時，透過矩陣平行處理不同架構的建構作業。

亦可選擇在本機透過專案[頂層目錄](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33)中的 `gradle.properties` 檔案覆寫此值：

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

建構應用程式**正式版**時，請務必移除這些旗標，因正式版需支援所有 ABI，而非僅限日常開發流程使用的單一架構。

## 啟用組態快取（僅限 Android）

自 React Native 0.79 起，可啟用 Gradle 組態快取功能。

執行 `yarn android` 進行 Android 建構時，Gradle 建構流程包含兩個階段（[來源](https://docs.gradle.org/current/userguide/build_lifecycle.html)）：

- 組態階段：評估所有 `.gradle` 檔案
- 執行階段：實際執行任務（如編譯 Java/Kotlin 程式碼等）

啟用組態快取後，後續建構將跳過組態階段。

此功能對於頻繁修改原生程式碼的情境特別有益，能有效提升建構速度。

以下範例展示啟用後，修改原生程式碼後重建 RN-Tester 的速度差異：

![gradle 組態快取](/docs/assets/gradle-config-caching.gif)

在 `android/gradle.properties` 檔案加入以下內容即可啟用 Gradle 組態快取：

```
org.gradle.configuration-cache=true
```

詳細資訊請參閱 [Gradle 官方文件](https://docs.gradle.org/current/userguide/configuration_cache.html)。

## 使用編譯器快取

如果您頻繁執行原生建置（無論是 C++ 或 Objective-C），使用**編譯器快取**可能會帶來顯著效益。

具體而言，您可以採用兩類快取：本地編譯器快取與分散式編譯器快取。

### 本地快取

:::info
以下說明適用於**Android 與 iOS**。
若您僅建置 Android 應用程式，可直接遵循指示操作。
若需同時建置 iOS 應用程式，請參閱下方 [XCode 特定設定](#xcode-specific-setup)章節的補充說明。
:::

建議使用 [**ccache**](https://ccache.dev/) 來快取原生建置的編譯過程。
Ccache 的工作原理是封裝 C++ 編譯器、儲存編譯結果，並在檢測到已存在中間編譯結果時跳過重複編譯。

Ccache 可透過多數作業系統的套件管理器安裝。在 macOS 上，可執行 `brew install ccache` 安裝。
或參閱 [官方安裝指南](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md) 從原始碼編譯安裝。

接著可執行兩次完整建置（例如在 Android 專案中先執行 `yarn react-native run-android`，刪除 `android/app/build` 資料夾後再次執行）。您會發現第二次建置速度大幅提升（耗時應從分鐘級縮短至秒級）。
建置過程中可透過 `ccache -s` 指令驗證快取是否生效，並查看快取命中率統計。

```
$ ccache -s
Summary:
  Hits:             196 /  3068 (6.39 %)
    Direct:           0 /  3068 (0.00 %)
    Preprocessed:   196 /  3068 (6.39 %)
  Misses:          2872
    Direct:        3068
    Preprocessed:  2872
  Uncacheable:        1
Primary storage:
  Hits:             196 /  6136 (3.19 %)
  Misses:          5940
  Cache size (GB): 0.60 / 20.00 (3.00 %)
```

注意：`ccache` 的統計數據是累計所有建置記錄的。可使用 `ccache --zero-stats` 在每次建置前重置統計，以準確測量單次快取效益。

如需清除快取，請執行 `ccache --clear`。

#### XCode 特定設定

為確保 `ccache` 在 iOS 與 XCode 環境正常運作，需於 `ios/Podfile` 中啟用 React Native 對 ccache 的支援。

用編輯器開啟 `ios/Podfile` 檔案，取消註解 `ccache_enabled` 參數。

```ruby
  post_install do |installer|
    # https://github.com/facebook/react-native/blob/main/packages/react-native/scripts/react_native_pods.rb#L197-L202
    react_native_post_install(
      installer,
      config[:reactNativePath],
      :mac_catalyst_enabled => false,
      # TODO: Uncomment the line below
      :ccache_enabled => true
    )
  end
```

#### 在 CI 環境使用此方案

Ccache 在 macOS 系統中會將快取儲存於 `/Users/$USER/Library/Caches/ccache` 目錄。
因此您可在 CI 流程中保存與還原此目錄以加速建置。

但需注意以下事項：

1. 在 CI 環境建議執行完整乾淨建置，避免快取污染問題。若採用前文提到的多 ABI 平行建置方案，通常無需在 CI 中使用 `ccache`。

2. `ccache` 預設依賴時間戳記判斷快取命中，這在 CI 環境中可能失效（因檔案每次都會重新下載）。解決方案是啟用 `compiler_check content` 選項，改為透過 [檔案內容雜湊值](https://ccache.dev/manual/4.3.html) 進行判斷。

### 分散式快取

類似本地快取的概念，對於頻繁執行原生建置的大型組織，可考慮採用分散式快取系統。

推薦使用 [sccache](https://github.com/mozilla/sccache) 實現此需求。
具體設定方式請參閱 sccache 的 [分散式編譯快速入門指南](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md)。