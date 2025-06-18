import RemoveGlobalCLI from './\_remove-global-cli.md';
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 安裝依賴套件

您需要安裝 Node、React Native 命令行工具、JDK 和 Android Studio。

雖然可以使用任何編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 Android 版 React Native 應用程式所需的工具鏈。

<h3>Node</h3>

請根據 [Linux 發行版的安裝說明](https://nodejs.org/en/download/package-manager/) 安裝 Node 16 或更新版本。

<h3>Java 開發套件 (JDK)</h3>

React Native 目前推薦使用 Java SE 開發套件 (JDK) 第 11 版。使用更高版本可能會遇到問題。您可以從 [AdoptOpenJDK](https://adoptopenjdk.net/) 下載安裝 [OpenJDK](http://openjdk.java.net)，或透過系統套件管理器安裝。

<h3>Android 開發環境</h3>

若您是 Android 開發新手，設置開發環境可能較為繁瑣。若已有相關經驗，則只需進行部分配置。無論如何，請仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請確保勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

接著點擊「Next」安裝這些元件。

> 若選項呈灰色不可勾選狀態，後續仍可安裝這些元件。

完成安裝並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生碼建置 React Native 應用程式時，需要特定安裝 `Android 13 (Tiramisu)` SDK。可透過 Android Studio 的 SDK 管理器安裝其他版本。

開啟 Android Studio 後，點擊「Configure」按鈕並選擇「SDK Manager」。

> 也可在 Android Studio 的「Settings」對話框中找到 SDK 管理器，路徑為：**Languages & Frameworks** → **Android SDK**。

在 SDK 管理器中選擇「SDK Platforms」標籤頁，勾選右下角的「Show Package Details」。找到並展開 `Android 13 (Tiramisu)` 項目，確保以下內容已勾選：

- `Android SDK Platform 33`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著切換至「SDK Tools」標籤頁，同樣勾選「Show Package Details」。找到並展開「Android SDK Build-Tools」項目，確保選中 `33.0.0` 版本。

最後點擊「Apply」下載並安裝 Android SDK 及相關建置工具。

<h4>3. 配置 ANDROID_HOME 環境變數</h4>

React Native 工具鏈需要設置特定環境變數才能建置含原生碼的應用程式。

將以下內容加入您的 `$HOME/.bash_profile` 或 `$HOME/.bashrc` 配置檔案（若使用 `zsh` 則為 `~/.zprofile` 或 `~/.zshrc`）：

```shell
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

> `.bash_profile` 是 `bash` 專用的配置檔案。若使用其他 shell，需編輯對應的 shell 配置檔案。

對於 `bash` 請輸入 `source $HOME/.bash_profile`，或輸入 `source $HOME/.zprofile` 將配置載入當前 shell。執行 `echo $ANDROID_HOME` 驗證 ANDROID_HOME 是否已設定，並執行 `echo $PATH` 確認正確的目錄已加入路徑中。

> 請確保使用正確的 Android SDK 路徑。您可以在 Android Studio 的「設定」對話框中找到 SDK 的實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

<h3>Watchman</h3>

請遵循 [Watchman 安裝指南](https://facebook.github.io/watchman/docs/install#buildinstall) 從原始碼編譯並安裝 Watchman。

> [Watchman](https://facebook.github.io/watchman/docs/install) 是 Facebook 開發的檔案系統變更監控工具。強烈建議安裝以提升效能並增強特定邊緣案例的相容性（意即：不安裝或許也能運作，但效果因人而異；現在安裝可避免後續可能遇到的問題）。

<h3>React Native 命令列介面</h3>

React Native 內建命令列介面。與其全域安裝並管理特定版本的 CLI，我們建議透過 Node.js 內建的 `npx` 在執行時存取當前版本。使用 `npx react-native <command>` 時，系統會自動下載並執行最新穩定版的 CLI。

<h2>建立新應用程式</h2>

<RemoveGlobalCLI />

React Native 內建命令列介面，可用於生成新專案。您無需全域安裝任何套件，直接使用 Node.js 內建的 `npx` 即可存取。以下示範建立名為「AwesomeProject」的新 React Native 專案：

```shell
npx react-native@latest init AwesomeProject
```

若您要將 React Native 整合至現有應用程式、專案中已安裝 [Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或為現有 React Native 專案新增 Android 支援（參見 [與現有應用程式整合](integration-with-existing-apps.md)），則無需此步驟。您也可使用第三方 CLI（如 [Ignite CLI](https://github.com/infinitered/ignite)）初始化 React Native 應用程式。

<h3>[選用] 使用特定版本或模板</h3>

若要使用特定 React Native 版本建立新專案，可加入 `--version` 參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

您也可透過 `--template` 參數使用自訂的 React Native 模板建立專案。

<h2>準備 Android 裝置</h2>

執行 React Native Android 應用程式需準備 Android 裝置，可以是實體裝置，或更常見的 Android 虛擬裝置（AVD），後者能在電腦上模擬 Android 裝置環境。

無論採用何種方式，均需將裝置設定為開發模式以執行 Android 應用程式。

<h3>使用實體裝置</h3>

若有實體 Android 裝置，可透過 USB 連接電腦後替代 AVD 進行開發，操作步驟請參閱[此文件](running-on-device.md)。

<h3>使用虛擬裝置</h3>

若使用 Android Studio 開啟 `./AwesomeProject/android`，可透過 Android Studio 內的「AVD Manager」查看可用虛擬裝置清單。尋找如下圖示的按鈕：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

若您剛安裝 Android Studio，可能需要[建立新 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」後，從清單中任選一款手機並點擊「Next」，接著選擇 **Tiramisu** API Level 33 映像檔。

> 我們建議在您的系統上配置 [VM 加速](https://developer.android.com/studio/run/emulator-acceleration.html#vm-linux) 以提升效能。完成這些步驟後，請返回 AVD 管理員。

點擊「下一步」然後「完成」以創建您的 AVD。此時您應該可以點擊 AVD 旁邊的綠色三角形按鈕來啟動它，接著進行下一步。

<h2>運行您的 React Native 應用程式</h2>

<h3>步驟 1：啟動 Metro</h3>

首先，您需要啟動 Metro，這是 React Native 內建的 JavaScript 打包工具。Metro「接收一個入口文件和各種選項，並返回一個包含所有代碼及其依賴項的單一 JavaScript 文件。」—[Metro 文檔](https://metrobundler.dev/docs/concepts)

要啟動 Metro，請在您的 React Native 專案文件夾中運行以下命令：

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

> 如果您熟悉網頁開發，Metro 非常類似於 webpack——但用於 React Native 應用程式。與 Kotlin 或 Java 不同，JavaScript 不是編譯的——React Native 也不是。打包與編譯不同，但它可以幫助提升啟動效能並將某些平台特定的 JavaScript 轉換為更廣泛支持的 JavaScript。

<h3>步驟 2：啟動您的應用程式</h3>

讓 Metro Bundler 在它自己的終端中運行。在您的 React Native 專案文件夾中打開一個新的終端。運行以下命令：

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

如果一切設置正確，您應該很快就能在 Android 模擬器中看到您的新應用程式運行。

這是運行應用程式的一種方式——您也可以直接從 Android Studio 中運行它。

> 如果無法正常運行，請參閱 [疑難排解](troubleshooting.md) 頁面。

<h3>修改您的應用程式</h3>

現在您已經成功運行了應用程式，讓我們來修改它。

- 在您選擇的文本編輯器中打開 `App.tsx` 並編輯一些代碼。
- 按兩次 <kbd>R</kbd> 鍵或從開發者選單（<kbd>Ctrl</kbd> + <kbd>M</kbd>）中選擇 `重新載入` 來查看您的變更！

<h3>完成了！</h3>

恭喜！您已成功運行並修改了您的第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 如果您想將這些新的 React Native 代碼添加到現有應用程式中，請查看 [整合指南](integration-with-existing-apps.md)。

如果您想了解更多關於 React Native 的資訊，請查看 [React Native 簡介](getting-started)。