import RemoveGlobalCLI from './\_remove-global-cli.md';

## 安裝依賴套件

您需要安裝 Node、Watchman、React Native 命令行工具、Ruby 版本管理工具、Xcode 和 CocoaPods。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Xcode 才能設置必要的工具來為 iOS 構建 React Native 應用程式。

### Node 與 Watchman

建議使用 [Homebrew](http://brew.sh/) 安裝 Node 和 Watchman。安裝 Homebrew 後，在終端機執行以下命令：

```shell
brew install node
brew install watchman
```

若系統已安裝 Node，請確認版本為 14 或更新。

[Watchman](https://facebook.github.io/watchman) 是 Facebook 開發的檔案系統監控工具，強烈建議安裝以提升效能。

### Ruby

[Ruby](https://www.ruby-lang.org/en/) 是一種通用程式語言。React Native 在某些與 iOS 依賴管理相關的腳本中使用 Ruby。如同其他程式語言，Ruby 也有多個不同版本。

React Native 使用 `.ruby-version` 檔案確保 Ruby 版本符合需求。目前 macOS 13.2 預裝 Ruby 2.6.10，但此版本 React Native 需要的是 2.7.6。建議安裝 Ruby 版本管理工具並在系統中安裝正確版本。

常見的 Ruby 版本管理工具包括：

- [rbenv](https://github.com/rbenv/rbenv)
- [RVM](https://rvm.io/)
- [chruby](https://github.com/postmodern/chruby)
- [asdf-vm](https://github.com/asdf-vm) 搭配 [asdf-ruby](https://github.com/asdf-vm/asdf-ruby) 插件

若要檢查當前 Ruby 版本，可執行以下命令：

```
ruby --version
```

React Native 使用[此版本](https://github.com/facebook/react-native/blob/v0.71.3/.ruby-version)的 Ruby。您也可以在 RN 專案根目錄的 `.ruby-version` 檔案中找到專案所需的版本。

### Ruby 的 Bundler

Ruby 使用 **gems** 來管理其依賴套件。您可以將 gem 視為 NPM 中的套件、Homebrew 中的 formula 或 CocoaPods 中的單一 pod。

Ruby 的 [Bundler](https://bundler.io/) 是一個用於管理專案 Ruby 依賴的 gem。我們需要 Ruby 來安裝 CocoaPods，使用 Bundler 可確保所有依賴套件版本一致，使專案正常運作。

若想了解更多關於此工具的必要性，可閱讀[這篇文章](https://bundler.io/guides/rationale.html#bundlers-purpose-and-rationale)。

### Xcode

請使用 Xcode 的**最新版本**。

最簡單的安裝方式是透過 [Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。安裝 Xcode 時會同時安裝 iOS 模擬器與構建 iOS 應用程式所需的所有工具。

#### 命令行工具

您還需要安裝 Xcode 命令行工具。打開 Xcode，從選單選擇「Preferences...」，進入 Locations 面板，在 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令行工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

#### 在 Xcode 中安裝 iOS 模擬器

要安裝模擬器，請打開<strong>Xcode > 偏好設定...</strong>並選擇<strong>元件</strong>標籤。選擇一個與您希望使用的iOS版本相對應的模擬器。

如果您使用的是Xcode 14.0或更高版本來安裝模擬器，請打開**Xcode > 設定 > 平台**標籤，然後點擊"+"圖標並選擇**iOS…**選項。

#### CocoaPods

[CocoaPods](https://cocoapods.org/)是iOS可用的依賴管理系統之一。它是用Ruby構建的，您可以使用在前幾步中配置的Ruby版本來安裝它。

更多資訊，請訪問[CocoaPods入門指南](https://guides.cocoapods.org/using/getting-started.html)。

### React Native 命令行界面

React Native內建了一個命令行界面。與其全局安裝和管理特定版本的CLI，我們建議您使用Node.js自帶的`npx`在運行時訪問當前版本。使用`npx react-native <command>`時，CLI的當前穩定版本將在下達命令時被下載並執行。

## 創建一個新的應用程序

<RemoveGlobalCLI />

您可以使用React Native內建的命令行界面來生成一個新項目。讓我們創建一個名為"AwesomeProject"的新React Native項目：

```shell
npx react-native@latest init AwesomeProject
```

如果您正在將React Native集成到現有應用程序中，或者您已在項目中安裝了[Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或者您正在為現有React Native項目添加iOS支持（參見[與現有應用集成](integration-with-existing-apps.md)），則不需要此步驟。您也可以使用第三方CLI來初始化您的React Native應用，例如[Ignite CLI](https://github.com/infinitered/ignite)。

:::info

如果您在iOS上遇到問題，請嘗試通過運行以下命令重新安裝依賴項：

1. `cd ios` 導航到iOS目錄
2. `bundle install` 安裝Bundler
   1. 如果需要：安裝[Ruby版本管理器](#ruby)並更新Ruby版本
3. `bundle exec pod install` 安裝iOS依賴項

:::

### [可選] 使用特定版本或模板

如果您想使用特定的React Native版本開始一個新項目，可以使用`--version`參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

您也可以使用`--template`參數從自定義的React Native模板開始一個項目。

> **注意** 如果上述命令失敗，您可能在電腦上全局安裝了舊版本的`react-native`或`react-native-cli`。嘗試卸載CLI並使用`npx`運行CLI。

### [可選] 配置您的環境

從React Native 0.69版本開始，可以使用模板提供的`.xcode.env`文件來配置Xcode環境。

`.xcode.env`文件包含一個環境變量，用於在`NODE_BINARY`變量中導出`node`可執行文件的路徑。
這是**建議的方法**，可以將構建基礎設施與系統版本的`node`解耦。如果與默認值不同，您應該使用自己的路徑或自己的`node`版本管理器來定制此變量。

除此之外，還可以添加任何其他環境變量，並在構建腳本階段中引用`.xcode.env`文件。如果您需要運行某些需要特定環境的腳本，這是**建議的方法**：它允許將構建階段與特定環境解耦。

## 運行您的React Native應用程序

### 步驟1：啟動Metro

首先，您需要啟動 Metro——React Native 內建的 JavaScript 打包工具。Metro「接收一個入口檔案和各種選項，並返回一個包含所有程式碼及其依賴項的單一 JavaScript 檔案。」——[Metro 文件](https://metrobundler.dev/docs/concepts)

要啟動 Metro，請在您的 React Native 專案資料夾中執行 `npx react-native start`：

```shell
npx react-native start
```

`react-native start` 會啟動 Metro 打包工具。

> 如果您使用 Yarn 套件管理器，在現有專案中執行 React Native 指令時，可以用 `yarn` 代替 `npx`。

> 如果您熟悉網頁開發，Metro 對 React Native 應用來說很像 webpack。與 Swift 或 Objective-C 不同，JavaScript 不需要編譯——React Native 也不需要。打包（bundling）與編譯不同，但它可以幫助提高啟動效能，並將某些平台特定的 JavaScript 轉換為更廣泛支援的 JavaScript。

### 步驟 2：啟動您的應用程式

讓 Metro 打包工具在其專用的終端機中運行。在您的 React Native 專案資料夾中開啟一個新的終端機，然後執行以下指令：

```shell
npx react-native run-ios
```

您應該很快就能看到您的新應用程式在 iOS 模擬器中運行。

![iOS 上的 AwesomeProject](/docs/assets/GettingStartediOSSuccess.png)

`npx react-native run-ios` 是運行應用程式的一種方式。您也可以直接在 Xcode 中運行它。

> 如果無法正常運行，請參閱[疑難排解](troubleshooting.md)頁面。

### 在實體裝置上運行

上述指令預設會自動在 iOS 模擬器中運行您的應用程式。如果您想在實體 iOS 裝置上運行應用程式，請按照[這裡](running-on-device.md)的說明操作。

### 修改您的應用程式

現在您已經成功運行了應用程式，讓我們來修改它。

- 在您選擇的文字編輯器中開啟 `App.tsx` 並編輯一些程式碼。
- 在 iOS 模擬器中按下 `⌘R` 重新載入應用程式，查看您的變更！

### 完成了！

恭喜！您已成功運行並修改了您的第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

## 接下來呢？

- 如果想將這段新的 React Native 程式碼整合到現有應用程式中，請參閱[整合指南](integration-with-existing-apps.md)。

如果您想進一步了解 React Native，請查看 [React Native 簡介](getting-started)。