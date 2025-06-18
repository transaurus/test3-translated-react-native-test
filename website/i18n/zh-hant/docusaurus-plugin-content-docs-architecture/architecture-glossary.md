---
id: architecture-glossary
title: Glossary
slug: /glossary
---

## 開發者選單

應用內開發者選單（僅在開發版本中可用），提供各種開發與除錯功能的存取。[在文件中了解更多關於開發者選單的資訊](/docs/debugging)。

## Fabric 渲染器

React Native 執行與網頁版 React 相同的框架程式碼。然而，React Native 渲染至通用平台視圖（宿主視圖），而非 DOM 節點（可視為網頁的宿主視圖）。透過 Fabric 渲染器實現了對宿主視圖的渲染。Fabric 讓 React 能與各平台溝通並管理其宿主視圖實例。Fabric 渲染器存在於 JavaScript 中，並以 C++ 程式碼提供的介面為目標。[在此部落格文章中了解更多關於 React 渲染器的資訊。](https://overreacted.io/react-as-a-ui-runtime/#renderers)

## 宿主平台

嵌入 React Native 的平台（例如 Android、iOS、macOS、Windows）。

## 宿主視圖樹（與宿主視圖）

宿主平台中視圖的樹狀表示（例如 Android、iOS）。在 Android 上，宿主視圖是 `android.view.ViewGroup`、`android.widget.TextView` 等的實例，這些是宿主視圖樹的基本構建塊。每個宿主視圖的大小和位置基於由 Yoga 計算的 `LayoutMetrics`，而每個宿主視圖的樣式和內容則基於來自 React Shadow Tree 的資訊。

## JavaScript 介面（JSI）

一種輕量級 API，用於將 JavaScript 引擎嵌入 C++ 應用程式。Fabric 使用它在 Fabric 的 C++ 核心與 React 之間進行通訊。

## Java 原生介面（JNI）

一種[用於編寫 Java 原生方法的 API](https://docs.oracle.com/javase/8/docs/technotes/guides/jni/)，用於在 Fabric 的 C++ 核心與以 Java 編寫的 Android 之間進行通訊。

## React 元件

一個 JavaScript 函數或類別，指示如何創建 React 元素。[在此部落格文章中了解更多關於 React 元件與元素的資訊。](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)

## React 複合元件

具有 `render` 實現的 React 元件，可簡化為其他 React 複合元件或 React 宿主元件。

## React 宿主元件或宿主元件

其視圖實現由宿主視圖提供的 React 元件（例如 `<View>、<Text>`）。在網頁上，ReactDOM 的宿主元件會是像 `<p>` 和 `<div>` 這樣的元件。

## React 元素樹（與 React 元素）

_React 元素樹_ 由 React 在 JavaScript 中創建，由 React 元素組成。_React 元素_ 是一個純 JavaScript 物件，描述螢幕上應顯示的內容。它包括屬性、樣式和子元素。React 元素僅存在於 JavaScript 中，可以表示 React 複合元件或 React 宿主元件的實例化。[在此部落格文章中了解更多關於 React 元件與元素的資訊。](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)

## React Native 框架

React Native 允許開發者使用 [React 編程範式](https://react.dev/learn/thinking-in-react)將應用程式部署到原生目標。React Native 團隊專注於創建**核心 API** 和**功能**，以滿足開發原生應用程式時最通用的使用案例。

將原生應用程式部署到生產環境通常需要一組工具和函式庫，這些工具和函式庫並非 React Native 預設提供，但對於將應用程式部署到生產環境至關重要。這些工具的範例包括：支援將應用程式發布到專用商店，或支援路由和導航機制。

當這些工具和函式庫被收集起來，形成一個建立在 React Native 之上的連貫框架時，我們將其定義為**React Native 框架**。

開源 React Native 框架的一個範例是 [Expo](https://expo.dev/)。

## React 陰影樹（與 React 陰影節點）

_React 陰影樹_由 Fabric 渲染器創建，由 React 陰影節點組成。React 陰影節點是一個物件，代表將被掛載的 React 主機組件，並包含源自 JavaScript 的屬性。它們還包含佈局資訊（x、y、寬度、高度）。在 Fabric 中，React 陰影節點物件存在於 C++ 中。在 Fabric 之前，這些物件存在於移動運行時堆中（例如 Android JVM）。

## Yoga 樹（與 Yoga 節點）

_Yoga 樹_由 [Yoga](https://www.yogalayout.dev/) 用於計算 React 陰影樹的佈局資訊。每個 React 陰影節點通常會創建一個 _Yoga 節點_，因為 React Native 使用 Yoga 來計算佈局。然而，這並非硬性要求。Fabric 也可以創建不使用 Yoga 的 React 陰影節點；每個 React 陰影節點的實現決定了如何計算佈局。