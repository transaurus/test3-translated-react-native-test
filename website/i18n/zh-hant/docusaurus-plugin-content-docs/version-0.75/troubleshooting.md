---
id: troubleshooting
title: Troubleshooting
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

以下是設定 React Native 時可能遇到的一些常見問題。若您遇到的問題未列於此，請嘗試[在 GitHub 上搜尋相關議題](https://github.com/facebook/react-native/issues/)。

### 連接埠已被佔用

[Metro 打包工具][metro]預設使用 8081 連接埠。若該連接埠已被其他程序佔用，您可以終止該程序或變更打包工具使用的連接埠。

#### 終止佔用 8081 連接埠的程序

執行以下指令找出正在監聽 8081 連接埠的程序 ID：

```shell
sudo lsof -i :8081
```

接著執行以下指令終止該程序：

```shell
kill -9 <PID>
```

在 Windows 系統上，您可以使用[資源監視器](https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-port-on-windows)找出佔用 8081 連接埠的程序，並透過工作管理員將其關閉。

#### 使用 8081 以外的連接埠

您可以透過 `port` 參數設定打包工具使用其他連接埠，在專案根目錄下執行：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm start -- --port=8088
```

</TabItem>
<TabItem value="yarn">

```shell
yarn start --port 8088
```

</TabItem>
</Tabs>

您還需要更新應用程式以從新連接埠載入 JavaScript 套件。若在 Xcode 中從裝置執行，可透過修改 `ios/__App_Name__.xcodeproj/project.pbxproj` 檔案中所有 `8081` 為您選擇的連接埠來達成。

### NPM 鎖定錯誤

若在使用 React Native CLI 時遇到類似 `npm WARN locking Error: EACCES` 的錯誤，請嘗試執行以下指令：

```shell
sudo chown -R $USER ~/.npm
sudo chown -R $USER /usr/local/lib/node_modules
```

### 缺少 React 相關函式庫

若您手動將 React Native 加入專案，請確認已包含所有使用到的相關依賴項，例如 `RCTText.xcodeproj`、`RCTImage.xcodeproj`。接著，這些依賴項建置出的二進位檔必須連結至您的應用程式二進位檔。請在 Xcode 專案設定的 `Linked Frameworks and Binaries` 區段中進行設定。更詳細步驟請參閱：[連結函式庫](linking-libraries-ios.md#content)。

若使用 CocoaPods，請確認已在 `Podfile` 中加入 React 及其子規格。例如，若您使用 `<Text />`、`<Image />` 和 `fetch()` API，則需在 `Podfile` 中加入：

```
pod 'React', :path => '../node_modules/react-native', :subspecs => [
  'RCTText',
  'RCTImage',
  'RCTNetwork',
  'RCTWebSocket',
]
```

接著，請確認已執行 `pod install` 且專案中已建立包含 React 的 `Pods/` 目錄。CocoaPods 會指示您後續需使用產生的 `.xcworkspace` 檔案才能使用這些安裝的依賴項。

#### 作為 CocoaPod 使用時 React Native 無法編譯

有一個名為 [cocoapods-fix-react-native](https://github.com/orta/cocoapods-fix-react-native) 的 CocoaPods 插件，可處理因使用依賴管理器而導致的原始碼潛在修正問題。

#### 參數列表過長：遞迴標頭檔擴展失敗

在專案的建置設定中，`User Search Header Paths` 和 `Header Search Paths` 是兩個指定 Xcode 應在何處尋找程式碼中 `#import` 標頭檔的設定。對於 Pods，CocoaPods 使用預設的特定資料夾陣列進行搜尋。請確認此設定未被覆寫，且設定的資料夾皆未過大。若其中一個資料夾過大，Xcode 將嘗試遞迴搜尋整個目錄並最終拋出上述錯誤。

要將「User Search Header Paths」和「Header Search Paths」的建置設定恢復為 CocoaPods 的預設值，請在「Build Settings」面板中選擇該項目，然後按下刪除鍵。這將移除自訂覆寫並恢復為 CocoaPod 的預設值。

### 無可用傳輸方式

React Native 實作了 WebSocket 的 polyfill。這些 [polyfill](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Core/InitializeCore.js) 會在你透過 `import React from 'react'` 引入 react-native 模組時初始化。如果你載入了另一個需要 WebSocket 的模組，例如 [Firebase](https://github.com/facebook/react-native/issues/3645)，請確保在 react-native 之後載入/引入它：

```
import React from 'react';
import Firebase from 'firebase';
```

## Shell 命令無回應例外

如果你遇到 ShellCommandUnresponsiveException 例外，例如：

```
Execution failed for task ':app:installDebug'.
  com.android.builder.testing.api.DeviceException: com.android.ddmlib.ShellCommandUnresponsiveException
```

嘗試在 `android/build.gradle` 中 [將 Gradle 版本降級至 1.2.3](https://github.com/facebook/react-native/issues/2720)。

## react-native init 卡住

如果你在執行 `npx react-native init` 時遇到卡住的問題，請嘗試以詳細模式再次執行，並參考 [#2797](https://github.com/facebook/react-native/issues/2797) 了解常見原因：

```shell
npx react-native init --verbose
```

當你在除錯程序或需要更多關於拋出錯誤的資訊時，可以使用詳細選項來輸出更多日誌和資訊，以釐清問題。

在你的專案根目錄下執行以下命令。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android -- --verbose
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android --verbose
```

</TabItem>
</Tabs>

## 無法啟動 react-native 套件管理器（在 Linux 上）

### 情況 1：錯誤 "code":"ENOSPC","errno":"ENOSPC"

此問題是由於 [inotify](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers)（Linux 上由 watchman 使用）可監視的目錄數量限制所導致。要解決此問題，請在終端機視窗中執行以下命令：

```shell
echo fs.inotify.max_user_watches=582222 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

### 錯誤：spawnSync ./gradlew EACCES

如果你在 macOS 上執行 `npm run android` 或 `yarn android` 時遇到上述錯誤，請嘗試執行 `sudo chmod +x android/gradlew` 命令，將 `gradlew` 檔案設為可執行。

[metro]: https://metrobundler.dev/