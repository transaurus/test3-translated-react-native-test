---
id: libraries
title: Using Libraries
author: Brent Vatne
authorURL: 'https://twitter.com/notbrent'
description: This guide introduces React Native developers to finding, installing, and using third-party libraries in their apps.
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

React Native 提供了一套內建的[核心元件與 API](./components-and-apis)，可直接在應用程式中使用。您不僅限於使用 React Native 內建的元件和 API。React Native 擁有數千名開發者組成的社群。如果核心元件與 API 無法滿足您的需求，您可以在社群中找到並安裝函式庫來擴充功能。

## 選擇套件管理工具

React Native 函式庫通常透過 [npm 套件庫](https://www.npmjs.com/) 安裝，使用 Node.js 套件管理工具如 [npm CLI](https://docs.npmjs.com/cli/npm) 或 [Yarn Classic](https://classic.yarnpkg.com/en/)。

若您的電腦已安裝 Node.js，則已內建 npm CLI。部分開發者偏好使用 Yarn Classic，因其安裝速度稍快且具備進階功能如 Workspaces。兩種工具皆適用於 React Native。為簡化說明，本指南將以 npm 為例。

> 💡 在 JavaScript 社群中，"函式庫"與"套件"兩詞常可互換使用。

## 安裝函式庫

要在專案中安裝函式庫，請於終端機中導航至專案目錄並執行安裝指令。以下以 `react-native-webview` 為例：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install react-native-webview
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add react-native-webview
```

</TabItem>
</Tabs>

我們安裝的函式庫包含原生程式碼，需先連結至應用程式才能使用。

## 在 iOS 上連結原生程式碼

React Native 使用 CocoaPods 管理 iOS 專案相依性，多數 React Native 函式庫遵循此慣例。若您使用的函式庫未採用此方式，請參閱其 README 獲取額外指示。多數情況下，以下指令皆適用。

在 `ios` 目錄中執行 `pod install` 以連結至原生 iOS 專案。若不想切換至 `ios` 目錄，可執行捷徑指令 `npx pod-install`。

```bash
npx pod-install
```

完成後，重新建置應用程式二進位檔以開始使用新函式庫：

```bash
npx react-native run-ios
```

## 在 Android 上連結原生程式碼

React Native 使用 Gradle 管理 Android 專案相依性。安裝含原生相依性的函式庫後，需重新建置應用程式二進位檔：

```bash
npx react-native run-android
```

## 尋找函式庫

[React Native Directory](https://reactnative.directory) 是專為 React Native 建置的函式庫可搜尋資料庫。這是為 React Native 應用尋找函式庫的首選之處。

目錄中許多函式庫來自 [React Native Community](https://github.com/react-native-community/) 或 [Expo](https://docs.expo.dev/versions/latest/)。

React Native Community 的函式庫由志願者與依賴 React Native 的公司成員推動。通常支援 iOS、tvOS、Android、Windows，但各專案支援程度不一。此組織中許多函式庫曾為 React Native 核心元件與 API。

Expo 建置的函式庫皆以 TypeScript 撰寫，並盡可能支援 iOS、Android 及 `react-native-web`。

若在目錄中找不到專為 React Native 設計的函式庫，[npm 套件庫](https://www.npmjs.com/) 是次佳選擇。npm 套件庫是 JavaScript 函式庫的權威來源，但所列函式庫未必全與 React Native 相容。React Native 是眾多 JavaScript 執行環境之一，其他包括 Node.js、網頁瀏覽器、Electron 等，npm 包含適用於所有這些環境的函式庫。

## 判斷函式庫相容性

### 是否與 React Native 相容？

通常，專為其他平台構建的函式庫無法與 React Native 相容。例如，`react-select` 是專為網頁平台構建並針對 `react-dom` 設計的，而 `rimraf` 則是為 Node.js 構建並與電腦檔案系統互動的工具。其他如 `lodash` 這類僅使用 JavaScript 語言特性的函式庫則可在任何環境中運作。隨著經驗累積，您會逐漸掌握判斷技巧，但在那之前最簡單的方法就是親自嘗試。若發現某個套件無法在 React Native 中使用，您可透過 `npm uninstall` 指令將其移除。

### 是否支援我的應用程式目標平台？

[React Native Directory](https://reactnative.directory) 提供依平台相容性（如 iOS、Android、Web 和 Windows）篩選的功能。若您想使用的函式庫未列於該目錄中，請參閱該函式庫的 README 文件以獲取更多資訊。

### 是否與我的 React Native 版本相容？

函式庫的最新版本通常會與最新版 React Native 相容。若您使用較舊版本，應查閱 README 文件以確認應安裝哪個版本的函式庫。您可透過執行 `npm install <函式庫名稱>@<版本號>` 來安裝特定版本，例如：`npm install @react-native-community/netinfo@^2.0.0`。