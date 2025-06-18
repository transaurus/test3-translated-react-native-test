## 安裝依賴套件

您需要安裝 Node、React Native 命令行工具、JDK 和 Android Studio。

雖然您可以使用任何編輯器開發應用程式，但必須安裝 Android Studio 才能設置建置 React Native Android 應用所需的工具鏈。

<h3>Node</h3>

請根據 [Linux 發行版的安裝說明](https://nodejs.org/en/download/package-manager/) 安裝 Node 18.18 或更新版本。

<h3>Java 開發套件 (JDK)</h3>

React Native 目前推薦使用 Java SE 開發套件 (JDK) 第 17 版。使用更高版本可能會遇到問題。您可以從 [AdoptOpenJDK](https://adoptopenjdk.net/) 下載安裝 [OpenJDK](https://openjdk.java.net)，或透過系統套件管理器安裝。

<h3>Android 開發環境</h3>

如果您是 Android 開發新手，設置開發環境可能會有些繁瑣。若您已有經驗，則只需進行部分配置。無論哪種情況，請務必仔細遵循後續步驟。

<h4 id="android-studio">1. 安裝 Android Studio</h4>

[下載並安裝 Android Studio](https://developer.android.com/studio/index.html)。在安裝精靈中，請確保勾選以下所有項目：

- `Android SDK`
- `Android SDK Platform`
- `Android Virtual Device`

接著點擊「Next」安裝這些元件。

> 若選項呈灰色不可勾選，您稍後仍有機會安裝這些元件。

完成安裝並看到歡迎畫面後，請繼續下一步。

<h4 id="android-sdk">2. 安裝 Android SDK</h4>

Android Studio 預設會安裝最新版 Android SDK。但使用原生程式碼建置 React Native 應用需要特定的 `Android 14 (UpsideDownCake)` SDK。可透過 Android Studio 的 SDK 管理器安裝其他版本。

開啟 Android Studio 後，點擊「Configure」按鈕並選擇「SDK Manager」。

> SDK 管理器也可在 Android Studio 的「Settings」對話框中找到，路徑為 **Languages & Frameworks** → **Android SDK**。

在 SDK 管理器中選擇「SDK Platforms」標籤頁，然後勾選右下角的「Show Package Details」。找到並展開 `Android 14 (UpsideDownCake)` 項目，確保勾選以下內容：

- `Android SDK Platform 34`
- `Intel x86 Atom_64 System Image` 或 `Google APIs Intel x86 Atom System Image`

接著切換到「SDK Tools」標籤頁，同樣勾選「Show Package Details」。找到並展開「Android SDK Build-Tools」，確保選中 `34.0.0` 版本。

最後點擊「Apply」下載並安裝 Android SDK 及相關建置工具。

<h4>3. 配置 ANDROID_HOME 環境變數</h4>

React Native 工具需要設置某些環境變數才能建置含原生程式碼的應用。

將以下內容加入您的 `$HOME/.bash_profile` 或 `$HOME/.bashrc` 設定檔（若使用 `zsh` 則為 `~/.zprofile` 或 `~/.zshrc`）：

```shell
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

> `.bash_profile` 是 `bash` 專用的。若使用其他 shell，您需要編輯對應的 shell 設定檔。

對於使用 `bash` 的使用者，請輸入 `source $HOME/.bash_profile`；若使用 `zsh` 則輸入 `source $HOME/.zprofile` 以載入設定檔至當前 shell。可透過執行 `echo $ANDROID_HOME` 確認 ANDROID_HOME 是否已設定，並執行 `echo $PATH` 檢查正確目錄是否已加入路徑。

> 請務必使用正確的 Android SDK 路徑。您可以在 Android Studio 的「Settings」對話框中找到 SDK 的實際位置，路徑為 **Languages & Frameworks** → **Android SDK**。

<h3>Watchman</h3>

請遵循 [Watchman 安裝指南](https://facebook.github.io/watchman/docs/install#buildinstall) 從原始碼編譯並安裝 Watchman。

> [Watchman](https://facebook.github.io/watchman/docs/install) 是 Facebook 開發的檔案系統監控工具。強烈建議安裝以提升效能，並增加特定邊緣案例的相容性（譯註：不安裝可能也能運作，但效果因環境而異；現在安裝可避免後續潛在問題）。

<h2>準備 Android 裝置</h2>

您需要一個 Android 裝置來執行 React Native Android 應用程式。這可以是實體 Android 裝置，或更常見的是使用 Android 虛擬裝置 (AVD) 在電腦上模擬 Android 裝置。

無論採用哪種方式，您都需要準備裝置以執行開發用的 Android 應用程式。

<h3>使用實體裝置</h3>

若您擁有實體 Android 裝置，可透過 USB 連接線將其連接至電腦，並按照[此處說明](running-on-device.md)設定，即可用於開發以取代 AVD。

<h3>使用虛擬裝置</h3>

若透過 Android Studio 開啟 `./AwesomeProject/android`，您可以在 Android Studio 中開啟「AVD Manager」查看可用的 Android 虛擬裝置 (AVDs) 清單。尋找如下圖示的按鈕：

<img src="/docs/assets/GettingStartedAndroidStudioAVD.svg" alt="Android Studio AVD Manager" width="100"/>

若您最近才安裝 Android Studio，可能需要[建立新的 AVD](https://developer.android.com/studio/run/managing-avds.html)。選擇「Create Virtual Device...」，然後從清單中任選一款手機並點擊「Next」，接著選擇 **UpsideDownCake** API Level 34 映像檔。

> 建議在系統中設定 [VM 加速](https://developer.android.com/studio/run/emulator-acceleration.html#vm-linux)以提升效能。完成設定後，請返回 AVD Manager。

點擊「Next」然後「Finish」建立您的 AVD。此時您應該能點擊 AVD 旁的綠色三角形按鈕來啟動它。

<h3>大功告成！</h3>

恭喜！您已成功設定開發環境。

<center><img src="/docs/assets/GettingStartedCongratulations.png" width="150"></img></center>

<h2>接下來呢？</h2>

- 若要將此 React Native 程式碼加入現有應用程式，請參閱[整合指南](integration-with-existing-apps.md)。
- 若想進一步了解 React Native，請查看[React Native 簡介](getting-started)。