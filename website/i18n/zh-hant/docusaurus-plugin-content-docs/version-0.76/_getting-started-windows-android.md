<h2>Installing dependencies</h2>

You will need Node, the React Native command line interface, a JDK, and Android Studio.

While you can use any editor of your choice to develop your app, you will need to install Android Studio in order to set up the necessary tooling to build your React Native app for Android.

<h3 id="jdk">Node, JDK</h3>

We recommend installing Node via [Chocolatey](https://chocolatey.org/install), a popular package manager for Windows.

It is recommended to use an LTS version of Node. If you want to be able to switch between different versions, you might want to install Node via [nvm-windows](https://github.com/coreybutler/nvm-windows), a Node version manager for Windows.

React Native also requires [Java SE Development Kit (JDK)](https://openjdk.java.net/projects/jdk/17/), which can be installed using Chocolatey as well.

Open an Administrator Command Prompt (right click Command Prompt and select "Run as Administrator"), then run the following command:

```powershell
choco install -y nodejs-lts microsoft-openjdk17
```

If you have already installed Node on your system, make sure it is Node 18 or newer. If you already have a JDK on your system, we recommend JDK17. You may encounter problems using higher JDK versions.

> You can find additional installation options on [Node's Downloads page](https://nodejs.org/en/download/).

> If you're using the latest version of Java Development Kit, you'll need to change the Gradle version of your project so it can recognize the JDK. You can do that by going to `{project root folder}\android\gradle\wrapper\gradle-wrapper.properties` and changing the `distributionUrl` value to upgrade the Gradle version. You can check out [here the latest releases of Gradle](https://gradle.org/releases/).

<h3>Android development environment</h3>

Setting up your development environment can be somewhat tedious if you're new to Android development. If you're already familiar with Android development, there are a few things you may need to configure. In either case, please make sure to carefully follow the next few steps.

<h4 id="android-studio">1. Install Android Studio</h4>

[Download and install Android Studio](https://developer.android.com/studio/index.html). While on Android Studio installation wizard, make sure the boxes next to all of the following items are checked:

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`
- If you are not already using Hyper-V: `Performance (Intel ® HAXM)` ([See here for AMD or Hyper-V](https://android-developers.googleblog.com/2018/07/android-emulator-amd-processor-hyper-v.html))

Then, click "Next" to install all of these components.

> If the checkboxes are grayed out, you will have a chance to install these components later on.

Once setup has finalized and you're presented with the Welcome screen, proceed to the next step.

<h4 id="android-sdk">2. Install the Android SDK</h4>

Android Studio installs the latest Android SDK by default. Building a React Native app with native code, however, requires the `Android 15 (VanillaIceCream)` SDK in particular. Additional Android SDKs can be installed through the SDK Manager in Android Studio.

To do that, open Android Studio, click on "More Actions" button and select "SDK Manager".

![Android Studio Welcome](/docs/assets/GettingStartedAndroidStudioWelcomeWindows.png)

> The SDK Manager can also be found within the Android Studio "Settings" dialog, under **Languages & Frameworks** → **Android SDK**.

在 SDK 管理器中選擇「SDK Platforms」標籤頁，然後勾選右下角的「Show Package Details」方塊。找到並展開 `Android 15 (VanillaIceCream)` 項目，確保以下項目已勾選：

- `Android SDK Platform 35`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著，選擇「SDK Tools」標籤頁，同樣勾選「Show Package Details」。找到並展開 `Android SDK Build-Tools` 項目，確保已選取 `35.0.0` 版本。

最後，點擊「Apply」以下載並安裝 Android SDK 及相關建置工具。

<h4>3. 設定 ANDROID_HOME 環境變數</h4>

React Native 工具需要設定一些環境變數才能建置包含原生代碼的應用程式。

1. 開啟 **Windows 控制台**。
2. 點擊 **使用者帳戶**，然後再次點擊 **使用者帳戶**。
3. 點擊 **變更我的環境變數**。
4. 點擊 **新增...** 以建立一個指向 Android SDK 路徑的新使用者變數 `ANDROID_HOME`：

![ANDROID_HOME 環境變數](/docs/assets/GettingStartedAndroidEnvironmentVariableANDROID_HOME.png)

預設情況下，SDK 會安裝在以下位置：

```powershell
%LOCALAPPDATA%\Android\Sdk
```

您可以在 Android Studio 的「Settings」對話框中找到 SDK 的實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

開啟新的命令提示字元視窗，以確保新的環境變數已載入，再繼續下一步。

1. 開啟 PowerShell
2. 複製並貼上 **Get-ChildItem -Path Env:\\** 到 PowerShell
3. 確認 `ANDROID_HOME` 已成功新增

<h4>4. 將 platform-tools 加入 Path</h4>

1. 開啟 **Windows 控制台**。
2. 點擊 **使用者帳戶**，然後再次點擊 **使用者帳戶**。
3. 點擊 **變更我的環境變數**。
4. 選取 **Path** 變數。
5. 點擊 **編輯**。
6. 點擊 **新增** 並將 platform-tools 的路徑加入清單。

此資料夾的預設位置為：

```powershell
%LOCALAPPDATA%\Android\Sdk\platform-tools
```

<h2>準備 Android 裝置</h2>

您需要一個 Android 裝置來執行 React Native Android 應用程式。這可以是實體的 Android 裝置，或者更常見的是使用 Android 虛擬裝置 (AVD)，讓您在電腦上模擬 Android 裝置。

無論哪種方式，您都需要準備裝置以執行開發用的 Android 應用程式。

<h3>使用實體裝置</h3>

如果您有實體的 Android 裝置，可以透過 USB 線將其連接到電腦，並按照[此處說明](running-on-device.md)來取代 AVD 進行開發。

<h3>使用虛擬裝置</h3>

如果您使用 Android Studio 開啟 `./AwesomeProject/android`，可以透過 Android Studio 內的「AVD Manager」查看可用的 Android 虛擬裝置 (AVD) 清單。尋找類似以下的圖示：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

如果您最近才安裝 Android Studio，可能需要[建立新的 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」，然後從清單中任選一款手機並點擊「Next」，接著選取 **VanillaIceCream** API Level 35 映像檔。

> 若尚未安裝HAXM，請點擊「Install HAXM」或依照[這些指示](https://github.com/intel/haxm/wiki/Installation-Instructions-on-Windows)進行設置，完成後返回AVD管理員。

點擊「Next」後選擇「Finish」即可建立AVD。此時您應能點擊AVD旁的綠色三角按鈕來啟動模擬器。

<h3>大功告成！</h3>

恭喜！您已成功設置開發環境。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 若要將此React Native程式碼整合至現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。
- 若想深入瞭解React Native，請查看[React Native入門指南](getting-started)。