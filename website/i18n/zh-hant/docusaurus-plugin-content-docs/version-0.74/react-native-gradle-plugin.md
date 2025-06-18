---
id: react-native-gradle-plugin
title: React Native Gradle Plugin
---

本指南說明如何配置 **React Native Gradle 插件**（通常簡稱 RNGP），以建構 Android 平台的 React Native 應用程式。

## 使用插件

React Native Gradle 插件以獨立 NPM 套件形式發佈，會隨 `react-native` 自動安裝。

透過 `npx react-native init` 建立的新專案已**預設配置**此插件。若以此指令建立應用程式，無需額外安裝步驟。

若需將 React Native 整合至既有專案，請參閱[對應頁面](/docs/next/integration-with-existing-apps#configuring-gradle)，內含插件安裝的具體指引。

## 配置插件

插件預設採用**開箱即用**的合理配置。僅在需要調整行為時，才需參考本指南進行自訂。

修改 `android/app/build.gradle` 中的 `react` 區塊即可配置插件：

```groovy
apply plugin: "com.facebook.react"

/**
 * This is the configuration block to customize your React Native Android app.
 * By default you don't need to apply any configuration, just uncomment the lines you need.
 */
react {
  // Custom configuration goes here.
}
```

各配置參數說明如下：

### `root`

此參數指向 React Native 專案根目錄（即存放 `package.json` 的資料夾）。預設值為 `..`。可依下列方式自訂：

```groovy
root = file("../")
```

### `reactNativeDir`

此參數指向 `react-native` 套件安裝目錄。預設值為 `../node_modules/react-native`。
若使用 monorepo 或其他套件管理器，可依環境調整此路徑。

自訂範例如下：

```groovy
reactNativeDir = file("../node_modules/react-native")
```

### `codegenDir`

此參數指向 `react-native-codegen` 套件安裝目錄。預設值為 `../node_modules/react-native-codegen`。
若使用 monorepo 或其他套件管理器，可依環境調整此路徑。

自訂範例如下：

```groovy
codegenDir = file("../node_modules/@react-native/codegen")
```

### `cliFile`

此參數指向 React Native CLI 的入口檔案。預設值為 `../node_modules/react-native/cli.js`。
插件需透過此檔案呼叫 CLI 進行應用程式打包與建構。

若使用 monorepo 或其他套件管理器，可依環境調整此路徑。
自訂範例如下：

```groovy
cliFile = file("../node_modules/react-native/cli.js")
```

### `debuggableVariants`

此參數列出可偵錯的變體版本（關於變體的詳細說明請參閱[使用變體](#using-variants)）。

插件預設僅將 `debug` 變體視為可偵錯，`release` 則否。若有其他變體（如 `staging`、`lite` 等），需手動調整此清單。

列為 `debuggableVariants` 的變體不會內建打包檔案，需依賴 Metro 運行。

自訂範例如下：

```groovy
debuggableVariants = ["liteDebug", "prodDebug"]
```

### `nodeExecutableAndArgs`

此參數定義執行所有腳本時使用的 node 指令與參數。預設值為 `[node]`，可依下列方式追加參數：

```groovy
nodeExecutableAndArgs = ["node"]
```

### `bundleCommand`

This is the name of the `bundle` command to be invoked when creating the bundle for your app. That's useful if you're using [RAM Bundles](https://reactnative.dev/docs/0.74/ram-bundles-inline-requires). By default is `bundle` but can be customized to add extra flags as follows:

```groovy
bundleCommand = "ram-bundle"
```

### `bundleConfig`

This is the path to a configuration file that will be passed to `bundle --config <file>` if provided. Default is empty (no config file will be probided). More information on bundling config files can be found [on the CLI documentation](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle). Can be customized as follow:

```groovy
bundleConfig = file(../rn-cli.config.js)
```

### `bundleAssetName`

This is the name of the bundle file that should be generated. Default is `index.android.bundle`. Can be customized as follow:

```groovy
bundleAssetName = "MyApplication.android.bundle"
```

### `entryFile`

The entry file used for bundle generation. The default is to search for `index.android.js` or `index.js`. Can be customized as follow:

```groovy
entryFile = file("../js/MyApplication.android.js")
```

### `extraPackagerArgs`

A list of extra flags that will be passed to the `bundle` command. The list of available flags is in [the CLI documentation](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle). Default is empty. Can be customized as follows:

```groovy
extraPackagerArgs = []
```

### `hermesCommand`

The path to the `hermesc` command (the Hermes Compiler). React Native comes with a version of the Hermes compiler bundled with it, so you generally won't be needing to customize this. The plugin will use the correct compiler for your system by default.

### `hermesFlags`

The list of flags to pass to `hermesc`. By default is `["-O", "-output-source-map"]`. You can customize it as follows

```groovy
hermesFlags = ["-O", "-output-source-map"]
```

## Using Flavors & Build Variants

When building Android apps, you might want to use [custom flavors](https://developer.android.com/studio/build/build-variants#product-flavors) to have different versions of your app starting from the same project.

Please refer to the [official Android guide](https://developer.android.com/studio/build/build-variants) to configure custom build types (like `staging`) or custom flavors (like `full`, `lite`, etc.).
By default new apps are created with two build types (`debug` and `release`) and no custom flavors.

The combination of all the build types and all the flavors generates a set of **build variants**. For instance for `debug`/`staging`/`release` build types and `full`/`lite` you will have 6 build variants: `fullDebug`, `fullStaging`, `fullRelease` and so on.

If you're using custom variants beyond `debug` and `release`, you need to instruct the React Native Gradle Plugin specifying which of your variants are **debuggable** using the [`debuggableVariants`](#debuggablevariants) configuration as follows:

```diff
apply plugin: "com.facebook.react"

react {
+ debuggableVariants = ["fullStaging", "fullDebug"]
}
```

This is necessary because the plugin will skip the JS bundling for all the `debuggableVariants`: you'll need Metro to run them. For example, if you list `fullStaging` in the `debuggableVariants`, you won't be able to publish it to a store as it will be missing the bundle.

## What is the plugin doing under the hood?

The React Native Gradle Plugin is responsible for configuring your Application build to ship React Native applications to production.
The plugin is also used inside 3rd party libraries, to run the [Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md) used for the New Architecture.

以下是該插件的主要職責概述：

- 為每個不可除錯的變體添加 `createBundle<Variant>JsAndAssets` 任務，負責調用 `bundle`、`hermesc` 和 `compose-source-map` 命令。
- 設定正確版本的 `com.facebook.react:react-android` 和 `com.facebook.react:hermes-android` 依賴項，從 `react-native` 的 `package.json` 讀取 React Native 版本。
- 配置必要的 Maven 倉庫（如 Maven Central、Google Maven Repo、JSC 本地 Maven 倉庫等），以獲取所有必需的 Maven 依賴項。
- 設定 NDK，讓您能夠建構採用新架構的應用程式。
- 配置 `buildConfigFields`，讓您在運行時能判斷是否啟用了 Hermes 或新架構。
- 將 Metro 開發伺服器端口設為 Android 資源，讓應用程式知道要連接的端口。
- 若函式庫或應用程式使用新架構的 Codegen，則調用 [React Native Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md)。