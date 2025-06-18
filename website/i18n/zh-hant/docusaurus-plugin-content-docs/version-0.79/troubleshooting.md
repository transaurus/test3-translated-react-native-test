---
id: troubleshooting
title: Troubleshooting
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

以下是設定 React Native 時可能遇到的一些常見問題。若您遇到的問題未列於此，請嘗試[在 GitHub 上搜尋相關議題](https://github.com/facebook/react-native/issues/)。

### 連接埠已被佔用

[Metro 打包工具][metro]預設使用 8081 連接埠。若該埠已被其他程序佔用，您可以選擇終止該程序，或變更打包工具使用的埠號。

#### 終止佔用 8081 埠的程序

執行以下指令找出正在監聽 8081 埠的程序識別碼：

```shell
sudo lsof -i :8081
```

接著執行以下指令終止程序：

```shell
kill -9 <PID>
```

在 Windows 系統上，您可透過[資源監視器](https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-port-on-windows)找出佔用 8081 埠的程序，並使用工作管理員將其關閉。

#### 使用 8081 以外的埠號

您可透過 `port` 參數設定打包工具使用其他埠號，於專案根目錄執行：

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

同時需更新應用程式設定以從新埠號載入 JavaScript 套件。若在 Xcode 中透過實機執行，請將 `ios/__App_Name__.xcodeproj/project.pbxproj` 檔案中所有 `8081` 出現處替換為您選擇的埠號。

### NPM 鎖定錯誤

若使用 React Native CLI 時遇到類似 `npm WARN locking Error: EACCES` 的錯誤，請嘗試執行：

```shell
sudo chown -R $USER ~/.npm
sudo chown -R $USER /usr/local/lib/node_modules
```

### 缺少 React 相關函式庫

若您手動將 React Native 加入專案，請確認已包含所有使用到的相依套件，例如 `RCTText.xcodeproj`、`RCTImage.xcodeproj`。接著需將這些套件編譯出的二進位檔連結至您的應用程式。請於 Xcode 專案設定的 `Linked Frameworks and Binaries` 區段進行設定。更詳細步驟請參閱：[連結函式庫](linking-libraries-ios.md#content)。

若使用 CocoaPods，請確認 Podfile 中已添加 React 及相關子規格。例如若使用 `<Text />`、`<Image />` 和 `fetch()` API，需在 Podfile 中加入：

```
pod 'React', :path => '../node_modules/react-native', :subspecs => [
  'RCTText',
  'RCTImage',
  'RCTNetwork',
  'RCTWebSocket',
]
```

接著請確認已執行 `pod install` 且專案中已生成包含 React 的 `Pods/` 目錄。CocoaPods 會指示您後續需使用生成的 `.xcworkspace` 檔案以啟用這些相依套件。

#### 作為 CocoaPod 使用時 React Native 無法編譯

可使用 [cocoapods-fix-react-native](https://github.com/orta/cocoapods-fix-react-native) 這個 CocoaPods 外掛，該工具會處理因使用相依管理器導致的原始碼差異問題。

#### 參數列表過長：遞迴標頭檔展開失敗

在專案的建置設定中，`User Search Header Paths` 和 `Header Search Paths` 這兩個配置指定了 Xcode 應搜尋程式碼中 `#import` 標頭檔的路徑。對於 Pods，CocoaPods 使用預設的特定資料夾陣列進行搜尋。請確認此配置未被覆寫，且設定的資料夾沒有過大。若某個資料夾過大，Xcode 會嘗試遞迴搜尋整個目錄並最終拋出上述錯誤。

若要將「User Search Header Paths」和「Header Search Paths」的建置設定恢復為 CocoaPods 預設值，請在「Build Settings」面板中選取該項目並按下刪除鍵。這將移除自訂覆寫並恢復為 CocoaPods 的預設值。

### 無可用傳輸方式

React Native 實作了 WebSockets 的 polyfill。這些 [polyfill](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Core/InitializeCore.js) 會隨著你在應用程式中透過 `import React from 'react'` 引入的 react-native 模組一起初始化。如果你載入了其他需要 WebSockets 的模組（例如 [Firebase](https://github.com/facebook/react-native/issues/3645)），請確保在 react-native 之後才載入/引入它：

```
import React from 'react';
import Firebase from 'firebase';
```

## Shell 指令無回應例外

若遇到類似以下的 ShellCommandUnresponsiveException 例外：

```
Execution failed for task ':app:installDebug'.
  com.android.builder.testing.api.DeviceException: com.android.ddmlib.ShellCommandUnresponsiveException
```

請嘗試在 `android/build.gradle` 中 [將 Gradle 版本降級至 1.2.3](https://github.com/facebook/react-native/issues/2720)。

## react-native init 卡住

如果在執行 `npx react-native init` 時卡住，請嘗試以詳細模式重新執行，並參考 [#2797](https://github.com/facebook/react-native/issues/2797) 了解常見原因：

```shell
npx react-native init --verbose
```

當你正在除錯程序或需要更詳細的錯誤資訊時，可以使用詳細選項來輸出更多日誌和資訊以鎖定問題。

在專案的根目錄下執行以下指令：

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

## 無法啟動 react-native 套件管理器（Linux 系統）

### 情況 1：錯誤 "code":"ENOSPC","errno":"ENOSPC"

此問題是由於 [inotify](https://github.com/guard/listen/blob/master/README.md#increasing-the-amount-of-inotify-watchers)（Linux 上由 watchman 使用）可監控的目錄數量不足所致。解決方法是在終端機中執行以下指令：

```shell
echo fs.inotify.max_user_watches=582222 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

### 錯誤：spawnSync ./gradlew EACCES

如果在 macOS 上執行 `npm run android` 或 `yarn android` 時遇到上述錯誤，請嘗試執行 `sudo chmod +x android/gradlew` 指令，將 `gradlew` 檔案設為可執行。

[metro]: https://metrobundler.dev/