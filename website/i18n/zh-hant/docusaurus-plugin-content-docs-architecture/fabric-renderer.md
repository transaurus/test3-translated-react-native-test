---
id: fabric-renderer
title: Fabric
---

Fabric 是 React Native 的新渲染系統，為舊版渲染系統的概念性演進。其核心原則在於將更多渲染邏輯統一至 C++、提升與[宿主平台](architecture-glossary.md#host-platform)的互通性，並為 React Native 開啟新功能。開發始於 2018 年，至 2021 年 Facebook 應用中的 React Native 已全面採用新渲染器。

本文件概述[新渲染器](architecture-glossary.md#fabric-render)及其概念，避免平台特定細節且不含程式碼片段或指引。內容涵蓋關鍵概念、動機、優勢，以及不同情境下渲染管線的綜覽。

## 新渲染器的動機與優勢

此渲染架構旨在實現舊版架構無法達成的更佳使用者體驗，例如：

- 透過提升[宿主視圖](architecture-glossary.md#host-view-tree-and-host-view)與 React 視圖的互通性，渲染器能同步測量並渲染 React 介面。舊版架構中，React Native 布局為非同步，導致嵌入宿主視圖時產生布局「跳動」問題。
- 支援多優先級與同步事件，使渲染器能優先處理特定使用者互動以確保即時響應。
- [與 React Suspense 整合](https://reactjs.org/blog/2019/11/06/building-great-user-experiences-with-concurrent-mode-and-suspense.html)，實現更直觀的 React 應用資料獲取設計。
- 在 React Native 啟用 React [並行功能](https://github.com/reactwg/react-18/discussions/4)。
- 更易實作 React Native 的伺服器端渲染。

新架構亦在程式碼品質、效能與擴展性帶來優勢：

- **型別安全**：透過程式碼生成確保 JavaScript 與[宿主平台](architecture-glossary.md#host-platform)間的型別安全。以 JavaScript 元件宣告為唯一來源，生成 C++ 結構體儲存屬性，若屬性不匹配將觸發建置錯誤。
- **共享 C++ 核心**：渲染器以 C++ 實作，核心程式碼跨平台共享，提升一致性並簡化新平台的適配。
- **更佳宿主平台互通性**：同步且執行緒安全的布局計算，改善嵌入宿主元件至 React Native 的使用體驗，意味著更易整合需同步 API 的宿主平台框架。
- **效能提升**：跨平台實作的渲染系統，使各平台皆能受益於單一平台痛點驅動的改進。例如視圖扁平化原為 Android 的效能解方，現預設支援 Android 與 iOS。
- **一致性**：跨平台的新渲染系統更易維持多平台一致性。
- **更快啟動**：宿主元件預設採用延遲初始化。
- **減少 JavaScript 與宿主平台間的資料序列化**：舊版 React 需將資料序列化為 JSON 傳輸，新渲染器透過 [JavaScript 介面 (JSI)](architecture-glossary.md#javascript-interfaces-jsi) 直接存取 JavaScript 值來優化傳輸效率。