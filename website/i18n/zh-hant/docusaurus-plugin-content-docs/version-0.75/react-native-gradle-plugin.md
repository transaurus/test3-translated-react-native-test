---
id: react-native-gradle-plugin
title: React Native Gradle Plugin
---

This guide describes how to configure the **React Native Gradle Plugin** (often referred as RNGP), when building your React Native application for Android.

## Using the plugin

The React Native Gradle Plugin is distributed as a separate NPM package which is installed automatically with `react-native`.

The plugin is **already configured** for new projects created using `npx react-native init`. You don't need to do any extra steps to install it if you created your app with this command.

If you're integrating React Native into an existing project, please refer to [the corresponding page](/docs/next/integration-with-existing-apps#configuring-gradle): it contains specific instructions on how to install the plugin.

## Configuring the plugin

By default, the plugin will work **out of the box** with sensible defaults. You should refer to this guide and customize the behavior only if you need it.

To configure the plugin you can modify the `react` block, inside your `android/app/build.gradle`:

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

Each configuration key is described below:

### `root`

This is the root folder of your React Native project, i.e. where the `package.json` file lives. Default is `..`. You can customize it as follows:

```groovy
root = file("../")
```

### `reactNativeDir`

This is the folder where the `react-native` package lives. Default is `../node_modules/react-native`.
If you're in a monorepo or using a different package manager, you can use adjust `reactNativeDir` to your setup.

You can customize it as follows:

```groovy
reactNativeDir = file("../node_modules/react-native")
```

### `codegenDir`

This is the folder where the `react-native-codegen` package lives. Default is `../node_modules/react-native-codegen`.
If you're in a monorepo or using a different package manager, you can adjust `codegenDir` to your setup.

You can customize it as follows:

```groovy
codegenDir = file("../node_modules/@react-native/codegen")
```

### `cliFile`

This is the entrypoint file for the React Native CLI. Default is `../node_modules/react-native/cli.js`.
The entrypoint file is needed as the plugin needs to invoke the CLI for bundling and creating your app.

If you're in a monorepo or using a different package manager, you can adjust `cliFile` to your setup.
You can customize it as follows:

```groovy
cliFile = file("../node_modules/react-native/cli.js")
```

### `debuggableVariants`

This is the list of variants that are debuggable (see [using variants](#using-variants) for more context on variants).

By default the plugin is considering as `debuggableVariants` only `debug`, while `release` is not. If you have other
variants (like `staging`, `lite`, etc.) you'll need to adjust this accordingly.

Variants that are listed as `debuggableVariants` will not come with a shipped bundle, so you'll need Metro to run them.

You can customize it as follows:

```groovy
debuggableVariants = ["liteDebug", "prodDebug"]
```

### `nodeExecutableAndArgs`

This is the list of node command and arguments that should be invoked for all the scripts. By default is `[node]` but can be customized to add extra flags as follows:

```groovy
nodeExecutableAndArgs = ["node"]
```

### `bundleCommand`

這是在建立應用程式套件時要呼叫的 `bundle` 指令名稱。若您使用 [RAM Bundles](https://reactnative.dev/docs/0.74/ram-bundles-inline-requires) 時會很有用。預設值為 `bundle`，但可透過以下方式自訂以添加額外參數：

```groovy
bundleCommand = "ram-bundle"
```

### `bundleConfig`

這是傳遞給 `bundle --config <file>` 的設定檔案路徑（若提供）。預設為空（不提供設定檔）。更多關於套件設定檔的資訊可參閱 [CLI 文件](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle)。可透過以下方式自訂：

```groovy
bundleConfig = file(../rn-cli.config.js)
```

### `bundleAssetName`

這是應生成的套件檔案名稱。預設為 `index.android.bundle`。可透過以下方式自訂：

```groovy
bundleAssetName = "MyApplication.android.bundle"
```

### `entryFile`

用於套件生成的入口檔案。預設會搜尋 `index.android.js` 或 `index.js`。可透過以下方式自訂：

```groovy
entryFile = file("../js/MyApplication.android.js")
```

### `extraPackagerArgs`

傳遞給 `bundle` 指令的額外參數列表。可用參數清單請參閱 [CLI 文件](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle)。預設為空。可透過以下方式自訂：

```groovy
extraPackagerArgs = []
```

### `hermesCommand`

`hermesc` 指令（Hermes 編譯器）的路徑。React Native 自帶了 Hermes 編譯器版本，因此通常無需自訂此項。插件預設會為您的系統使用正確的編譯器。

### `hermesFlags`

傳遞給 `hermesc` 的參數列表。預設為 `["-O", "-output-source-map"]`。可透過以下方式自訂：

```groovy
hermesFlags = ["-O", "-output-source-map"]
```

## 使用風味與建置變體

在建置 Android 應用程式時，您可能會想使用 [自訂風味](https://developer.android.com/studio/build/build-variants#product-flavors) 來從同一個專案產生不同版本的應用程式。

請參閱 [官方 Android 指南](https://developer.android.com/studio/build/build-variants) 來設定自訂建置類型（如 `staging`）或自訂風味（如 `full`、`lite` 等）。
新應用程式預設會建立兩種建置類型（`debug` 和 `release`）且無自訂風味。

所有建置類型與風味的組合會產生一組 **建置變體**。例如對於 `debug`/`staging`/`release` 建置類型和 `full`/`lite` 風味，您將有 6 個建置變體：`fullDebug`、`fullStaging`、`fullRelease` 等。

若您使用 `debug` 和 `release` 以外的自訂變體，需透過 [`debuggableVariants`](#debuggablevariants) 設定指示 React Native Gradle 插件哪些變體是 **可除錯的**，如下所示：

```diff
apply plugin: "com.facebook.react"

react {
+ debuggableVariants = ["fullStaging", "fullDebug"]
}
```

這是必要的，因為插件會跳過所有 `debuggableVariants` 的 JS 套件生成：您需要 Metro 來執行它們。例如，若您將 `fullStaging` 列在 `debuggableVariants` 中，將無法將其發布到商店，因為它會缺少套件。

## 插件在底層做了什麼？

React Native Gradle 插件負責設定您的應用程式建置，以將 React Native 應用程式發布至生產環境。
該插件也用於第三方函式庫中，以執行新架構所使用的 [Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md)。

以下是該插件的主要職責概述：

- 為每個不可調試的變體添加 `createBundle<Variant>JsAndAssets` 任務，負責調用 `bundle`、`hermesc` 和 `compose-source-map` 命令。
- 從 `react-native` 的 `package.json` 中讀取 React Native 版本，設置正確的 `com.facebook.react:react-android` 和 `com.facebook.react:hermes-android` 依賴版本。
- 配置必要的 Maven 倉庫（如 Maven Central、Google Maven Repo、JSC 本地 Maven 倉庫等），以獲取所有必需的 Maven 依賴項。
- 配置 NDK，讓您能夠構建使用新架構的應用程式。
- 設置 `buildConfigFields`，以便在運行時判斷是否啟用了 Hermes 或新架構。
- 將 Metro 開發伺服器端口設置為 Android 資源，讓應用程式知道連接的端口。
- 如果函式庫或應用程式使用新架構的 Codegen，則調用 [React Native Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md)。