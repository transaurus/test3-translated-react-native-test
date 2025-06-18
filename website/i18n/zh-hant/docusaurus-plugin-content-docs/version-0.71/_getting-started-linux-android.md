import RemoveGlobalCLI from './\_remove-global-cli.md';

## 安裝依賴套件

您需要安裝 Node、React Native 命令行工具、JDK 以及 Android Studio。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 Android 版 React Native 應用程式所需的工具。

<h3>Node</h3>

請依照 [Linux 發行版的安裝說明](https://nodejs.org/en/download/package-manager/) 安裝 Node 14 或更新版本。

<h3>Java 開發套件 (JDK)</h3>

React Native 目前建議使用 Java SE 開發套件 (JDK) 第 11 版。使用更高版本的 JDK 可能會遇到問題。您可以從 [AdoptOpenJDK](https://adoptopenjdk.net/) 下載安裝 [OpenJDK](http://openjdk.java.net)，或透過系統套件管理器安裝。

<h3>Android 開發環境</h3>

如果您是 Android 開發新手，設置開發環境可能會有些繁瑣。若您已熟悉 Android 開發，則可能需要進行一些配置。無論哪種情況，請務必仔細遵循接下來的步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請確保勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

接著點擊「Next」安裝所有元件。

> 若選項呈灰色不可選，您稍後仍有機會安裝這些元件。

完成安裝並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生代碼建置 React Native 應用程式需要特定的 `Android 13 (Tiramisu)` SDK。可透過 Android Studio 的 SDK 管理器安裝其他 Android SDK 版本。

開啟 Android Studio 後，點擊「Configure」按鈕並選擇「SDK Manager」。

> 您也可以在 Android Studio 的「Settings」對話框中找到 SDK 管理器，路徑為 **Languages & Frameworks** → **Android SDK**。

在 SDK 管理器內選擇「SDK Platforms」標籤頁，然後勾選右下角的「Show Package Details」。找到並展開 `Android 13 (Tiramisu)` 項目，確保以下項目已勾選：

- `Android SDK Platform 33`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著選擇「SDK Tools」標籤頁，同樣勾選「Show Package Details」。找到並展開「Android SDK Build-Tools」項目，確保已選取 `33.0.0` 版本。

最後點擊「Apply」下載並安裝 Android SDK 及相關建置工具。

<h4>3. 配置 ANDROID_HOME 環境變數</h4>

React Native 工具需要設置某些環境變數才能建置包含原生代碼的應用程式。

將以下內容加入您的 `$HOME/.bash_profile` 或 `$HOME/.bashrc` 配置檔案（若使用 `zsh` 則為 `~/.zprofile` 或 `~/.zshrc`）：

```shell
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

> `.bash_profile` 是 `bash` 專用的設定檔。若使用其他 shell，您需要編輯對應的 shell 專用配置檔案。

對於 `bash` 請輸入 `source $HOME/.bash_profile`，或輸入 `source $HOME/.zprofile` 以將配置載入當前 shell。執行 `echo $ANDROID_HOME` 驗證 ANDROID_HOME 是否已設定，並執行 `echo $PATH` 確認正確的目錄已加入路徑中。

> 請確保使用正確的 Android SDK 路徑。您可以在 Android Studio 的「設定」對話框中找到 SDK 的實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

<h3>Watchman</h3>

請依照 [Watchman 安裝指南](https://facebook.github.io/watchman/docs/install#buildinstall) 從原始碼編譯並安裝 Watchman。

> [Watchman](https://facebook.github.io/watchman/docs/install) 是 Facebook 開發的檔案系統變更監控工具。強烈建議安裝以提升效能，並避免特定邊緣案例的相容性問題（白話版：不裝或許能運作，但效果因人而異；現在安裝可避免未來可能的頭痛狀況）。

<h3>React Native 命令列介面</h3>

React Native 內建命令列介面。與其全域安裝特定版本的 CLI，我們建議透過 Node.js 內建的 `npx` 在執行時存取最新版本。使用 `npx react-native <command>` 時，系統會自動下載並執行當前穩定版的 CLI。

<h2>建立新應用程式</h2>

<RemoveGlobalCLI />

React Native 內建命令列介面，可用於生成新專案。透過 Node.js 內建的 `npx` 即可直接使用，無需全域安裝。以下建立名為「AwesomeProject」的新專案：

```shell
npx react-native@latest init AwesomeProject
```

若您要將 React Native 整合至現有應用程式、專案中已安裝 [Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或為現有 React Native 專案新增 Android 支援（參見 [與現有應用程式整合](integration-with-existing-apps.md)），則無需執行此步驟。您也可使用第三方 CLI（如 [Ignite CLI](https://github.com/infinitered/ignite)）初始化 React Native 應用程式。

<h3>[選用] 使用特定版本或模板</h3>

若要使用特定 React Native 版本建立專案，可加入 `--version` 參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

您也可透過 `--template` 參數使用自訂的 React Native 模板建立專案。

<h2>準備 Android 裝置</h2>

執行 React Native Android 應用程式需準備 Android 裝置，可以是實體裝置，或更常見的 Android 虛擬裝置（AVD）來模擬電腦上的 Android 環境。

無論採用何種方式，均需設定裝置以執行開發用 Android 應用程式。

<h3>使用實體裝置</h3>

若有實體 Android 裝置，可透過 USB 連接電腦後，依照[此處說明](running-on-device.md)設定，即可替代 AVD 進行開發。

<h3>使用虛擬裝置</h3>

若用 Android Studio 開啟 `./AwesomeProject/android`，可透過 Android Studio 內的「AVD Manager」查看可用虛擬裝置清單。尋找如下圖示的按鈕：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

若您最近才安裝 Android Studio，可能需要[建立新的 AVD](https://developer.android.com/studio/run/managing-avds.html)。點選「Create Virtual Device...」，然後從清單中選擇任一款手機並點擊「Next」，接著選擇 **Tiramisu** API Level 33 的系統映像。

> 建議您配置[虛擬機器加速](https://developer.android.com/studio/run/emulator-acceleration.html#vm-linux)以提升效能。完成設定後，請返回 AVD 管理員。

點擊「Next」後再按「Finish」即可建立 AVD。此時您應能點擊 AVD 旁的綠色三角形按鈕來啟動模擬器，接著繼續下一步。

<h2>執行您的 React Native 應用程式</h2>

<h3>步驟 1：啟動 Metro</h3>

首先需啟動 Metro——React Native 內建的 JavaScript 打包工具。Metro「接收一個入口檔案與各項設定，最終輸出一份包含所有程式碼與依賴項的單一 JavaScript 檔案。」——[Metro 文件](https://metrobundler.dev/docs/concepts)

請在 React Native 專案目錄下執行 `npx react-native start` 來啟動 Metro：

```shell
npx react-native start
```

`react-native start` 指令會啟動 Metro 打包工具。

> 若使用 Yarn 套件管理器，在現有專案中執行 React Native 指令時可用 `yarn` 替代 `npx`。

> 熟悉網頁開發者可以將 Metro 視為 React Native 的 webpack。不同於 Kotlin 或 Java，JavaScript 不需編譯——React Native 也是。打包雖不等同於編譯，但能改善啟動效能並將部分平台特定的 JavaScript 轉換為相容性更高的程式碼。

<h3>步驟 2：啟動應用程式</h3>

讓 Metro 打包工具在獨立終端機中運行。另開新終端機進入專案目錄，執行以下指令：

```shell
npx react-native run-android
```

若所有設定正確，您將很快在 Android 模擬器中看到新應用程式運行。

`npx react-native run-android` 是執行應用程式的方式之一，您亦可直接透過 Android Studio 運行。

> 若遇到問題，請參閱[疑難排解](troubleshooting.md)頁面。

<h3>修改應用程式</h3>

成功運行應用程式後，現在來修改它。

- 用文字編輯器開啟 `App.tsx` 並修改部分程式碼。
- 快速按兩次 `R` 鍵，或從開發者選單（`Ctrl + M`）選擇「Reload」即可查看變更！

<h3>大功告成！</h3>

恭喜！您已成功運行並修改了第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來？</h2>

- 若要將此 React Native 程式碼整合至現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。

若想深入瞭解 React Native，請查看 [React Native 簡介](getting-started)。