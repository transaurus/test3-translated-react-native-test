---
title: Helping migrate React Native libraries to the New Architecture
authors: [cipolleschi]
tags: [announcement]
date: 2022-06-16
---

**簡而言之**：我們正在完善支援 React Native 新架構的資源。我們已發布兩個儲存庫來協助遷移應用程式（[RNNewArchitectureApp](https://github.com/react-native-community/RNNewArchitectureApp)）和函式庫（[RNNewArchitectureLibraries](https://github.com/react-native-community/RNNewArchitectureLibraries)）。同時我們正在更新官網的[新架構指南](https://github.com/facebook/react-native-website/pull/3037)，並創建了[GitHub 工作小組](https://github.com/reactwg/react-native-new-architecture/discussions)來解答新架構相關問題。

<!--truncate-->

## 引言

本文將分享關於遷移工具和資源的最新進展，協助您將**原生模組**和**原生元件**升級為新架構對應的**TurboModule**與**Fabric 元件**。

React Native 開發者廣泛使用開源函式庫來構建應用程式。為建立完整且一致的生態系統，這些函式庫必須完成遷移，才能讓所有人受益於新架構帶來的效能提升與新功能。

以下是我們為支援函式庫開發者遷移至新架構所進行的措施：

- **文件**：我們正在擴充官網的[新架構指南](https://github.com/facebook/react-native-website/pull/3037)，涵蓋更多新架構概念與元件開發方法。
- **遷移範例**：我們建立了兩個示範儲存庫，分別展示如何將 React Native 應用遷移至新架構（[RNNewArchitectureApp](https://github.com/react-native-community/RNNewArchitectureApp)），以及如何開發同時兼容兩種架構的**Fabric 元件**和**TurboModule**（[RNNewArchitectureLibraries](https://github.com/react-native-community/RNNewArchitectureLibraries)）。
- **支援**：今年初我們成立了專屬的[GitHub 工作小組](https://github.com/reactwg/react-native-new-architecture/discussions)，用於討論新架構相關問題。

本文將深入解析這些資源，並詳細說明如何有效運用。最後我們將提供當前主流 React Native 函式庫的遷移狀態概覽。

### 文件

過去六個月，我們新增了[採用新架構指南](https://github.com/reactwg/react-native-new-architecture#guides)與[Fabric 架構深度解析](/architecture/overview)。未來計劃擴充內容，包含 TurboModule 開發、CodeGen 解析等主題，預計在 0.70 版本發布時更新。

當前**新架構指南**涵蓋如何正確遷移[應用程式](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/enable-apps.md)與[函式庫](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/enable-libraries-prerequisites.md)以支援新架構。

若您想追蹤指南進展或提供反饋，可關注[此](https://github.com/facebook/react-native-website/pull/3037)拉取請求。

### 遷移範例

為方便開發者參照實作，我們準備了兩個範例儲存庫。

#### RNNewArchitectureApp

[此儲存庫](https://github.com/react-native-community/RNNewArchitectureApp)展示如何將 React Native 0.67 版本的應用程式、原生模組與原生元件從舊架構遷移至新架構的最新版本。每個提交對應一個獨立的遷移步驟。

<figure>
    <img src="/blog/assets/new-arch-example-steps-to-migrate-an-app.png" alt="Example steps to migrate an app" />
    <figcaption>Commit list for a migration in the RNNewArchitectureApp repository</figcaption>
</figure>

儲存庫結構如下：

- **main**分支僅包含 README.md 文件，用於導覽其他分支。
- 多個遷移分支展示從特定 RN 版本到另一版本的遷移過程。

部分遷移分支還包含一個 **RUN.md** 文件，以更易讀的方式描述每個提交中應用的具體步驟。

我們計劃讓這個範例與最新的穩定版本保持同步，針對每個即將發布的 React Native 小版本新增遷移步驟。如果您發現任何步驟存在問題，請在該存儲庫中提交 issue。這種做法將持續到我們合理認為大多數 React Native 用戶已遷移至新架構為止。

#### RNNewArchitectureLibraries

類似地，[這個存儲庫](https://github.com/react-native-community/RNNewArchitectureLibraries)提供了逐步指南，說明如何創建 **TurboModule** 和 **Fabric 組件**，重點在於確保新架構與舊架構之間的向後兼容性。

該存儲庫的組織方式與前一個類似：

- **main** 分支沒有代碼，僅包含一個 README.md 文件，用於宣傳其他分支。
- 其他分支展示如何開發 **TurboModules** 和 **Fabric Components**。

我們計劃讓這個範例隨著 React Native 的新版本更新，特別是影響庫開發的版本，並新增更多關於如何使用高級功能的範例（例如：實現命令、事件發射器、自定義狀態）。如果您發現錯誤，請在範例存儲庫中提交 issue。

### 支持

我們創建了一個專用的[工作組](https://github.com/reactwg/react-native-new-architecture)，為社區提供提問和獲取新架構更新的空間。如果您是庫維護者，這是一個寶貴的資源，可以找到問題的答案，並讓我們了解您的需求。要加入，請遵循[這些說明](https://github.com/reactwg/react-native-new-architecture#how-to-join-the-working-group)。歡迎所有人參與。

工作組分為以下幾個部分：

- [公告](https://github.com/reactwg/react-native-new-architecture/discussions/categories/announcements)：分享 RN 新架構推出的里程碑和重要更新的地方
- [深度探討](https://github.com/reactwg/react-native-new-architecture/discussions/categories/deep-dive)：討論深度探討和技術特定主題的地方
- [文檔](https://github.com/reactwg/react-native-new-architecture/discussions/categories/documentation)：討論新架構文檔和遷移材料的地方
- [庫](https://github.com/reactwg/react-native-new-architecture/discussions/categories/libraries)：討論第三方庫及其遷移至新架構的故事的地方
- [問答](https://github.com/reactwg/react-native-new-architecture/discussions/categories/q-a)：向社區尋求新架構主題幫助的地方
- [發布](https://github.com/reactwg/react-native-new-architecture/discussions/categories/releases)：討論特定版本的錯誤和構建問題的地方

要有效使用這個工作組：

- **確保您的庫列在[庫](https://github.com/reactwg/react-native-new-architecture/discussions/categories/libraries)部分**。這將幫助我們分享您的庫遷移的狀態更新，並幫助我們了解庫作者面臨的困難，以便更好地支持您。
- **如果您遇到阻礙並需要支持，請利用[問答](https://github.com/reactwg/react-native-new-architecture/discussions/categories/q-a)部分**。我們的團隊和社區專家會監控並盡力提供支持。
- **關注其他部分可能影響您的主題**。新版本可能正好引入了您需要的 API。您可以通過 GitHub 訂閱特定討論。

我們計劃支持這個工作組，直到**新架構**默認啟用且所有主要庫都已遷移至該架構為止。

### 熱門庫的遷移狀態

庫維護者一直在[工作組中](https://github.com/reactwg/react-native-new-architecture/discussions/categories/libraries)與我們分享他們的遷移進展，我們希望為您提供一個快速概覽：

- [react-native-gesture-handler](https://github.com/reactwg/react-native-new-architecture/discussions/15): ✅ 已完成遷移
- [react-native-navigation](https://github.com/reactwg/react-native-new-architecture/discussions/17): 🏃‍♂️ 進行中
- [react-native-pager-view](https://github.com/reactwg/react-native-new-architecture/discussions/16): 🏃‍♂️ 進行中
- [react-native-reanimated](https://github.com/reactwg/react-native-new-architecture/discussions/14): ✅ 已完成遷移。正在進行效能測試與分析
- [react-native-screens](https://github.com/reactwg/react-native-new-architecture/discussions/13): 🏃‍♂️ 進行中
- [react-native-slider](https://github.com/reactwg/react-native-new-architecture/discussions/38): 🎬 已開始
- [react-native-template-new-architecture](https://github.com/reactwg/react-native-new-architecture/discussions/21): ✅ 已完成遷移。逐步採用/測試更多配套函式庫
- [react-native-template-typescript](https://github.com/reactwg/react-native-new-architecture/discussions/22): ✅ 已完成遷移
- [react-native-webview](https://github.com/reactwg/react-native-new-architecture/discussions/19): 🎬 已開始

## 後續步驟

我們致力於支持 React Native 社群採用新架構。具體而言，我們將持續：

- 在**工作小組**中提供最大程度的支援
- 在 **RNNewArchitecture** 儲存庫中提供更多關於如何透過新架構實現卓越成果的範例
- 提供清晰且最新的**新架構**文件
- 在**工作小組**中追蹤重要 React Native 函式庫的遷移狀態
- 簡化開發者的遷移路徑

此外，React Native 0.69 將為採用新架構的應用程式和函式庫開發者帶來改進的開發者體驗。您可以在[此處](https://github.com/reactwg/react-native-releases/discussions/21)找到更多關於 0.69.0 版本的資訊。

我們對於將與**新架構**共同建構的未來感到興奮！