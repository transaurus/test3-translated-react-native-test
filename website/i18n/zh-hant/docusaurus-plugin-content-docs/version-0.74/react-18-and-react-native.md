---
id: react-18-and-react-native
title: React 18 & React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';
import NewArchitectureWarning from './\_markdown-new-architecture-warning.mdx';

<NewArchitectureWarning/>

本頁面說明如何透過 React Native 的新架構（New Architecture）來搭配使用 React 18 與 React Native。

> **簡要說明：** 第一個相容於 React 18 的 React Native 版本為 **0.69.0**。若要使用 React 18 的新功能，包括自動批次處理（automatic batching）、`startTransition` 和 `useDeferredValue`，您必須將 React Native 應用程式遷移至新架構。

## React 18 與 React Native 新架構

React 18 引入了[多項新功能](https://reactjs.org/blog/2022/03/29/react-v18.html)，包括：

- 自動批次處理
- 新的嚴格模式行為
- 新鉤子（`useId`、`useSyncExternalStore`）

同時也包含新的並行功能：

- `startTransition`
- `useTransition`
- `useDeferredValue`
- 完整的 Suspense 支援

React 18 的並行功能是建立在新的並行渲染引擎之上。並行渲染是一種幕後新機制，讓 React 能夠同時準備多個版本的 UI。

基於舊架構的 React Native 先前版本**無法**支援並行渲染或並行功能。這是因為舊架構依賴於變更原生樹（native trees），這不允許 React 同時準備多個版本的樹。

幸運的是，新架構從底層開始就是以並行渲染為目標設計，並完全相容於 React 18。這意味著，若要在 React Native 應用程式中升級至 React 18，您的應用程式**需要使用 React Native 的新架構**，包括 Fabric 原生元件（Fabric Native Components）和 Turbo 原生模組（Turbo Native Modules）。

這表示您只要啟用新架構，就能立即使用 React 18 的新功能。由於新的並行功能是透過使用如 `startTransition` 或 `Suspense` 等功能來選擇性啟用，我們預期對於遷移至新架構或建立啟用新架構的新應用程式的使用者來說，React 18 能夠開箱即用，僅需極少變更。

### 舊架構使用者的注意事項

仍在使用舊架構的應用程式，即使 `package.json` 檔案中列有 React 18 作為依賴項，也將以 React 17 模式運行。