---
id: build-speed
title: Speeding up your Build phase
---

建構 React Native 應用程式可能**相當耗時**，會佔用開發者數分鐘的時間。
隨著專案規模擴大，特別是在擁有多位 React Native 開發者的大型組織中，這個問題會更加明顯。

為緩解此效能問題，本頁面提供了一些**提升建構速度**的建議。

## 開發期間僅建構單一 ABI (僅限 Android)

當你在本地建構 Android 應用程式時，預設會建構所有 4 種[應用程式二進位介面 (ABI)](https://developer.android.com/ndk/guides/abis)：`armeabi-v7a`、`arm64-v8a`、`x86` 和 `x86_64`。

但若你僅需在本地建構並於模擬器或實體裝置上測試，可能不需要建構所有 ABI。

此舉可將**原生建構時間**減少約 75%。

若使用 React Native CLI，可在 `run-android` 指令中加入 `--active-arch-only` 旗標。此旗標會確保從運行中的模擬器或連接的手機取得正確的 ABI。確認此方法運作正常時，控制台會顯示如 `info Detected architectures arm64-v8a` 的訊息。

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

因此，若直接透過命令列使用 Gradle 建構（不使用 CLI），可如下指定要建構的 ABI：

```
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

這在 CI 上建構 Android 應用程式時特別有用，可透過矩陣平行化不同架構的建構流程。

如有需要，也可在本機使用專案[頂層目錄](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33)中的 `gradle.properties` 檔案覆寫此值：

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

建構應用程式的**正式版**時，請務必移除這些旗標，以確保產出的 apk/app bundle 支援所有 ABI，而非僅限日常開發使用的架構。

## 使用編譯器快取

若頻繁執行原生建構（C++ 或 Objective-C），使用**編譯器快取**可能帶來效益。

具體而言，可使用兩類快取：本地編譯器快取與分散式編譯器快取。

### 本地快取

:::info
以下說明適用於**Android 與 iOS**。
若僅建構 Android 應用程式，直接遵循即可。
若需建構 iOS 應用程式，請參閱下方的[XCode 專屬設定](#xcode-specific-setup)章節。
:::

建議使用 [**ccache**](https://ccache.dev/) 來快取原生建構的編譯結果。
Ccache 透過包裝 C++ 編譯器運作，儲存編譯結果，若偵測到相同的編譯階段結果已存在，則跳過該次編譯。

安裝方式可參閱[官方安裝指南](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md)。

在 macOS 上，可透過 `brew install ccache` 安裝。
安裝完成後，可如下設定以快取 NDK 編譯結果：

```
ln -s $(which ccache) /usr/local/bin/gcc
ln -s $(which ccache) /usr/local/bin/g++
ln -s $(which ccache) /usr/local/bin/cc
ln -s $(which ccache) /usr/local/bin/c++
ln -s $(which ccache) /usr/local/bin/clang
ln -s $(which ccache) /usr/local/bin/clang++
```

此操作會在 `/usr/local/bin/` 中建立名為 `gcc`、`g++` 等指向 ccache 的符號連結。

只要 `$PATH` 變數中 `/usr/local/bin/` 的路徑順位先於 `/usr/bin/` 即可正常運作（此為預設設定）。

你可以使用 `which` 指令來驗證是否生效：

```
$ which gcc
/usr/local/bin/gcc
```

若結果顯示為 `/usr/local/bin/gcc`，即代表你實際呼叫的是會包裹 `gcc` 指令的 `ccache`。

:::caution
請注意，此 `ccache` 設定會影響你電腦上所有的編譯行為，不僅限於 React Native 相關編譯。請自行承擔使用風險。若你發現其他軟體無法安裝或編譯，可能是此設定導致。此時可透過以下指令移除符號連結：

```
unlink /usr/local/bin/gcc
unlink /usr/local/bin/g++
unlink /usr/local/bin/cc
unlink /usr/local/bin/c++
unlink /usr/local/bin/clang
unlink /usr/local/bin/clang++
```

即可還原至預設編譯器狀態。
:::

接著可執行兩次完整建置（例如在 Android 專案中先執行 `yarn react-native run-android`，刪除 `android/app/build` 資料夾後再次執行）。你會發現第二次建置速度大幅提升（應只需數秒而非數分鐘）。建置過程中可執行 `ccache -s` 驗證快取命中率。

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

注意 `ccache` 會累積所有建置的統計數據。若要重置數據以驗證單次建置的快取命中率，可使用 `ccache --zero-stats`。

如需清除快取，請執行 `ccache --clear`。

#### XCode 專屬設定

要讓 `ccache` 在 iOS 和 XCode 環境正常運作，需額外執行以下步驟：

1. 必須修改 Xcode 和 `xcodebuild` 呼叫編譯器指令的方式。預設情況下它們會使用編譯器二進位檔的「完整路徑」，因此不會使用 `/usr/local/bin` 中的符號連結。可透過以下任一種方式設定 Xcode 使用「相對名稱」：

- 若直接使用命令列，可在指令前綴環境變數：`CLANG=clang CLANGPLUSPLUS=clang++ LD=clang LDPLUSPLUS=clang++ xcodebuild <xcodebuild 指令其餘部分>`
- 在 `ios/Podfile` 的 `post_install` 區段修改 Xcode workspace 的編譯器設定（於執行 `pod install` 時生效）：

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

2. 需設定 ccache 配置以允許特定程度的寬鬆行為與快取模式，使 Xcode 編譯時能觸發快取命中。若透過環境變數設定，以下為與標準配置不同的關鍵參數：

```bash
export CCACHE_SLOPPINESS=clang_index_store,file_stat_matches,include_file_ctime,include_file_mtime,ivfsoverlay,pch_defines,modules,system_headers,time_macros
export CCACHE_FILECLONE=true
export CCACHE_DEPEND=true
export CCACHE_INODECACHE=true
```

相同設定亦可透過 `ccache.conf` 檔案或其他 ccache 支援的方式配置。詳情請參閱 [ccache 官方手冊](https://ccache.dev/manual/4.3.html)。

#### 在 CI 環境使用此方案

在 macOS 上，ccache 會將快取儲存於 `/Users/$USER/Library/Caches/ccache` 資料夾。因此也可在 CI 環境儲存與還原此資料夾以加速建置。

但需注意以下事項：

1. 在 CI 環境建議執行完整乾淨建置，避免快取污染問題。若採用前文提到的多 ABI 平行建置方案，通常不需在 CI 使用 `ccache`。

2. `ccache` 依賴時間戳記計算快取命中，此機制在 CI 環境效果不佳（因每次執行都會重新下載檔案）。解決方案是改用 `compiler_check content` 選項，改為[對檔案內容進行雜湊計算](https://ccache.dev/manual/4.3.html)。

### 分散式快取

與本地緩存類似，您可能會考慮為原生建置使用分散式緩存。
這對於頻繁進行原生建置的大型組織尤其有用。

我們建議使用 [sccache](https://github.com/mozilla/sccache) 來實現此目的。
有關如何設置和使用此工具的說明，請參考 sccache 的 [分散式編譯快速入門指南](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md)。