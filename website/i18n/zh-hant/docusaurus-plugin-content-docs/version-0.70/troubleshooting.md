---
id: troubleshooting
title: Troubleshooting
---

以下是設置 React Native 時可能遇到的一些常見問題。若您遇到的問題未列於此，請嘗試[在 GitHub 上搜尋相關議題](https://github.com/facebook/react-native/issues/)。

### 連接埠已被佔用

[Metro 打包工具][metro]預設運行於 8081 連接埠。若該連接埠已被其他進程佔用，您可以選擇終止該進程，或變更打包工具使用的連接埠。

#### 終止佔用 8081 連接埠的進程

執行以下指令找出正在監聽 8081 連接埠的進程 ID：

```shell
sudo lsof -i :8081
```

接著執行以下指令終止該進程：

```shell
kill -9 <PID>
```

在 Windows 系統上，您可以使用[資源監視器](https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-port-on-windows)找出佔用 8081 連接埠的進程，並透過工作管理員將其停止。

#### 使用 8081 以外的連接埠

您可透過 `port` 參數設定打包工具使用其他連接埠：

```shell
npx react-native start --port=8088
```

同時需更新應用程式設定，使其從新連接埠載入 JavaScript 套件組合。若在 Xcode 中於裝置上運行，請將 `ios/__App_Name__.xcodeproj/project.pbxproj` 檔案中所有 `8081` 替換為您選擇的連接埠。

### NPM 鎖定錯誤

若使用 React Native CLI 時遇到類似 `npm WARN locking Error: EACCES` 的錯誤，請嘗試執行：

```shell
sudo chown -R $USER ~/.npm
sudo chown -R $USER /usr/local/lib/node_modules
```

### 缺少 React 相關函式庫

若您手動將 React Native 加入專案，請確認已包含所有使用到的相依套件，例如 `RCTText.xcodeproj`、`RCTImage.xcodeproj`。接著，這些相依套件建置出的二進位檔需連結至您的應用程式二進位檔。請於 Xcode 專案設定的 `Linked Frameworks and Binaries` 區段進行設定。更詳細步驟請參閱：[連結函式庫](linking-libraries-ios.md#content)。

若使用 CocoaPods，請確認已在 `Podfile` 中添加 React 及其子規格。例如，若您使用 `<Text />`、`<Image />` 和 `fetch()` API，需在 `Podfile` 中加入：

```
pod 'React', :path => '../node_modules/react-native', :subspecs => [
  'RCTText',
  'RCTImage',
  'RCTNetwork',
  'RCTWebSocket',
]
```

接著請確認已執行 `pod install`，且專案中已生成包含 React 的 `Pods/` 目錄。CocoaPods 會指示您後續需使用生成的 `.xcworkspace` 檔案才能使用這些安裝的相依套件。

#### 作為 CocoaPod 使用時 React Native 無法編譯

可使用名為 [cocoapods-fix-react-native](https://github.com/orta/cocoapods-fix-react-native) 的 CocoaPods 插件，該插件會處理因使用相依套件管理器而導致的原始碼潛在修復問題。

#### 參數列表過長：遞迴標頭擴展失敗

在專案的建置設定中，`User Search Header Paths` 和 `Header Search Paths` 是兩個指定 Xcode 應在何處尋找程式碼中 `#import` 標頭檔的配置。對於 Pods，CocoaPods 使用預設的特定資料夾陣列進行搜尋。請確認此配置未被覆寫，且設定的資料夾皆未過大。若其中一個資料夾過大，Xcode 將嘗試遞迴搜尋整個目錄，並最終拋出上述錯誤。

要將 `User Search Header Paths` 和 `Header Search Paths` 建置設定恢復為 CocoaPods 設定的預設值，請在建置設定面板中選取該項目並按下刪除鍵。這將移除自訂覆寫並恢復為 CocoaPod 預設值。

### 無可用傳輸方式

React Native implements a polyfill for WebSockets. These [polyfills](https://github.com/facebook/react-native/blob/0.70-stable/Libraries/Core/InitializeCore.js) are initialized as part of the react-native module that you include in your application through `import React from 'react'`. If you load another module that requires WebSockets, such as [Firebase](https://github.com/facebook/react-native/issues/3645), be sure to load/require it after react-native:

```
import React from 'react';
import Firebase from 'firebase';
```

## Shell Command Unresponsive Exception

If you encounter a ShellCommandUnresponsiveException exception such as:

```
Execution failed for task ':app:installDebug'.
  com.android.builder.testing.api.DeviceException: com.android.ddmlib.ShellCommandUnresponsiveException
```

Try [downgrading your Gradle version to 1.2.3](https://github.com/facebook/react-native/issues/2720) in `android/build.gradle`.

## react-native init hangs

If you run into issues where running `npx react-native init` hangs in your system, try running it again in verbose mode and referring to [#2797](https://github.com/facebook/react-native/issues/2797) for common causes:

```shell
npx react-native init --verbose
```

When you're debugging a process or need to know a little more about the error being thrown, you may want to use the verbose option to output more logs and information to nail down your issue.

Run the following command in your root directory.

```shell
npx react-native run-android --verbose
```

## Unable to start react-native package manager (on Linux)

### Case 1: Error "code":"ENOSPC","errno":"ENOSPC"

Issue caused by the number of directories [inotify](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers) (used by watchman on Linux) can monitor. To solve it, run this command in your terminal window

```shell
echo fs.inotify.max_user_watches=582222 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

### Error: spawnSync ./gradlew EACCES

If you run into issue where executing `npm run android` on macOS throws the above error, try to run `sudo chmod +x android/gradlew` command to make `gradlew` files into executable.

[metro]: https://metrobundler.dev/