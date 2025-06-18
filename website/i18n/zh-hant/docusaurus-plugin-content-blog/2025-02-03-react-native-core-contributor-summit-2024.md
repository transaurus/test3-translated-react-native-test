---
title: 'React Native Core Contributor Summit 2024 Recap'
authors: [thymikee, szymonrybczak, mojavad, stmoy]
image: https://raw.githubusercontent.com/facebook/react-native-website/9915d9c0b32ef348958c8119f6e83e571c1c0ba3/website/static/blog/assets/react-native-core-contributor-summit-2024-1.jpeg
tags: [engineering]
date: 2025-02-03
---

每年，React Native 社群的核心貢獻者都會與 React Native 團隊聚首，共同規劃這個專案的發展方向。

去年也不例外——只有一個小變化。我們通常會在 [React Universe Conf](https://www.reactuniverseconf.com)（前身為 React Native EU）前一天於 [Callstack](https://www.callstack.com/open-source) 位於弗羅茨瓦夫的總部舉行會議。2024 年，我們從過往經驗中學習，將高峰會延長為連續兩天，以便有更多非結構化的交流時間。

![all-participants](/blog/assets/react-native-core-contributor-summit-2024-1.jpeg)

<!--truncate-->

這項年度傳統已成為貢獻者分享見解與表達關切的寶貴機會，同時也讓核心團隊能向 React Native 生態系的關鍵貢獻者——包括合作企業、獨立函式庫作者與夥伴們——分享計畫並收集反饋。

我們將高峰會分為兩大主題軌道，涵蓋以下議題：

- [版本發布](#releases)
- [新架構之後的下一步？](#whats-next-after-the-new-architecture)
- [原生模組的 Web APIs 規範](#web-apis-for-native-modules)
- [LeanCore 2.0](#leancore-20)
- [Nitro 模組——透過將 props 暴露為 jsi::Values 解除視圖元件限制](#nitro-modules---unblocking-view-components-by-exposing-props-as-jsivalues)
- [樹外平台與 CocoaPods](#out-of-tree-platforms--cocoapods)
- [桌面端的 React Native](#react-native-on-desktop)

在這篇部落格中，我們想讓您先睹為快這場聚會的成果。

## 版本發布

我們深入討論了 React Native 的發布流程。核心團隊高度認同讓 Meta 外部貢獻者參與版本發布的價值，並強調每日構建（nightly releases）的重要性——這對 React Native visionOS 等樹外平台、函式庫維護者（如 Reanimated）和框架（如 Expo）尤其有益。關於發布頻率，與會者意見分歧：部分成員希望加快發布節奏以迅速推送修復，另一些則擔憂這對第三方函式庫與升級作業的衝擊。

我們也集思廣益，探討如何減少非預期的破壞性變更，並改善 React Native 與第三方依賴套件之間相容性的溝通機制。

這場會議充分展現了管理 React Native 版本發布的複雜性，以及考量整個生態系各環節需求時，這個議題的敏感性。

## 新架構之後的下一步？

隨著新架構已穩定發布，我們討論了接下來的重點方向：什麼可能成為下一個重大里程碑？討論圍繞以下主題展開：

- **網頁相容性**——討論圍繞著 React Strict DOM 專案的方向，該專案應被視為暫時的 polyfill，而 Xplat 團隊將在 React Native 核心中實現真正的跨平台功能。
- **穩定核心 API**——我們發現需要更多共識，這對應用開發者、函式庫作者和樹外平台意味著什麼。例如，可能需要從共享的 C++ 程式碼庫中提取 iOS 和 Android 的平台原生邏輯。這部分內容在 LeanCore 2.0 的討論中有所涵蓋。
- **舊架構支援**——正如預期，團隊確認基於並行渲染的 React 19 新功能將無法在舊架構中運作。新功能主要針對新架構。由於 React 19 發布時程的阻礙，目前仍不清楚如何劃分新舊架構支援的功能界線。
- **React Native 第三方函式庫**——現今函式庫作者可以使用 TurboModules、ExpoModules，以及最近的 NitroModules 來實現橋接原生平台功能的相同目標。我們需要更好的文件來說明如何做到這一點。
- **Brownfield 文件**——在峰會期間，官方關於將 React Native 整合到原生應用中的文件已經相當過時。此後，團隊已跟進並為 Android 和 iOS 提供了更新且更簡單的文件。
- **Metro web 的 Tree-shaking**——核心 Metro 團隊願意合併 Expo 團隊在這方面的工作。

## 原生模組的 Web API

這場會議專注於微軟提出的 RFC，其核心思想是將一部分 Web API 引入 React Native。這旨在通過利用熟悉的 API 來增強 React Native 的可擴展性，並吸引更多網頁開發者。這將開放大量現有的開源網頁函式庫，這些函式庫目前沒有明確的 React Native 支援。

![web-apis](/blog/assets/react-native-core-contributor-summit-2024-2.jpeg)

標準化 Web API 規範不僅有益，而且對 React Native 的成長至關重要，並與我們的「多平台」願景和 react-strict-dom 專案高度契合。網頁通過其規範提供了一個統一的接口，而 React Native 社群模組目前缺乏這一點。微軟已經確定了約 200 個核心 Web API，可以首先為他們支援的平台實現：iOS、Android、Windows 和 macOS。

我們鼓勵函式庫開發者在可能的情況下將其 API 與網頁規範對齊，因為這種標準化將提升跨平台的程式碼可攜性和開發者體驗。

雖然這項提案對 React Native 的未來似乎有益，但我們仍在討論下一步該如何推進。我們注意到的一個問題是 API 的治理，以及它們是否需要與平台實現分開存放在不同的儲存庫中。另一個問題是，如果特定平台允許的行為不符合 W3C 的官方規範，該如何處理。我們需要找出如何避免捆綁不必要的模組，例如通過 Babel 插件。更不用說這項計劃的範圍相當龐大。

會議的結論強化了兩點：首先，React Native 社群在可能的情況下採用網頁相容規範方面有強烈的共識。其次，我們需要為這些 Web API 實現如何為不同平台單獨維護制定清晰的技術策略。微軟與 Callstack 可以合作完善原始的 RFC，並作為社群倡議為少量 API 提供概念驗證實現。這種漸進式方法將幫助我們在擴大範圍之前驗證設計和開發者體驗。

## LeanCore 2.0

2019 年，React Native 團隊啟動了 LeanCore 計劃，目標是解決 React Native 核心的覆蓋範圍問題，並減少過時和遺留的 API 和元件。自那時起，React Native 的元件和 API 範圍早已需要另一輪清理。

如今，有許多元件沒有積極維護，且有更好的社群替代方案。此外，還有一些重複的元件，最終應為了可維護性而進行整合。

在 API 方面，許多 JS 層的 API 與原生 iOS 和 Android 實現綁定，而不是真正平台無關。例如，Pressable 有像 `android_disableSound` 和 `android_ripple` 這樣的屬性。理想情況下，React Native 元件應具有最小的 API 範圍，且不與任何特定平台綁定。

隨著樹外平台（Out-of-Tree platforms）的成長與生態系採用率提升，需要建立一條路徑來精簡 React Native 核心的元件與 API 介面，減輕核心團隊的負擔，同時讓樹外平台與函式庫維護者能更輕鬆地保持同步更新。

此舉還有額外效益：能降低初學者學習 React Native 的門檻，因為減少了重複元件與需要避開的「陷阱」。當存在更優質的社群替代方案時，可引導開發者優先採用這些替代方案。

會議中我們討論了以下重點：

- Lean Core 的高層次動機與對各方的益處（開發者、函式庫維護者、Meta）
- 彙整實際生產環境 React Native 應用中的元件使用狀況
- 判定哪些元件適合從核心移除的標準
- 執行 Lean Core 2.0 的具體行動方案：
  - 元件棄用的高層次流程
  - 處理 Meta 內部仍在使用但已有更好社群替代方案的元件

下一步將由核心貢獻者組成小組，負責收集更多遙測數據、評估社群替代方案，並起草詳細變更提案的 RFC 文件。

## Nitro 模組 - 透過 jsi::Value 暴露屬性來解除視圖元件限制

近期 Marc Rousavy 提出 Nitro 模組作為建立原生模組的新方案。此技術採用實驗性 C++/Swift 互通機制，並包含多項能提升特定情境效能的強化功能。但在此次會議中，我們深入探討了 Nitro 模組與現有 TurboModules 之間的各種權衡取捨。

雖然 Nitro 模組具備效能優勢，但也存在需考量的限制。例如實驗性互通功能可能帶來 TurboModules 所沒有的複雜度或相容性問題。我們聚焦於這些權衡點，以及將部分改進上遊整合至 React Native 核心的可能性，讓所有開發者都能受益於高效能模組。

## 樹外平台與 CocoaPods 管理

樹外平台展現了 React Native 的完整潛力，讓我們能在行動裝置、桌面端甚至 VR/XR 設備等不同平台共享同一套 JS 程式碼。但目前建立這類平台並非易事，實際上缺乏關於開發與維護標準的指導方針。此外 React Native 核心某種程度上仍與 Android/iOS 平台綁定。未來我們應追求所有平台地位平等，透過統一 API 與 C++/JS 核心整合的願景。

![樹外平台](/blog/assets/react-native-core-contributor-summit-2024-3.jpeg)

與會的各平台維護者共同探討了當前痛點、面臨的挑戰，以及如何統一樹外平台的開發與維護流程。

會議另一重點是討論 CocoaPods 及其未來發展。近期 CocoaPods 團隊宣布進入維護模式，將不再推出重大更新。我們評估了各種替代方案的優缺點，並研議遷移路徑。

## 桌面端 React Native 發展

微軟的 Steven 與 Saad（react-native-windows 和 react-native-macos 維護者）主持會議收集桌面平台相關反饋。討論主題包含：如何提升 React Native 桌面端採用率（例如在 Visual Studio 建立專屬工作流，或透過 Nx 整合桌面支援），以及改善 Expo 支援這個長期阻礙採用的痛點。

macOS 與 Windows 在社群模組的可用性上存在顯著差異，主要原因在於 iOS 程式碼大多能與 macOS 相容，而 React Native for Windows (RNW) 則需要專門的實作。微軟團隊在為 RNW 開發新架構時，發現 C++ 模組具有跨平台共享程式碼的潛力，這有望減輕開發桌面平台的負擔。值得注意的是，社群方面 Software Mansion 正積極為其熱門模組（如 React Native Screens、Gesture Handler 和 Reanimated）新增桌面平台支援。

---

我們仍驚嘆於短短幾天的密集交流竟能促成如此大量的知識共享與想法碰撞。本次峰會為多項倡議播下種子，這些倡議將協助我們改進並重塑 React Native 生態系。

若您有意參與 React Native 開發，請務必加入我們的開放倡議並閱讀官網的[貢獻指南](https://reactnative.dev/contributing/overview)。期待未來也能與您線下相會！