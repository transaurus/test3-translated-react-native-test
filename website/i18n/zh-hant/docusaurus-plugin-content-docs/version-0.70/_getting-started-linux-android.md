import RemoveGlobalCLI from './\_remove-global-cli.md';

## 安裝依賴項

您需要安裝 Node、React Native 命令行界面、JDK 和 Android Studio。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Android Studio 才能設置構建 React Native Android 應用所需的工具。

<h3>Node</h3>

請根據 [Linux 發行版的安裝說明](https://nodejs.org/en/download/package-manager/) 安裝 Node 14 或更新版本。

<h3>Java 開發套件</h3>

React Native 目前推薦使用 Java SE 開發套件 (JDK) 第 11 版。使用更高版本的 JDK 可能會遇到問題。您可以從 [AdoptOpenJDK](https://adoptopenjdk.net/) 下載並安裝 [OpenJDK](http://openjdk.java.net)，或透過系統套件管理器安裝。

<h3>Android 開發環境</h3>

如果您是 Android 開發新手，設置開發環境可能會有些繁瑣。如果您已經熟悉 Android 開發，則可能需要進行一些配置。無論哪種情況，請務必仔細遵循接下來的幾個步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在 Android Studio 安裝精靈中，請確保勾選以下所有項目旁邊的方框：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

然後，點擊「Next」安裝所有這些組件。

> 如果複選框呈灰色顯示，您稍後仍有機會安裝這些組件。

安裝完成並顯示歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新的 Android SDK。然而，使用原生代碼構建 React Native 應用需要特定的 `Android 12 (S)` SDK。可以透過 Android Studio 的 SDK 管理器安裝其他 Android SDK。

為此，請打開 Android Studio，點擊「Configure」按鈕並選擇「SDK Manager」。

> SDK 管理器也可以在 Android Studio 的「Settings」對話框中找到，位於 **Languages & Frameworks** → **Android SDK**。

在 SDK 管理器中選擇「SDK Platforms」標籤，然後勾選右下角的「Show Package Details」。找到並展開 `Android 12 (S)` 條目，確保勾選以下項目：

- `Android SDK Platform 31`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著，選擇「SDK Tools」標籤並同樣勾選「Show Package Details」。找到並展開「Android SDK Build-Tools」條目，確保選擇 `31.0.0`。

最後，點擊「Apply」下載並安裝 Android SDK 及相關構建工具。

<h4>3. 配置 ANDROID_SDK_ROOT 環境變數</h4>

React Native 工具需要設置一些環境變數才能構建包含原生代碼的應用。

將以下行添加到您的 `$HOME/.bash_profile` 或 `$HOME/.bashrc` 配置文件中（如果您使用 `zsh`，則為 `~/.zprofile` 或 `~/.zshrc`）：

```shell
export ANDROID_SDK_ROOT=$HOME/Library/Android/Sdk
export PATH=$PATH:$ANDROID_SDK_ROOT/emulator
export PATH=$PATH:$ANDROID_SDK_ROOT/platform-tools
```

> `.bash_profile` 是 `bash` 專用的。如果您使用其他 shell，則需要編輯相應的 shell 專用配置文件。

Type `source $HOME/.bash_profile` for `bash` or `source $HOME/.zprofile` to load the config into your current shell. Verify that ANDROID_SDK_ROOT has been set by running `echo $ANDROID_SDK_ROOT` and the appropriate directories have been added to your path by running `echo $PATH`.

> Please make sure you use the correct Android SDK path. You can find the actual location of the SDK in the Android Studio "Settings" dialog, under **Languages & Frameworks** → **Android SDK**.

<h3>Watchman</h3>

Follow the [Watchman installation guide](https://facebook.github.io/watchman/docs/install#buildinstall) to compile and install Watchman from source.

> [Watchman](https://facebook.github.io/watchman/docs/install) is a tool by Facebook for watching changes in the filesystem. It is highly recommended you install it for better performance and increased compatibility in certain edge cases (translation: you may be able to get by without installing this, but your mileage may vary; installing this now may save you from a headache later).

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

If you have a physical Android device, you can use it for development in place of an AVD by plugging it in to your computer using a USB cable and following the instructions [here](running-on-device.md).

<h3>Using a virtual device</h3>

If you use Android Studio to open `./AwesomeProject/android`, you can see the list of available Android Virtual Devices (AVDs) by opening the "AVD Manager" from within Android Studio. Look for an icon that looks like this:

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

If you have recently installed Android Studio, you will likely need to [create a new AVD](https://developer.android.com/studio/run/managing-avds.html). Select "Create Virtual Device...", then pick any Phone from the list and click "Next", then select the **S** API Level 31 image.

> We recommend configuring [VM acceleration](https://developer.android.com/studio/run/emulator-acceleration.html#vm-linux) on your system to improve performance. Once you've followed those instructions, go back to the AVD Manager.

Click "Next" then "Finish" to create your AVD. At this point you should be able to click on the green triangle button next to your AVD to launch it, then proceed to the next step.

<h2>Running your React Native application</h2>

<h3>Step 1: Start Metro</h3>

First, you will need to start Metro, the JavaScript bundler that ships with React Native. Metro "takes in an entry file and various options, and returns a single JavaScript file that includes all your code and its dependencies."—[Metro Docs](https://metrobundler.dev/docs/concepts)

To start Metro, run `npx react-native start` inside your React Native project folder:

```shell
npx react-native start
```

`react-native start` starts Metro Bundler.

> If you use the Yarn package manager, you can use `yarn` instead of `npx` when running React Native commands inside an existing project.

> If you're familiar with web development, Metro is a lot like webpack—for React Native apps. Unlike Kotlin or Java, JavaScript isn't compiled—and neither is React Native. Bundling isn't the same as compiling, but it can help improve startup performance and translate some platform-specific JavaScript into more widely supported JavaScript.

<h3>Step 2: Start your application</h3>

Let Metro Bundler run in its own terminal. Open a new terminal inside your React Native project folder. Run the following:

```shell
npx react-native run-android
```

If everything is set up correctly, you should see your new app running in your Android emulator shortly.

`npx react-native run-android` is one way to run your app - you can also run it directly from within Android Studio.

> If you can't get this to work, see the [Troubleshooting](troubleshooting.md) page.

<h3>Modifying your app</h3>

Now that you have successfully run the app, let's modify it.

- Open `App.js` in your text editor of choice and edit some lines.
- Press the `R` key twice or select `Reload` from the Developer Menu (`Ctrl + M`) to see your changes!

<h3>That's it!</h3>

Congratulations! You've successfully run and modified your first React Native app.

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>Now what?</h2>

- If you want to add this new React Native code to an existing application, check out the [Integration guide](integration-with-existing-apps.md).

If you're curious to learn more about React Native, check out the [Introduction to React Native](getting-started).