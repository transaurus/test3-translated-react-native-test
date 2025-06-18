---
id: react-devtools
title: React Developer Tools
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

您可以使用[獨立版 React Developer Tools](https://github.com/facebook/react/tree/main/packages/react-devtools)來除錯 React 元件層級結構。請先全域安裝 `react-devtools` 套件：

<Tabs groupId="package-manager" defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install -g react-devtools
```

</TabItem>
<TabItem value="yarn">

```shell
yarn global add react-devtools
```

</TabItem>
</Tabs>

現在從終端機執行 `react-devtools` 來啟動獨立版 DevTools 應用程式。數秒內應會自動連接至您的模擬器。

```shell
react-devtools
```

![React DevTools](/docs/assets/ReactDevTools.png)

:::info
若想避免全域安裝，可將 `react-devtools` 加入專案依賴。先執行 `npm install --save-dev react-devtools` 安裝套件，再於 `package.json` 的 `scripts` 區段加入 `"react-devtools": "react-devtools"`，最後在專案目錄執行 `npm run react-devtools` 即可開啟 DevTools。
:::

## 與 React Native 檢查器整合

開啟開發者選單並點選「切換檢查器」，將顯示可點擊任何 UI 元素查看其資訊的疊加層：

![React Native 檢查器](/docs/assets/Inspector.gif)

當 `react-devtools` 運行時，檢查器會進入折疊模式，並改以 DevTools 作為主要介面。此模式下點擊模擬器中的元件，將在 DevTools 中顯示對應元件：

![React DevTools 檢查器整合](/docs/assets/ReactDevToolsInspector.gif)

您可再次從選單選擇「切換檢查器」來退出此模式。

## 除錯應用程式狀態

[Reactotron](https://github.com/infinitered/reactotron) 是一款開源桌面應用，可檢查 Redux 或 MobX-State-Tree 的應用狀態、查看自訂日誌、執行重置狀態等指令、儲存/恢復狀態快照，並提供其他 React Native 應用除錯功能。

安裝說明請參閱 [README 文件](https://github.com/infinitered/reactotron)。若使用 Expo，此文章詳細說明 [Expo 專案的安裝方式](https://shift.infinite.red/start-using-reactotron-in-your-expo-project-today-in-3-easy-steps-a03d11032a7a)。

## 疑難排解

:::tip
啟動 React DevTools 後請遵循指示操作。若在開啟 DevTools 前已運行應用程式，可能需要[開啟開發者選單](/docs/debugging#accessing-the-dev-menu)來建立連接。

![React DevTools 連接過程](/docs/assets/ReactDevToolsConnection.gif)
:::

:::info
若連接模擬器時遇到問題（特別是 Android 12），請嘗試在新終端機執行 `adb reverse tcp:8097 tcp:8097`。
:::