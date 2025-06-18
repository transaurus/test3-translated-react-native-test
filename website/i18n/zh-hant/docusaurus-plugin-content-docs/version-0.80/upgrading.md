---
id: upgrading
title: Upgrading to new versions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

升級至新版本的 React Native 將讓您獲得更多 API、視圖、開發者工具和其他好處。升級需要一些努力，但我們會盡量讓這個過程簡單明瞭。

## Expo 專案

將您的 Expo 專案升級至新版本的 React Native 需要更新 `package.json` 文件中的 `react-native`、`react` 和 `expo` 套件版本。Expo 建議逐步升級 SDK 版本，一次一個版本。這樣做可以幫助您找出升級過程中出現的問題和斷點。請參閱 [Upgrading Expo SDK Walkthrough](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/) 以獲取有關升級專案的最新資訊。

## React Native 專案

由於典型的 React Native 專案基本上由一個 Android 專案、一個 iOS 專案和一個 JavaScript 專案組成，升級可能會相當棘手。[Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 是一個網路工具，可以幫助您在升級應用程式時提供兩個版本之間的所有變更。它還會顯示特定文件的註解，以幫助理解為什麼需要這些變更。

### 1. 選擇版本

首先，您需要選擇要從哪個版本升級到哪個版本，預設情況下會選擇最新的主要版本。選擇後，您可以點擊「Show me how to upgrade」按鈕。

💡 主要更新會在頂部顯示一個「useful content」部分，其中包含幫助您升級的連結。

### 2. 升級依賴項

第一個顯示的文件是 `package.json`，更新其中顯示的依賴項是個好主意。例如，如果 `react-native` 和 `react` 顯示為變更，則可以通過運行以下命令在您的專案中安裝它們：

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

### 3. 升級您的專案文件

新版本可能包含對其他文件的更新，這些文件在您運行 `npx react-native init` 時生成，這些文件會在 [Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 頁面的 `package.json` 之後列出。如果沒有其他變更，您只需要重新建置專案即可繼續開發。如果有變更，您需要手動將它們應用到您的專案中。

### 疑難排解

#### 我已完成所有變更，但我的應用程式仍在使用舊版本

這類錯誤通常與快取有關，建議安裝 [react-native-clean-project](https://github.com/pmadruga/react-native-clean-project) 來清除所有專案快取，然後再次運行。