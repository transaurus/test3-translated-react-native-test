# 為你的模組建立函式庫

React Native 擁有豐富的函式庫生態系統來解決常見問題。我們在 [reactnative.directory](https://reactnative.directory) 網站上收集了 React Native 函式庫，這是每位 React Native 開發者都值得收藏的寶貴資源。

有時，你可能會開發一個值得提取為獨立函式庫以供重複使用的模組。這可能是你想在所有應用中重用的函式庫、想作為開源元件分發給生態系統的函式庫，甚至是打算銷售的函式庫。

本指南將教你：

- 如何將模組提取為函式庫
- 如何使用 NPM 分發函式庫

## 將模組提取為函式庫

你可以使用 [`create-react-native-library`](https://callstack.github.io/react-native-builder-bob/create) 工具來建立新函式庫。此工具會設置一個包含所有必要樣板程式碼的新函式庫：所有配置檔案及各平台所需的檔案。它還提供了一個互動式選單來引導你完成函式庫的建立過程。

要將模組提取為獨立函式庫，可按照以下步驟操作：

1. 建立新函式庫
2. 將程式碼從應用移至函式庫
3. 更新程式碼以反映新結構
4. 發佈它

### 1. 建立函式庫

1. 執行以下命令開始建立過程：

```sh
npx create-react-native-library@latest <Name of Your Library>
```

2. 為你的模組命名。必須是有效的 npm 名稱，因此應全部使用小寫字母。可以使用 `-` 分隔單字。
3. 為套件添加描述。
4. 繼續填寫表單，直到遇到問題 _"你想開發什麼類型的函式庫？"_
   ![函式庫類型](/docs/assets/what-library.png)
5. 為本指南選擇 _Turbo 模組_ 選項。注意你可以同時為新架構和舊架構建立函式庫。
6. 接著，你可以選擇要存取平台（Kotlin 和 Objective-C）的函式庫，還是共享的 C++ 函式庫（適用於 Android 和 iOS 的 C++）。
7. 最後，選擇 `Test App` 作為最後一個選項。此選項會在函式庫資料夾內建立一個已配置好的獨立應用程式。

互動式提示完成後，工具會建立一個資料夾，其結構在 Visual Studio Code 中如下所示：

<img class="half-size" alt="Folder structure after initializing a new library." src="/docs/assets/turbo-native-modules/c++visualstudiocode.webp" />

你可以自由瀏覽已建立的程式碼。但最重要的部分包括：

- `android` 資料夾：存放 Android 程式碼
- `cpp` 資料夾：存放 C++ 程式碼
- `ios` 資料夾：存放 iOS 程式碼
- `src` 資料夾：存放 JS 程式碼

`package.json` 已配置了我們提供給 `create-react-native-library` 工具的所有資訊，包括套件名稱和描述。請注意，`package.json` 也已配置為執行 Codegen。

```json
  "codegenConfig": {
    "name": "RN<your module name>Spec",
    "type": "all",
    "jsSrcsDir": "src",
    "outputDir": {
      "ios": "ios/generated",
      "android": "android/generated"
    },
    "android": {
      "javaPackageName": "com.<name-of-the-module>"
    }
  },
```

最後，函式庫已包含所有基礎設施，可讓函式庫與 iOS 和 Android 連結。

### 2. 從你的應用複製程式碼

本指南的其餘部分假設你已在應用中建立了一個本地 Turbo 原生模組，遵循網站上其他指南所示的步驟：平台特定的 Turbo 原生模組，或[跨平台 Turbo 原生模組](./pure-cxx-modules)。但這也適用於元件和舊架構的模組與元件。你需要根據情況調整要複製和更新的檔案。

<!-- TODO: add links for Turbo Native Modules -->

1. **[傳統架構模組與元件無需此步驟]** 將應用中 `specs` 資料夾的程式碼移至 `create-react-native-library` 生成的 `src` 資料夾。
2. 更新 `index.ts` 文件以正確導出 Turbo Native Module 規格，使其可從函式庫存取。例如：

```ts
import NativeSampleModule from './NativeSampleModule';

export default NativeSampleModule;
```

3. 複製原生模組程式碼：

   - 若有原生模組程式碼，將 `android/src/main/java/com/<模組名稱>` 的內容替換為應用中的對應程式碼。
   - 若有 iOS 原生模組程式碼，替換 `ios` 資料夾內容。
   - 若有 C++ 原生模組程式碼，替換 `cpp` 資料夾內容。

4. **[傳統架構模組與元件無需此步驟]** 更新所有舊規格名稱的引用至新名稱（即函式庫 `package.json` 中 `codegenConfig` 定義的名稱）。例如：若應用中 `codegenConfig.name` 設為 `AppSpecs`，而函式庫中名為 `RNNativeSampleModuleSpec`，則需將所有 `AppSpecs` 替換為 `RNNativeSampleModuleSpec`。

完成！您已將所有必要程式碼從應用移至獨立函式庫。

## 測試您的函式庫

`create-react-native-library` 工具已預設配置好一個範例應用程式，可直接用於測試函式庫功能。

在 `example` 資料夾中，您會看到與 [`react-native-community/template`](https://github.com/react-native-community/template) 相同結構的新 React Native 應用。

測試步驟：

1. 進入 `example` 資料夾。
2. 執行 `yarn install` 安裝依賴項。
3. 僅限 iOS：安裝 CocoaPods（`cd ios && pod install`）。
4. 在 `example` 資料夾下執行 `yarn android` 建置並運行 Android。
5. 在 `example` 資料夾下執行 `yarn ios` 建置並運行 iOS。

## 將函式庫作為本地模組使用

某些情況下，您可能希望直接將函式庫作為本地模組重用，無需發佈至 NPM。

此時您的專案結構可能是函式庫與應用程式處於同層級目錄。

```shell
Development
├── App
└── Library
```

使用 `create-react-native-library` 創建的函式庫仍適用此場景。

1. 在應用程式目錄執行 `yarn add ../函式庫名稱` 添加本地依賴。
2. 僅限 iOS：進入 `App/ios` 執行 `bundle exec pod install` 安裝依賴。
3. 更新 `App.tsx` 以導入函式庫程式碼。例如：

```tsx
import NativeSampleModule from '@site/versioned_docs/version-0.78/Library/src/index';
```

若立即運行應用，Metro 會因無法存取 `函式庫` 資料夾中的 JS 文件而報錯。需修改 `metro.config.js` 如下：

```diff
const {getDefaultConfig, mergeConfig} = require('@react-native/metro-config');

/**
 * Metro configuration
 * https://reactnative.dev/docs/metro
 *
 * @type {import('metro-config').MetroConfig}
 */
+ const path = require('path');

- const config = {}
+ const config = {
+  // Make Metro able to resolve required external dependencies
+  watchFolders: [
+    path.resolve(__dirname, '../Library'),
+  ],
+  resolver: {
+    extraNodeModules: {
+      'react-native': path.resolve(__dirname, 'node_modules/react-native'),
+    },
+  },
+};

module.exports = mergeConfig(getDefaultConfig(__dirname), config);
```

`watchFolders` 配置讓 Metro 監控指定路徑（此處為 `../函式庫`）的文件變更。  
`resolver` 屬性確保函式庫能正確引用應用使用的 React Native 程式碼，避免導入失敗。

現在即可正常建置並運行應用：

- 在 `example` 資料夾中執行 `yarn android` 來建置並運行 Android 應用。
- 在 `example` 資料夾中執行 `yarn ios` 來建置並運行 iOS 應用。

## 將函式庫發佈至 NPM

由於 `create-react-native-library` 已預先配置好相關設定，因此發佈至 NPM 的所有準備工作皆已完成。

1. 在模組中安裝相依套件：執行 `yarn install`。
2. 建置函式庫：執行 `yarn prepare`。
3. 發佈函式庫：執行 `yarn release`。

稍候片刻，您即可在 NPM 上找到您的函式庫。若要驗證是否成功發佈，請執行：

```bash
npm view <package.name>
```

其中 `package.name` 是您在初始化函式庫時於 `package.json` 檔案中設定的名稱。

現在，您可以透過執行以下指令將函式庫安裝至您的應用程式中：

```bash
yarn add <package.name>
```

:::note
僅限 iOS：每當您安裝含有原生程式碼的新模組時，必須重新安裝 CocoaPods，建議執行 `bundle exec pod install`（推薦使用 Ruby 的 Bundler）或直接執行 `pod install`（不推薦）。
:::

恭喜！您已成功發佈第一個 React Native 函式庫。