---
id: troubleshooting
title: Troubleshooting
---

以下是設定 React Native 時可能遇到的一些常見問題。若您遇到的問題未列於此，請嘗試[在 GitHub 上搜尋相關議題](https://github.com/facebook/react-native/issues/)。

### 連接埠已被佔用

[Metro 打包工具][metro]預設運行於 8081 連接埠。若該埠已被其他進程佔用，您可以終止該進程，或變更打包工具使用的埠號。

#### 終止佔用 8081 埠的進程

執行以下指令找出監聽 8081 埠的進程 ID：

```shell
sudo lsof -i :8081
```

接著執行以下指令終止該進程：

```shell
kill -9 <PID>
```

在 Windows 系統上，您可透過[資源監視器](https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-port-on-windows)找出佔用 8081 埠的進程，並使用工作管理員將其關閉。

#### 使用 8081 以外的連接埠

您可透過 `port` 參數設定打包工具使用其他埠號：

```shell
npx react-native start --port=8088
```

同時需更新應用程式設定，使其從新埠號載入 JavaScript 套件。若透過 Xcode 在裝置上運行，請將 `ios/__App_Name__.xcodeproj/project.pbxproj` 檔案中所有 `8081` 替換為您選擇的埠號。

### NPM 鎖定錯誤

若使用 React Native CLI 時遇到類似 `npm WARN locking Error: EACCES` 的錯誤，請嘗試執行：

```shell
sudo chown -R $USER ~/.npm
sudo chown -R $USER /usr/local/lib/node_modules
```

### 缺少 React 相關函式庫

若您手動將 React Native 加入專案，請確認已包含所有使用到的相依套件，例如 `RCTText.xcodeproj`、`RCTImage.xcodeproj`。接著需將這些套件編譯的二進位檔連結至您的應用程式。請於 Xcode 專案設定的 `Linked Frameworks and Binaries` 區段進行設定。詳細步驟參見：[連結函式庫](linking-libraries-ios.md#content)。

若使用 CocoaPods，請確認已於 `Podfile` 中添加 React 及相關子規格。例如，若您使用 `<Text />`、`<Image />` 和 `fetch()` API，需在 `Podfile` 中添加：

```
pod 'React', :path => '../node_modules/react-native', :subspecs => [
  'RCTText',
  'RCTImage',
  'RCTNetwork',
  'RCTWebSocket',
]
```

接著請確認已執行 `pod install`，且專案中已建立包含 React 的 `Pods/` 目錄。CocoaPods 會指示您後續需使用生成的 `.xcworkspace` 檔案來載入這些相依套件。

#### 作為 CocoaPod 時 React Native 無法編譯

可使用名為 [cocoapods-fix-react-native](https://github.com/orta/cocoapods-fix-react-native) 的 CocoaPods 外掛，該外掛會處理因使用相依套件管理器而導致的原始碼修正問題。

#### 參數列表過長：遞迴標頭檔擴展失敗

在專案的建置設定中，`User Search Header Paths` 和 `Header Search Paths` 是兩個指定 Xcode 搜尋程式碼中 `#import` 標頭檔路徑的設定。對於 Pods，CocoaPods 會使用預設的特定資料夾路徑。請確認這些設定未被覆寫，且設定的路徑中沒有過大的資料夾。若其中包含過大資料夾，Xcode 會嘗試遞迴搜尋整個目錄，最終拋出上述錯誤。

若要將 `User Search Header Paths` 和 `Header Search Paths` 恢復為 CocoaPods 的預設值，請在建置設定面板中選取該項目並按下刪除鍵。此操作會移除自訂覆寫設定，恢復為 CocoaPods 的預設值。

### 無可用傳輸方式

React Native 為 WebSocket 實現了 polyfill。這些 [polyfill](https://github.com/facebook/react-native/blob/0.71-stable/Libraries/Core/InitializeCore.js) 會隨著你在應用程式中透過 `import React from 'react'` 引入的 react-native 模組一併初始化。如果你載入了其他需要 WebSocket 的模組（例如 [Firebase](https://github.com/facebook/react-native/issues/3645)），請確保在 react-native 之後載入/引入它：

```
import React from 'react';
import Firebase from 'firebase';
```

## Shell 命令無回應異常

如果你遇到 ShellCommandUnresponsiveException 異常，例如：

```
Execution failed for task ':app:installDebug'.
  com.android.builder.testing.api.DeviceException: com.android.ddmlib.ShellCommandUnresponsiveException
```

請嘗試在 `android/build.gradle` 中 [將 Gradle 版本降級至 1.2.3](https://github.com/facebook/react-native/issues/2720)。

## react-native init 卡住

如果你遇到執行 `npx react-native init` 時系統卡住的問題，請嘗試以詳細模式重新執行，並參考 [#2797](https://github.com/facebook/react-native/issues/2797) 了解常見原因：

```shell
npx react-native init --verbose
```

當你正在除錯程序或需要更多關於拋出錯誤的資訊時，可以使用詳細選項來輸出更多日誌和資訊，以鎖定問題所在。

在你的根目錄中執行以下命令。

```shell
npx react-native run-android --verbose
```

## 無法啟動 react-native 套件管理器（在 Linux 上）

### 情況 1：錯誤 "code":"ENOSPC","errno":"ENOSPC"

此問題是由於 [inotify](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers)（Linux 上由 watchman 使用）可監視的目錄數量限制所導致。要解決此問題，請在終端機視窗中執行以下命令：

```shell
echo fs.inotify.max_user_watches=582222 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

### 錯誤：spawnSync ./gradlew EACCES

如果你在 macOS 上執行 `npm run android` 時遇到上述錯誤，請嘗試執行 `sudo chmod +x android/gradlew` 命令，將 `gradlew` 文件設為可執行。

[metro]: https://metrobundler.dev/