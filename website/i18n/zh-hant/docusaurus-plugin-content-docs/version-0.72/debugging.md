---
id: debugging
title: Debugging Basics
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 存取開發者選單

React Native 提供了一個應用內建的開發者選單，其中包含多種除錯選項。您可以透過搖動裝置或使用鍵盤快捷鍵來存取開發者選單：

- iOS 模擬器：<kbd>Cmd ⌘</kbd> + <kbd>D</kbd>（或裝置 > 搖動）
- Android 模擬器：<kbd>Cmd ⌘</kbd> + <kbd>M</kbd>（macOS）或 <kbd>Ctrl</kbd> + <kbd>M</kbd>（Windows 和 Linux）

另外，對於 Android 裝置和模擬器，您可以在終端機中執行 `adb shell input keyevent 82`。

![](/docs/assets/DevMenu.png)

:::note
開發者選單在正式（生產）版本中會被停用。
:::

## LogBox

開發版本中的錯誤和警告會顯示在應用內的 LogBox 中。

:::note
LogBox 在正式（生產）版本中會被停用。
:::

### 主控台錯誤與警告

主控台錯誤和警告會顯示為帶有紅色或黃色標記的螢幕通知，分別代表主控台中的錯誤或警告數量。若要查看主控台錯誤或警告，請點擊通知以查看完整的日誌資訊，並可翻頁瀏覽主控台中的所有日誌。

這些通知可以透過 `LogBox.ignoreAllLogs()` 來隱藏。這在進行產品演示時非常有用。此外，也可以透過 `LogBox.ignoreLogs()` 來逐個隱藏通知。這對於無法修復的第三方依賴項中的嘈雜警告特別有用。

:::info
忽略日誌應作為最後手段，並應建立任務來修復所有被忽略的日誌。
:::

```tsx
import {LogBox} from 'react-native';

// Ignore log notification by message:
LogBox.ignoreLogs(['Warning: ...']);

// Ignore all log notifications:
LogBox.ignoreAllLogs();
```

### 未處理的錯誤

未處理的 JavaScript 錯誤（例如 `undefined is not a function`）會自動開啟全螢幕的 LogBox 錯誤，並顯示錯誤來源。這些錯誤可以關閉或最小化，以便您查看錯誤發生時的應用狀態，但應始終予以解決。

### 語法錯誤

當發生語法錯誤時，全螢幕的 LogBox 錯誤會自動開啟，並顯示堆疊追蹤和語法錯誤的位置。此錯誤無法關閉，因為它代表無效的 JavaScript 執行，必須修復後才能繼續使用應用。若要關閉這些錯誤，請修復語法錯誤並儲存以自動關閉（啟用快速重新整理時）或按 <kbd>Cmd ⌘</kbd>/<kbd>Ctrl</kbd> + <kbd>R</kbd> 重新載入（停用快速重新整理時）。

## Chrome 開發者工具

若要在 Chrome 中除錯 JavaScript 程式碼，請從開發者選單中選擇「開啟除錯器」。這將在 [http://localhost:8081/debugger-ui](http://localhost:8081/debugger-ui) 開啟一個新分頁。

從這裡，從 Chrome 選單中選擇 `更多工具 → 開發者工具` 以開啟 [Chrome DevTools](https://developer.chrome.com/devtools)。或者，您可以使用快捷鍵 <kbd>⌥ Option</kbd> + <kbd>Cmd ⌘</kbd> + <kbd>I</kbd>（macOS）/ <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>I</kbd>（Windows 和 Linux）。

- 如果您是 Chrome DevTools 的新手，我們建議您閱讀文件中的[主控台](https://developer.chrome.com/docs/devtools/#console)和[來源](https://developer.chrome.com/docs/devtools/#sources)標籤。
- 您可能希望啟用[在捕獲例外時暫停](https://developer.chrome.com/docs/devtools/javascript/breakpoints/#exceptions)以獲得更好的除錯體驗。

:::info
React Developer Tools Chrome 擴充功能不適用於 React Native，但你可以改用其獨立版本。閱讀[此章節](react-devtools)了解使用方法。
:::

:::note
在 Android 上，若偵錯工具與裝置的時間出現偏差，可能會導致動畫和事件行為異常。可執行 ``adb shell "date `date +%m%d%H%M%Y.%S%3N`"`` 指令同步時間（使用實體裝置需 root 權限）。
:::

### 在實體裝置上偵錯

:::info
若使用 Expo CLI，此設定已自動完成。
:::

<Tabs groupId="platform" defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="ios">

On iOS devices, open the file [`RCTWebSocketExecutor.mm`](https://github.com/facebook/react-native/blob/master/packages/react-native/React/CoreModules/RCTWebSocketExecutor.mm) and change "localhost" to the IP address of your computer, then select "Debug JS Remotely" from the Dev Menu.

</TabItem>
<TabItem value="android">

On Android 5.0+ devices connected via USB, you can use the [`adb` command line tool](http://developer.android.com/tools/help/adb.html) to set up port forwarding from the device to your computer:

```sh
adb reverse tcp:8081 tcp:8081
```

Alternatively, select "Settings" from the Dev Menu, then update the "Debug server host for device" setting to match the IP address of your computer.

</TabItem>
</Tabs>

:::note
若遇到問題，可能是某個 Chrome 擴充功能與偵錯工具發生衝突。建議停用所有擴充功能後逐一重新啟用，以找出有問題的擴充功能。
:::

<details>
<summary>Advanced: Debugging using a custom JavaScript debugger</summary>

To use a custom JavaScript debugger in place of Chrome Developer Tools, set the `REACT_DEBUGGER` environment variable to a command that will start your custom debugger. You can then select "Open Debugger" from the Dev Menu to start debugging.

The debugger will receive a list of all project roots, separated by a space. For example, if you set `REACT_DEBUGGER="node /path/to/launchDebugger.js --port 2345 --type ReactNative"`, then the command `node /path/to/launchDebugger.js --port 2345 --type ReactNative /path/to/reactNative/app` will be used to start your debugger.

:::note
Custom debugger commands executed this way should be short-lived processes, and they shouldn't produce more than 200 kilobytes of output.
:::

</details>

## Safari 開發者工具

無需啟用「遠端偵錯 JavaScript」即可使用 Safari 偵錯 iOS 版應用程式。

- 實體裝置設定：`設定 → Safari → 進階 → 確保「網頁檢查器」已啟用`（模擬器不需此步驟）
- Mac 端啟用開發選單：`Safari 設定（或偏好設定）→ 進階 → 勾選「在選單列顯示開發選單」`
- 選擇應用程式的 JSContext：`開發 → 模擬器（或其他裝置）→ JSContext`
- Safari 的網頁檢查器將開啟，內含控制台與偵錯工具

預設可能未啟用原始碼對應（sourcemaps），可參考[此指南](http://blog.nparashuram.com/2019/10/debugging-react-native-ios-apps-with.html)或[影片](https://www.youtube.com/watch?v=GrGqIIz51k4)啟用功能，並在原始碼正確位置設定中斷點。

注意：每次應用程式重新載入（透過即時重新載入或手動重新載入）時，都會建立新的 JSContext。勾選「自動顯示 JSContext 的網頁檢查器」可避免手動選擇最新 JSContext。

## 效能監控

透過開發選單選擇「效能監控」，可啟用效能疊加層協助診斷效能問題。