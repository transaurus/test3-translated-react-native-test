---
id: react-native-gradle-plugin
title: React Native Gradle Plugin
---

本指南說明如何配置 **React Native Gradle 插件**（通常簡稱 RNGP），以便為 Android 構建 React Native 應用程式。

## 使用插件

React Native Gradle 插件作為獨立的 NPM 套件分發，會隨 `react-native` 自動安裝。

使用 `npx react-native init` 創建的新專案已**預設配置**該插件。若以此命令創建應用，無需額外安裝步驟。

若需將 React Native 整合至現有專案，請參閱[對應頁面](/docs/next/integration-with-existing-apps#configuring-gradle)，其中包含插件安裝的具體指引。

## 配置插件

插件預設採用**開箱即用**的合理配置。僅在需要時才參考本指南調整行為。

可透過修改 `android/app/build.gradle` 中的 `react` 區塊來配置插件：

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

此參數指向 React Native 專案的根目錄（即包含 `package.json` 的目錄）。預設值為 `..`。可依如下方式自訂：

```groovy
root = file("../")
```

### `reactNativeDir`

此參數指向 `react-native` 套件的安裝目錄。預設值為 `../node_modules/react-native`。
若處於 monorepo 環境或使用其他套件管理器，可調整此路徑以匹配您的設定。

自訂方式如下：

```groovy
reactNativeDir = file("../node_modules/react-native")
```

### `codegenDir`

此參數指向 `react-native-codegen` 套件的安裝目錄。預設值為 `../node_modules/react-native-codegen`。
若處於 monorepo 環境或使用其他套件管理器，可調整此路徑以匹配您的設定。

自訂方式如下：

```groovy
codegenDir = file("../node_modules/@react-native/codegen")
```

### `cliFile`

此參數指向 React Native CLI 的入口文件。預設值為 `../node_modules/react-native/cli.js`。
插件需透過此文件調用 CLI 來進行打包和應用程式構建。

若處於 monorepo 環境或使用其他套件管理器，可調整此路徑以匹配您的設定。
自訂方式如下：

```groovy
cliFile = file("../node_modules/react-native/cli.js")
```

### `debuggableVariants`

此參數列出可調試的變體（關於變體的詳細說明請參見[使用變體](#using-variants)）。

插件預設僅將 `debug` 變體視為可調試，而 `release` 則否。若有其他變體（如 `staging`、`lite` 等），需相應調整此設定。

列為 `debuggableVariants` 的變體不會包含預編譯套件，需依賴 Metro 運行。

自訂方式如下：

```groovy
debuggableVariants = ["liteDebug", "prodDebug"]
```

### `nodeExecutableAndArgs`

此參數定義執行所有腳本時使用的 node 命令及參數。預設值為 `[node]`，可依如下方式添加額外參數：

```groovy
nodeExecutableAndArgs = ["node"]
```

### `bundleCommand`

這是建立應用程式套件時要調用的 `bundle` 指令名稱。若您使用 [RAM Bundles](https://reactnative.dev/docs/0.74/ram-bundles-inline-requires) 時會很有用。預設值為 `bundle`，但可透過以下方式自訂以添加額外參數：

```groovy
bundleCommand = "ram-bundle"
```

### `bundleConfig`

此為傳遞給 `bundle --config <file>` 的設定檔路徑（若提供）。預設為空（不提供設定檔）。更多關於套件設定檔的資訊可參閱 [CLI 文件](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle)。可透過以下方式自訂：

```groovy
bundleConfig = file(../rn-cli.config.js)
```

### `bundleAssetName`

此為應生成的套件檔案名稱。預設為 `index.android.bundle`。可透過以下方式自訂：

```groovy
bundleAssetName = "MyApplication.android.bundle"
```

### `entryFile`

用於生成套件的入口檔案。預設會搜尋 `index.android.js` 或 `index.js`。可透過以下方式自訂：

```groovy
entryFile = file("../js/MyApplication.android.js")
```

### `extraPackagerArgs`

傳遞給 `bundle` 指令的額外參數列表。可用參數清單請參閱 [CLI 文件](https://github.com/react-native-community/cli/blob/main/docs/commands.md#bundle)。預設為空。可透過以下方式自訂：

```groovy
extraPackagerArgs = []
```

### `hermesCommand`

`hermesc` 指令（Hermes 編譯器）的路徑。React Native 已內建 Hermes 編譯器版本，因此通常無需自訂此設定。外掛程式預設會為您的系統使用正確的編譯器。

### `hermesFlags`

傳遞給 `hermesc` 的參數列表。預設為 `["-O", "-output-source-map"]`。可透過以下方式自訂：

```groovy
hermesFlags = ["-O", "-output-source-map"]
```

### `enableBundleCompression`

是否在將套件資源打包成 `.apk` 時進行壓縮。

停用 `.bundle` 的壓縮可讓其直接映射到記憶體，從而改善啟動時間，但會增加磁碟上的應用程式大小。請注意，`.apk` 的下載大小幾乎不受影響，因為 `.apk` 檔案在下載前會經過壓縮。

此功能預設為停用，除非您非常在意應用程式的磁碟空間，否則不應啟用。

## 使用風味與建置變體

在建置 Android 應用程式時，您可能會想使用 [自訂風味](https://developer.android.com/studio/build/build-variants#product-flavors) 來從同一個專案產生不同版本的應用程式。

請參閱 [官方 Android 指南](https://developer.android.com/studio/build/build-variants) 來設定自訂建置類型（如 `staging`）或自訂風味（如 `full`、`lite` 等）。新應用程式預設會建立兩種建置類型（`debug` 和 `release`）且無自訂風味。

所有建置類型與風味的組合會產生一組 **建置變體**。例如，對於 `debug`/`staging`/`release` 建置類型和 `full`/`lite` 風味，您將有 6 個建置變體：`fullDebug`、`fullStaging`、`fullRelease` 等。

若您使用 `debug` 和 `release` 以外的自訂變體，需透過 [`debuggableVariants`](#debuggablevariants) 設定來指示 React Native Gradle 外掛程式哪些變體是 **可偵錯的**，如下所示：

```diff
apply plugin: "com.facebook.react"

react {
+ debuggableVariants = ["fullStaging", "fullDebug"]
}
```

這是必要的，因為插件會跳過所有`debuggableVariants`的JS打包流程：你需要運行Metro來處理這些變體。例如，如果你在`debuggableVariants`中列出`fullStaging`，將無法將其發佈到商店，因為它會缺少打包檔案。

## 插件在底層做了什麼？

React Native Gradle插件負責配置你的應用程式構建，以便將React Native應用程式發佈到生產環境。該插件也被第三方函式庫用於運行[Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md)，這是新架構的一部分。

以下是插件的主要職責概述：

- 為每個非可調試變體添加`createBundle<Variant>JsAndAssets`任務，負責調用`bundle`、`hermesc`和`compose-source-map`命令。
- 根據`react-native`的`package.json`中的版本，設置正確版本的`com.facebook.react:react-android`和`com.facebook.react:hermes-android`依賴。
- 配置必要的Maven倉庫（如Maven Central、Google Maven Repo、JSC本地Maven倉庫等）以獲取所有必需的Maven依賴。
- 配置NDK，讓你能夠構建使用新架構的應用程式。
- 設置`buildConfigFields`，讓你在運行時能判斷是否啟用了Hermes或新架構。
- 將Metro開發伺服器端口設置為Android資源，讓應用程式知道要連接哪個端口。
- 如果函式庫或應用程式使用新架構的Codegen，則調用[React Native Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md)。