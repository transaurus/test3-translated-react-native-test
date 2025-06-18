## 安裝依賴套件

您需要安裝 Node、Watchman、React Native 命令行工具、JDK 以及 Android Studio。

雖然您可以使用任何偏好的編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 React Native Android 應用所需的工具鏈。

<h3>Node 與 Watchman</h3>

建議透過 [Homebrew](https://brew.sh/) 安裝 Node 和 Watchman。安裝 Homebrew 後，在終端機執行以下指令：

```shell
brew install node
brew install watchman
```

若系統已安裝 Node，請確認版本為 18 或更新。

[Watchman](https://facebook.github.io/watchman) 是 Facebook 開發的檔案系統監控工具，強烈建議安裝以提升效能。

<h3>Java 開發套件 (JDK)</h3>

建議使用 [Homebrew](https://brew.sh/) 安裝 Azul **Zulu** 發行版的 OpenJDK。安裝 Homebrew 後，在終端機執行以下指令：

```shell
brew install --cask zulu@17

# Get path to where cask was installed to double-click installer
brew info --cask zulu@17
```

安裝 JDK 後，請在 `~/.zshrc`（或 `~/.bash_profile`）中新增或更新 `JAVA_HOME` 環境變數。

若依上述步驟安裝，JDK 預設路徑通常為 `/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home`：

```shell
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home
```

Zulu OpenJDK 發行版提供 **Intel 和 M1 晶片 Mac 專用版本**，可確保在 M1 Mac 上獲得比 Intel 版 JDK 更快的建置速度。

若系統已安裝 JDK，建議使用 JDK 17 版本，更高版本可能導致相容性問題。

<h3>Android 開發環境</h3>

對於 Android 開發新手而言，環境設置可能較為繁瑣。若已有相關經驗，則僅需調整部分設定。無論如何，請務必仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

接著點擊「Next」安裝這些元件。

> 若選項呈灰色不可選，後續仍可安裝這些元件。

完成安裝並顯示歡迎畫面後，繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK，但使用原生碼建置 React Native 應用需特定安裝 `Android 14 (UpsideDownCake)` SDK。可透過 Android Studio 的 SDK 管理員安裝其他版本。

開啟 Android Studio 後，點擊「More Actions」按鈕並選擇「SDK Manager」。

![Android Studio 歡迎畫面](/docs/assets/GettingStartedAndroidStudioWelcomeMacOS.png)

> 亦可在 Android Studio「設定」對話框的 **Languages & Frameworks** → **Android SDK** 路徑下找到 SDK 管理員。

在 SDK 管理員中切換至「SDK Platforms」分頁，勾選右下角「Show Package Details」。找到並展開 `Android 14 (UpsideDownCake)` 項目，確認以下元件已勾選：

- `Android SDK Platform 34`
- `Intel x86 Atom_64 系統映像` 或 `Google APIs Intel x86 Atom 系統映像` 或 (適用於 Apple M1 晶片) `Google APIs ARM 64 v8a 系統映像`

接著，切換至「SDK Tools」分頁，同樣勾選「顯示套件詳細資訊」。找到並展開「Android SDK Build-Tools」項目，確保已選取 `34.0.0` 版本。

最後點擊「Apply」按鈕以下載並安裝 Android SDK 及相關建置工具。

<h4>3. 設定 ANDROID_HOME 環境變數</h4>

React Native 工具需要設定某些環境變數才能建置包含原生代碼的應用程式。

請將以下內容加入您的 `~/.zprofile` 或 `~/.zshrc` 設定檔（若使用 `bash` 則為 `~/.bash_profile` 或 `~/.bashrc`）：

```shell
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

執行 `source ~/.zprofile`（或 `bash` 用戶執行 `source ~/.bash_profile`）使設定生效。可透過執行 `echo $ANDROID_HOME` 確認變數是否設定成功，並執行 `echo $PATH` 檢查路徑是否正確加入。

> 請務必使用正確的 Android SDK 路徑。實際路徑可在 Android Studio 的「Settings」對話框中查看，路徑位於 **Languages & Frameworks** → **Android SDK**。

<h2>準備 Android 裝置</h2>

您需要一台 Android 裝置來執行 React Native 應用程式。可使用實體 Android 裝置，或更常見的做法是使用 Android 虛擬裝置（AVD）在電腦上模擬 Android 環境。

無論採用哪種方式，都需要先將裝置設定為開發模式。

<h3>使用實體裝置</h3>

若有實體 Android 裝置，可透過 USB 連接電腦後，依照[此處說明](running-on-device.md)設定以取代 AVD 進行開發。

<h3>使用虛擬裝置</h3>

若透過 Android Studio 開啟 `./AwesomeProject/android` 專案，可點擊工具列中的「AVD Manager」圖示（圖示樣式如下）查看可用虛擬裝置清單：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

若剛安裝 Android Studio，可能需要[建立新 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」後，任選手機型號並點擊「Next」，接著選取 **UpsideDownCake** API Level 34 系統映像。

點擊「Next」後選擇「Finish」完成建立。此時可點擊 AVD 旁的綠色三角形按鈕啟動模擬器。

<h3>大功告成！</h3>

恭喜！您已成功設定開發環境。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來？</h2>

- 若要將 React Native 整合至現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。
- 想深入瞭解 React Native？請查看[入門介紹](getting-started)。