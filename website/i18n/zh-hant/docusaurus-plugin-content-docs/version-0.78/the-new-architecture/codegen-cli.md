# Codegen 命令行工具

手動調用 Gradle 或執行腳本可能難以記憶且流程繁瑣。

為簡化流程，我們開發了可協助執行這些任務的 CLI 工具：**Codegen** 命令行工具。此命令會為您的專案執行 [@react-native/codegen](https://www.npmjs.com/package/@react-native/codegen)，提供以下選項：

```sh
npx @react-native-community/cli codegen --help
Usage: rnc-cli codegen [options]

Options:
  --verbose            Increase logging verbosity
  --path <path>        Path to the React Native project root. (default: "/Users/MyUsername/projects/my-app")
  --platform <string>  Target platform. Supported values: "android", "ios", "all". (default: "all")
  --outputPath <path>  Path where generated artifacts will be output to.
  -h, --help           display help for command
```

## 範例

- 從當前工作目錄讀取 `package.json`，依據其 codegenConfig 生成程式碼。

```shell
npx @react-native-community/cli codegen
```

- 從當前工作目錄讀取 `package.json`，在 codegenConfig 定義的位置生成 iOS 程式碼。

```shell
npx @react-native-community/cli codegen --platform ios
```

- 從 `third-party/some-library` 讀取 `package.json`，在 `third-party/some-library/android/generated` 生成 Android 程式碼。

```shell
npx @react-native-community/cli codegen \
    --path third-party/some-library \
    --platform android \
    --outputPath third-party/some-library/android/generated
```

# 將生成程式碼納入函式庫

Codegen CLI 是函式庫開發者的利器，可用於預覽生成程式碼以確認需實作的介面。

通常生成程式碼不會包含在函式庫中，而是由使用該函式庫的應用程式在建置時執行 Codegen。此設定適用於多數情況，但 Codegen 也提供透過 `includesGeneratedCode` 屬性將生成程式碼直接納入函式庫的機制。

理解使用 `includesGeneratedCode = true` 的影響至關重要。包含生成程式碼具有以下優勢：

- 無需依賴應用程式為您執行 **Codegen**，生成程式碼始終存在。
- 實作檔案與生成介面保持一致（使您的函式庫程式碼更能抵禦 Codegen 的 API 變更）。
- 無需在 Android 上維護兩套檔案以支援兩種架構。僅需保留新架構檔案，即可確保向後相容。
- 由於所有原生程式碼均已包含，可將函式庫的原生部分作為預建置發佈。

但您也需注意一項缺點：

- 生成程式碼將使用函式庫內部定義的 React Native 版本。若您的函式庫基於 React Native 0.76 發佈，生成程式碼將以該版本為基礎。這可能導致生成程式碼與使用**舊版** React Native 的應用程式不相容（例如運行於 React Native 0.75 的應用）。

## 啟用 `includesGeneratedCode`

啟用此設定的步驟：

- 在函式庫 `package.json` 的 `codegenConfig` 欄位新增 `includesGeneratedCode` 屬性，設為 `true`。
- 使用 Codegen CLI 在本機執行 **Codegen**。
- 更新 `package.json` 以包含生成程式碼。
- 更新 `podspec` 以包含生成程式碼。
- 更新 `build.Gradle` 檔案以包含生成程式碼。
- 在 `react-native.config.js` 中更新 `cmakeListsPath`，使 Gradle 不在建置目錄中尋找 CMakeLists 檔案，而是轉向您的 outputDir。