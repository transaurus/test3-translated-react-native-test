---
id: upgrading
title: Upgrading to new versions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

升級至新版本的 React Native 將讓您獲得更多 API、視圖、開發者工具和其他好處。升級過程需要少量努力，但我們會盡量使其直觀簡單。

## Expo 專案

要將您的 Expo 專案升級至新版本的 React Native，需要更新 `package.json` 文件中的 `react-native`、`react` 和 `expo` 套件版本。Expo 建議逐步遞增升級 SDK 版本，一次升級一個版本。這樣做可以幫助您定位升級過程中出現的中斷和問題。請參閱 [Upgrading Expo SDK Walkthrough](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/) 以獲取有關升級專案的最新資訊。

## React Native 專案

由於典型的 React Native 專案基本上由一個 Android 專案、一個 iOS 專案和一個 JavaScript 專案組成，升級可能會相當棘手。[Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 是一個網頁工具，可以通過提供兩個版本之間完整的變更集來幫助您升級應用程式。它還會顯示特定文件的註解，以幫助理解為什麼需要該變更。

### 1. 選擇版本

首先，您需要選擇要從哪個版本升級到哪個版本，預設情況下會選擇最新的主要版本。選擇後，您可以點擊「Show me how to upgrade」按鈕。

💡 主要更新會在頂部顯示一個「有用內容」部分，其中包含升級時可以幫助您的連結。

### 2. 升級依賴項

顯示的第一個文件是 `package.json`，最好更新其中顯示的依賴項。例如，如果 `react-native` 和 `react` 顯示為變更，則可以通過運行以下命令在您的專案中安裝它們：

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

### 3. 升級專案文件

新版本可能包含對其他文件的更新，這些文件在您運行 `npx react-native init` 時生成，這些文件列在 [Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 頁面的 `package.json` 之後。如果沒有其他變更，您只需重新建置專案即可繼續開發。如果有變更，您需要手動將其應用到您的專案中。

### 疑難排解

#### 我已完成所有變更，但我的應用程式仍在使用舊版本

這類錯誤通常與快取有關，建議安裝 [react-native-clean-project](https://github.com/pmadruga/react-native-clean-project) 以清除所有專案快取，然後再次運行。