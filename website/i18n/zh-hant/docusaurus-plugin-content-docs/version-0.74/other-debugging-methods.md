---
id: other-debugging-methods
title: Other Debugging Methods
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

本頁涵蓋了除[開啟偵錯工具](./debugging#opening-the-debugger)所述之外的其他 JavaScript 偵錯方法。如果您使用的是新建立的 React Native 或 Expo 應用程式，建議從該處開始。

## Safari 開發者工具（直接 JSC 偵錯）

當您的應用程式使用 [JavaScriptCore](https://trac.webkit.org/wiki/JavaScriptCore) (JSC) 作為執行環境時，可以使用 Safari 來偵錯 iOS 版本的應用程式。

1. **僅限實體裝置**：開啟「設定」應用程式，導航至 Safari > 進階，並確保「網頁檢查器」已啟用。
2. 在 Mac 上開啟 Safari 並啟用「開發」選單。這可以在 Safari > 設定... 的「進階」標籤頁中找到，然後勾選「顯示網頁開發者功能」。
3. 在「開發」選單中找到您的裝置，並從子選單中選擇「JSContext」項目。這將開啟 Safari 的網頁檢查器，其中包含與 Chrome DevTools 類似的「主控台」和「來源」面板。

![開啟 Safari 網頁檢查器](/docs/assets/debugging-safari-developer-tools.jpg)

:::tip
雖然原始碼對應可能預設未啟用，但您可以參考[此指南](https://blog.nparashuram.com/2019/10/debugging-react-native-ios-apps-with.html)或[影片](https://www.youtube.com/watch?v=GrGqIIz51k4)來啟用它們，並在原始碼的正確位置設定中斷點。
:::

:::tip
每次重新載入應用程式時，都會建立一個新的 JSContext。選擇「自動顯示 JSContext 的網頁檢查器」可以避免手動選擇最新的 JSContext。
:::

## 遠端 JavaScript 偵錯（已棄用）

:::warning
遠端 JavaScript 偵錯在 React Native 0.73 中已棄用，並將在未來版本中移除。
:::

遠端 JavaScript 偵錯將外部網頁瀏覽器（Chrome）連接到您的應用程式，並在網頁中執行 JavaScript 程式碼。這讓您能像偵錯任何網頁應用程式一樣使用 Chrome 的偵錯工具。請注意，瀏覽器環境可能非常不同，且並非所有 React Native 模組在此偵錯方式下都能正常運作。

### 設定

自 React Native 0.73 起，遠端 JavaScript 偵錯必須使用 `NativeDevSettings` 模組**手動啟用**。

```js
import NativeDevSettings from 'react-native/Libraries/NativeModules/specs/NativeDevSettings';

function MyApp() {
  // Assign this to a dev-only button or useEffect call
  const connectToRemoteDebugger = () => {
    NativeDevSettings.setIsDebuggingRemotely(true);
  };
}
```

當呼叫 `NativeDevSettings.setIsDebuggingRemotely(true)` 時，將在 [http://localhost:8081/debugger-ui](http://localhost:8081/debugger-ui) 開啟新分頁。

從此頁面，可透過以下方式開啟 Chrome DevTools：

- 檢視 > 開發人員 > 開發人員工具
- <kbd>⌥ Option</kbd> + <kbd>Cmd ⌘</kbd> + <kbd>I</kbd> (macOS) / <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>I</kbd> (Windows 和 Linux)。

「主控台」和「來源」面板將允許您檢查 React Native 程式碼。

![Chrome 中的遠端偵錯工具視窗](/docs/assets/debugging-chrome-remote-debugger.jpg)

:::info
在遠端 JavaScript 偵錯模式下，Chrome 中的網頁版 React DevTools 無法與 React Native 搭配使用。請參閱 [React DevTools](./react-devtools) 指南以了解如何使用獨立版本的 React DevTools。
:::

:::note
在 Android 上，如果偵錯工具與裝置的時間不同步，動畫和事件行為等可能無法正常運作。可以執行 ``adb shell "date `date +%m%d%H%M%Y.%S%3N`"`` 來修正此問題。使用實體裝置時需要 root 權限。
:::

### 在實體裝置上偵錯

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
若遇到問題，可能是某個 Chrome 擴充功能與偵錯工具發生非預期互動。請嘗試停用所有擴充功能後逐一重新啟用，直到找出造成問題的擴充元件。
:::