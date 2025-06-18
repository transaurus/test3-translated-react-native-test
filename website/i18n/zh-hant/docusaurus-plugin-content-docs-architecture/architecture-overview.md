---
id: architecture-overview
title: Architecture Overview
slug: /overview
---

:::info
歡迎閱讀架構概覽！如果您剛開始使用 React Native，請參考<a href="/docs/getting-started">指南</a>章節。繼續閱讀以了解 React Native 的內部運作機制！

本節內容仍在持續完善中，未來將新增更多資料。請記得定期回來查看最新資訊。
:::

架構概覽旨在分享 React Native 內部運作的原理性概述，主要面向函式庫開發者與核心貢獻者。若您是應用程式開發者，無需熟悉此部分內容也能有效使用 React Native。但閱讀本概覽仍能幫助您深入理解底層機制。歡迎在<a href="https://github.com/reactwg/react-native-new-architecture/discussions/9">工作組討論區</a>分享您的反饋意見。

## 目錄

- [關於新架構](landing-page)
- 渲染系統
  - [Fabric 渲染器](fabric-renderer)
  - [渲染、提交與掛載](render-pipeline)
  - [跨平台實現](xplat-implementation)
  - [視圖扁平化](view-flattening)
  - [線程模型](threading-model)
- 建構工具
  - [捆綁式 Hermes](bundled-hermes)
- [術語表](glossary)