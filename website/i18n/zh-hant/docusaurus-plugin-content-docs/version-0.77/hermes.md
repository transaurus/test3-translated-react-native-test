---
id: hermes
title: Using Hermes
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<a href="https://hermesengine.dev">
  <img width={300} height={300} className="hermes-logo" src="/docs/assets/HermesLogo.svg" style={{height: "auto"}}/>
</a>

[Hermes](https://hermesengine.dev) 是一個專為 React Native 優化的開源 JavaScript 引擎。對多數應用而言，相較於 JavaScriptCore，使用 Hermes 能帶來更快的啟動時間、更低的記憶體使用量以及更小的應用體積。
React Native 預設已啟用 Hermes，無需額外配置。

## 內建版 Hermes

React Native 附帶了 Hermes 的**內建版本**。
我們會在每次發布新版 React Native 時為您構建對應的 Hermes 版本，這能確保您使用的 Hermes 與當前 React Native 版本完全相容。

此變更對 React Native 使用者完全透明。您仍可透過本頁面描述的指令停用 Hermes。
您可[在此頁面閱讀更多技術實現細節](/architecture/bundled-hermes)。

## 確認 Hermes 已啟用

若您近期新建了專案，應能在歡迎畫面查看 Hermes 啟用狀態：

![在 AwesomeProject 中查看 JS 引擎狀態的位置](/docs/assets/HermesApp.jpg)

JavaScript 中會存在一個全域變數 `HermesInternal` 可用於驗證 Hermes 是否啟用：

```jsx
const isHermes = () => !!global.HermesInternal;
```

:::caution
若您使用非標準方式載入 JS 套件包，可能出現 `HermesInternal` 變數存在但未使用高效能預編譯位元碼的情況。
請確認您使用的是 `.hbc` 檔案，並參照下文進行前後效能比對。
:::

要驗證 Hermes 的優勢，請嘗試建置應用的正式版本進行比較。例如在專案根目錄執行：

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

此指令會在建置時將 JavaScript 編譯為 Hermes 位元碼，從而提升應用在裝置上的啟動速度。

## 切換回 JavaScriptCore

React Native 也支援使用 JavaScriptCore 作為 [JavaScript 引擎](javascript-environment)。請依以下指示停用 Hermes。

### Android

編輯 `android/gradle.properties` 檔案，將 `hermesEnabled` 改回 false：

```diff
# Use this property to enable or disable the Hermes JS engine.
# If set to false, you will be using JSC instead.
hermesEnabled=false
```

### iOS

編輯 `ios/Podfile` 檔案並進行如下變更：

```diff
   use_react_native!(
     :path => config[:reactNativePath],
+    :hermes_enabled => false,
     # An absolute path to your application root.
     :app_path => "#{Pod::Config.instance.installation_root}/.."
   )
```