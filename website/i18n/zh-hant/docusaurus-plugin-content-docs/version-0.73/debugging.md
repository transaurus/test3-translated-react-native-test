---
id: debugging
title: Debugging Basics
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 存取開發者選單

React Native 提供了一個內建的開發者選單，其中包含多種除錯選項。您可以透過搖動裝置或使用鍵盤快捷鍵來存取開發者選單：

- iOS 模擬器：<kbd>Cmd ⌘</kbd> + <kbd>D</kbd>（或選擇「裝置」>「搖動」）
- Android 模擬器：<kbd>Cmd ⌘</kbd> + <kbd>M</kbd>（macOS）或 <kbd>Ctrl</kbd> + <kbd>M</kbd>（Windows 和 Linux）

另外，對於 Android 裝置和模擬器，您可以在終端機中執行 `adb shell input keyevent 82`。

![React Native 開發者選單](/docs/assets/debugging-dev-menu.jpg)

:::note
開發者選單在正式（生產）版本中會被停用。
:::

## 開啟除錯器

除錯器能讓您瞭解並除錯 JavaScript 程式碼的執行情況，類似於網頁瀏覽器的功能。

:::info
**在 Expo 專案中**，於 CLI 按下 <kbd>j</kbd> 可直接開啟 Hermes 除錯器。
:::

<Tabs groupId="js-debugger" queryString defaultValue={constants.defaultJsDebugger} values={constants.jsDebuggers}>
<TabItem value="hermes">

Hermes supports the Chrome debugger by implementing the Chrome DevTools Protocol. This means Chrome's tools can be used to directly debug JavaScript running on Hermes, on an emulator or on a physical device.

1. In a Chrome browser window, navigate to `chrome://inspect`.
2. Use the "Configure..." button to add the dev server address (typically `localhost:8081`).
3. You should now see a "Hermes React Native" target with an **"inspect"** link. Click this to open the debugger.

![Overview of Chrome's inspect interface and a connected Hermes debugger window](/docs/assets/debugging-hermes-debugger-instructions.jpg)

</TabItem>
<TabItem value="flipper">

[Flipper](https://fbflipper.com/) is a native debugging tool which provides JavaScript debugging capabilities for React Native via an embedded Chrome DevTools panel.

To debug JavaScript code in Flipper, select **"Open Debugger"** from the Dev Menu. Learn more about Flipper [here](https://fbflipper.com/docs/features/react-native/).

:::info
To debug using Flipper, the Flipper app must be [installed on your system](https://fbflipper.com/docs/getting-started/).
:::

![The Flipper desktop app opened to the Hermes debugger panel](/docs/assets/debugging-flipper-console.jpg)

:::warning
Debugging React Native apps with Flipper is [deprecated in React Native 0.73](https://github.com/react-native-community/discussions-and-proposals/pull/641). We will eventually remove out-of-the box support for JS debugging via Flipper.
:::

</TabItem>
<TabItem value="new-debugger">

:::note
**This is an experimental feature** and several features may not work reliably today. When this feature does launch in future, we intend for it to work more completely than the current debugging methods.
:::

The React Native team is working on a new JavaScript debugger experience, intended to replace Flipper, with a preview available as of React Native 0.73.

The new debugger can be enabled via React Native CLI. This will also enable <kbd>j</kbd> to debug.

```sh
npx react-native start --experimental-debugger
```

When selecting **"Open Debugger"** in the Dev Menu, this will launch the new debugger using Google Chrome or Microsoft Edge.

![The new debugger frontend opened to the "Welcome" pane](/docs/assets/debugging-debugger-welcome.jpg)

</TabItem>
</Tabs>

## React 開發者工具

您可以使用 React 開發者工具來檢查 React 元素樹、props 和 state。

```sh
npx react-devtools
```

![React 開發者工具視窗](/docs/assets/debugging-react-devtools-blank.jpg)

:::tip

**學習如何使用 React 開發者工具！**

- [React 開發者工具指南](/docs/0.73/react-devtools)
- [React 開發者工具（react.dev）](https://react.dev/learn/react-developer-tools)

:::

## LogBox

開發版本中的錯誤和警告會顯示在應用程式內的 LogBox 中。

![LogBox 警告與展開的 LogBox 語法錯誤](/docs/assets/debugging-logbox.jpg)

:::note
LogBox 在正式（生產）版本中會被停用。
:::

### 主控台錯誤與警告

主控台錯誤和警告會以螢幕通知的形式顯示，並附有紅色或黃色徽章及通知計數。若要查看錯誤或警告的詳細資訊，請點選通知以展開檢視並瀏覽其他日誌。

可以使用 `LogBox.ignoreAllLogs()` 來停用 LogBox 通知。這在進行產品演示等場合非常有用。此外，也可以透過 `LogBox.ignoreLogs()` 針對特定日誌停用通知，這對於處理第三方依賴中無法修復的嘈雜警告特別實用。

:::info
忽略日誌應作為最後手段，並建立任務來修復任何被忽略的日誌。
:::

```js
import {LogBox} from 'react-native';

// Ignore log notification by message
LogBox.ignoreLogs([
  // Exact message
  'Warning: componentWillReceiveProps has been renamed',

  // Substring or regex match
  /GraphQL error: .*/,
]);

// Ignore all log notifications
LogBox.ignoreAllLogs();
```

### 語法錯誤

當發生 JavaScript 語法錯誤時，LogBox 會開啟並顯示錯誤位置。在此狀態下，LogBox 無法關閉，因為您的程式碼無法執行。LogBox 會在語法錯誤修復後自動關閉——無論是透過快速重新整理還是手動重新載入。

## 效能監控器

在 Android 和 iOS 上，開發期間可以透過在開發者選單中選擇「效能監控器」來切換內建的效能疊加層。進一步瞭解此功能請參閱[這裡](/docs/performance)。

![iOS 和 Android 上的效能監控器疊加層](/docs/assets/debugging-performance-monitor.jpg)

:::info
效能監控器在應用程式內執行，僅作為參考。我們建議使用 Android Studio 和 Xcode 的原生工具來進行精確的效能測量。
:::