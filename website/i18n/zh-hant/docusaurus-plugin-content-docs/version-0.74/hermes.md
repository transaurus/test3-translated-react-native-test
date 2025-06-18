---
id: hermes
title: Using Hermes
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<a href="https://hermesengine.dev">
  <img width={300} height={300} className="hermes-logo" src="/docs/assets/HermesLogo.svg" style={{height: "auto"}}/>
</a>

[Hermes](https://hermesengine.dev) 是一個專為 React Native 優化的開源 JavaScript 引擎。對多數應用程式而言，相較於 JavaScriptCore，使用 Hermes 能帶來更快的啟動時間、更低的記憶體使用量以及更小的應用程式體積。
React Native 預設使用 Hermes，無需額外配置即可啟用。

## 內建的 Hermes

React Native 附帶了 **內建版本** 的 Hermes。
每當我們發布新版本的 React Native 時，都會為您構建相應的 Hermes 版本。這確保您使用的 Hermes 版本與當前 React Native 版本完全相容。

過去，我們曾遇到 Hermes 與 React Native 版本匹配的問題。此方案徹底解決了該問題，並為用戶提供與特定 React Native 版本相容的 JS 引擎。

此變更對 React Native 用戶完全透明。您仍可透過本頁面描述的指令停用 Hermes。
您可[在此頁面閱讀更多技術實現細節](/architecture/bundled-hermes)。

## 確認 Hermes 已啟用

若您最近從零開始創建新應用程式，應在歡迎視窗中檢查 Hermes 是否啟用：

![在 AwesomeProject 中查看 JS 引擎狀態的位置](/docs/assets/HermesApp.jpg)

JavaScript 中將提供全域變數 `HermesInternal` 用於驗證 Hermes 是否正在使用：

```jsx
const isHermes = () => !!global.HermesInternal;
```

:::caution
若您使用非標準方式載入 JS 套件包，可能出現 `HermesInternal` 變數可用但實際未使用高效能預編譯位元碼的情況。
請確認您正在使用 `.hbc` 檔案，並按照下文所述進行前後效能比對。
:::

要驗證 Hermes 的優勢，請嘗試建立發行版本進行比較。例如在專案根目錄執行：

<Tabs groupId="platform" queryString defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="android">

[//]: # 'Android'

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android -- --mode="release"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android --mode release
```

</TabItem>
</Tabs>

</TabItem>
<TabItem value="ios">

[//]: # 'iOS'

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios -- --mode="Release"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios --mode Release
```

</TabItem>
</Tabs>

</TabItem>
</Tabs>

此操作將在構建時將 JavaScript 編譯為位元碼，從而提升裝置上的應用程式啟動速度。

## 使用 Chrome 開發者工具調試 Hermes 上的 JS

Hermes 透過實作 Chrome 檢查器協議支援 Chrome 調試器。這意味著可直接使用 Chrome 工具來調試運行在 Hermes 上的 JavaScript，無論是模擬器還是實體裝置。

:::info
請注意，這與[調試](debugging#remote-debugging)章節記載的「遠端 JS 調試」（現已棄用）有顯著差異——後者實際是在開發機（筆電或桌機）的 Chrome V8 引擎上運行 JS 程式碼，而非連接至裝置上運行的 JS 引擎。
:::

Chrome 透過 Metro 連接至裝置上運行的 Hermes，因此需知曉 Metro 監聽位置。通常為 `localhost:8081`，但這是[可配置的](https://metrobundler.dev/docs/configuration)。執行 `yarn start` 時，該地址會顯示在啟動時的 stdout 中。

確認 Metro 伺服器監聽位置後，可透過以下步驟連接 Chrome：

1. 在 Chrome 瀏覽器中訪問 `chrome://inspect`
2. 點擊 `Configure...` 按鈕添加 Metro 伺服器地址（通常為上述的 `localhost:8081`）

![Chrome 開發者工具裝置頁面的配置按鈕](/docs/assets/HermesDebugChromeConfig.png)

![添加 Chrome 開發者工具網路目標的對話框](/docs/assets/HermesDebugChromeMetroAddress.png)

3. 現在您應該會看到一個標示為「Hermes React Native」的目標項目，並帶有「inspect」檢查連結，點擊即可啟動偵錯工具。若未顯示「inspect」連結，請確認 Metro 伺服器是否正常運作。![目標檢查連結](/docs/assets/HermesDebugChromeInspect.png)

4. 您現在可以使用 Chrome 偵錯工具。例如，若要在下次執行 JavaScript 時中斷點，請點擊暫停按鈕，並在應用程式中觸發會導致 JavaScript 執行的動作。![偵錯工具中的暫停按鈕](/docs/assets/HermesDebugChromePause.png)

## 在舊版 React Native 中啟用 Hermes

自 React Native 0.70 起，Hermes 已成為預設引擎。本節說明如何在舊版 React Native 中啟用 Hermes。
首先，請確保您至少使用 React Native 0.60.4（Android）或 0.64（iOS）版本以啟用 Hermes。

若您現有的應用程式基於較舊的 React Native 版本，需先進行升級。請參閱[升級至新版 React Native](/docs/upgrading)了解升級方式。升級後，請確認所有功能正常運作，再切換至 Hermes。

:::caution[React Native 相容性注意事項]
每個 Hermes 版本皆針對特定 RN 版本設計。基本原則是嚴格遵循 [Hermes 發佈版本](https://github.com/facebook/hermes/releases)。
版本不相容可能導致應用程式直接崩潰的最壞情況。
:::

:::info[Windows 使用者注意]
Hermes 需依賴 [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/en-us/download/details.aspx?id=48145)。
:::

### Android

編輯 `android/gradle.properties` 檔案，確認 `hermesEnabled` 設為 true：

```diff
# Use this property to enable or disable the Hermes JS engine.
# If set to false, you will be using JSC instead.
hermesEnabled=true
```

:::note
此屬性於 React Native 0.71 新增。若在 `gradle.properties` 中找不到該設定，請參考您使用的 React Native 版本對應文件。
:::

此外，若使用 ProGuard，需在 `proguard-rules.pro` 中加入以下規則：

```
-keep class com.facebook.hermes.unicode.** { *; }
-keep class com.facebook.jni.** { *; }
```

接著，若您已至少建構過應用程式一次，請先清除建構：

```shell
$ cd android && ./gradlew clean
```

完成！現在您應可如常開發與部署應用程式：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android
```

</TabItem>
</Tabs>

### iOS

自 React Native 0.64 起，Hermes 亦支援 iOS。要啟用 iOS 版 Hermes，請編輯 `ios/Podfile` 檔案並進行如下調整：

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

預設情況下，若您使用新架構（New Architecture）將自動啟用 Hermes。透過指定如 `true` 或 `false` 的值，可依需求手動啟用/停用。

設定完成後，可透過以下指令安裝 Hermes pods：

```shell
$ cd ios && pod install
```

完成！現在您應可如常開發與部署應用程式：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios
```

</TabItem>
</Tabs>

## 切換回 JavaScriptCore

React Native 亦支援使用 JavaScriptCore 作為 [JavaScript 引擎](javascript-environment)。請依以下指示退出 Hermes。

### Android

編輯 `android/gradle.properties` 檔案，將 `hermesEnabled` 改回 false：

```diff
# Use this property to enable or disable the Hermes JS engine.
# If set to false, you will be using JSC instead.
hermesEnabled=false
```

### iOS

編輯 `ios/Podfile` 檔案並進行如下調整：

```diff
   use_react_native!(
     :path => config[:reactNativePath],
+    :hermes_enabled => false,
     # Enables Flipper.
     #
     # Note that if you have use_frameworks! enabled, Flipper will not work and
     # you should disable the next line.
     :flipper_configuration => flipper_config,
     # An absolute path to your application root.
     :app_path => "#{Pod::Config.instance.installation_root}/.."
   )
```