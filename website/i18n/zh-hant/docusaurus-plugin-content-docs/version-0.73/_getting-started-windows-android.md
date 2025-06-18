import RemoveGlobalCLI from './\_remove-global-cli.md';
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<h2>安裝依賴套件</h2>

您需要安裝 Node、React Native 命令行界面、JDK 和 Android Studio。

雖然您可以選擇任何編輯器來開發應用程式，但必須安裝 Android Studio 才能設置建置 Android 版 React Native 應用程式所需的工具。

<h3 id="jdk">Node 與 JDK</h3>

我們建議透過 Windows 熱門的套件管理工具 [Chocolatey](https://chocolatey.org/install) 來安裝 Node。

建議使用 Node 的 LTS 版本。若需切換不同版本，可考慮安裝 Windows 版 Node 版本管理工具 [nvm-windows](https://github.com/coreybutler/nvm-windows)。

React Native 還需要 [Java SE 開發套件 (JDK)](https://openjdk.java.net/projects/jdk/17/)，同樣可透過 Chocolatey 安裝。

以系統管理員身分開啟命令提示字元 (對命令提示字元按右鍵選擇「以系統管理員身分執行」)，然後執行以下指令：

```powershell
choco install -y nodejs-lts microsoft-openjdk17
```

若系統已安裝 Node，請確認版本為 18 或更新。若已安裝 JDK，建議使用 JDK17。使用更高版本的 JDK 可能會遇到問題。

> 您可以在 [Node 下載頁面](https://nodejs.org/en/download/) 找到其他安裝選項。

> 若使用最新版 Java 開發套件，需變更專案的 Gradle 版本以識別 JDK。請至 `{專案根目錄}\android\gradle\wrapper\gradle-wrapper.properties` 修改 `distributionUrl` 值來升級 Gradle 版本。可參考 [Gradle 最新版本發布](https://gradle.org/releases/)。

<h3>Android 開發環境</h3>

若您不熟悉 Android 開發，設置開發環境可能較為繁瑣。若已熟悉 Android 開發，則只需進行部分配置。無論如何，請仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請確保勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`
- 若未使用 Hyper-V：`Performance (Intel ® HAXM)` ([AMD 或 Hyper-V 參見此處](https://android-developers.googleblog.com/2018/07/android-emulator-amd-processor-hyper-v.html))

接著點擊「Next」安裝所有元件。

> 若選項呈灰色不可選，後續仍可安裝這些元件。

安裝完成並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生碼建置 React Native 應用程式需要特定的 `Android 14 (UpsideDownCake)` SDK。可透過 Android Studio 的 SDK 管理員安裝其他 Android SDK。

開啟 Android Studio，點擊「More Actions」按鈕並選擇「SDK Manager」。

![Android Studio 歡迎畫面](/docs/assets/GettingStartedAndroidStudioWelcomeWindows.png)

> SDK 管理員也可在 Android Studio 的「Settings」對話框中找到，路徑為 **Languages & Frameworks** → **Android SDK**。

在 SDK 管理器中選擇「SDK Platforms」標籤頁，然後勾選右下角的「顯示套件詳細資訊」。找到並展開 `Android 14 (UpsideDownCake)` 項目，確保以下項目已被勾選：

- `Android SDK Platform 34`
- `Intel x86 Atom_64 系統映像檔` 或 `Google APIs Intel x86 Atom 系統映像檔`

接著選擇「SDK Tools」標籤頁，同樣勾選「顯示套件詳細資訊」。找到並展開 `Android SDK Build-Tools` 項目，確保已選取 `34.0.0` 版本。

最後點擊「Apply」以下載並安裝 Android SDK 及相關建置工具。

<h4>3. 設定 ANDROID_HOME 環境變數</h4>

React Native 工具需要設定某些環境變數才能建置包含原生代碼的應用程式。

1. 開啟 **Windows 控制台**
2. 點擊 **使用者帳戶**，然後再次點擊 **使用者帳戶**
3. 點擊 **變更我的環境變數**
4. 點擊 **新增...** 建立一個新的 `ANDROID_HOME` 使用者變數，指向您的 Android SDK 路徑：

![ANDROID_HOME 環境變數](/docs/assets/GettingStartedAndroidEnvironmentVariableANDROID_HOME.png)

SDK 預設安裝在以下位置：

```powershell
%LOCALAPPDATA%\Android\Sdk
```

您可以在 Android Studio 的「設定」對話框中找到 SDK 的實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

開啟新的命令提示字元視窗，以確保新設定的環境變數已載入，再繼續下一步。

1. 開啟 powershell
2. 複製並貼上 **Get-ChildItem -Path Env:\\** 到 powershell
3. 確認 `ANDROID_HOME` 已成功新增

<h4>4. 將 platform-tools 加入 Path</h4>

1. 開啟 **Windows 控制台**
2. 點擊 **使用者帳戶**，然後再次點擊 **使用者帳戶**
3. 點擊 **變更我的環境變數**
4. 選取 **Path** 變數
5. 點擊 **編輯**
6. 點擊 **新增** 並將 platform-tools 的路徑加入清單

此資料夾的預設位置為：

```powershell
%LOCALAPPDATA%\Android\Sdk\platform-tools
```

<h3>React Native 命令列介面</h3>

React Native 內建命令列介面。我們建議您使用 Node.js 自帶的 `npx` 在執行時存取當前版本，而非全域安裝和管理特定版本的 CLI。透過 `npx react-native <command>` 執行指令時，將會下載並執行當前穩定版的 CLI。

<h2>建立新應用程式</h2>

<RemoveGlobalCLI />

React Native 內建命令列介面，可用於生成新專案。您無需全域安裝任何套件，使用 Node.js 自帶的 `npx` 即可存取此功能。讓我們建立一個名為「AwesomeProject」的新 React Native 專案：

```shell
npx react-native@latest init AwesomeProject
```

如果您是將 React Native 整合至現有應用程式、專案中已安裝 [Expo](https://docs.expo.dev/bare/installing-expo-modules/)，或是為現有 React Native 專案新增 Android 支援（參見 [與現有應用程式整合](integration-with-existing-apps.md)），則無需執行此步驟。您也可以使用第三方 CLI（如 [Ignite CLI](https://github.com/infinitered/ignite)）來初始化 React Native 應用程式。

<h3>[選用] 使用特定版本或模板</h3>

若想使用特定 React Native 版本建立新專案，可使用 `--version` 參數：

```shell
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

你也可以使用 `--template` 參數來啟動一個自定義 React Native 模板的專案。

<h2>準備 Android 裝置</h2>

你需要一個 Android 裝置來運行你的 React Native Android 應用程式。這可以是實體的 Android 裝置，或者更常見的是使用 Android 虛擬裝置（AVD），它允許你在電腦上模擬 Android 裝置。

無論哪種方式，你都需要準備裝置以運行開發用的 Android 應用程式。

<h3>使用實體裝置</h3>

如果你有實體的 Android 裝置，你可以透過 USB 線將其連接到電腦，並按照[這裡](running-on-device.md)的指示來代替 AVD 進行開發。

<h3>使用虛擬裝置</h3>

如果你使用 Android Studio 打開 `./AwesomeProject/android`，你可以透過 Android Studio 中的「AVD Manager」查看可用的 Android 虛擬裝置（AVDs）列表。尋找一個看起來像這樣的圖標：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

如果你最近安裝了 Android Studio，你可能需要[創建一個新的 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」，然後從列表中選擇任何手機並點擊「Next」，接著選擇 **UpsideDownCake** API Level 34 映像。

> 如果你沒有安裝 HAXM，點擊「Install HAXM」或按照[這些指示](https://github.com/intel/haxm/wiki/Installation-Instructions-on-Windows)來設置，然後返回 AVD Manager。

點擊「Next」然後「Finish」來創建你的 AVD。此時，你應該可以點擊 AVD 旁邊的綠色三角形按鈕來啟動它，然後繼續下一步。

<h2>運行你的 React Native 應用程式</h2>

<h3>步驟 1：啟動 Metro</h3>

[**Metro**](https://facebook.github.io/metro/) 是 React Native 的 JavaScript 構建工具。要啟動 Metro 開發伺服器，請從你的專案文件夾運行以下命令：

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
如果你熟悉網頁開發，Metro 類似於 Vite 和 webpack 等打包工具，但它是專為 React Native 設計的。例如，Metro 使用 [Babel](https://babel.dev/) 來將 JSX 等語法轉換為可執行的 JavaScript。
:::

<h3>步驟 2：啟動你的應用程式</h3>

讓 Metro Bundler 在自己的終端中運行。在你的 React Native 專案文件夾中打開一個新的終端。運行以下命令：

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

> 如果無法運行，請參閱[疑難排解](troubleshooting.md)頁面。

<h3>修改你的應用程式</h3>

現在你已經成功運行了應用程式，讓我們來修改它。

- 在你選擇的文字編輯器中打開 `App.tsx` 並編輯一些行。
- 按兩下 <kbd>R</kbd> 鍵或從開發者選單（<kbd>Ctrl</kbd> + <kbd>M</kbd>）中選擇 `Reload` 來查看你的變更！

<h3>就是這樣！</h3>

恭喜！你已經成功運行並修改了你的第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 如果想將這段新的 React Native 程式碼整合到現有應用程式中，請參閱[整合指南](integration-with-existing-apps.md)。

如果想深入瞭解 React Native，請查看[React Native 簡介](getting-started)。