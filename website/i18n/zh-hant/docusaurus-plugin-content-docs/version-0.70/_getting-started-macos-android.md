import RemoveGlobalCLI from './\_remove-global-cli.md';

## 安裝依賴套件

您需要安裝 Node、Watchman、React Native 命令行工具、JDK 以及 Android Studio。

雖然您可以選擇任何編輯器來開發應用程式，但必須安裝 Android Studio 才能設置建置 React Native Android 應用所需的工具鏈。

<h3>Node 與 Watchman</h3>

建議使用 [Homebrew](http://brew.sh/) 安裝 Node 和 Watchman。安裝 Homebrew 後，在終端機執行以下命令：

```shell
brew install node
brew install watchman
```

若系統已安裝 Node，請確認版本為 14 或更新。

[Watchman](https://facebook.github.io/watchman) 是 Facebook 開發的檔案系統監控工具，強烈建議安裝以提升效能。

<h3>Java 開發套件 (JDK)</h3>

建議使用 [Homebrew](http://brew.sh/) 安裝 Azul **Zulu** 發行版的 OpenJDK。安裝 Homebrew 後，在終端機執行以下命令：

```shell
brew install --cask zulu@11

# Get path to where cask was installed to double-click installer
brew info --cask zulu@11
```

安裝 JDK 後，請更新 `JAVA_HOME` 環境變數。若依上述步驟安裝，JDK 路徑通常為 `/Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home`

Zulu OpenJDK 發行版提供 **Intel 和 M1 晶片 Mac 專用 JDK**，可確保 M1 Mac 的建置速度優於 Intel 版 JDK。

若系統已安裝 JDK，建議使用 JDK 11 版本。使用更高版本可能會遇到相容性問題。

<h3>Android 開發環境</h3>

若您是 Android 開發新手，設置開發環境可能較為繁瑣；若已有經驗，則需注意部分配置。無論如何，請仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

點擊「Next」安裝這些元件。

> 若選項呈灰色不可勾選，後續仍可安裝這些元件。

完成安裝並顯示歡迎畫面後，繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生碼建置 React Native 應用需特定安裝 `Android 12 (S)` SDK，可透過 Android Studio 的 SDK 管理器追加安裝。

開啟 Android Studio 後，點擊「More Actions」按鈕並選擇「SDK Manager」。

![Android Studio 歡迎畫面](/docs/assets/GettingStartedAndroidStudioWelcomeMacOS.png)

> 也可在 Android Studio 的「Settings」對話框中，透過 **Languages & Frameworks** → **Android SDK** 路徑找到 SDK 管理器。

在 SDK 管理器中選擇「SDK Platforms」標籤頁，勾選右下角「Show Package Details」。找到並展開 `Android 12 (S)` 項目，確認以下元件已勾選：

- `Android SDK Platform 31`
- `Intel x86 Atom_64 System Image` or `Google APIs Intel x86 Atom System Image` or (for Apple M1 Silicon) `Google APIs ARM 64 v8a System Image`

Next, select the "SDK Tools" tab and check the box next to "Show Package Details" here as well. Look for and expand the "Android SDK Build-Tools" entry, then make sure that `31.0.0` is selected.

Finally, click "Apply" to download and install the Android SDK and related build tools.

<h4>3. Configure the ANDROID_SDK_ROOT environment variable</h4>

The React Native tools require some environment variables to be set up in order to build apps with native code.

Add the following lines to your `$HOME/.bash_profile` or `$HOME/.bashrc` (if you are using `zsh` then `~/.zprofile` or `~/.zshrc`) config file:

```shell
export ANDROID_SDK_ROOT=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_SDK_ROOT/emulator
export PATH=$PATH:$ANDROID_SDK_ROOT/platform-tools
```

> `.bash_profile` is specific to `bash`. If you're using another shell, you will need to edit the appropriate shell-specific config file.

Type `source $HOME/.bash_profile` for `bash` or `source $HOME/.zprofile` to load the config into your current shell. Verify that ANDROID_SDK_ROOT has been set by running `echo $ANDROID_SDK_ROOT` and the appropriate directories have been added to your path by running `echo $PATH`.

> Please make sure you use the correct Android SDK path. You can find the actual location of the SDK in the Android Studio "Settings" dialog, under **Languages & Frameworks** → **Android SDK**.

<h3>React Native Command Line Interface</h3>

React Native has a built-in command line interface. Rather than install and manage a specific version of the CLI globally, we recommend you access the current version at runtime using `npx`, which ships with Node.js. With `npx react-native <command>`, the current stable version of the CLI will be downloaded and executed at the time the command is run.

<h2>Creating a new application</h2>

<RemoveGlobalCLI />

React Native has a built-in command line interface, which you can use to generate a new project. You can access it without installing anything globally using `npx`, which ships with Node.js. Let's create a new React Native project called "AwesomeProject":

```shell
npx react-native init AwesomeProject
```

This is not necessary if you are integrating React Native into an existing application, or if you've installed [Expo](https://docs.expo.dev/bare/installing-expo-modules/) in your project, or if you're adding Android support to an existing React Native project (see [Integration with Existing Apps](integration-with-existing-apps.md)). You can also use a third-party CLI to init your React Native app, such as [Ignite CLI](https://github.com/infinitered/ignite).

<h3>[Optional] Using a specific version or template</h3>

If you want to start a new project with a specific React Native version, you can use the `--version` argument:

```shell
npx react-native init AwesomeProject --version X.XX.X
```

You can also start a project with a custom React Native template, like TypeScript, with `--template` argument:

```shell
npx react-native init AwesomeTSProject --template react-native-template-typescript
```

<h2>Preparing the Android device</h2>

You will need an Android device to run your React Native Android app. This can be either a physical Android device, or more commonly, you can use an Android Virtual Device which allows you to emulate an Android device on your computer.

Either way, you will need to prepare the device to run Android apps for development.

<h3>Using a physical device</h3>

若您擁有實體 Android 裝置，可透過 USB 連接電腦後，依照[此處說明](running-on-device.md)將其用於開發以替代 AVD。

<h3>使用虛擬裝置</h3>

若使用 Android Studio 開啟 `./AwesomeProject/android` 資料夾，可透過 Android Studio 內的「AVD Manager」查看可用 Android 虛擬裝置 (AVD) 清單。尋找如下圖示的按鈕：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

若您剛安裝 Android Studio，可能需要[建立新 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」後，從清單中任選一款手機，點擊「Next」，接著選擇 **S** API Level 31 系統映像。

點擊「Next」後選擇「Finish」以建立 AVD。此時您應能點擊 AVD 旁的綠色三角形按鈕啟動模擬器，並繼續下一步。

<h2>執行您的 React Native 應用程式</h2>

<h3>步驟 1：啟動 Metro</h3>

首先需啟動 Metro——React Native 內建的 JavaScript 打包工具。Metro「接收入口檔案與各項參數，回傳包含所有程式碼與依賴項的單一 JavaScript 檔案」——[Metro 文件](https://metrobundler.dev/docs/concepts)

在 React Native 專案資料夾內執行 `npx react-native start` 以啟動 Metro：

```shell
npx react-native start
```

`react-native start` 指令會啟動 Metro 打包工具。

> 若使用 Yarn 套件管理器，在現有專案中執行 React Native 指令時可用 `yarn` 替代 `npx`。

> 熟悉網頁開發者會發現 Metro 類似 webpack——但用於 React Native 應用。與 Kotlin 或 Java 不同，JavaScript 不需編譯——React Native 亦是如此。打包雖不等同編譯，但能提升啟動效能並將部分平台專用 JavaScript 轉換為更通用的 JavaScript。

<h3>步驟 2：啟動應用程式</h3>

讓 Metro 打包工具在獨立終端機中運行。另開新終端機進入 React Native 專案資料夾，執行：

```shell
npx react-native run-android
```

若一切設定正確，您將很快在 Android 模擬器中看到新應用程式運行。

![Android 上的 AwesomeProject](/docs/assets/GettingStartedAndroidSuccessMacOS.png)

`npx react-native run-android` 是執行應用程式的方式之一——您亦可直接透過 Android Studio 運行。

> 若無法順利執行，請參閱[疑難排解](troubleshooting.md)頁面。

<h3>修改應用程式</h3>

成功運行應用程式後，現在來修改它。

- 用文字編輯器開啟 `App.js` 並編輯部分程式碼。
- 快速按兩次 `R` 鍵或從開發者選單 (`⌘M`) 選擇「Reload」即可查看變更！

<h3>大功告成！</h3>

恭喜！您已成功運行並修改了第一個 React Native 應用程式。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來？</h2>

- 若想將此 React Native 程式碼整合至現有應用，請參閱[整合指南](integration-with-existing-apps.md)。

若想深入瞭解 React Native，請查看 [React Native 簡介](getting-started)。