import RemoveGlobalCLI from './\_remove-global-cli.md';
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<h2>安裝依賴套件</h2>

您需要安裝 Node、React Native 命令行介面、JDK 和 Android Studio。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 React Native Android 應用所需的工具。

<h3 id="jdk">Node 與 JDK</h3>

我們建議透過 Windows 熱門套件管理工具 [Chocolatey](https://chocolatey.org) 安裝 Node。

建議使用 Node 的 LTS 版本。若需切換不同版本，可透過 Windows 的 Node 版本管理工具 [nvm-windows](https://github.com/coreybutler/nvm-windows) 安裝。

React Native 還需要 [Java SE 開發套件 (JDK)](https://openjdk.java.net/projects/jdk/11/)，同樣可透過 Chocolatey 安裝。

以系統管理員身分開啟命令提示字元（右鍵點擊命令提示字元並選擇「以系統管理員身分執行」），然後執行以下指令：

```powershell
choco install -y nodejs-lts microsoft-openjdk11
```

若系統已安裝 Node，請確認版本為 16 或更新。若已安裝 JDK，建議使用 JDK11。使用更高版本的 JDK 可能會遇到問題。

> 更多安裝選項請參閱 [Node 官方下載頁面](https://nodejs.org/en/download/)。

> 若使用最新版 Java 開發套件，需變更專案的 Gradle 版本以識別 JDK。請至 `{專案根目錄}\android\gradle\wrapper\gradle-wrapper.properties` 修改 `distributionUrl` 值來升級 Gradle 版本。最新 Gradle 版本請參考 [Gradle 發佈頁面](https://gradle.org/releases/)。

<h3>Android 開發環境</h3>

若您不熟悉 Android 開發，設置開發環境可能較為繁瑣。若已熟悉 Android 開發，仍需進行部分配置。無論如何，請仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請確保勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`
- 若未使用 Hyper-V：`Performance (Intel ® HAXM)`（[AMD 或 Hyper-V 用戶請參閱此處](https://android-developers.googleblog.com/2018/07/android-emulator-amd-processor-hyper-v.html)）

點擊「Next」安裝所有元件。

> 若選項呈灰色不可選，後續仍可安裝這些元件。

安裝完成並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生碼建置 React Native 應用需特定安裝 `Android 13 (Tiramisu)` SDK。其他 Android SDK 可透過 Android Studio 的 SDK 管理員安裝。

開啟 Android Studio，點擊「More Actions」按鈕並選擇「SDK Manager」。

![Android Studio 歡迎畫面](/docs/assets/GettingStartedAndroidStudioWelcomeWindows.png)

> SDK 管理員也可在 Android Studio「設定」對話框的 **Languages & Frameworks** → **Android SDK** 中找到。

在SDK管理器中選擇「SDK Platforms」標籤頁，然後勾選右下角的「Show Package Details」。找到並展開「Android 13 (Tiramisu)」項目，確保以下項目已被勾選：

- `Android SDK Platform 33`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著選擇「SDK Tools」標籤頁，同樣勾選「Show Package Details」。找到並展開「Android SDK Build-Tools」項目，確保選中`33.0.0`版本。

最後點擊「Apply」下載並安裝Android SDK及相關建置工具。

<h4>3. 配置ANDROID_HOME環境變數</h4>

React Native工具需要設置一些環境變數才能建置包含原生代碼的應用程式。

1. 打開**Windows控制面板**
2. 點擊**使用者帳戶**，然後再次點擊**使用者帳戶**
3. 點擊**變更我的環境變數**
4. 點擊**新增...**創建一個指向Android SDK路徑的`ANDROID_HOME`使用者變數：

![ANDROID_HOME環境變數](/docs/assets/GettingStartedAndroidEnvironmentVariableANDROID_HOME.png)

SDK預設安裝在以下位置：

```powershell
%LOCALAPPDATA%\Android\Sdk
```

您可以在Android Studio的「Settings」對話框中找到SDK的實際位置，路徑為**Languages & Frameworks** → **Android SDK**。

請開啟新的命令提示字元視窗以確保新環境變數已載入，再繼續下一步。

1. 開啟powershell
2. 複製貼上**Get-ChildItem -Path Env:\\**到powershell
3. 確認`ANDROID_HOME`已成功添加

<h4>4. 將platform-tools加入Path</h4>

1. 打開**Windows控制面板**
2. 點擊**使用者帳戶**，然後再次點擊**使用者帳戶**
3. 點擊**變更我的環境變數**
4. 選擇**Path**變數
5. 點擊**編輯**
6. 點擊**新增**並將platform-tools的路徑加入清單

此資料夾的預設位置為：

```powershell
%LOCALAPPDATA%\Android\Sdk\platform-tools
```

<h3>React Native命令行介面</h3>

React Native內建命令行介面。我們建議您使用Node.js自帶的`npx`在運行時存取當前版本，而非全域安裝和管理特定版本的CLI。通過`npx react-native <command>`，系統會在執行命令時下載並運行當前穩定的CLI版本。

<h2>創建新應用程式</h2>

<RemoveGlobalCLI />

React Native內建命令行介面，可用於生成新專案。您無需全域安裝任何套件，直接使用Node.js自帶的`npx`即可存取。讓我們創建一個名為「AwesomeProject」的新React Native專案：

```shell
npx react-native@latest init AwesomeProject
```

如果您是將React Native整合到現有應用程式中，或已在專案中安裝[Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或是為現有React Native專案添加Android支援（參見[與現有應用程式整合](integration-with-existing-apps.md)），則無需此步驟。您也可以使用第三方CLI（如[Ignite CLI](https://github.com/infinitered/ignite)）來初始化React Native應用程式。

<h3>[可選]使用特定版本或模板</h3>

若想使用特定React Native版本創建新專案，可使用`--version`參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

你也可以使用 `--template` 參數來啟動一個自訂的 React Native 模板專案。

<h2>準備 Android 裝置</h2>

你需要一個 Android 裝置來運行你的 React Native Android 應用程式。這可以是實體的 Android 裝置，或者更常見的是使用 Android 虛擬裝置 (AVD)，它允許你在電腦上模擬 Android 裝置。

無論哪種方式，你都需要準備裝置以運行開發用的 Android 應用程式。

<h3>使用實體裝置</h3>

如果你有實體的 Android 裝置，你可以透過 USB 線將其連接到電腦，並按照[這裡](running-on-device.md)的指示來代替 AVD 進行開發。

<h3>使用虛擬裝置</h3>

如果你使用 Android Studio 打開 `./AwesomeProject/android`，可以透過 Android Studio 內的「AVD Manager」查看可用的 Android 虛擬裝置 (AVDs) 列表。尋找一個看起來像這樣的圖標：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

如果你最近安裝了 Android Studio，可能需要[創建一個新的 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」，然後從列表中選擇任何手機並點擊「Next」，接著選擇 **Tiramisu** API Level 33 映像。

> 如果你沒有安裝 HAXM，點擊「Install HAXM」或按照[這些指示](https://github.com/intel/haxm/wiki/Installation-Instructions-on-Windows)進行設置，然後返回 AVD Manager。

點擊「Next」然後「Finish」來創建你的 AVD。此時，你應該可以點擊 AVD 旁邊的綠色三角形按鈕來啟動它，然後繼續下一步。

<h2>運行你的 React Native 應用程式</h2>

<h3>步驟 1：啟動 Metro</h3>

首先，你需要啟動 Metro，這是 React Native 內建的 JavaScript 打包工具。Metro「接收一個入口文件和各種選項，並返回一個包含所有代碼及其依賴項的單一 JavaScript 文件。」—[Metro 文檔](https://metrobundler.dev/docs/concepts)

要啟動 Metro，請在你的 React Native 專案文件夾內運行以下命令：

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

> 如果你熟悉網頁開發，Metro 很像 webpack——但用於 React Native 應用程式。與 Kotlin 或 Java 不同，JavaScript 不是編譯的——React Native 也不是。打包與編譯不同，但它可以幫助提高啟動性能，並將一些平台特定的 JavaScript 轉換為更廣泛支持的 JavaScript。

<h3>步驟 2：啟動你的應用程式</h3>

讓 Metro Bundler 在自己的終端中運行。在你的 React Native 專案文件夾內打開一個新的終端，運行以下命令：

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

如果一切設置正確，你應該很快就能看到你的新應用程式在 Android 模擬器中運行。

![AwesomeProject on Android](/docs/assets/GettingStartedAndroidSuccessWindows.png)

這是運行應用程式的一種方式——你也可以直接從 Android Studio 中運行它。

> 如果無法運行，請查看[疑難排解](troubleshooting.md)頁面。

<h3>修改你的應用程式</h3>

現在你已經成功運行了應用程式，讓我們來修改它。

- 在你選擇的文字編輯器中打開 `App.tsx` 並編輯一些行。
- 按兩次 <kbd>R</kbd> 鍵，或從開發者選單 (<kbd>Ctrl</kbd> + <kbd>M</kbd>) 中選擇 `Reload` 來查看你的變更！

<h3>大功告成！</h3>

恭喜！您已成功運行並修改了第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 若想將這段 React Native 程式碼整合至現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。

如果想深入瞭解 React Native，請查閱[React Native 簡介](getting-started)。