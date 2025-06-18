---
id: upgrading
title: Upgrading to new versions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

升級至新版本的 React Native 將使您能使用更多 API、視圖元件、開發者工具及其他功能。升級過程需要少量工作，但我們會盡量使其直觀簡便。

## Expo 專案

要將 Expo 專案升級至新版本的 React Native，需更新 `package.json` 檔案中的 `react-native`、`react` 和 `expo` 套件版本。Expo 建議逐步遞增升級 SDK 版本，每次僅升級一個版本。此方式有助於準確定位升級過程中出現的中斷與問題。請參閱 [升級 Expo SDK 逐步指南](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/) 以獲取最新的專案升級資訊。

## React Native 專案

由於典型的 React Native 專案本質上由 Android 專案、iOS 專案和 JavaScript 專案組成，升級過程可能較為複雜。[升級助手](https://react-native-community.github.io/upgrade-helper/) 是一個網頁工具，可透過提供兩個版本間完整變更集來協助您升級應用程式。該工具還會顯示特定檔案的註解，以幫助理解為何需要該變更。

### 1. 選擇版本

首先需選擇要從哪個版本升級至哪個版本，預設會選取最新的主要版本。選擇完成後，可點擊「顯示升級方式」按鈕。

💡 主要版本更新時，頂部會顯示「實用內容」區段，其中包含協助升級的相關連結。

### 2. 升級相依套件

首先顯示的是 `package.json` 檔案，建議更新其中顯示的相依套件。例如，若 `react-native` 和 `react` 顯示為變更項目，可透過執行以下指令在專案中安裝：

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

新版本可能包含其他檔案的更新，這些檔案會在執行 `npx react-native init` 時生成，它們列於 [升級助手](https://react-native-community.github.io/upgrade-helper/) 頁面的 `package.json` 之後。若無其他變更，僅需重新建置專案即可繼續開發。若有變更，則需手動將其套用至您的專案中。

### 疑難排解

#### 我已完成所有變更，但應用程式仍使用舊版本

此類錯誤通常與快取相關，建議安裝 [react-native-clean-project](https://github.com/pmadruga/react-native-clean-project) 來清除專案的所有快取，然後再次執行專案。