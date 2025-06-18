---
id: build-speed
title: Speeding up your Build phase
---

Building your React Native app could be **expensive** and take several minutes of developers time.
This can be problematic as your project grows and generally in bigger organizations with multiple React Native developers.

To mitigate this performance hit, this page shares some suggestions on how to **improve your build time**.

:::info

Please note that those suggestions are advanced feature that requires some amount of understanding of how the native build tools work.

:::

## Build only one ABI during development (Android-only)

When building your android app locally, by default you build all the 4 [Application Binary Interfaces (ABIs)](https://developer.android.com/ndk/guides/abis) : `armeabi-v7a`, `arm64-v8a`, `x86` & `x86_64`.

However, you probably don't need to build all of them if you're building locally and testing your emulator or on a physical device.

This should reduce your **native build time** by a ~75% factor.

If you're using the React Native CLI, you can add the `--active-arch-only` flag to the `run-android` command. This flag will make sure the correct ABI is picked up from either the running emulator or the plugged in phone. To confirm that this approach is working fine, you'll see a message like `info Detected architectures arm64-v8a` on console.

```
$ yarn react-native run-android --active-arch-only

[ ... ]
info Running jetifier to migrate libraries to AndroidX. You can disable it using "--no-jetifier" flag.
Jetifier found 1037 file(s) to forward-jetify. Using 32 workers...
info JS server already running.
info Detected architectures arm64-v8a
info Installing the app...
```

This mechanism relies on the `reactNativeArchitectures` Gradle property.

Therefore, if you're building directly with Gradle from the command line and without the CLI, you can specify the ABI you want to build as follows:

```
$ ./gradlew :app:assembleDebug -PreactNativeArchitectures=x86,x86_64
```

This can be useful if you wish to build your Android App on a CI and use a matrix to parallelize the build of the different architectures.

If you wish, you can also override this value locally, using the `gradle.properties` file you have in the [top-level folder](https://github.com/facebook/react-native/blob/19cf70266eb8ca151aa0cc46ac4c09cb987b2ceb/template/android/gradle.properties#L30-L33) of your project:

```
# Use this property to specify which architecture you want to build.
# You can also override it from the CLI using
# ./gradlew <task> -PreactNativeArchitectures=x86_64
reactNativeArchitectures=armeabi-v7a,arm64-v8a,x86,x86_64
```

Once you build a **release version** of your app, don't forget to remove those flags as you want to build an apk/app bundle that works for all the ABIs and not only for the one you're using in your daily development workflow.

## Enable Configuration Caching (Android-only)

Since React Native 0.79, you can also enable Gradle Configuration Caching.

When you’re running an Android build with `yarn android`, you will be executing a Gradle build that is composed by two steps ([source](https://docs.gradle.org/current/userguide/build_lifecycle.html)):

- Configuration phase, when all the `.gradle` files are evaluated.
- Execution phase, when the tasks are actually executed so the Java/Kotlin code is compiled and so on.

You will now be able to enable Configuration Caching, which will allow you to skip the Configuration phase on subsequent builds.

This is beneficial when making frequent changes to the native code as it improves build times.

For example here you can see how rebuilding faster it is to rebuild RN-Tester after a change in the native code:

![gradle config caching](/docs/assets/gradle-config-caching.gif)

You can enable Gradle Configuration Caching by adding the following line in your `android/gradle.properties` file:

```
org.gradle.configuration-cache=true
```

Please refer to the [official Gradle documentation](https://docs.gradle.org/current/userguide/configuration_cache.html) for more resources on Configuration Caching.

## Use a compiler cache

如果您頻繁執行原生建置（無論是 C++ 或 Objective-C），使用**編譯器快取**可能會帶來效益。

具體而言，您可以使用兩種類型的快取：本地編譯器快取與分散式編譯器快取。

### 本地快取

:::info
以下指令適用於**Android 與 iOS**。
若您僅建置 Android 應用程式，可直接遵循指示操作。
若同時建置 iOS 應用程式，請參閱下方 [XCode 特定設定](#xcode-specific-setup)章節的說明。
:::

建議使用 [**ccache**](https://ccache.dev/) 來快取原生建置的編譯過程。
Ccache 的工作原理是封裝 C++ 編譯器，儲存編譯結果，並在偵測到相同的中間編譯結果時跳過重新編譯。

Ccache 可透過多數作業系統的套件管理器取得。在 macOS 上，可透過 `brew install ccache` 安裝。或參閱 [官方安裝指南](https://github.com/ccache/ccache/blob/master/doc/INSTALL.md) 從原始碼編譯安裝。

接著可執行兩次完整建置（例如在 Android 上先執行 `yarn react-native run-android`，刪除 `android/app/build` 資料夾後再次執行）。您會發現第二次建置速度大幅提升（應僅需數秒而非數分鐘）。建置過程中可透過 `ccache -s` 驗證快取命中率。

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

注意 `ccache` 的統計數據是累計所有建置記錄的。可使用 `ccache --zero-stats` 在建置前重置數據以驗證快取效益。

如需清除快取，可執行 `ccache --clear`

#### XCode 特定設定

要讓 `ccache` 在 iOS 與 XCode 環境正常運作，需在 `ios/Podfile` 中啟用 React Native 對 ccache 的支援。

用編輯器開啟 `ios/Podfile` 並取消註解 `ccache_enabled` 設定行。

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

Ccache 在 macOS 上使用 `/Users/$USER/Library/Caches/ccache` 資料儲存快取。因此可在 CI 環境保存與還原此資料夾以加速建置。

但需注意以下事項：

1. 在 CI 環境建議執行完整清除建置，避免快取污染問題。若採用前文提到的多 ABI 平行建置方法，通常不需在 CI 使用 `ccache`。

2. `ccache` 依賴時間戳記計算快取命中，這在 CI 環境效果不佳（因檔案每次都會重新下載）。解決方法是啟用 `compiler_check content` 選項，改為 [對檔案內容進行雜湊驗證](https://ccache.dev/manual/4.3.html)。

### 分散式快取

與本地快取類似，可考慮對原生建置使用分散式快取。這對需要頻繁執行原生建置的大型組織特別有用。

建議使用 [sccache](https://github.com/mozilla/sccache) 實現此功能。具體設定方法請參閱 sccache 的 [分散式編譯快速入門指南](https://github.com/mozilla/sccache/blob/main/docs/DistributedQuickstart.md)。