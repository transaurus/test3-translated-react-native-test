---
id: upgrading
title: Upgrading to new versions
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

升級至新版本的 React Native 將讓您獲得更多 API、視圖元件、開發者工具和其他優化功能。升級過程需要少量工作量，但我們會盡量使其流程直觀易操作。

## Expo 專案

要將 Expo 專案升級至新版本 React Native，需更新 `package.json` 中的 `react-native`、`react` 和 `expo` 套件版本。Expo 建議逐次遞增升級 SDK 版本，此方式有助於準確定位升級過程中出現的中斷或問題。詳見 [Expo SDK 升級指南](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/) 獲取最新的專案升級資訊。

## React Native 專案

由於典型的 React Native 專案本質上由 Android 專案、iOS 專案和 JavaScript 專案組成，升級過程可能較為複雜。[升級助手](https://react-native-community.github.io/upgrade-helper/) 是一個網頁工具，可透過提供兩個版本間完整變更集來協助應用程式升級，同時針對特定檔案顯示註解以說明變更原因。

### 1. 選擇版本

首先需選擇要從哪個版本升級至哪個版本（預設選取最新主要版本），點擊「顯示升級方式」按鈕後即可查看升級指引。

💡 主要版本更新時，頂部會顯示「實用內容」區塊，其中包含升級相關的輔助連結。

:::tip
您亦可執行 `npx react-native upgrade` 命令，該命令會自動檢測當前版本與最新可用版本，並顯示已預選版本的升級助手頁面連結。
:::

### 2. 升級依賴項

首先顯示的是 `package.json` 檔案，建議更新其中標示的依賴項。例如若顯示 `react-native` 和 `react` 需變更，可執行以下命令進行安裝：

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

新版本可能包含執行 `npx react-native init` 時生成的檔案更新，這些檔案會列在升級助手頁面的 `package.json` 之後。若無其他變更，只需重新建置專案即可繼續開發。

若存在變更，可手動複製頁面中的變更內容進行更新，或透過執行以下 React Native CLI 升級命令完成：

```shell
npx react-native upgrade
```

此命令會比對您的檔案與最新模板，並執行以下操作：

- 若模板中有新檔案，則建立該檔案
- 若模板檔案與您的檔案完全相同，則跳過
- 若您的專案檔案與模板存在差異，將提示您選擇保留原檔案或覆寫為模板版本

> 注意：部分升級無法透過 React Native CLI 自動完成（例如從 `0.28` 升至 `0.29` 或 `0.56` 升至 `0.57`），需手動操作。升級時請務必查閱 [版本發布說明](https://github.com/facebook/react-native/releases) 以確認專案所需的特定手動變更。

### 疑難排解

#### 已完成所有變更但應用程式仍使用舊版本

此類錯誤通常與快取有關，建議安裝 [react-native-clean-project](https://github.com/pmadruga/react-native-clean-project) 清除專案所有快取後重新執行。