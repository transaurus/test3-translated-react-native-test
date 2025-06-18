---
id: upgrading
title: Upgrading to new versions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

升級至新版本的 React Native 將讓您獲得更多 API、視圖元件、開發者工具和其他優質功能。升級過程需要少量工作，但我們會盡量使其直觀簡便。

## Expo 專案

要將 Expo 專案升級至新版本的 React Native，需更新 `package.json` 檔案中的 `react-native`、`react` 和 `expo` 套件版本。Expo 提供 `upgrade` 指令來自動處理這些核心套件及其他已知相依套件的升級。請參閱 [Expo SDK 升級指南](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/) 獲取最新的專案升級資訊。

## React Native 專案

由於標準 React Native 專案本質上由 Android 專案、iOS 專案和 JavaScript 專案組成，升級過程較為複雜。[Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 是一款網頁工具，可透過顯示兩個版本間所有變更檔案來協助升級，並提供特定檔案的註解說明變更原因。

### 1. 選擇版本

首先需選擇要從哪個版本升級至哪個版本（預設選取最新主要版本）。選擇後點擊「Show me how to upgrade」按鈕。

💡 主要版本升級時，頂部會顯示「useful content」區塊，包含升級輔助連結。

:::tip
或可執行 `npx react-native upgrade` 指令，該指令會自動檢測當前版本與最新版本，並顯示已預選版本的 Upgrade Helper 頁面連結。
:::

### 2. 升級相依套件

首先顯示的是 `package.json` 檔案，建議更新其中標示需變更的相依套件。例如若顯示需變更 `react-native` 和 `react`，可執行以下指令安裝：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
# {{VERSION}} and {{REACT_VERSION}} are the release versions showing in the diff
npm install react-native@{{VERSION}}
npm install react@{{REACT_VERSION}}
```

</TabItem>
<TabItem value="yarn">

```shell
# {{VERSION}} and {{REACT_VERSION}} are the release versions showing in the diff
yarn add react-native@{{VERSION}}
yarn add react@{{REACT_VERSION}}
```

</TabItem>
</Tabs>

### 3. 升級專案檔案

新版本可能包含執行 `npx react-native init` 時生成的模板檔案更新，這些檔案會列在 Upgrade Helper 頁面的 `package.json` 之後。若無其他變更，只需重新建置專案即可繼續開發。

若有變更，可手動複製頁面中的變更內容貼至專案，或執行以下 React Native CLI 升級指令：

```shell
npx react-native upgrade
```

此指令會比對專案檔案與最新模板，執行以下操作：

- 若模板中有新檔案，則建立該檔案
- 若模板檔案與專案檔案完全相同，則跳過
- 若專案檔案與模板不同，將提示您選擇保留原檔案或覆寫為模板版本

> 注意：部分升級無法透過 React Native CLI 自動完成（如 `0.28` 至 `0.29` 或 `0.56` 至 `0.57`），需手動處理。升級時請務必查閱 [版本發布說明](https://github.com/facebook/react-native/releases) 以確認專案需進行的特殊調整。

### 疑難排解

#### 已完成所有變更，但應用程式仍使用舊版本

此類問題通常與快取有關，建議安裝 [react-native-clean-project](https://github.com/pmadruga/react-native-clean-project) 清除專案所有快取後重新執行。