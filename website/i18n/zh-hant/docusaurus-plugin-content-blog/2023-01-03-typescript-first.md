---
title: First-class Support for TypeScript
authors: [lunaleaps, NickGerleman]
tags: [typescript, engineering]
date: 2023-01-03
---

# TypeScript 的一流支援

隨著 0.71 版本的發布，React Native 透過以下變更來提升 TypeScript 的使用體驗：

- [新的應用程式範本預設為 TypeScript](/blog/2023/01/03/typescript-first#new-app-template-is-typescript-by-default)
- [React Native 內建 TypeScript 宣告](/blog/2023/01/03/typescript-first#declarations-shipped-with-react-native)
- [React Native 文件以 TypeScript 為優先](/blog/2023/01/03/typescript-first#documentation-is-typescript-first)

本文將說明這些變更對 TypeScript 或 Flow 使用者意味著什麼。

<!--truncate-->

## 新的應用程式範本預設為 TypeScript

從 0.71 版本開始，當你透過 React Native CLI 建立新的 React Native 應用程式時，預設將獲得一個 TypeScript 應用程式！

```shell
npx react-native init My71App --version 0.71.0
```

![螢幕截圖顯示由 React Native CLI 生成的新應用程式在 iPhone 模擬器中運行。旁邊是 Visual Studio Code 編輯器的截圖，打開了「App.tsx」檔案，表明它正在運行一個 TypeScript 檔案。](/blog/assets/typescript-first-new-app.png)

新生成應用程式的起點將是 `App.tsx` 而非 `App.js`——完全使用 TypeScript 類型。新專案已預設設定好 `tsconfig.json`，因此你的 IDE 將立即協助你撰寫類型化的程式碼！

## React Native 內建 TypeScript 宣告

0.71 是首個內建 TypeScript (TS) 宣告的版本。

此前，React Native 的 TypeScript 宣告由位於 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 儲存庫中的 [`@types/react-native`](https://www.npmjs.com/package/@types/react-native) 提供。將 TypeScript 類型與 React Native 原始碼共同存放的決定是為了提升正確性和維護性。

`@types/react-native` 僅為穩定版本提供類型。這意味著如果你想在 TypeScript 中使用 React Native 的預發布版本，你必須使用舊版本的類型，而這些類型可能不準確。發布 `@types/react-native` 也容易出錯。發布會延遲於 React Native 的發布，且該過程需要手動檢查 React Native 公共 API 的類型變更並更新 TS 宣告以匹配。

將 TS 類型與 React Native 原始碼共同存放後，TS 類型的可見性和所有權更高。我們的團隊正在積極開發工具以維持 Flow 和 TS 之間的一致性。

此變更還移除了 React Native 使用者需要管理的一個依賴項。升級至 0.71 或更高版本時，你可以移除 `@types/react-native` 作為依賴項。[請參考新應用程式範本以了解如何設定 TypeScript 支援。](https://github.com/facebook/react-native/blob/main/template/tsconfig.json)

我們計劃在 0.73 及之後的版本中棄用 `@types/react-native`。具體而言，這意味著：

- 將發布追蹤 React Native 0.71 和 0.72 版本的 `@types/react-native`。它們將與相關發布分支中 React Native 的類型完全相同。
- 對於 React Native 0.73 及之後的版本，TS 類型將僅能從 React Native 中獲取。

### 如何遷移

請盡快遷移至新的共同存放類型。以下是根據你的需求提供的遷移詳細資訊。

#### 應用程式維護者

一旦你升級至 React Native >= 0.71，你可以從 `devDependency` 中移除 `@types/react-native`。

:::note

若您因使用的函式庫將 `@types/react-native` 列為 `peerDependency` 而出現警告，請向該函式庫提交 issue 或 PR 要求改用[可選的 peerDependencies](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#peerdependenciesmeta)，目前可暫時忽略此警告。

:::

#### 函式庫維護者

針對 React Native 0.71 以下版本的函式庫，可能使用 `@types/react-native` 作為 `peerDependency` 來對應用程式的型別定義進行檢查。此依賴項應在 [`peerDependenciesMeta`](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#peerdependenciesmeta) 中標記為可選，如此一來，對於不使用 TypeScript 的使用者或已內建型別定義的 0.71 使用者，就不需要這些型別定義。

#### 依賴 `@types/react-native` 的型別定義維護者

請查閱 [0.71 版本引入的重大變更](https://github.com/facebook/react-native/blob/main/CHANGELOG.md)，確認您是否已準備好遷移。

### 如果我使用 Flow 怎麼辦？

Flow 使用者仍可對目標為 0.71+ 的應用程式進行型別檢查，但範本中不再預設包含其配置邏輯。

Flow 使用者過去會透過合併新應用範本中的 `.flowconfig` 並手動更新 `flow-bin` 來升級 React Native 的 Flow 型別定義。新應用範本已不再包含 `.flowconfig`，但 [React Native 儲存庫中仍保留了一份](https://github.com/facebook/react-native/blob/main/.flowconfig)，可作為您應用程式的基礎。

若您需要以 Flow 建立新的 React Native 應用程式，可參考 [0.70 版本的新應用範本](https://github.com/facebook/react-native/tree/0.70-stable/template)。

### 如果我在 TypeScript 型別定義中發現錯誤怎麼辦？

無論您使用的是內建 TS 型別定義還是 `@types/react-native`，若發現錯誤，請向 [React Native](https://github.com/facebook/react-native) 和 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 儲存庫提交 PR。若您不確定如何修正，請在 React Native 儲存庫中提交 GitHub issue，並在問題中標記 [@lunaleaps](https://github.com/lunaleaps)。

## 文件以 TypeScript 為優先

為確保一致的 TypeScript 體驗，我們已對 React Native 文件進行多項更新，將 TypeScript 作為新的預設語言。

程式碼範例現在支援內嵌 TypeScript，並已更新超過 170 個互動式程式碼範例，使其在新範本中通過 linting、格式化和型別檢查。大多數範例同時適用於 TypeScript 和 JavaScript。若有不兼容之處，您可以查看任一語言的範例。

若發現錯誤或有改進建議，請記住網站也是開源的，我們非常歡迎您的 PR！

## 感謝 React Native TypeScript 社群！

最後，我們要感謝多年來社群為確保 React Native 開發者能使用 TypeScript 所付出的努力。

我們要感謝所有自 [2015 年](https://github.com/DefinitelyTyped/DefinitelyTyped/commit/efce0c25ec532a4651859f10eda49e97a5716a42)以來維護 `@types/react-native` 的[貢獻者](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react-native/index.d.ts#L3)！我們看到了大家為確保 React Native 使用者獲得最佳體驗所付出的努力與用心。

感謝 [@acoates](https://github.com/acoates)、[@eps1lon](https://github.com/eps1lon)、[@kelset](https://github.com/kelset)、[@tido64](https://github.com/tido64)、[@Titozzz](https://github.com/Titozzz) 和 [@ZihanChen-MSFT](https://github.com/ZihanChen-MSFT) 的協助，包括諮詢、提問、溝通與審查變更，共同推動將 TypeScript 類型定義整合至 React Native 核心。

同時，我們也要感謝 [`react-native-template-typescript` 的維護者們](https://github.com/react-native-community/react-native-template-typescript/graphs/contributors)，從最初就為 React Native 的新專案開發提供了 TypeScript 支援。

我們期待未來能直接在 React Native 代碼庫中展開更緊密的合作，持續提升 React Native 的開發者體驗！