---
title: Hermes as the Default
authors: [micleo]
tags: [announcement, release]
---

# Hermes 作為預設引擎的部落格公告

去年十月，我們[宣佈](/blog/2021/10/26/toward-hermes-being-the-default)已開始著手**將 Hermes 設為所有 React Native 應用程式的預設引擎**。

Hermes 在 Meta 內部為 React Native 提供了巨大價值，我們相信開源社群也將受益。Hermes 專為資源受限設備設計，並針對啟動時間、應用大小和記憶體消耗進行優化。與其他 JS 引擎的關鍵差異在於其能預先將 JavaScript 原始碼編譯為位元組碼。這些預編譯的位元組碼會打包進二進位檔中，省去直譯器在應用啟動時執行此昂貴步驟的需求。

自公告以來，我們投入大量工作改進 Hermes。如今，我們興奮地宣布**React Native 0.70 將預設搭載 Hermes 引擎**。這意味著所有基於 v0.70 的新專案都將預設啟用 Hermes。隨著七月即將展開的部署，我們希望與社群密切合作，確保過渡過程順暢並為所有用戶帶來價值。本篇部落格將說明此變更的預期效果、效能基準測試、新功能等內容。請注意，您無需等待 React Native 0.70 發布才使用 Hermes——可**依照[這些指示](/docs/hermes#enabling-hermes)在現有 React Native 應用中啟用 Hermes**。

請注意，雖然新 React Native 專案將預設啟用 Hermes，但對其他引擎的支援仍會持續。

<!--truncate-->

## 效能基準測試

我們測量了對應用開發者至關重要的三項指標：TTI（可交互時間）、二進位檔大小和記憶體消耗。測試使用 React Native 應用 [Mattermost](https://github.com/mattermost/mattermost-mobile)，並在 2020 年的高端硬體上針對 Android 和 iOS 執行實驗。

- TTI（可交互時間）指從應用啟動到用戶能與之互動的時間長度。在此基準測試中，我們定義為從點擊應用圖示到首屏渲染完成的時間。同時提供 Mattermost 啟動過程的螢幕錄影。
- 二進位檔大小在 Android 上測量為 APK 大小，iOS 上則為 IPA 大小。
- 記憶體消耗數據透過在數分鐘內操作 Mattermost 應用收集，兩引擎下執行的應用操作完全一致。

## Android 基準測試數據

所有 Android 測試均在三星 Galaxy S20 上執行。

<figure>
  <img src="/blog/assets/hermes-default-android-data.png" alt="Android Benchmarking Data" />
</figure>

### TTI 影片

<figure>
  <img src="/blog/assets/hermes-default-android-video.gif" alt="Android TTI Video" />
</figure>

## iOS 基準測試數據

所有 iOS 測試均在 iPhone 12 Pro 上執行。

<figure>
  <img src="/blog/assets/hermes-default-ios-data.png" alt="iOS Benchmarking Data" />
</figure>

### TTI 影片

<figure>
  <img src="/blog/assets/hermes-default-ios-video.gif" alt="iOS TTI Video" />
</figure>

### 減速版 TTI 影片（更清晰展示啟動時間差異）

<figure>
  <img src="/blog/assets/hermes-default-ios-slow-video.gif" alt="iOS Slowed Down TTI Video" />
</figure>

## React Native/Hermes 整合

我們解決了一個長期存在、導致相容性問題且在發布新 React Native 版本時反覆出現的問題：過去 React Native 透過 CocoaPods 和 npm 分發的預編譯二進位檔依賴 Hermes，這可能導致 API 或 [ABI 不相容](https://github.com/react-native-community/discussions-and-proposals/issues/257)。為解決此問題，從 React Native 0.69 開始，Hermes 會與每個 React Native 版本同步構建，確保與各版本完全相容。此舉也創造了更緊密的整合，能加速功能開發或錯誤修復的迭代週期，並讓我們更有信心正確執行 Hermes 的重大變更。更深入的整合變更說明可參閱[此處](https://github.com/facebook/react-native-website/pull/3159/files)。

## iOS Intl 支援

我們已完成 iOS 平台對 [`Intl`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl)（ECMAScript 國際化 API）的實作，該 API 提供廣泛的語言敏感功能。這是長期存在的[缺口](https://github.com/facebook/hermes/issues/23)，導致部分開發者無法使用 Hermes。與 Microsoft 合作開發的 Android 實作已隨 React Native 0.65 發布。透過 React Native 0.70，開發者將在雙平台獲得原生支援。

典型的 `Intl` 實作需要導入大型查找表或 [Unicode CLDR](https://cldr.unicode.org/index) 等資料，但這可能導致二進位檔大小增加多達 6MB。為避免 Hermes 的二進位檔膨脹，我們透過呼叫 iOS 本身提供的 API 來實作 `Intl`，這意味著我們能直接利用 iOS 內建的語言環境與國際化資料。

## 進行中的工作

在持續演進 Hermes 的同時，我們希望讓社群了解當前優先事項：改善開發者體驗，並確保沒有人會因缺乏 JavaScript 語言功能而放棄使用 Hermes。具體而言，我們正在：

- 讓開發者能直接從 Chrome 開發者工具介面執行取樣分析器。
- 新增對 [`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) 的支援，這是社群的長期需求，由於無法透過 polyfill 實現，可能阻礙部分開發者採用 Hermes。
- 新增對 [`WeakRef`](https://github.com/facebook/hermes/issues/658) 的支援，將為開發者提供新的記憶體管理控制選項。

## 總結

Hermes 成為預設引擎標誌著長期旅程的開始。我們正在開發新功能，讓社群能持續多年打造高效應用程式。也鼓勵社群透過 [GitHub 儲存庫](https://github.com/facebook/react-native)回報錯誤、提問、提供反饋或想法！我們已建立 `hermes` 標籤，可用於任何 Hermes 相關的討論。