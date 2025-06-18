---
title: React Native Core Contributor Summit 2022
authors: [thymikee, cortinico]
tags: [announcement]
date: 2022-11-22
---

# React Native 核心貢獻者峰會 2022

歷經多年疫情與僅限線上的活動後，我們深感是時候讓 React Native 的核心貢獻者們齊聚一堂！

因此，我們在九月初召集了 React Native 活躍的核心貢獻者、函式庫維護者，以及 Meta 的 React Native 與 Metro 團隊成員，舉辦了**核心貢獻者峰會 2022**。[Callstack](https://www.callstack.com/) 在其波蘭弗羅茨瓦夫總部主辦了此次峰會，作為同期舉行的 [React Native EU](https://www.react-native.eu/) 大會的一部分。

我們與 React Native 核心團隊共同設計了一系列**工作坊**，供與會者參與。主題包括：

- React Native Codegen 與 TypeScript 支援
- React Native 新架構函式庫遷移
- React Native 單體式倉庫
- Metro 網頁與生態系統對齊
- Metro 簡化發布流程

這兩天的知識分享與協作成果令我們驚艷。在這篇部落格中，我們將帶您一窺此次聚會的成果。

<!--truncate-->

### React Native Codegen 與 TypeScript 支援

[React Native 的 Codegen](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/codegen.md) 是 React Native 新架構的核心部分。支援並改進它是我們對 React Native 未來的首要任務之一。例如，今年初我們新增了從 TypeScript 規格（而非 Flow）生成通用程式碼的支援。

在此工作坊中，我們藉機向新貢獻者介紹 Codegen，解釋其核心概念與運作方式。隨後我們聚焦於兩大重點：

#### 1. 支援 Codegen **目前未支援的新類型**。其中高度需求的類型之一是 [TypeScript 的字串聯合類型](https://github.com/Titozzz/react-native/tree/codegen-string-union)。

一支小型團隊進入會議室專攻此任務。過程中他們遭遇並克服了若干困難，例如如何執行 Codegen 的單元測試。團隊花費相當時間理解程式碼執行流程後才開始處理程式碼。經過數小時協作，最終產出能識別字串聯合類型的首個原型。此經驗對於討論未來理想的設計模式與架構極具價值。

#### 2. 改進 **[iOS 的自動連結功能](https://github.com/facebook/react-native/pull/34580)**，以補足缺失的使用情境。

具體而言，當函式庫與應用程式共存於單體式倉庫時，自動連結無法妥善運作。Android 已支援此情境，但 iOS 尚缺。

與貢獻者協作 Codegen 的過程讓我們意識到其程式碼庫的複雜性。例如，新增對某類型的支援需在四個不同位置重複相同程式碼：使用 Flow 規格的模組、使用 TypeScript 規格的模組、使用 Flow 規格的元件，以及使用 TypeScript 規格的元件。

此發現促使我們建立[統整任務](https://github.com/facebook/react-native/issues/34872)，向社群尋求協助以改善程式碼庫的可維護性。

參與情況超乎預期：我們在 **5 天內迅速分配了前 40 項任務**。截至十月底，社群已完成 **47 項任務**，另有許多已準備就緒等待合併。

此計畫也為所有參與改進的貢獻者們助力了 [Hacktoberfest](https://hacktoberfest.com/)！

### React Native 新架構函式庫遷移

React Native 領域的熱門話題是新架構（New Architecture）。讓**函式庫**支援新架構是[整個生態系統遷移](/blog/2022/06/16/resources-migrating-your-react-native-library-to-the-new-architecture)的關鍵點。因此，我們希望協助函式庫維護者遷移至新架構。

這場會議最初以腦力激盪形式展開，核心貢獻者有機會向 React Native 團隊提出所有關於新架構的疑問。這種面對面的反饋循環對雙方都至關重要——既讓核心貢獻者釐清疑問，也讓 React Native 團隊收集意見。部分共享的反饋與顧慮將在 React Native 0.71 版本中實現。

接著我們實際進行了多個函式庫的新架構遷移。在此會議期間，我們啟動了數個社群套件的遷移流程，例如 `react-native-document-picker`、`react-native-store-review` 和 `react-native-orientation`。

提醒您，若您也在遷移函式庫並需要支援，請聯繫我們在 GitHub 上的[新架構工作小組](https://github.com/reactwg/react-native-new-architecture)。

### React Native Monorepo

目前發布新版 React Native 並非易事。React Native 是 NPM 上下載量最高的套件之一，我們必須確保發布流程順暢。

因此我們計劃重構 `react-native` 代碼庫並實作**Monorepo RFC**（[#480](https://github.com/react-native-community/discussions-and-proposals/pull/480)）。

會議中，我們先收集每位貢獻者的意見進行腦力激盪，這至關重要——我們需要演進代碼庫的同時，盡量減少對下游依賴項的破壞性變更。

接著我們從兩個方向著手：首先擴充持續整合基礎設施以支援 monorepo，在測試環境中加入 [Verdaccio](https://verdaccio.org/)；隨後開始為多個套件重新命名與添加作用域（scope），共產出 6 項具體貢獻。

您可透過此[統整議題](https://github.com/facebook/react-native/issues/34692)追蹤進度，我們期待在不久的將來分享更多成果。

### Metro Web 與生態系統對齊

我們的 JavaScript 打包工具 [Metro](https://github.com/facebook/metro) 是 React Native 開發體驗的基礎核心，我們必須確保它能與 JS 生態系的最新標準相容。

本場會議聚焦於討論如何強化 Metro 功能集，以更好地支援網頁使用情境並與 npm 及打包工具生態系整合。主要討論兩大方向：

#### 1. 採用 `"exports"`（[套件入口點](https://nodejs.org/api/packages.html#package-entry-points)）規範

根據 [Node.js 文件](https://nodejs.org/api/packages.html#package-entry-points)：

<!-- alex ignore clearly -->

:::info
["exports"](https://nodejs.org/api/packages.html#exports) 提供了現代化的 ["main"](https://nodejs.org/api/packages.html#main) 替代方案，允許定義多個入口點、支援跨環境的條件式解析，並**禁止使用 ["exports"](https://nodejs.org/api/packages.html#exports) 定義之外的任何入口點**。這種封裝機制讓模組作者能明確定義套件的公開介面。
:::

採用 `"exports"` 規範潛力巨大。會議中我們辯論了如何用 `"exports"` 處理[平台特定代碼](/docs/platform-specific-code#platform-specific-extensions)。綜合考量後，我們提出了一個幾乎無破壞性的 `"exports"` 實作計劃：為 Metro 解析器新增 `"strict"` 與 `"non-strict"` 模式，並討論如何透過 [builder-bob](https://github.com/callstack/react-native-builder-bob) 讓函式庫創作者無痛採用嚴格模式。

這場討論產出以下成果：

1. 一份關於 Metro 如何與 React Native 協同處理套件導出的 [RFC](https://github.com/react-native-community/discussions-and-proposals/pull/534)。
2. 一份提議 Node.js 將 "react-native" 納入社群條件的 [RFC](https://github.com/nodejs/node/pull/45367)。

#### 2. 網頁與打包工具生態系

Metro 團隊分享了與 Expo 合作進展，並計劃延續此模式來實現未來的程式碼分割與樹搖優化支援。我們再次討論了 ES 模組支援，並考量未來可能的功能如 Yarn PnP 和網頁端的輸出優化。探討了 Metro 核心如何與 Jest 共享邏輯和資料結構，以及進一步重用的可能性。

開發者們提出了關於程式碼分割與第三方工具整合的深刻用例，這促使我們討論 Metro 的擴展點改進與現有文件強化。

這些討論為隔日簡化發布流程的議程奠定了良好基礎。

### Metro 簡化發布流程

如前所述，發布 React Native 並非易事。

當需要同步發布 React Native、React Native CLI 和 Metro 時，複雜度更高。這些工具相互依存——React Native 和 CLI 都依賴 Metro，這使得任一套件的新版本發布都會產生摩擦。

目前我們透過直接溝通與協調發布來管理，但仍有改進空間。

本議程中，我們重新審視了 React Native、Metro 與 CLI 之間的**依賴關係**。發現當初 [「Lean Core」計劃](https://github.com/react-native-community/discussions-and-proposals/issues/6) 將 CLI 從 React Native 抽離時，某些設計決策導致這兩個專案形成相互依賴且功能重複。當時的決策雖合理且讓 CLI 團隊能快速迭代，

現在正是重新檢視的時機，結合兩團隊經驗找出解決方案。最終決議由 Metro 團隊接管 [`@react-native-community/cli-plugin-metro`](https://github.com/react-native-community/cli/tree/main/packages/cli-plugin-metro) 開發，暫時將其移回 React Native 核心，後續很可能併入 Metro monorepo。

![](/blog/assets/core-contributor-summit-2022.jpg)

除了在白板上耗時三小時繪製套件依賴圖外，最大收穫是 CLI 與 Metro 團隊深入交流彼此的問題、經驗與計劃，達成更深入的理解。

若非實際會面，我們無法達成此層級的合作。

---

我們仍驚嘆於短短數日的密集交流竟能催生如此大量的知識共享與想法碰撞。本次峰會播下的種子，將助力重塑 React Native 生態系。

再次感謝 [Callstack](https://www.callstack.com/) 的場地支援，以及所有參與 2022 核心貢獻者峰會的夥伴。

若您有意參與 React Native 開發，請加入我們的開放計劃並閱讀官網的[貢獻指南](https://reactnative.dev/contributing/overview)。期待未來能與您線下相會！