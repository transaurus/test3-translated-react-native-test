---
id: hermes
title: Using Hermes
---

<a href="https://hermesengine.dev">
  <img width={300} height={300} className="hermes-logo" src="/docs/assets/HermesLogo.svg" style={{height: "auto"}}/>
</a>

[Hermes](https://hermesengine.dev) is an open-source JavaScript engine optimized for React Native. For many apps, using Hermes will result in improved start-up time, decreased memory usage, and smaller app size when compared to JavaScriptCore.
Hermes is used by default by React Native and no additional configuration is required to enable it.

## Bundled Hermes

React Native comes with a **bundled version** of Hermes.
We will be building a version of Hermes for you whenever we release a new version of React Native. This will make sure you're consuming a version of Hermes which is fully compatible with the version of React Native you're using.

Historically, we had problems with matching versions of Hermes with versions of React Native. This fully eliminates this problem, and offers users a JS engine that is compatible with the specific React Native version.

This change is fully transparent to users of React Native. You can still disable Hermes using the command described in this page.
You can [read more about the technical implementation on this page](/architecture/bundled-hermes).

## Confirming Hermes is in use

If you've recently created a new app from scratch, you should see if Hermes is enabled in the welcome view:

![Where to find JS engine status in AwesomeProject](/docs/assets/HermesApp.jpg)

A `HermesInternal` global variable will be available in JavaScript that can be used to verify that Hermes is in use:

```jsx
const isHermes = () => !!global.HermesInternal;
```

:::caution
If you are using a non-standard way of loading the JS bundle, it is possible that the `HermesInternal` variable is available but you aren't using the highly optimised pre-compiled bytecode.
Confirm that you are using the `.hbc` file and also benchmark the before/after as detailed below.
:::

To see the benefits of Hermes, try making a release build/deployment of your app to compare. For example:

```shell
$ npx react-native run-android --variant release
```

or for iOS:

```shell
$ npx react-native run-ios --configuration Release
```

This will compile JavaScript to bytecode during build time which will improve your app's startup speed on device.

## Debugging JS on Hermes using Google Chrome's DevTools

Hermes supports the Chrome debugger by implementing the Chrome inspector protocol. This means Chrome's tools can be used to directly debug JavaScript running on Hermes, on an emulator or on a real, physical, device.

:::info
Note that this is very different with the "Remote JS Debugging" from the In-App Developer Menu documented in the [Debugging](debugging#debugging-using-a-custom-javascript-debugger) section, which actually runs the JS code on Chrome's V8 on your development machine (laptop or desktop).
:::

Chrome connects to Hermes running on device via Metro, so you'll need to know where Metro is listening. Typically this will be on `localhost:8081`, but this is [configurable](https://metrobundler.dev/docs/configuration). When running `yarn start` the address is written to stdout on startup.

Once you know where the Metro server is listening, you can connect with Chrome using the following steps:

1. Navigate to `chrome://inspect` in a Chrome browser instance.

2. Use the `Configure...` button to add the Metro server address (typically `localhost:8081` as described above).

![Configure button in Chrome DevTools devices page](/docs/assets/HermesDebugChromeConfig.png)

![Dialog for adding Chrome DevTools network targets](/docs/assets/HermesDebugChromeMetroAddress.png)

3. 現在您應該會看到一個標示為「Hermes React Native」的目標項目，並帶有「inspect」連結，點擊即可開啟偵錯工具。若未顯示「inspect」連結，請確認 Metro 伺服器是否正常運作。![目標檢查連結](/docs/assets/HermesDebugChromeInspect.png)

4. 您現在可以使用 Chrome 的偵錯工具。例如，若要在下次執行 JavaScript 時中斷點，點擊暫停按鈕並觸發應用程式中會執行 JavaScript 的動作。![偵錯工具中的暫停按鈕](/docs/assets/HermesDebugChromePause.png)

## 在舊版 React Native 中啟用 Hermes

自 React Native 0.70 起，Hermes 已成為預設引擎。本節說明如何在舊版 React Native 中啟用 Hermes。
首先，請確保您使用的 React Native 版本至少為 0.60.4（Android）或 0.64（iOS）以啟用 Hermes。

若您現有的應用程式基於較舊的 React Native 版本，需先進行升級。詳見[升級至新版 React Native](/docs/upgrading)。升級後，請確認所有功能正常運作，再切換至 Hermes。

:::caution[React Native 相容性注意事項]
每個 Hermes 版本皆針對特定 RN 版本設計。基本原則是嚴格遵循 [Hermes 發佈版本](https://github.com/facebook/hermes/releases)。
版本不相容可能導致應用程式直接崩潰。
:::

:::info[Windows 使用者注意]
Hermes 需安裝 [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/en-us/download/details.aspx?id=48145)。
:::

### Android

編輯 `android/app/build.gradle` 檔案並進行以下變更：

```diff
  project.ext.react = [
      entryFile: "index.js",
-     enableHermes: false  // clean and rebuild if changing
+     enableHermes: true  // clean and rebuild if changing
  ]

// ...

if (enableHermes) {
-    def hermesPath = "../../node_modules/hermes-engine/android/";
-    debugImplementation files(hermesPath + "hermes-debug.aar")
-    releaseImplementation files(hermesPath + "hermes-release.aar")
+    //noinspection GradleDynamicVersion
+    implementation("com.facebook.react:hermes-engine:+") { // From node_modules
+        exclude group:'com.facebook.fbjni'
+    }
} else {
```

若您使用 ProGuard，需在 `proguard-rules.pro` 中加入以下規則：

```
-keep class com.facebook.hermes.unicode.** { *; }
-keep class com.facebook.jni.** { *; }
```

接著，若您已至少建構過應用程式一次，請先清理建構：

```shell
$ cd android && ./gradlew clean
```

完成！現在您應能如常開發與部署應用程式：

```shell
$ npx react-native run-android
```

:::note[Android App Bundle 注意事項]
Android App Bundle 自 React Native 0.62 起支援。
:::

### iOS

自 React Native 0.64 起，Hermes 亦支援 iOS。要啟用 iOS 版 Hermes，請編輯 `ios/Podfile` 檔案並進行以下變更：

```diff
   use_react_native!(
     :path => config[:reactNativePath],
     # to enable hermes on iOS, change `false` to `true` and then install pods
     # By default, Hermes is disabled on Old Architecture, and enabled on New Architecture.
     # You can enable/disable it manually by replacing `flags[:hermes_enabled]` with `true` or `false`.
-    :hermes_enabled => flags[:hermes_enabled],
+    :hermes_enabled => true
   )
```

預設情況下，若您使用新架構（New Architecture）將自動啟用 Hermes。您可透過設定如 `true` 或 `false` 來手動啟用/停用。

設定完成後，執行以下指令安裝 Hermes pods：

```shell
$ cd ios && pod install
```

完成！現在您應能如常開發與部署應用程式：

```shell
$ npx react-native run-ios
```

## 切換回 JavaScriptCore

React Native 亦支援使用 JavaScriptCore 作為 [JavaScript 引擎](javascript-environment)。請依以下指示停用 Hermes。

### Android

編輯 `android/app/build.gradle` 檔案並進行以下變更：

```diff
  project.ext.react = [
-     enableHermes: true,  // clean and rebuild if changing
+     enableHermes: false,  // clean and rebuild if changing
  ]
```

### iOS

編輯 `ios/Podfile` 檔案並進行以下變更：

```diff
   use_react_native!(
     :path => config[:reactNativePath],
     # Hermes is now enabled by default. Disable by setting this flag to false.
     # Upcoming versions of React Native may rely on get_default_flags(), but
     # we make it explicit here to aid in the React Native upgrade process.
-    :hermes_enabled => flags[:hermes_enabled],
+    :hermes_enabled => false,
   )
```