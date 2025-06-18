---
id: react-native-gradle-plugin
title: React Native Gradle Plugin
---

本指南說明如何配置 **React Native Gradle 插件**（通常簡稱為 RNGP），以便為 Android 建置您的 React Native 應用程式。

## 使用插件

React Native Gradle 插件以獨立的 NPM 套件形式分發，安裝 `react-native` 時會自動安裝此插件。

透過 `npx react-native init` 建立的新專案**已預先配置**此插件。若您使用此指令建立應用程式，則無需進行額外安裝步驟。

若您要將 React Native 整合至現有專案，請參閱[對應頁面](/docs/next/integration-with-existing-apps#configuring-gradle)：其中包含安裝插件的具體說明。

## 配置插件

預設情況下，插件會以**開箱即用**的合理預設值運作。僅在需要時才應參考本指南進行客製化調整。

您可透過修改 `android/app/build.gradle` 中的 `react` 區塊來配置插件：

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

此為 React Native 專案的根目錄，即存放 `package.json` 的資料夾。預設值為 `..`。您可依下列方式客製化：

```groovy
root = file("../")
```

### `reactNativeDir`

此為存放 `react-native` 套件的資料夾。預設值為 `../node_modules/react-native`。
若您使用 monorepo 或其他套件管理器，可調整 `reactNativeDir` 以符合您的設定。

客製化方式如下：

```groovy
reactNativeDir = file("../node_modules/react-native")
```

### `codegenDir`

此為存放 `react-native-codegen` 套件的資料夾。預設值為 `../node_modules/react-native-codegen`。
若您使用 monorepo 或其他套件管理器，可調整 `codegenDir` 以符合您的設定。

客製化方式如下：

```groovy
codegenDir = file("../node_modules/@react-native/codegen")
```

### `cliFile`

此為 React Native CLI 的入口檔案。預設值為 `../node_modules/react-native/cli.js`。
插件需要透過此入口檔案呼叫 CLI 來進行打包和建立應用程式。

若您使用 monorepo 或其他套件管理器，可調整 `cliFile` 以符合您的設定。
客製化方式如下：

```groovy
cliFile = file("../node_modules/react-native/cli.js")
```

### `debuggableVariants`

此為可除錯的變體清單（有關變體的更多背景資訊，請參閱[使用變體](#using-variants)）。

預設情況下，插件僅將 `debug` 視為 `debuggableVariants`，而 `release` 則否。若您有其他變體（如 `staging`、`lite` 等），則需相應調整此設定。

列為 `debuggableVariants` 的變體不會附帶打包好的 bundle，因此您需要執行 Metro 來運作這些變體。

客製化方式如下：

```groovy
debuggableVariants = ["liteDebug", "prodDebug"]
```

### `nodeExecutableAndArgs`

此為執行所有腳本時應呼叫的 node 指令及其參數清單。預設值為 `[node]`，但可依下列方式添加額外旗標進行客製化：

```groovy
nodeExecutableAndArgs = ["node"]
```

### `bundleCommand`

這是在為應用程式建立打包檔案時要調用的 `bundle` 指令名稱。若您使用 [RAM Bundles](https://reactnative.dev/docs/0.74/ram-bundles-inline-requires) 時會很有用。預設值為 `bundle`，但可透過以下方式自訂以添加額外參數：

```groovy
bundleCommand = "ram-bundle"
```

### `bundleConfig`

此為傳遞給 `bundle --config <檔案>` 的設定檔路徑（若提供）。預設為空（不提供設定檔）。更多關於打包設定檔的資訊可參閱 [CLI 文件](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle)。可透過以下方式自訂：

```groovy
bundleConfig = file(../rn-cli.config.js)
```

### `bundleAssetName`

此為應生成的打包檔案名稱。預設為 `index.android.bundle`。可透過以下方式自訂：

```groovy
bundleAssetName = "MyApplication.android.bundle"
```

### `entryFile`

用於打包生成的入口檔案。預設會搜尋 `index.android.js` 或 `index.js`。可透過以下方式自訂：

```groovy
entryFile = file("../js/MyApplication.android.js")
```

### `extraPackagerArgs`

傳遞給 `bundle` 指令的額外參數列表。可用參數清單請參閱 [CLI 文件](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle)。預設為空。可透過以下方式自訂：

```groovy
extraPackagerArgs = []
```

### `hermesCommand`

`hermesc` 指令（Hermes 編譯器）的路徑。React Native 自帶了 Hermes 編譯器的版本，因此通常不需要自訂此項。插件預設會為您的系統使用正確的編譯器。

### `hermesFlags`

傳遞給 `hermesc` 的參數列表。預設為 `["-O", "-output-source-map"]`。可透過以下方式自訂：

```groovy
hermesFlags = ["-O", "-output-source-map"]
```

## 使用產品風味與建置變體

在建置 Android 應用程式時，您可能會想使用 [自訂風味](https://developer.android.com/studio/build/build-variants#product-flavors) 來從同一專案產生不同版本的應用程式。

請參閱 [官方 Android 指南](https://developer.android.com/studio/build/build-variants) 來設定自訂建置類型（如 `staging`）或自訂風味（如 `full`、`lite` 等）。
新建立的應用程式預設會有兩種建置類型（`debug` 和 `release`）且無自訂風味。

所有建置類型與風味的組合會生成一組**建置變體**。例如對於 `debug`/`staging`/`release` 建置類型與 `full`/`lite` 風味，您將有 6 種建置變體：`fullDebug`、`fullStaging`、`fullRelease` 等。

若您使用 `debug` 和 `release` 以外的自訂變體，需透過 [`debuggableVariants`](#debuggablevariants) 設定指示 React Native Gradle 插件哪些變體是**可調試的**，如下所示：

```diff
apply plugin: "com.facebook.react"

react {
+ debuggableVariants = ["fullStaging", "fullDebug"]
}
```

這是必要的，因為插件會跳過所有 `debuggableVariants` 的 JS 打包：您需要 Metro 來執行它們。例如，若您在 `debuggableVariants` 中列出 `fullStaging`，將無法將其發布到商店，因為它會缺少打包檔案。

## 插件在底層做了什麼？

React Native Gradle 插件負責設定您的應用程式建置，以將 React Native 應用程式發布至生產環境。
該插件也用於第三方函式庫中，以執行新架構所使用的 [Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md)。

以下是該插件的主要職責概述：

- 為每個不可除錯的變體添加 `createBundle<Variant>JsAndAssets` 任務，負責調用 `bundle`、`hermesc` 和 `compose-source-map` 指令。
- 從 `react-native` 的 `package.json` 讀取 React Native 版本，設定正確的 `com.facebook.react:react-android` 和 `com.facebook.react:hermes-android` 依賴項版本。
- 配置必要的 Maven 儲存庫（如 Maven Central、Google Maven Repo、JSC 本地 Maven 儲存庫等），以獲取所有必需的 Maven 依賴項。
- 設定 NDK，讓您能夠建構採用新架構的應用程式。
- 配置 `buildConfigFields`，使您能在執行階段判斷是否啟用了 Hermes 或新架構。
- 將 Metro 開發伺服器端口設定為 Android 資源，讓應用程式知道要連接的端口。
- 若函式庫或應用程式使用新架構的 Codegen，則調用 [React Native Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md)。