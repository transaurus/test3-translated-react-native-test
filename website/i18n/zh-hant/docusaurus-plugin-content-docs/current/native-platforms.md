---
id: native-platform
title: Native Platform
---

您的應用程式可能需要存取 react-native 或社群維護的數百個[第三方函式庫](https://reactnative.directory/)中未直接提供的平台功能。也許您想從 JavaScript 執行環境重複使用現有的 Objective-C、Swift、Java、Kotlin 或 C++ 程式碼。無論原因為何，React Native 提供了一套強大的 API，可將您的原生程式碼與 JavaScript 應用程式碼連接起來。

本指南介紹：

- **原生模組 (Native Modules):** 沒有使用者介面 (UI) 的原生函式庫。例如持久化儲存、通知、網路事件。這些功能可透過 JavaScript 函式和物件供使用者存取。
- **原生元件 (Native Components):** 原生平台視圖、小工具和控制項，可透過 React 元件在應用程式的 JavaScript 碼中使用。

:::note
您可能之前熟悉以下內容：

- [舊版原生模組](./legacy/native-modules-intro)；
- [舊版原生元件](./legacy/native-components-android)；

這些是我們已棄用的原生模組和元件 API。由於我們的互操作層，您仍可在新架構中使用許多這些舊版函式庫。您應考慮：

- 使用替代函式庫，
- 升級至支援新架構的較新函式庫版本，或
- 自行將這些函式庫移植至 Turbo 原生模組或 Fabric 原生元件。

:::

1. 原生模組
   - [Android 與 iOS](turbo-native-modules.md)
   - [跨平台 C++](the-new-architecture/pure-cxx-modules.md)
   - [進階：自訂 C++ 類型](the-new-architecture/custom-cxx-types.md)
2. Fabric 原生元件
   - [Android 與 iOS](fabric-native-components.md)