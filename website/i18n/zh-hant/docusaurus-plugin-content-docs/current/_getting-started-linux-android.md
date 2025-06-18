## 安裝依賴套件

您需要安裝 Node、React Native 命令行工具、JDK 和 Android Studio。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 React Native Android 應用所需的工具鏈。

<h3>Node</h3>

請依照 [Linux 發行版的安裝說明](https://nodejs.org/en/download/package-manager/) 安裝 Node 18.18 或更新版本。

<h3>Java 開發套件 (JDK)</h3>

React Native 目前推薦使用 Java SE 開發套件 (JDK) 第 17 版。使用更高版本可能會遇到問題。您可以從 [AdoptOpenJDK](https://adoptopenjdk.net/) 下載安裝 [OpenJDK](https://openjdk.java.net)，或透過系統套件管理器安裝。

<h3>Android 開發環境</h3>

如果您是 Android 開發新手，設置開發環境可能有些繁瑣。若您已熟悉 Android 開發，則只需進行部分配置。無論哪種情況，請務必仔細遵循接下來的步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請確保勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

接著點擊「Next」安裝所有元件。

> 若複選框呈灰色不可選狀態，您稍後仍有機會安裝這些元件。

當安裝完成並顯示歡迎畫面時，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生代碼建置 React Native 應用需要特定的 `Android 15 (VanillaIceCream)` SDK。可透過 Android Studio 的 SDK 管理器安裝其他 Android SDK。

操作方式：開啟 Android Studio，點擊「Configure」按鈕並選擇「SDK Manager」。

> 您也可以在 Android Studio 的「Settings」對話框中找到 SDK 管理器，路徑為 **Languages & Frameworks** → **Android SDK**。

在 SDK 管理器中選擇「SDK Platforms」標籤頁，然後勾選右下角的「Show Package Details」。找到並展開 `Android 15 (VanillaIceCream)` 項目，確保勾選以下內容：

- `Android SDK Platform 35`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著切換至「SDK Tools」標籤頁，同樣勾選「Show Package Details」。找到並展開「Android SDK Build-Tools」項目，確保選中 `35.0.0` 版本。

最後點擊「Apply」下載並安裝 Android SDK 及相關建置工具。

<h4>3. 配置 ANDROID_HOME 環境變數</h4>

React Native 工具鏈需要設置特定環境變數才能建置包含原生代碼的應用。

將以下內容加入您的 `$HOME/.bash_profile` 或 `$HOME/.bashrc` 配置檔案（若使用 `zsh` 則為 `~/.zprofile` 或 `~/.zshrc`）：

```shell
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

> `.bash_profile` 是 `bash` 專用的設定檔。若使用其他 shell，您需要編輯對應的 shell 配置檔案。

Type `source $HOME/.bash_profile` for `bash` or `source $HOME/.zprofile` to load the config into your current shell. Verify that ANDROID_HOME has been set by running `echo $ANDROID_HOME` and the appropriate directories have been added to your path by running `echo $PATH`.

> Please make sure you use the correct Android SDK path. You can find the actual location of the SDK in the Android Studio "Settings" dialog, under **Languages & Frameworks** → **Android SDK**.

<h3>Watchman</h3>

Follow the [Watchman installation guide](https://facebook.github.io/watchman/docs/install#buildinstall) to compile and install Watchman from source.

> [Watchman](https://facebook.github.io/watchman/docs/install) is a tool by Facebook for watching changes in the filesystem. It is highly recommended you install it for better performance and increased compatibility in certain edge cases (translation: you may be able to get by without installing this, but your mileage may vary; installing this now may save you from a headache later).

<h2>Preparing the Android device</h2>

You will need an Android device to run your React Native Android app. This can be either a physical Android device, or more commonly, you can use an Android Virtual Device which allows you to emulate an Android device on your computer.

Either way, you will need to prepare the device to run Android apps for development.

<h3>Using a physical device</h3>

If you have a physical Android device, you can use it for development in place of an AVD by plugging it in to your computer using a USB cable and following the instructions [here](running-on-device.md).

<h3>Using a virtual device</h3>

If you use Android Studio to open `./AwesomeProject/android`, you can see the list of available Android Virtual Devices (AVDs) by opening the "AVD Manager" from within Android Studio. Look for an icon that looks like this:

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

If you have recently installed Android Studio, you will likely need to [create a new AVD](https://developer.android.com/studio/run/managing-avds.html). Select "Create Virtual Device...", then pick any Phone from the list and click "Next", then select the **VanillaIceCream** API Level 35 image.

> We recommend configuring [VM acceleration](https://developer.android.com/studio/run/emulator-acceleration.html#vm-linux) on your system to improve performance. Once you've followed those instructions, go back to the AVD Manager.

Click "Next" then "Finish" to create your AVD. At this point you should be able to click on the green triangle button next to your AVD to launch it.

<h3>That's it!</h3>

Congratulations! You successfully set up your development environment.

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>Now what?</h2>

- If you want to add this new React Native code to an existing application, check out the [Integration guide](integration-with-existing-apps.md).
- If you're curious to learn more about React Native, check out the [Introduction to React Native](getting-started).