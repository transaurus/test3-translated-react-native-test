---
id: upgrading
title: Upgrading to new versions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

升級至新版本的 React Native 將讓您獲得更多 API、視圖元件、開發者工具及其他功能。升級過程需要少量操作，但我們會盡量使其直觀簡便。

## Expo 專案

將 Expo 專案升級至新版本 React Native 需更新 `package.json` 中的 `react-native`、`react` 和 `expo` 套件版本。Expo 建議逐次遞增升級 SDK 版本，此方式有助於定位升級過程中出現的中斷或問題。詳見 [Expo SDK 升級指南](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/) 獲取最新的專案升級資訊。

## React Native 專案

由於標準 React Native 專案本質上由 Android 專案、iOS 專案和 JavaScript 專案組成，升級過程較為複雜。[升級助手](https://react-native-community.github.io/upgrade-helper/) 是一款網頁工具，可透過提供兩個版本間完整變更集來協助升級，並針對特定檔案顯示註解說明變更原因。

### 1. 選擇版本

首先需選擇起始版本與目標版本（預設為最新主要版本），選擇後可點擊「Show me how to upgrade」按鈕。

💡 主要版本更新時，頂部會顯示「實用內容」區塊，包含升級輔助連結。

### 2. 升級依賴項

首先顯示的是 `package.json` 檔案，建議更新其中標示的依賴項。例如若顯示 `react-native` 和 `react` 需變更，可執行以下指令安裝：

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

新版本可能包含執行 `npx react-native init` 時生成的其他檔案更新，這些檔案會列於 [升級助手](https://react-native-community.github.io/upgrade-helper/) 頁面的 `package.json` 之後。若無其他變更，僅需重新建置專案即可繼續開發；若有變更則需手動套用至專案中。

### 疑難排解

#### 已完成所有變更但應用程式仍使用舊版本

此類錯誤通常與快取相關，建議安裝 [react-native-clean-project](https://github.com/pmadruga/react-native-clean-project) 清除專案所有快取後重新執行。