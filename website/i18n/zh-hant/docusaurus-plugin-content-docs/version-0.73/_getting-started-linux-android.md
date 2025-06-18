import RemoveGlobalCLI from './\_remove-global-cli.md';
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 安裝依賴套件

您需要安裝 Node、React Native 命令行界面、JDK 和 Android Studio。

雖然您可以選擇任何編輯器來開發應用程式，但必須安裝 Android Studio 才能設置必要的工具來為 Android 構建 React Native 應用程式。

<h3>Node</h3>

請依照 [Linux 發行版的安裝說明](https://nodejs.org/en/download/package-manager/) 安裝 Node 18 或更新版本。

<h3>Java 開發套件 (JDK)</h3>

React Native 目前推薦使用 Java SE 開發套件 (JDK) 第 17 版。使用更高版本的 JDK 可能會遇到問題。您可以從 [AdoptOpenJDK](https://adoptopenjdk.net/) 下載並安裝 [OpenJDK](https://openjdk.java.net)，或透過系統套件管理器安裝。

<h3>Android 開發環境</h3>

如果您是 Android 開發的新手，設置開發環境可能會有些繁瑣。如果您已經熟悉 Android 開發，則可能需要配置一些內容。無論哪種情況，請務必仔細遵循接下來的幾個步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在 Android Studio 安裝精靈中，請確保勾選以下所有項目的複選框：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

然後，點擊「下一步」安裝所有這些組件。

> 如果複選框呈灰色，您稍後仍有機會安裝這些組件。

安裝完成並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新的 Android SDK。然而，使用原生代碼構建 React Native 應用程式需要特定的 `Android 14 (UpsideDownCake)` SDK。可以透過 Android Studio 的 SDK 管理器安裝其他 Android SDK。

為此，請打開 Android Studio，點擊「Configure」按鈕並選擇「SDK Manager」。

> SDK 管理器也可以在 Android Studio 的「Settings」對話框中找到，位於 **Languages & Frameworks** → **Android SDK**。

在 SDK 管理器中選擇「SDK Platforms」標籤頁，然後勾選右下角的「Show Package Details」。找到並展開 `Android 14 (UpsideDownCake)` 條目，確保勾選以下項目：

- `Android SDK Platform 34`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著，選擇「SDK Tools」標籤頁並同樣勾選「Show Package Details」。找到並展開「Android SDK Build-Tools」條目，確保選擇 `34.0.0`。

最後，點擊「Apply」下載並安裝 Android SDK 及相關構建工具。

<h4>3. 配置 ANDROID_HOME 環境變數</h4>

React Native 工具需要設置一些環境變數才能使用原生代碼構建應用程式。

將以下行添加到您的 `$HOME/.bash_profile` 或 `$HOME/.bashrc` 配置文件中（如果您使用 `zsh`，則為 `~/.zprofile` 或 `~/.zshrc`）：

```shell
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

> `.bash_profile` 是 `bash` 特有的。如果您使用其他 shell，則需要編輯相應的 shell 專用配置文件。

對於 `bash` 請輸入 `source $HOME/.bash_profile`，或輸入 `source $HOME/.zprofile` 將配置載入當前 shell。通過執行 `echo $ANDROID_HOME` 驗證 ANDROID_HOME 是否已設置，並執行 `echo $PATH` 檢查正確的目錄是否已添加到路徑中。

> 請確保使用正確的 Android SDK 路徑。您可以在 Android Studio 的「Settings」對話框中找到 SDK 的實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

<h3>Watchman</h3>

按照 [Watchman 安裝指南](https://facebook.github.io/watchman/docs/install#buildinstall) 從原始碼編譯並安裝 Watchman。

> [Watchman](https://facebook.github.io/watchman/docs/install) 是 Facebook 開發的用於監控文件系統變更的工具。強烈建議安裝它以獲得更好的性能，並在某些邊緣情況下提高兼容性（譯註：不安裝可能也能運行，但效果因環境而異；現在安裝可避免後續潛在問題）。

<h3>React Native 命令行工具</h3>

React Native 內建了命令行工具。與其全域安裝並管理特定版本的 CLI，我們建議您使用 Node.js 自帶的 `npx` 在運行時訪問當前版本。通過 `npx react-native <command>` 執行命令時，將自動下載並運行當前穩定版的 CLI。

<h2>創建新應用</h2>

<RemoveGlobalCLI />

React Native 內建的命令行工具可用於生成新項目。您無需全域安裝任何東西，直接使用 Node.js 自帶的 `npx` 即可訪問該工具。讓我們創建一個名為「AwesomeProject」的新 React Native 項目：

```shell
npx react-native@latest init AwesomeProject
```

如果您是將 React Native 集成到現有應用中，或已在項目中安裝 [Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或是為現有 React Native 項目添加 Android 支持（參見 [與現有應用集成](integration-with-existing-apps.md)），則無需此步驟。您也可以使用第三方 CLI（如 [Ignite CLI](https://github.com/infinitered/ignite)）來初始化 React Native 應用。

<h3>[可選] 使用特定版本或模板</h3>

如果想用特定 React Native 版本創建新項目，可使用 `--version` 參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

您也可以通過 `--template` 參數使用自定義的 React Native 模板啟動項目。

<h2>準備 Android 設備</h2>

運行 React Native Android 應用需要 Android 設備。可以是實體 Android 設備，或更常見的做法是使用 Android 虛擬設備（AVD）在電腦上模擬 Android 設備。

無論哪種方式，都需要準備設備以運行開發用的 Android 應用。

<h3>使用實體設備</h3>

如果有實體 Android 設備，可通過 USB 線連接電腦，按照[此處說明](running-on-device.md)將其用於開發以替代 AVD。

<h3>使用虛擬設備</h3>

如果用 Android Studio 打開 `./AwesomeProject/android`，可以通過 Android Studio 內的「AVD Manager」查看可用虛擬設備（AVD）列表。尋找如下圖標：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

如果最近安裝了 Android Studio，可能需要[創建新 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」，然後從列表中選擇任意手機並點擊「Next」，接著選擇 **UpsideDownCake** API Level 34 鏡像。

> 我們建議在您的系統上配置 [VM 加速](https://developer.android.com/studio/run/emulator-acceleration.html#vm-linux) 以提升效能。完成這些指示後，請返回 AVD 管理員。

點擊「下一步」然後「完成」以創建您的 AVD。此時您應該可以點擊 AVD 旁邊的綠色三角形按鈕來啟動它，然後繼續下一步。

<h2>運行您的 React Native 應用程式</h2>

<h3>步驟 1：啟動 Metro</h3>

[**Metro**](https://facebook.github.io/metro/) 是 React Native 的 JavaScript 建構工具。要啟動 Metro 開發伺服器，請在您的專案資料夾中運行以下命令：

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
如果您熟悉網頁開發，Metro 類似於 Vite 和 webpack 等打包工具，但專為 React Native 端到端設計。例如，Metro 使用 [Babel](https://babel.dev/) 將 JSX 等語法轉換為可執行的 JavaScript。
:::

<h3>步驟 2：啟動您的應用程式</h3>

讓 Metro Bundler 在它自己的終端機中運行。在您的 React Native 專案資料夾中開啟一個新的終端機。運行以下命令：

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

如果一切設置正確，您應該很快就能看到您的新應用程式在 Android 模擬器中運行。

這是運行應用程式的一種方式——您也可以直接從 Android Studio 中運行它。

> 如果無法正常運作，請參閱 [疑難排解](troubleshooting.md) 頁面。

<h3>修改您的應用程式</h3>

現在您已成功運行應用程式，讓我們來修改它。

- 在您選擇的文字編輯器中開啟 `App.tsx` 並編輯一些行。
- 按兩下 <kbd>R</kbd> 鍵或從開發選單中選擇 `重新載入` (<kbd>Ctrl</kbd> + <kbd>M</kbd>) 以查看您的變更！

<h3>完成了！</h3>

恭喜！您已成功運行並修改了您的第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 如果您想將這段新的 React Native 代碼添加到現有應用程式中，請查看 [整合指南](integration-with-existing-apps.md)。

如果您想了解更多關於 React Native 的資訊，請查看 [React Native 簡介](getting-started)。