import RemoveGlobalCLI from './\_remove-global-cli.md';
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 安裝依賴套件

您需要安裝 Node、Watchman、React Native 命令行界面、JDK 以及 Android Studio。

雖然您可以使用任何喜歡的編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 React Native Android 應用程式所需的工具。

<h3>Node 與 Watchman</h3>

建議使用 [Homebrew](http://brew.sh/) 安裝 Node 和 Watchman。安裝 Homebrew 後，在終端機執行以下命令：

```shell
brew install node
brew install watchman
```

若系統已安裝 Node，請確認版本為 16 或更新。

[Watchman](https://facebook.github.io/watchman) 是 Facebook 開發的檔案系統監控工具，強烈建議安裝以提升效能。

<h3>Java 開發套件 (JDK)</h3>

建議使用 [Homebrew](http://brew.sh/) 安裝 Azul **Zulu** 發行版的 OpenJDK。安裝 Homebrew 後，在終端機執行以下命令：

```shell
brew install --cask zulu@11

# Get path to where cask was installed to double-click installer
brew info --cask zulu@11
```

安裝 JDK 後，請更新 `JAVA_HOME` 環境變數。若依上述步驟安裝，JDK 路徑通常為 `/Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home`

Zulu OpenJDK 發行版提供 **Intel 和 M1 Mac 專用 JDK**，可確保在 M1 Mac 上的建置速度優於 Intel 架構 JDK。

若系統已安裝 JDK，建議使用 JDK 11 版本。使用更高版本可能會遇到問題。

<h3>Android 開發環境</h3>

若您是 Android 開發新手，設置開發環境可能較為繁瑣。若已有經驗，則需調整部分設定。無論如何，請務必仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

接著點擊「Next」安裝所有元件。

> 若選項呈灰色不可選，後續仍可安裝這些元件。

完成安裝並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生碼建置 React Native 應用程式需特定安裝 `Android 13 (Tiramisu)` SDK。可透過 Android Studio 的 SDK 管理員安裝其他版本。

開啟 Android Studio 後，點擊「More Actions」按鈕並選擇「SDK Manager」。

![Android Studio 歡迎畫面](/docs/assets/GettingStartedAndroidStudioWelcomeMacOS.png)

> 也可在 Android Studio「設定」對話框的 **Languages & Frameworks** → **Android SDK** 中找到 SDK 管理員。

在 SDK 管理員中選擇「SDK Platforms」標籤頁，勾選右下角「Show Package Details」。找到並展開 `Android 13 (Tiramisu)` 項目，確認以下內容已勾選：

- `Android SDK Platform 33`
- `Intel x86 Atom_64 系統映像` 或 `Google APIs Intel x86 Atom 系統映像` 或 (適用於 Apple M1 晶片) `Google APIs ARM 64 v8a 系統映像`

接著，選擇「SDK Tools」標籤頁，同樣勾選「顯示套件詳細資訊」。找到並展開「Android SDK Build-Tools」項目，確保已選取 `33.0.0` 版本。

最後，點擊「Apply」以下載並安裝 Android SDK 及相關建置工具。

<h4>3. 設定 ANDROID_HOME 環境變數</h4>

React Native 工具需要設定某些環境變數才能建置包含原生代碼的應用程式。

將以下內容加入您的 `~/.zprofile` 或 `~/.zshrc` 設定檔（若使用 `bash`，則為 `~/.bash_profile` 或 `~/.bashrc`）：

```shell
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

執行 `source ~/.zprofile`（或 `bash` 用戶執行 `source ~/.bash_profile`）以載入設定至當前 shell。透過執行 `echo $ANDROID_HOME` 確認 ANDROID_HOME 已設定，並執行 `echo $PATH` 檢查正確目錄是否已加入路徑。

> 請確保使用正確的 Android SDK 路徑。您可以在 Android Studio 的「Settings」對話框中找到 SDK 實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

<h3>React Native 命令列介面</h3>

React Native 內建命令列介面。與其全域安裝並管理特定版本的 CLI，我們建議您使用 Node.js 自帶的 `npx` 在執行時存取當前版本。透過 `npx react-native <command>` 執行指令時，將自動下載並執行當前穩定版的 CLI。

<h2>建立新應用程式</h2>

<RemoveGlobalCLI />

React Native 內建命令列介面，可用於生成新專案。您無需全域安裝任何套件，直接使用 Node.js 自帶的 `npx` 即可存取。以下示範建立名為「AwesomeProject」的新 React Native 專案：

```shell
npx react-native@latest init AwesomeProject
```

若您要將 React Native 整合至現有應用程式、專案中已安裝 [Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或為現有 React Native 專案新增 Android 支援（參見 [與現有應用程式整合](integration-with-existing-apps.md)），則無需此步驟。您也可使用第三方 CLI（如 [Ignite CLI](https://github.com/infinitered/ignite)）初始化 React Native 應用程式。

<h3>[選用] 使用特定版本或範本</h3>

若要使用特定 React Native 版本建立新專案，可加入 `--version` 參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

您也可透過 `--template` 參數使用自訂 React Native 範本建立專案。

<h2>準備 Android 裝置</h2>

執行 React Native Android 應用程式需要 Android 裝置。您可使用實體 Android 裝置，或更常見的做法是使用 Android 虛擬裝置（AVD）在電腦上模擬 Android 裝置。

無論採用何種方式，均需準備裝置以執行開發用 Android 應用程式。

<h3>使用實體裝置</h3>

若有實體 Android 裝置，可透過 USB 連接電腦後，依照[此處說明](running-on-device.md)設定為開發用途，無需使用 AVD。

<h3>使用虛擬裝置</h3>

若使用 Android Studio 開啟 `./AwesomeProject/android` 專案，可透過點選工具列中的「AVD Manager」圖示（外觀如下）查看可用安卓虛擬裝置清單：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

若剛安裝 Android Studio，需先[建立新 AVD](https://developer.android.com/studio/run/managing-avds.html)。點選「Create Virtual Device...」後選擇任一手機型號，接著選取 **Tiramisu** 系統的 API Level 33 映像檔。

依序點擊「Next」與「Finish」完成建立。此時可點擊 AVD 旁的綠色三角按鈕啟動模擬器，即可繼續後續步驟。

<h2>執行 React Native 應用程式</h2>

<h3>步驟 1：啟動 Metro 打包工具</h3>

首先需啟動 Metro——React Native 內建的 JavaScript 打包工具。根據 [Metro 官方文件](https://metrobundler.dev/docs/concepts)，其功能為「接收入口檔案與各項參數，最終輸出包含所有程式碼與依賴項的單一 JavaScript 檔案」。

在專案根目錄執行以下指令啟動 Metro：

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

> 對於熟悉網頁開發者，Metro 類似 React Native 的 webpack。需注意 JavaScript 不同於 Kotlin/Java 需編譯，打包（Bundling）主要用於提升啟動效能與轉譯平台特定語法。

<h3>步驟 2：啟動應用程式</h3>

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android
```

</TabItem>
</Tabs>

若環境設定正確，稍後即可在安卓模擬器中看見新建立的應用程式。

![Android 模擬器中的 AwesomeProject](/docs/assets/GettingStartedAndroidSuccessMacOS.png)

此為執行方式之一，亦可直接透過 Android Studio 啟動應用程式。

> 若執行失敗，請參閱[疑難排解指南](troubleshooting.md)。

<h3>修改應用程式</h3>

成功執行後，現在開始修改應用程式：

- 用文字編輯器開啟 `App.tsx` 並修改內容
- 快速按兩次 <kbd>R</kbd> 鍵，或透過開發者選單（<kbd>Cmd ⌘</kbd> + <kbd>M</kbd>）選擇「Reload」即可預覽變更

<h3>大功告成！</h3>

恭喜！您已成功執行並修改第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>後續步驟</h2>

- 如需將此 React Native 專案整合至現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)

想深入學習 React Native？建議閱讀 [React Native 入門指南](getting-started)。