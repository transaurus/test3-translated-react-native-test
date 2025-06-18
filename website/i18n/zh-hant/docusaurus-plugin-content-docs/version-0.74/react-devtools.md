---
id: react-devtools
title: React DevTools
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

[React DevTools](https://github.com/facebook/react/tree/main/packages/react-devtools) 可用於偵錯應用程式中的 React 元件層級結構。

獨立版本的 React DevTools 可連接至 React Native 應用程式。使用時請安裝或執行 `react-devtools` 套件，通常能在數秒內自動連接至模擬器。

```sh
npx react-devtools
```

![React DevTools 介面](/docs/assets/debugging-react-devtools-detail.jpg)

<details>
<summary>💡 Installing React DevTools globally</summary>

We recommend running `react-devtools` via `npx`, but you can also install a given version globally.

<Tabs groupId="package-manager" defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```sh
npm install -g react-devtools
```

</TabItem>
<TabItem value="yarn">

```shell
yarn global add react-devtools
```

</TabItem>
</Tabs>

Then, run the global `react-devtools` command:

```sh
react-devtools
```

</details>

<details>
<summary>💡 Adding React DevTools as a project dependency</summary>

If you prefer to avoid global installations, you can add `react-devtools` as a project dependency. Add the `react-devtools` package to your project using `npm install --save-dev react-devtools`, then add `"react-devtools": "react-devtools"` to the `scripts` section in your `package.json`, and then run `npm run react-devtools` from your project folder to open the DevTools.

</details>

:::tip
更多使用技巧請參閱 [react.dev 上的 React Developer Tools 指南](https://react.dev/learn/react-developer-tools)。
:::

## 與元素檢查器整合

React Native 提供「顯示元素檢查器」功能（位於開發選單），可點選任何 UI 元素查看其詳細資訊。

![元素檢查器操作示範影片](/docs/assets/debugging-element-inspector.gif)

當 React DevTools 連線時，元素檢查器會進入**折疊模式**，並改以 DevTools 作為主要介面。此模式下點擊模擬器元件會自動導航至 DevTools 對應元件。

可透過選單中的「隱藏元素檢查器」退出此模式。

![React DevTools 與元素檢查器整合示範](/docs/assets/debugging-element-inspector-react-devtools.gif)

## 應用程式狀態偵錯

[Reactotron](https://github.com/infinitered/reactotron) 是開源桌面工具，可檢視 Redux 或 MobX-State-Tree 狀態、自訂日誌、執行狀態重置指令、儲存/還原狀態快照等 React Native 偵錯功能。

安裝說明請參閱 [README 文件](https://github.com/infinitered/reactotron)。Expo 使用者請參考 [Expo 專案整合指南](https://shift.infinite.red/start-using-reactotron-in-your-expo-project-today-in-3-easy-steps-a03d11032a7a)。

## 疑難排解

:::tip
執行 React DevTools 後請遵循指示操作。若在開啟 DevTools 前已啟動應用程式，可能需要[開啟開發選單](./debugging#accessing-the-dev-menu)手動連接。

![React DevTools 連線流程示範](/docs/assets/debugging-react-devtools-connection.gif)
:::

:::info
若 Android 模擬器連線異常，請嘗試在新終端機執行 `adb reverse tcp:8097 tcp:8097`。
:::