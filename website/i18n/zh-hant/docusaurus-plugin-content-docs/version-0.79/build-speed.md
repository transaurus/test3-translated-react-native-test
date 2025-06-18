---
id: build-speed
title: Speeding up your Build phase
---

建構你的 React Native 應用程式可能**相當耗時**，會佔用開發者數分鐘的時間。隨著專案規模增長，特別是在擁有多位 React Native 開發者的大型組織中，這個問題會更加明顯。

為緩解此效能瓶頸，本頁面將分享如何**提升建構速度**的建議。

:::info

請注意，這些建議屬於進階功能，需要對原生建構工具運作方式有一定程度的理解。

:::

## 開發期間僅建構單一 ABI（僅限 Android）

當你在本地建構 Android 應用程式時，預設會建構所有 4 種[應用程式二進位介面 (ABI)](https://developer.android.com/ndk/guides/abis)：`armeabi-v7a`、`arm64-v8a`、`x86` 和 `x86_64`。

但若你僅在本地建構並於模擬器或實體裝置測試，實際上可能不需要建構所有 ABI。

此舉可將**原生建構時間**縮短約 75%。

若使用 React Native CLI，可在 `run-android` 指令中加入 `--active-arch-only` 旗標。此旗標會確保從運行中的模擬器或連接的手機取得正確的 ABI。要確認此方法運作正常，你會在控制台看到類似 `info Detected architectures arm64-v8a` 的訊息。

```
$ yarn react-native run-android --active-arch-only

[ ... ]
info Running jetifier to migrate libraries to AndroidX. You can disable it using "--no-jetifier" flag.
Jetifier found 1037 file(s) to forward-jetify. Using 32 workers...
info JS server already running.
info Detected architectures arm64-v8a
info Installing the app...
```

此機制依賴於 `reactNativeArchitectures` Gradle 屬性。

因此，若你直接透過命令列使用 Gradle 建構（不使用 CLI），可透過以下方式指定要建構的 ABI：

```
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

這在你想於 CI 上建構 Android 應用程式，並使用矩陣平行化不同架構的建構時特別有用。

如有需要，也可透過專案[頂層目錄](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33)中的 `gradle.properties` 檔案覆寫此值：

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

當你建構應用程式的**正式版**時，請務必移除這些旗標，因為你需要的是能適用所有 ABI 的 apk/app bundle，而非僅限日常開發流程中使用的單一 ABI。

## 啟用設定快取（僅限 Android）

自 React Native 0.79 起，你還可啟用 Gradle 設定快取（Configuration Caching）。

當你執行 `yarn android` 進行 Android 建構時，實際上是執行一個由兩個階段組成的 Gradle 建構流程（[來源](https://docs.gradle.org/current/userguide/build_lifecycle.html)）：

- 設定階段：評估所有 `.gradle` 檔案
- 執行階段：實際執行任務（如編譯 Java/Kotlin 程式碼等）

現在你可啟用設定快取，這將讓後續建構跳過設定階段。

當頻繁修改原生程式碼時，此功能特別有益，因為它能提升建構速度。

例如下圖展示在修改原生程式碼後，重新建構 RN-Tester 的速度差異：

![gradle 設定快取](/docs/assets/gradle-config-caching.gif)

要啟用 Gradle 設定快取，請在 `android/gradle.properties` 檔案中加入以下設定：

```
org.gradle.configuration-cache=true
```

更多關於設定快取的資源，請參閱 [Gradle 官方文件](https://docs.gradle.org/current/userguide/configuration_cache.html)。

## 使用編譯器快取

如果您頻繁執行原生建置（無論是 C++ 或 Objective-C），使用**編譯器快取**可能會帶來效益。

具體而言，您可以使用兩種類型的快取：本地編譯器快取和分散式編譯器快取。

### 本地快取

:::info
以下說明適用於**Android 和 iOS**。
若您僅建置 Android 應用程式，直接遵循即可。
若您同時建置 iOS 應用程式，請參閱下方的 [XCode 特定設定](#xcode-specific-setup)章節。
:::

我們建議使用 [**ccache**](https://ccache.dev/) 來快取原生建置的編譯過程。
Ccache 的工作原理是封裝 C++ 編譯器，儲存編譯結果，並在已有中間編譯結果時跳過重新編譯。

Ccache 可透過多數作業系統的套件管理器安裝。在 macOS 上，可使用 `brew install ccache` 安裝。
或參閱 [官方安裝指南](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md) 從原始碼安裝。

接著可執行兩次乾淨建置（例如在 Android 上先執行 `yarn react-native run-android`，刪除 `android/app/build` 資料夾後再次執行）。您會發現第二次建置速度大幅提升（應只需數秒而非數分鐘）。
建置時可透過 `ccache -s` 驗證 `ccache` 運作狀態並檢查快取命中率。

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

注意 `ccache` 會累積所有建置的統計數據。可使用 `ccache --zero-stats` 在建置前重置數據以驗證快取命中率。

若需清除快取，可執行 `ccache --clear`

#### XCode 特定設定

為確保 `ccache` 在 iOS 和 XCode 環境正常運作，需在 `ios/Podfile` 中啟用 React Native 對 ccache 的支援。

開啟 `ios/Podfile` 並取消註解 `ccache_enabled` 行。

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

#### 在 CI 上使用此方法

Ccache 在 macOS 上使用 `/Users/$USER/Library/Caches/ccache` 資料夾儲存快取。
因此您可在 CI 環境儲存與還原此資料夾以加速建置。

但需注意以下事項：

1. 在 CI 上建議執行完整乾淨建置，避免快取污染問題。若遵循前文方法平行建置 4 種 ABI，通常不需在 CI 使用 `ccache`。

2. `ccache` 依賴時間戳記計算快取命中，這在 CI 環境效果不佳（因檔案每次都會重新下載）。需改用 `compiler_check content` 選項，透過[檔案內容雜湊值](https://ccache.dev/manual/4.3.html)判斷。

### 分散式快取

類似本地快取，您可考慮對原生建置使用分散式快取。
這對頻繁執行原生建置的大型組織特別有用。

我們推薦使用 [sccache](https://github.com/mozilla/sccache) 實現此功能。
詳細設定請參閱 sccache 的[分散式編譯快速指南](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md)。