---
id: other-debugging-methods
title: Other Debugging Methods
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

本頁面涵蓋如何使用舊版 JavaScript 除錯方法。若您正在開始一個新的 React Native 或 Expo 應用程式，我們建議使用 [React Native DevTools](./react-native-devtools)。

## Safari 開發者工具（直接 JSC 除錯）

當您的應用程式使用 [JavaScriptCore](https://trac.webkit.org/wiki/JavaScriptCore)（JSC）作為執行環境時，可使用 Safari 來除錯 iOS 版本的應用程式。

1. **僅限實體裝置**：開啟「設定」應用程式，導航至 Safari > 進階，並確保「網頁檢查器」已啟用。
2. 在您的 Mac 上，開啟 Safari 並啟用「開發」選單。此選項位於 Safari > 設定... 的「進階」標籤頁中，勾選「顯示網頁開發者功能」。
3. 在「開發」選單中找到您的裝置，並從子選單中選擇「JSContext」項目。這將開啟 Safari 的網頁檢查器，其中包含與 Chrome DevTools 類似的「主控台」和「來源」面板。

![開啟 Safari 網頁檢查器](/docs/assets/debugging-safari-developer-tools.jpg)

:::tip
雖然原始碼對應（source maps）可能預設未啟用，但您可以參考[此指南](https://blog.nparashuram.com/2019/10/debugging-react-native-ios-apps-with.html)或[影片](https://www.youtube.com/watch?v=GrGqIIz51k4)來啟用它們，並在原始碼的正確位置設定中斷點。
:::

:::tip
每次應用程式重新載入時，都會建立一個新的 JSContext。勾選「自動顯示 JSContext 的網頁檢查器」可避免手動選擇最新的 JSContext。
:::

## 遠端 JavaScript 除錯（已移除）

:::warning[重要]
自 React Native 0.79 起，遠端 JavaScript 除錯功能已被移除。請參閱原始的[棄用公告](https://github.com/react-native-community/discussions-and-proposals/discussions/734)。

若您使用的是舊版 React Native，請查閱[對應版本的說明文件](/versions)。
:::

![Chrome 中的遠端除錯器視窗](/docs/assets/debugging-chrome-remote-debugger.jpg)