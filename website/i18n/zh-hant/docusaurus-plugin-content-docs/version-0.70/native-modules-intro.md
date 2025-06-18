---
id: native-modules-intro
title: Native Modules Intro
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

有時 React Native 應用需要存取 JavaScript 預設無法使用的原生平台 API，例如存取 Apple Pay 或 Google Pay 的原生 API。也許您想重用現有的 Objective-C、Swift、Java 或 C++ 函式庫，而不必在 JavaScript 中重新實作，或者為圖像處理等任務編寫高效能、多線程的代碼。

NativeModule 系統將 Java/Objective-C/C++（原生）類別的實例暴露給 JavaScript（JS）作為 JS 物件，從而允許您從 JS 中執行任意的原生代碼。雖然我們不預期此功能會成為常規開發流程的一部分，但其存在至關重要。如果 React Native 沒有導出您的 JS 應用所需的本機 API，您應該能夠自行導出！

## 原生模組設定

有兩種方式可以為您的 React Native 應用編寫原生模組：

1. 直接在您的 React Native 應用 iOS/Android 專案中
2. 作為一個 NPM 套件，可以作為依賴項安裝到您的/其他 React Native 應用中

本指南將首先引導您在 React Native 應用中直接實作一個原生模組。然而，您在以下指南中構建的原生模組可以作為 NPM 套件分發。如果您有興趣這樣做，請查看 [將原生模組設定為 NPM 套件](native-modules-setup) 指南。

## 開始使用

在以下部分中，我們將引導您完成在 React Native 應用中直接構建原生模組的指南。作為前提條件，您需要一個 React Native 應用來進行操作。如果您還沒有，可以按照 [這裡](getting-started) 的步驟設定一個 React Native 應用。

想像您想要從 React Native 應用中的 JavaScript 存取 iOS/Android 原生日曆 API 以創建日曆事件。React Native 並未公開一個 JavaScript API 來與原生日曆函式庫通信。然而，通過原生模組，您可以編寫與原生日曆 API 通信的原生代碼。然後，您可以通過 React Native 應用中的 JavaScript 調用該原生代碼。

在以下部分中，您將為 [Android](native-modules-android) 和 [iOS](native-modules-ios) 創建這樣一個日曆原生模組。