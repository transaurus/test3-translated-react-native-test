---
id: upgrading
title: Upgrading to new versions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

升級至新版本的 React Native 將讓您獲得更多 API、視圖元件、開發者工具和其他優化功能。升級過程需要少量調整，但我們會盡可能簡化流程。

## Expo 專案

升級 Expo 專案的 React Native 版本需要更新 `package.json` 中的 `react-native`、`react` 和 `expo` 套件版本。Expo 建議採用漸進式升級策略，每次只更新一個 SDK 版本，這有助於準確定位升級過程中出現的問題。詳見 [Expo SDK 升級指南](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/) 獲取最新升級資訊。

## React Native 專案

由於標準 React Native 專案本質上由 Android 專案、iOS 專案和 JavaScript 專案組成，升級過程較為複雜。[升級助手](https://react-native-community.github.io/upgrade-helper/) 這個網頁工具能完整顯示兩個版本間的所有變更，協助您完成應用程式升級。該工具還會針對特定檔案提供註解，說明變更原因。

### 1. 選擇版本

首先需選擇要從哪個版本升級至哪個版本（預設會選取最新的主要版本）。選擇完成後可點擊「Show me how to upgrade」按鈕。

💡 主要版本更新時，工具頂部會顯示「實用內容」區塊，提供升級相關的協助連結。

### 2. 升級依賴套件

首先顯示的是 `package.json` 檔案，建議先更新其中標示的依賴套件。例如若顯示 `react-native` 和 `react` 有變更，可執行以下指令進行安裝：

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

新版本可能包含執行 `npx react-native init` 時生成的檔案更新，這些檔案會列在 [升級助手](https://react-native-community.github.io/upgrade-helper/) 頁面的 `package.json` 之後。若無其他變更，只需重新建置專案即可繼續開發；若有變更則需手動套用至您的專案。

### 疑難排解

#### 已完成所有變更但應用程式仍使用舊版本

此類問題通常與快取有關，建議安裝 [react-native-clean-project](https://github.com/pmadruga/react-native-clean-project) 清除專案所有快取後重新執行。