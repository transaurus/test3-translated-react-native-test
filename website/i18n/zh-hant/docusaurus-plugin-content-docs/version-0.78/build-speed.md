---
id: build-speed
title: Speeding up your Build phase
---

建構 React Native 應用程式可能**相當耗時**，會佔用開發者數分鐘的時間。隨著專案規模增長，特別是在擁有多位 React Native 開發者的大型組織中，此問題會更加明顯。

為緩解此效能瓶頸，本頁面提供數項**加速建構時間**的實用建議。

## 開發期間僅建構單一 ABI (僅限 Android)

本地建構 Android 應用程式時，預設會同時建構所有 4 種[應用程式二進位介面 (ABI)](https://developer.android.com/ndk/guides/abis)：`armeabi-v7a`、`arm64-v8a`、`x86` 和 `x86_64`。

但若僅需在本地模擬器或實體裝置測試，通常無需建構全部 ABI。

此舉可將**原生建構時間**縮短約 75%。

若使用 React Native CLI，可在 `run-android` 指令加入 `--active-arch-only` 參數。此參數會自動偵測運行中模擬器或連接裝置的 ABI 架構。成功時，控制台將顯示如 `info Detected architectures arm64-v8a` 的確認訊息。

```
$ yarn react-native run-android --active-arch-only

[ ... ]
info Running jetifier to migrate libraries to AndroidX. You can disable it using "--no-jetifier" flag.
Jetifier found 1037 file(s) to forward-jetify. Using 32 workers...
info JS server already running.
info Detected architectures arm64-v8a
info Installing the app...
```

此機制依賴於 Gradle 屬性 `reactNativeArchitectures`。

因此若直接透過命令列使用 Gradle 建構（不經 CLI），可透過以下方式指定目標 ABI：

```
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

此方式特別適用於 CI 環境，可透過建構矩陣平行處理不同架構。

亦可於本地透過專案[頂層目錄](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33)中的 `gradle.properties` 檔案覆寫此值：

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

請注意建構**正式版應用程式**時，務必移除這些參數，以確保產出的 apk/app bundle 支援所有 ABI 架構，而非僅限日常開發使用的單一架構。

## 使用編譯器快取

若需頻繁執行原生建構（C++ 或 Objective-C），使用**編譯器快取**將顯著提升效率。

主要可採用兩類快取：本地編譯器快取與分散式編譯器快取。

### 本地快取

:::info
以下說明適用於**Android 與 iOS** 建構。
若僅建構 Android 應用程式，直接依說明操作即可。
若需同時建構 iOS 應用程式，請參閱下方 [XCode 專屬設定](#xcode-specific-setup)章節。
:::

建議使用 [**ccache**](https://ccache.dev/) 快取原生建構的編譯結果。其運作原理是透過封裝 C++ 編譯器，儲存編譯產出物，若偵測到相同的中間編譯結果即跳過重新編譯。

ccache 可透過多數作業系統的套件管理器安裝。macOS 使用者可執行 `brew install ccache`，或參照[官方安裝指南](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md)從原始碼編譯安裝。

建議執行兩次完整建構（例如 Android 可先執行 `yarn react-native run-android`，刪除 `android/app/build` 資料夾後再次執行）。可觀察到第二次建構速度大幅提升（耗時應從數分鐘縮短至數秒）。建構過程中可透過 `ccache -s` 指令驗證快取命中率。

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

請注意，`ccache` 會累積所有建置的統計數據。您可以使用 `ccache --zero-stats` 在建置前重置這些數據，以驗證快取命中率。

若需要清除快取，可執行 `ccache --clear`

#### XCode 專屬設定

為確保 `ccache` 在 iOS 和 XCode 環境中正常運作，您需要在 `ios/Podfile` 中啟用 React Native 對 ccache 的支援。

用編輯器開啟 `ios/Podfile` 並取消註解 `ccache_enabled` 這行。

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

#### 在 CI 環境使用此方法

Ccache 在 macOS 上使用 `/Users/$USER/Library/Caches/ccache` 資料夾儲存快取。
因此您也可以在 CI 環境保存與還原對應資料夾來加速建置。

但需注意以下幾點：

1. 在 CI 環境建議執行完整乾淨建置，以避免快取污染問題。若採用前文段落提及的方法，您應能將原生建置平行分散到 4 種不同 ABI 架構，如此很可能就不需要在 CI 使用 `ccache`。

2. `ccache` 依賴時間戳記計算快取命中，這在 CI 環境效果不佳，因為每次執行都會重新下載檔案。解決方法是使用 `compiler_check content` 選項，改為[對檔案內容進行雜湊計算](https://ccache.dev/manual/4.3.html)。

### 分散式快取

與本地快取類似，您可以考慮對原生建置使用分散式快取。
這對於需要頻繁執行原生建置的大型組織特別有用。

我們推薦使用 [sccache](https://github.com/mozilla/sccache) 實現此功能。
具體設定與使用方法，請參閱 sccache 的[分散式編譯快速入門指南](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md)。