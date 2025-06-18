---
id: build-speed
title: Speeding up your Build phase
---

建構你的 React Native 應用程式可能**相當耗時**，會佔用開發者數分鐘的時間。
隨著專案規模增長，特別是在擁有多位 React Native 開發者的大型組織中，這個問題會更加明顯。

為緩解此效能瓶頸，本頁面將分享一些**提升建構速度**的實用建議。

## 開發期間僅建構單一 ABI（僅限 Android）

當你在本地建構 Android 應用程式時，預設會同時建構所有 4 種[應用程式二進位介面 (ABI)](https://developer.android.com/ndk/guides/abis)：`armeabi-v7a`、`arm64-v8a`、`x86` 和 `x86_64`。

但若你僅需在本地模擬器或實體裝置上測試，其實無需建構全部 ABI 版本。

此做法可將**原生建構時間**縮短約 75%。

若使用 React Native CLI，可在 `run-android` 指令中加入 `--active-arch-only` 旗標。此旗標會自動偵測運行中模擬器或連接裝置的 ABI 架構。成功時，控制台將顯示如 `info Detected architectures arm64-v8a` 的確認訊息。

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

因此若直接透過 Gradle 指令行建構（不使用 CLI），可透過以下方式指定目標 ABI：

```
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

此方法特別適用於在 CI 環境中，透過矩陣平行建構不同架構的 Android 應用程式。

亦可透過修改專案[頂層目錄](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33)中的 `gradle.properties` 檔案來覆寫此值：

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

請注意，建構**正式版應用程式**時務必移除這些旗標，以確保產出的 apk/app bundle 支援所有 ABI 架構，而非僅限日常開發使用的單一架構。

## 使用編譯器快取

若需頻繁執行原生建構（C++ 或 Objective-C），採用**編譯器快取**可顯著提升效率。

具體可分為兩類快取：本地編譯器快取與分散式編譯器快取。

### 本地快取

:::info
以下說明適用於**Android 與 iOS** 建構。
若僅建構 Android 應用程式，直接依步驟操作即可。
若需建構 iOS 應用程式，請額外參照下方 [XCode 專屬設定](#xcode-specific-setup)章節。
:::

建議使用 [**ccache**](https://ccache.dev/) 來快取原生建構的編譯結果。
Ccache 透過封裝 C++ 編譯器運作，儲存編譯結果並在後續建構時直接調用快取，跳過重複編譯階段。

安裝方式請參照[官方安裝指南](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md)。

在 macOS 上可透過 `brew install ccache` 安裝。安裝完成後，可依以下方式設定以快取 NDK 編譯結果：

```
ln -s $(which ccache) /usr/local/bin/gcc
ln -s $(which ccache) /usr/local/bin/g++
ln -s $(which ccache) /usr/local/bin/cc
ln -s $(which ccache) /usr/local/bin/c++
ln -s $(which ccache) /usr/local/bin/clang
ln -s $(which ccache) /usr/local/bin/clang++
```

此操作會在 `/usr/local/bin/` 目錄下建立名為 `gcc`、`g++` 等指向 ccache 的符號連結。

只要 `/usr/local/bin/` 路徑優先於 `/usr/bin/` 出現在 `$PATH` 變數中即可正常運作（此為預設設定）。

你可以使用 `which` 指令來驗證是否生效：

```
$ which gcc
/usr/local/bin/gcc
```

若結果顯示為 `/usr/local/bin/gcc`，則代表你實際呼叫的是 `ccache`，它會包裹 `gcc` 的呼叫。

:::caution
請注意，此 `ccache` 設定會影響你機器上所有的編譯行為，不僅限於 React Native 相關的編譯。使用時請自行承擔風險。若你遇到其他軟體安裝或編譯失敗的情況，這可能是原因之一。若發生此狀況，你可以透過以下指令移除已建立的符號連結：

```
unlink /usr/local/bin/gcc
unlink /usr/local/bin/g++
unlink /usr/local/bin/cc
unlink /usr/local/bin/c++
unlink /usr/local/bin/clang
unlink /usr/local/bin/clang++
```

以恢復機器至原始狀態並使用預設的編譯器。
:::

接著你可以執行兩次完整建置（例如在 Android 上，可以先執行 `yarn react-native run-android`，刪除 `android/app/build` 資料夾後再次執行相同指令）。你會注意到第二次建置速度大幅提升（應只需數秒而非數分鐘）。
在建置過程中，你可以透過 `ccache -s` 驗證 `ccache` 是否正常運作，並檢查快取命中/未命中率。

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

需注意 `ccache` 會累積所有建置的統計數據。你可以使用 `ccache --zero-stats` 在建置前重置數據，以驗證快取命中率。

若需清除快取，可執行 `ccache --clear`

#### XCode 專屬設定

要確保 `ccache` 在 iOS 和 XCode 環境正常運作，需額外執行以下步驟：

1. 必須調整 Xcode 和 `xcodebuild` 呼叫編譯器指令的方式。預設情況下它們會使用編譯器二進位檔的「完整路徑」，因此安裝在 `/usr/local/bin` 的符號連結將不會被使用。你可以透過以下任一方式設定 Xcode 使用「相對名稱」呼叫編譯器：

- 若直接使用命令列，可在指令前綴環境變數：`CLANG=clang CLANGPLUSPLUS=clang++ LD=clang LDPLUSPLUS=clang++ xcodebuild <xcodebuild 指令列其餘參數>`
- 在 `ios/Podfile` 中加入 `post_install` 區段，於 `pod install` 階段修改 Xcode workspace 中的編譯器設定：

```ruby
  post_install do |installer|
    react_native_post_install(installer)

    # ...possibly other post_install items here

    installer.pods_project.targets.each do |target|
      target.build_configurations.each do |config|
        # Using the un-qualified names means you can swap in different implementations, for example ccache
        config.build_settings["CC"] = "clang"
        config.build_settings["LD"] = "clang"
        config.build_settings["CXX"] = "clang++"
        config.build_settings["LDPLUSPLUS"] = "clang++"
      end
    end

    __apply_Xcode_12_5_M1_post_install_workaround(installer)
  end
```

2. 需要設定 ccache 配置，允許一定程度的寬鬆和快取行為，使 ccache 能在 Xcode 編譯期間註冊快取命中。若透過環境變數設定，以下為與標準配置不同的 ccache 配置參數：

```bash
export CCACHE_SLOPPINESS=clang_index_store,file_stat_matches,include_file_ctime,include_file_mtime,ivfsoverlay,pch_defines,modules,system_headers,time_macros
export CCACHE_FILECLONE=true
export CCACHE_DEPEND=true
export CCACHE_INODECACHE=true
```

相同配置也可透過 `ccache.conf` 檔案或 ccache 提供的其他機制完成。更多細節請參閱 [官方 ccache 手冊](https://ccache.dev/manual/4.3.html)。

#### 在 CI 上使用此方法

Ccache 在 macOS 上會使用 `/Users/$USER/Library/Caches/ccache` 資料夾儲存快取。
因此你也可以在 CI 上保存與恢復該資料夾以加速建置。

但需注意以下事項：

1. 在 CI 上建議執行完整清除建置，以避免快取污染問題。若遵循前文提到的平行化建置方法，你應能將原生建置分散至 4 種不同 ABI 上執行，此時在 CI 上很可能不需要使用 `ccache`。

2. `ccache` 依賴時間戳記計算快取命中，這在 CI 環境效果不佳，因為每次執行時檔案都會重新下載。為解決此問題，需使用 `compiler_check content` 選項，改為[對檔案內容進行雜湊計算](https://ccache.dev/manual/4.3.html)。

### 分散式快取

與本地緩存類似，您可能會考慮為原生建置使用分散式緩存。
這對於頻繁進行原生建置的大型組織尤其有用。

我們推薦使用 [sccache](https://github.com/mozilla/sccache) 來實現此目的。
關於如何設置和使用此工具，我們參考 sccache 的[分散式編譯快速入門指南](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md)。

## 疑難排解

本節提供解決一些最常見建置效能問題的指引。