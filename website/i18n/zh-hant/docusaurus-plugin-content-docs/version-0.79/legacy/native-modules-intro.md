---
id: native-modules-intro
title: Native Modules Intro
---

import NativeDeprecated from '@site/versioned_docs/version-0.79/the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

有時 React Native 應用需要存取 JavaScript 預設未提供的原生平台 API，例如存取 Apple 或 Google Pay 的原生 API。或許您想重用現有的 Objective-C、Swift、Java 或 C++ 函式庫，而不必在 JavaScript 中重新實作，或是為影像處理等任務編寫高效能、多執行緒的程式碼。

NativeModule 系統將 Java/Objective-C/C++（原生）類別的實例作為 JS 物件暴露給 JavaScript（JS），從而允許您從 JS 中執行任意原生程式碼。雖然我們不預期此功能會成為常規開發流程的一部分，但其存在至關重要。如果 React Native 未導出您的 JS 應用所需的原生 API，您應該能夠自行導出！

## 原生模組設定

為您的 React Native 應用編寫原生模組有以下幾種方式：

1. 建立可匯入 React Native 應用的本地函式庫。閱讀[建立本地函式庫](local-library-setup)指南以了解更多。
2. 直接在 React Native 應用的 iOS/Android 專案中編寫
3. 作為 NPM 套件發布，供您的或其他 React Native 應用安裝為依賴項。

本指南將首先引導您在 React Native 應用中直接實作原生模組。然而，您在以下指南中建置的原生模組也可以作為 NPM 套件發布。如果您有興趣這樣做，請查看[將原生模組設定為 NPM 套件](native-modules-setup)指南。

## 開始使用

在以下章節中，我們將引導您在 React Native 應用中直接建置原生模組。作為先決條件，您需要一個 React Native 應用來進行開發。如果尚未建立，您可以按照[這裡](../getting-started)的步驟設定 React Native 應用。

想像您希望從 React Native 應用中的 JavaScript 存取 iOS/Android 原生日曆 API 以建立日曆事件。React Native 並未提供與原生日曆函式庫通訊的 JavaScript API。然而，透過原生模組，您可以編寫與原生日曆 API 通訊的原生程式碼。然後，您可以透過 React Native 應用中的 JavaScript 調用該原生程式碼。

在以下章節中，您將為 [Android](native-modules-android) 和 [iOS](native-modules-ios) 建立這樣的 Calendar 原生模組。