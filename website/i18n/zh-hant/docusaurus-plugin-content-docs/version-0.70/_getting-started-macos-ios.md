import RemoveGlobalCLI from './\_remove-global-cli.md';

## 安裝依賴套件

您需要安裝 Node、Watchman、React Native 命令行介面、Ruby 版本管理器、Xcode 和 CocoaPods。

雖然您可以選擇任何編輯器來開發應用程式，但需要安裝 Xcode 才能設置必要的工具來為 iOS 構建 React Native 應用程式。

### Node 與 Watchman

我們建議使用 [Homebrew](http://brew.sh/) 安裝 Node 和 Watchman。安裝 Homebrew 後，在終端機中執行以下命令：

```shell
brew install node
brew install watchman
```

如果您的系統已安裝 Node，請確保版本為 14 或更新。

[Watchman](https://facebook.github.io/watchman) 是 Facebook 開發的檔案系統變更監控工具。強烈建議安裝以提升效能。

### Ruby

React Native 使用 `.ruby-version` 檔案確保 Ruby 版本符合需求。目前 macOS 13.2 預裝的 Ruby 2.6.10 並不符合此版本 React Native 的要求（2.7.5）。建議安裝 Ruby 版本管理器並在系統中安裝正確的 Ruby 版本。

常見的 Ruby 版本管理器包括：

- [rbenv](https://github.com/rbenv/rbenv)
- [RVM](https://rvm.io/)
- [chruby](https://github.com/postmodern/chruby)
- [asdf-vm](https://github.com/asdf-vm) 搭配 [asdf-ruby](https://github.com/asdf-vm/asdf-ruby) 插件

若要檢查當前 Ruby 版本，可執行以下命令：

```
ruby --version
```

React Native 使用 [此版本](https://github.com/facebook/react-native/blob/v0.70.7/.ruby-version) 的 Ruby。您也可以在 RN 專案根目錄的 `.ruby-version` 檔案中找到專案所需的版本。

### Bundler

[Bundler](https://bundler.io/) 是管理專案 Ruby 依賴套件的 Ruby gem。我們需要 Ruby 來安裝 CocoaPods，使用 Bundler 可確保所有依賴套件版本一致，使專案正常運作。

若想了解更多關於此工具的必要性，可閱讀 [這篇文章](https://bundler.io/guides/rationale.html#bundlers-purpose-and-rationale)。

### Xcode

請使用 **最新版本** 的 Xcode。

最簡單的安裝方式是透過 [Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。安裝 Xcode 時會同時安裝 iOS 模擬器及構建 iOS 應用程式所需的所有工具。

#### 命令行工具

您還需要安裝 Xcode 命令行工具。打開 Xcode，從選單中選擇「Preferences...」，進入 Locations 面板，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令行工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

#### 在 Xcode 中安裝 iOS 模擬器

要安裝模擬器，請打開 <strong>Xcode > Preferences...</strong> 並選擇 <strong>Components</strong> 標籤。選擇與您想使用的 iOS 版本對應的模擬器。

#### CocoaPods

[CocoaPods](https://cocoapods.org/) 是用 Ruby 開發的，可直接透過 macOS 預裝的 Ruby 進行安裝。

如需更多資訊，請參閱 [CocoaPods 入門指南](https://guides.cocoapods.org/using/getting-started.html)。

### React Native 命令列介面

React Native 內建了命令列介面。與其全域安裝並管理特定版本的 CLI，我們建議您使用 Node.js 自帶的 `npx` 在執行時存取當前版本。透過 `npx react-native <command>`，系統會在執行命令時下載並執行當前穩定版的 CLI。

## 建立新應用程式

<RemoveGlobalCLI />

您可以使用 React Native 內建的命令列介面來生成新專案。讓我們建立一個名為「AwesomeProject」的新 React Native 專案：

```shell
npx react-native init AwesomeProject
```

如果您是將 React Native 整合到現有應用程式中，或已在專案中安裝 [Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或是為現有 React Native 專案新增 Android 支援（請參閱[與現有應用程式整合](integration-with-existing-apps.md)），則無需此步驟。您也可以使用第三方 CLI（如 [Ignite CLI](https://github.com/infinitered/ignite)）來初始化 React Native 應用程式。

:::info

若在 iOS 環境遇到問題，請嘗試透過以下指令重新安裝依賴項：

1. `cd ios` 進入 iOS 目錄
2. `bundle install` 安裝 Bundler
   1. 如有需要：安裝 [Ruby 版本管理工具](#ruby) 並更新 Ruby 版本
3. `bundle exec pod install` 安裝 iOS 依賴項

:::

### [選用] 使用特定版本或模板

若要使用特定 React Native 版本建立新專案，可使用 `--version` 參數：

```shell
npx react-native init AwesomeProject --version X.XX.X
```

您也可以透過 `--template` 參數使用自訂模板（如 TypeScript）建立專案：

```shell
npx react-native init AwesomeTSProject --template react-native-template-typescript
```

> **注意** 若上述指令執行失敗，可能是您的電腦上全域安裝了舊版 `react-native` 或 `react-native-cli`。請嘗試解除安裝 CLI 並改用 `npx` 執行。

### [選用] 設定環境

從 React Native 0.69 版開始，可使用模板提供的 `.xcode.env` 檔案來設定 Xcode 環境。

`.xcode.env` 檔案包含一個環境變數，用於在 `NODE_BINARY` 變數中匯出 `node` 可執行檔的路徑。這是**建議做法**，可將建置基礎設施與系統版本的 `node` 解耦。若與預設值不同，您應自訂此變數為自己的路徑或 `node` 版本管理工具。

此外，您還可新增其他環境變數，並在建置指令碼階段引入 `.xcode.env` 檔案。若需執行特定環境的指令碼，這是**建議做法**：可將建置階段與特定環境解耦。

## 執行 React Native 應用程式

### 步驟 1：啟動 Metro

首先，您需要啟動 Metro——React Native 內建的 JavaScript 打包工具。Metro「接收一個入口檔案和各種選項，並回傳一個包含所有程式碼及其依賴項的單一 JavaScript 檔案。」——[Metro 文件](https://metrobundler.dev/docs/concepts)

請在 React Native 專案目錄中執行 `npx react-native start` 來啟動 Metro：

```shell
npx react-native start
```

`react-native start` 會啟動 Metro 打包工具。

> 若使用 Yarn 套件管理工具，在現有專案中執行 React Native 指令時，可用 `yarn` 取代 `npx`。

> 如果你熟悉網頁開發，Metro 就像是 React Native 應用程式中的 webpack。與 Swift 或 Objective-C 不同，JavaScript 不需要編譯，React Native 也是如此。打包（bundling）並不等同於編譯，但它可以幫助提升啟動性能，並將某些平台特定的 JavaScript 轉換為更廣泛支援的 JavaScript。

### 步驟 2：啟動你的應用程式

讓 Metro Bundler 在其專用的終端機中運行。在你的 React Native 專案資料夾中開啟一個新的終端機，並執行以下命令：

```shell
npx react-native run-ios
```

你應該很快就能看到你的新應用程式在 iOS 模擬器中運行。

![AwesomeProject on iOS](/docs/assets/GettingStartediOSSuccess.png)

`npx react-native run-ios` 是運行應用程式的一種方式。你也可以直接從 Xcode 中運行它。

> 如果無法順利運行，請參閱 [疑難排解](troubleshooting.md) 頁面。

### 在實體裝置上運行

上述命令預設會自動在 iOS 模擬器上運行你的應用程式。如果你想在實際的 iOS 裝置上運行應用程式，請按照 [這裡](running-on-device.md) 的指示操作。

### 修改你的應用程式

現在你已經成功運行了應用程式，讓我們來修改它。

- 在你選擇的文字編輯器中開啟 `App.js` 並編輯一些內容。
- 在 iOS 模擬器中按下 `⌘R` 重新載入應用程式，查看你的變更！

### 完成了！

恭喜！你已經成功運行並修改了你的第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

## 接下來呢？

- 如果你想將這段新的 React Native 程式碼添加到現有的應用程式中，請查看 [整合指南](integration-with-existing-apps.md)。

如果你對 React Native 有更多興趣，可以閱讀 [React Native 簡介](getting-started)。