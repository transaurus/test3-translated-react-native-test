<h2>安裝依賴套件</h2>

您需要安裝 Node、React Native 命令行工具、JDK 和 Android Studio。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 React Native Android 應用程式所需的工具。

<h3 id="jdk">Node 與 JDK</h3>

我們建議透過 [Chocolatey](https://chocolatey.org/install)（Windows 上流行的套件管理工具）安裝 Node。

建議使用 Node 的 LTS 版本。若需切換不同版本，可透過 [nvm-windows](https://github.com/coreybutler/nvm-windows)（Windows 的 Node 版本管理工具）安裝 Node。

React Native 還需要 [Java SE 開發套件 (JDK)](https://openjdk.java.net/projects/jdk/17/)，同樣可透過 Chocolatey 安裝。

以系統管理員身分開啟命令提示字元（對命令提示字元按右鍵並選擇「以系統管理員身分執行」），然後執行以下命令：

```powershell
choco install -y nodejs-lts microsoft-openjdk17
```

若系統已安裝 Node，請確認版本為 18 或更新。若已安裝 JDK，建議使用 JDK17。使用更高版本的 JDK 可能會遇到問題。

> 您可以在 [Node 下載頁面](https://nodejs.org/en/download/) 找到其他安裝選項。

> 若使用最新版 Java 開發套件，需變更專案的 Gradle 版本以識別 JDK。請至 `{專案根目錄}\android\gradle\wrapper\gradle-wrapper.properties` 修改 `distributionUrl` 值來升級 Gradle 版本。可參考 [Gradle 最新版本發布](https://gradle.org/releases/)。

<h3>Android 開發環境</h3>

若您是 Android 開發新手，設置開發環境可能較繁瑣。若已有經驗，則只需配置部分項目。無論哪種情況，請仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請確保勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`
- 若未使用 Hyper-V：`Performance (Intel ® HAXM)`（[AMD 或 Hyper-V 參見此處](https://android-developers.googleblog.com/2018/07/android-emulator-amd-processor-hyper-v.html)）

接著點擊「Next」安裝所有元件。

> 若選項呈灰色不可選，後續仍可安裝這些元件。

安裝完成並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生碼建置 React Native 應用程式需特定安裝 `Android 14 (UpsideDownCake)` SDK。可透過 Android Studio 的 SDK 管理工具安裝其他版本。

開啟 Android Studio，點擊「More Actions」按鈕並選擇「SDK Manager」。

![Android Studio 歡迎畫面](/docs/assets/GettingStartedAndroidStudioWelcomeWindows.png)

> SDK Manager 也可在 Android Studio 的「Settings」對話框中找到，路徑為 **Languages & Frameworks** → **Android SDK**。

在 SDK 管理器中選擇「SDK Platforms」標籤頁，然後勾選右下角的「Show Package Details」。找到並展開 `Android 14 (UpsideDownCake)` 項目，確保以下項目已勾選：

- `Android SDK Platform 34`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著選擇「SDK Tools」標籤頁，同樣勾選「Show Package Details」。找到並展開 `Android SDK Build-Tools` 項目，確保已選取 `34.0.0` 版本。

最後點擊「Apply」以下載並安裝 Android SDK 及相關建置工具。

<h4>3. 設定 ANDROID_HOME 環境變數</h4>

React Native 工具需要設定某些環境變數才能建置包含原生代碼的應用程式。

1. 開啟 **Windows 控制台**
2. 點擊 **使用者帳戶**，然後再次點擊 **使用者帳戶**
3. 點擊 **變更我的環境變數**
4. 點擊 **新增...** 建立一個指向 Android SDK 路徑的新使用者變數 `ANDROID_HOME`：

![ANDROID_HOME 環境變數](/docs/assets/GettingStartedAndroidEnvironmentVariableANDROID_HOME.png)

SDK 預設安裝於以下位置：

```powershell
%LOCALAPPDATA%\Android\Sdk
```

您可以在 Android Studio 的「Settings」對話框中找到 SDK 的實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

請開啟新的命令提示字元視窗，以確保新環境變數已載入，再繼續下一步。

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

<h2>準備 Android 裝置</h2>

您需要一個 Android 裝置來執行 React Native Android 應用程式。這可以是實體 Android 裝置，或更常見的是使用 Android 虛擬裝置 (AVD)，讓您在電腦上模擬 Android 裝置。

無論採用哪種方式，您都需要準備裝置以執行開發用的 Android 應用程式。

<h3>使用實體裝置</h3>

如果您有實體 Android 裝置，可以使用 USB 線連接電腦，並依照[此處說明](running-on-device.md)設定，即可用於開發取代 AVD。

<h3>使用虛擬裝置</h3>

如果您使用 Android Studio 開啟 `./AwesomeProject/android`，可以透過 Android Studio 內的「AVD Manager」查看可用的 Android 虛擬裝置 (AVD) 清單。尋找類似以下的圖示：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

如果您最近才安裝 Android Studio，可能需要[建立新的 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」，然後從清單中任選一款手機，點擊「Next」，接著選擇 **UpsideDownCake** API Level 34 映像檔。

> 如果尚未安裝HAXM，請點擊「安裝HAXM」或依照[這些指示](https://github.com/intel/haxm/wiki/Installation-Instructions-on-Windows)進行設定，完成後返回AVD管理員。

點擊「下一步」然後「完成」以建立您的AVD。此時您應該可以點擊AVD旁的綠色三角形按鈕來啟動它。

<h3>大功告成！</h3>

恭喜！您已成功設定開發環境。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 若想將此React Native程式碼加入現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。
- 若想深入瞭解React Native，請查看[React Native簡介](getting-started)。