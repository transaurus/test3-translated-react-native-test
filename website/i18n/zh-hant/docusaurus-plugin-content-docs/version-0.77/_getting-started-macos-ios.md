## 安裝依賴套件

您需要安裝 Node、Watchman、React Native 命令行工具、Xcode 和 CocoaPods。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Xcode 才能設置建置 iOS 版 React Native 應用程式所需的工具。

### Node 與 Watchman

建議使用 [Homebrew](https://brew.sh/) 安裝 Node 和 Watchman。安裝 Homebrew 後，在終端機執行以下命令：

```shell
brew install node
brew install watchman
```

若系統已安裝 Node，請確認版本為 18.18 或更新。

[Watchman](https://facebook.github.io/watchman) 是 Facebook 開發的檔案系統監控工具。強烈建議安裝以提升效能。

### Xcode

請使用 **最新版本** 的 Xcode。

最簡便的安裝方式是透過 [Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。安裝 Xcode 會同時安裝 iOS 模擬器與建置 iOS 應用程式所需的所有工具。

#### 命令行工具

還需安裝 Xcode 命令行工具。開啟 Xcode，從選單選擇 **Settings... (或 Preferences...)**，進入 Locations 面板，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令行工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

#### 在 Xcode 中安裝 iOS 模擬器

要安裝模擬器，開啟 **Xcode > Settings... (或 Preferences...)** 並選擇 **Platforms (或 Components)** 標籤頁。選擇與您所需 iOS 版本對應的模擬器。

若使用 Xcode 14.0 或更高版本安裝模擬器，請開啟 **Xcode > Settings > Platforms** 標籤頁，點擊 "+" 圖示並選擇 **iOS…** 選項。

#### CocoaPods

[CocoaPods](https://cocoapods.org/) 是 iOS 的依賴管理工具之一。CocoaPods 是一個 Ruby [gem](https://en.wikipedia.org/wiki/RubyGems)。您可使用 macOS 內建的 Ruby 版本安裝 CocoaPods。

更多資訊請參閱 [CocoaPods 入門指南](https://guides.cocoapods.org/using/getting-started.html)。

### [選填] 環境設定

從 React Native 0.69 版開始，可使用模板提供的 `.xcode.env` 檔案設定 Xcode 環境。

`.xcode.env` 檔案包含一個環境變數，用於在 `NODE_BINARY` 變數中導出 `node` 執行檔的路徑。
這是 **建議做法**，可將建置基礎設施與系統版本的 `node` 解耦。若與預設值不同，您應根據自身路徑或 Node 版本管理器自訂此變數。

此外，還可添加其他環境變數，並在建置腳本階段引入 `.xcode.env` 檔案。若需執行特定環境的腳本，此為 **建議做法**：可將建置階段與特定環境解耦。

:::info
若您已使用 [NVM](https://nvm.sh/)（可協助安裝與切換 Node.js 版本的指令工具）與 [zsh](https://ohmyz.sh/)，建議將初始化 NVM 的程式碼從 `~/.zshrc` 移至 `~/.zshenv` 檔案中，以協助 Xcode 找到您的 Node 執行檔：

```zsh
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # 此指令會載入 nvm
```

同時請確認 Xcode 專案中所有「shell script build phase」均使用 `/bin/zsh` 作為其 shell。
:::

<h3>大功告成！</h3>

恭喜！您已成功設定開發環境。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 若需將此 React Native 程式碼整合至現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。
- 若想深入瞭解 React Native，請參閱[React Native 簡介](getting-started)。