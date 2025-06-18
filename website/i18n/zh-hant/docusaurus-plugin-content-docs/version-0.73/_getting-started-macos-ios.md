import RemoveGlobalCLI from './\_remove-global-cli.md';
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 安裝依賴套件

您需要安裝 Node、Watchman、React Native 命令行工具、Xcode 和 CocoaPods。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Xcode 才能設置必要的工具來為 iOS 構建 React Native 應用程式。

### Node 與 Watchman

我們建議使用 [Homebrew](https://brew.sh/) 安裝 Node 和 Watchman。安裝 Homebrew 後，在終端機中執行以下命令：

```shell
brew install node
brew install watchman
```

如果系統已安裝 Node，請確保版本為 18 或更新。

[Watchman](https://facebook.github.io/watchman) 是 Facebook 開發的檔案系統監控工具，強烈建議安裝以提升效能。

### Xcode

請使用 **最新版本** 的 Xcode。

最簡便的安裝方式是透過 [Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。安裝 Xcode 會同時安裝 iOS 模擬器和構建 iOS 應用所需的所有工具。

#### 命令行工具

您還需要安裝 Xcode 命令行工具。打開 Xcode，從選單中選擇 **Settings... (或 Preferences...)**，進入 Locations 面板，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令行工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

#### 在 Xcode 中安裝 iOS 模擬器

要安裝模擬器，打開 **Xcode > Settings... (或 Preferences...)** 並選擇 **Platforms (或 Components)** 標籤頁，選擇您需要的 iOS 版本對應的模擬器。

若使用 Xcode 14.0 或更高版本安裝模擬器，請打開 **Xcode > Settings > Platforms** 標籤頁，點擊 "+" 圖標並選擇 **iOS…** 選項。

#### CocoaPods

[CocoaPods](https://cocoapods.org/) 是 iOS 的依賴管理工具之一，屬於 Ruby [gem](https://en.wikipedia.org/wiki/RubyGems)。您可以使用 macOS 最新版本內建的 Ruby 安裝 CocoaPods。

更多資訊請參閱 [CocoaPods 入門指南](https://guides.cocoapods.org/using/getting-started.html)。

### React Native 命令行工具

React Native 內建命令行工具。我們建議透過 Node.js 自帶的 `npx` 在運行時使用當前版本，而非全局安裝特定版本。使用 `npx react-native <command>` 時，將自動下載並執行最新穩定版的 CLI。

## 建立新專案

<RemoveGlobalCLI />

您可以使用 React Native 內建命令行工具生成新專案。以下指令將建立名為 "AwesomeProject" 的新專案：

```shell
npx react-native@latest init AwesomeProject
```

若您要將 React Native 整合至現有應用程式、專案中已安裝 [Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或是為現有 React Native 專案添加 iOS 支援（參見 [整合現有應用](integration-with-existing-apps.md)），則無需執行此步驟。您也可以使用第三方 CLI（如 [Ignite CLI](https://github.com/infinitered/ignite)）初始化 React Native 專案。

:::info

若在 iOS 環境遇到問題，可嘗試重新安裝依賴套件，步驟如下：

1. 執行 `cd ios` 進入 `ios` 資料夾
2. 執行 `bundle install` 安裝 [Bundler](https://bundler.io/)
3. 執行 `bundle exec pod install` 安裝由 CocoaPods 管理的 iOS 依賴項

:::

### [選用] 使用特定版本或模板

若要使用特定 React Native 版本初始化專案，可加入 `--version` 參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

亦可透過 `--template` 參數使用自訂的 React Native 模板啟動專案

> **注意** 若上述指令執行失敗，可能是系統中安裝了舊版全域 `react-native` 或 `react-native-cli`。請嘗試解除安裝 CLI 後改用 `npx` 執行

### [選用] 環境配置

自 React Native 0.69 版起，可透過模板提供的 `.xcode.env` 檔案配置 Xcode 環境

`.xcode.env` 檔案包含 `NODE_BINARY` 環境變數，用於導出 `node` 執行檔路徑。此為**建議做法**，可將建置基礎設施與系統版 `node` 解耦。若預設值不符需求，應自行調整此變數或指定節點版本管理器路徑

此外，還可添加其他環境變數，並在建置腳本階段引入 `.xcode.env` 檔案。若需執行特定環境的腳本，此為**建議做法**：能有效隔離建置階段與特定環境的依賴關係

:::info
若使用 [NVM](https://nvm.sh/)（Node.js 版本管理工具）與 [zsh](https://ohmyz.sh/)，建議將初始化 NVM 的代碼從 `~/.zshrc` 移至 `~/.zshenv` 檔案，協助 Xcode 定位 Node 執行檔：

```zsh
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # 載入 nvm
```

同時請確認 Xcode 專案中所有「shell 腳本建置階段」均使用 `/bin/zsh` 作為 shell
:::

## 執行 React Native 應用程式

### 步驟 1：啟動 Metro

[**Metro**](https://facebook.github.io/metro/) 是 React Native 的 JavaScript 建置工具。請在專案目錄執行以下指令啟動 Metro 開發伺服器：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm start
```

</TabItem>
<TabItem value="yarn">

```shell
yarn start
```

</TabItem>
</Tabs>

:::note
若熟悉網頁開發，Metro 的功能類似 Vite 或 webpack 等打包工具，但專為 React Native 端到端設計。例如 Metro 使用 [Babel](https://babel.dev/) 將 JSX 等語法轉換為可執行的 JavaScript
:::

### 步驟 2：啟動應用程式

讓 Metro Bundler 在獨立終端機運行，另開新終端機進入 React Native 專案目錄後執行：

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

稍候即可在 iOS 模擬器中看見新應用程式運行

![iOS 上的 AwesomeProject](/docs/assets/GettingStartediOSSuccess.png)

此為執行應用程式的方式之一，亦可直接透過 Xcode 運行

> 若無法正常執行，請參閱[疑難排解](troubleshooting.md)頁面

### 在實體裝置上運行

上述指令預設會在 iOS 模擬器上執行您的應用程式。若要在實體 iOS 裝置上運行應用程式，請參閱[此處說明](running-on-device.md)。

### 修改您的應用程式

現在您已成功運行應用程式，接下來讓我們進行修改。

- 在您選擇的文字編輯器中開啟 `App.tsx` 並編輯部分內容。
- 在 iOS 模擬器中按下 <kbd>Cmd ⌘</kbd> + <kbd>R</kbd> 重新載入應用程式，即可查看變更效果！

### 完成！

恭喜！您已成功運行並修改了第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

## 接下來呢？

- 若要將此 React Native 程式碼整合至現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。

若想深入瞭解 React Native，請查看 [React Native 簡介](getting-started)。