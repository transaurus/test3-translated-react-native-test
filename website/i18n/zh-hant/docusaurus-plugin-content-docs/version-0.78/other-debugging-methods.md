---
id: other-debugging-methods
title: Other Debugging Methods
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

本頁面涵蓋如何使用舊版 JavaScript 除錯方法。如果您正在開始一個新的 React Native 或 Expo 應用程式，我們建議使用 [React Native DevTools](./react-native-devtools)。

## Safari 開發者工具（直接 JSC 除錯）

當您的應用程式使用 [JavaScriptCore](https://trac.webkit.org/wiki/JavaScriptCore) (JSC) 作為運行時，您可以使用 Safari 來除錯 iOS 版本的應用程式。

1. **僅限實體裝置**：打開設定應用程式，導航至 Safari > 進階，並確保「網頁檢查器」已開啟。
2. 在您的 Mac 上，打開 Safari 並啟用「開發」選單。這可以在 Safari > 設定... 中找到，然後選擇「進階」標籤，再勾選「顯示網頁開發者功能」。
3. 在「開發」選單中找到您的裝置，並從子選單中選擇「JSContext」項目。這將打開 Safari 的網頁檢查器，其中包含類似 Chrome DevTools 的控制台和原始碼面板。

![開啟 Safari 網頁檢查器](/docs/assets/debugging-safari-developer-tools.jpg)

:::tip
雖然原始碼映射可能預設未啟用，但您可以參考[此指南](https://blog.nparashuram.com/2019/10/debugging-react-native-ios-apps-with.html)或[影片](https://www.youtube.com/watch?v=GrGqIIz51k4)來啟用它們，並在原始碼的正確位置設定中斷點。
:::

:::tip
每次應用程式重新載入時，都會建立一個新的 JSContext。選擇「自動顯示 JSContext 的網頁檢查器」可以避免手動選擇最新的 JSContext。
:::

## 遠端 JavaScript 除錯（已棄用）

:::warning
遠端 JavaScript 除錯在 React Native 0.73 中已被棄用，並將在未來的版本中移除。
:::

遠端 JavaScript 除錯將外部網頁瀏覽器（Chrome）連接到您的應用程式，並在網頁中執行您的 JavaScript 程式碼。這讓您可以使用 Chrome 的除錯工具，就像處理任何網頁應用程式一樣。請注意，瀏覽器環境可能非常不同，並非所有 React Native 模組在這種除錯方式下都能正常運作。

### 設定

自 React Native 0.73 起，遠端 JavaScript 除錯必須使用 `NativeDevSettings` 模組**手動啟用**。

```js
import NativeDevSettings from 'react-native/Libraries/NativeModules/specs/NativeDevSettings';

function MyApp() {
  // Assign this to a dev-only button or useEffect call
  const connectToRemoteDebugger = () => {
    NativeDevSettings.setIsDebuggingRemotely(true);
  };
}
```

當呼叫 `NativeDevSettings.setIsDebuggingRemotely(true)` 時，這將在 [http://localhost:8081/debugger-ui](http://localhost:8081/debugger-ui) 開啟一個新分頁。

從此頁面，可以透過以下方式開啟 Chrome DevTools：

- 檢視 > 開發人員 > 開發人員工具
- <kbd>⌥ Option</kbd> + <kbd>Cmd ⌘</kbd> + <kbd>I</kbd> (macOS) / <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>I</kbd> (Windows 和 Linux)。

控制台和原始碼面板將允許您檢查您的 React Native 程式碼。

![Chrome 中的遠端除錯器視窗](/docs/assets/debugging-chrome-remote-debugger.jpg)

:::info
在遠端 JavaScript 除錯模式下，Chrome 中的網頁版 React DevTools 無法與 React Native 一起使用。請參閱 [React Native DevTools](./react-native-devtools) 指南，了解如何在我們的整合除錯器中使用 React DevTools。
:::

:::note
在 Android 上，如果除錯器和裝置之間的時間漂移，動畫和事件行為等可能無法正常運作。可以透過執行 ``adb shell "date `date +%m%d%H%M%Y.%S%3N`"`` 來解決此問題。使用實體裝置時需要 root 權限。
:::

### 在實體裝置上除錯

:::info
若您使用 Expo CLI，此設定已預先配置完成。
:::

<Tabs groupId="platform" defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="ios">

On iOS devices, open the file [`RCTWebSocketExecutor.mm`](https://github.com/facebook/react-native/blob/master/packages/react-native/React/CoreModules/RCTWebSocketExecutor.mm) and change "localhost" to the IP address of your computer.

</TabItem>
<TabItem value="android">

On Android 5.0+ devices connected via USB, you can use the [`adb` command line tool](http://developer.android.com/tools/help/adb.html) to set up port forwarding from the device to your computer:

```sh
adb reverse tcp:8081 tcp:8081
```

</TabItem>
</Tabs>

:::note
若遇到問題，可能是某個 Chrome 擴充功能與偵錯工具產生非預期交互。建議停用所有擴充功能後逐一重新啟用，直到找出造成問題的擴充元件。
:::