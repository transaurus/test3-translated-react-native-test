---
title: Releasing 0.56
author: Lorenzo Sciandra
authorTitle: Core Maintainer & React Native Developer at Drivetribe
authorURL: 'https://github.com/kelset'
authorImageURL: 'https://avatars2.githubusercontent.com/u/16104054?s=460&v=4'
authorTwitter: kelset
tags: [announcement, release]
---

眾所期待的 React Native 0.56 版本現已正式發布 🎉。本篇部落格文章將重點介紹此版本中的一些[變更內容](https://github.com/react-native-community/react-native-releases/blob/master/CHANGELOG.md#highlights)，同時也藉此說明自三月以來我們的工作重點。

### 重大變更的兩難，或「何時發布？」

[貢獻者指南](https://github.com/facebook/react-native/blob/master/CONTRIBUTING.md)說明了所有 React Native 變更需經過的整合流程。該專案由[眾多不同工具](https://github.com/facebook/react-native-website/issues/370)組成，需要協調與持續維護才能確保一切正常運作。再加上活躍的開源社群不斷回饋貢獻，您將能體會這個專案規模之龐大。

由於 React Native 的廣泛採用，重大變更必須格外謹慎處理，且流程不如我們預期順暢。核心團隊決定跳過四月和五月的發布，以便整合並測試一系列新的重大變更。過程中我們透過[專屬社群溝通管道](https://github.com/react-native-community/react-native-releases/issues/14)，確保 2018 年六月發布的穩定版 (`0.56.0`) 能讓耐心等待的使用者順利採用。

`0.56.0` 完美嗎？不，如同所有軟體：但我們已達到「等待更高穩定性」與「測試結果成功可推進」的平衡點，認為現在是發布的適當時機。此外，我們也知悉最終 `0.56.0` 版本中仍有[部分](https://github.com/facebook/react-native/issues/19955)[未解決](https://github.com/facebook/react-native/issues/19827)[的](https://github.com/facebook/react-native/issues/19763)[問題](https://github.com/facebook/react-native/issues/19859)。多數開發者應能順利升級至 `0.56.0`。若您因上述問題受阻，歡迎參與討論，我們期待與您共同解決這些問題。

您可將 `0.56.0` 視為邁向更穩定框架的基石：可能需要一兩周廣泛採用後才能磨平所有邊緣案例，但這將使 2018 年七月發布的 `0.57.0` 版本更加完善。

我們要特別感謝[共 67 位貢獻者提交的 818 次提交](https://github.com/facebook/react-native/compare/v0.55.4...v0.56.0-rc.4) (!)，這些努力將協助您打造更優質的應用程式 👏。

現在，閒話少說...

## 重大變更

### Babel 7

如您所知，讓我們能使用最新 JavaScript 功能的轉譯工具 Babel 即將[升級至 v7](https://babeljs.io/blog/2017/12/27/nearing-the-7.0-release)。由於新版本帶來重要變更，我們認為現在是升級的好時機，讓 [Metro](https://github.com/facebook/metro) 能[充分利用其改進功能](https://github.com/facebook/metro/issues/92)。

若您升級時遇到問題，請參考[相關文件章節](https://new.babeljs.io/docs/en/next/v7-migration.html)。

### 現代化 Android 支援

在 Android 方面，周邊工具鏈已大幅更新。我們已升級至 [Gradle 3.5](https://github.com/facebook/react-native/commit/699e5eebe807d1ced660d2d2f39b5679d26925da)、[Android SDK 26](https://github.com/facebook/react-native/commit/065c5b6590de18281a8c592a04240751c655c03c)、[Fresco 至 1.9.0 版與 OkHttp 至 3.10.0 版](https://github.com/facebook/react-native/commit/6b07602915157f54c39adbf0f9746ac056ad2d13)，甚至將 [NDK API 目標版本設為 API 16](https://github.com/facebook/react-native/commit/5ae97990418db613cd67b1fb9070ece976d17dc7)。這些變更應能無縫運作並帶來更快的建置速度，更重要的是能協助開發者符合下個月生效的 [Google Play 商店新規範](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

特別感謝 [Dulmandakh](https://github.com/dulmandakh) 提交大量 PR 促成這些改動 👏。

未來還有更多相關工作需要推進，您可透過[專屬議題](https://github.com/facebook/react-native/issues/19297)追蹤 Android 支援更新的規劃討論（另有一個關於 [JSC](https://github.com/facebook/react-native/issues/19737) 的獨立議題）。

### 新版 Node、Xcode、React 與 Flow——全面升級！

Node 8 現成為 React Native 的標準環境。雖然先前已進行測試，但隨著 Node 6 進入維護模式，我們正式全面採用。React 也同步更新至 16.4 版，帶來大量修復。

我們將停止支援 iOS 8，[改以 iOS 9 作為最低支援版本](https://github.com/facebook/react-native/commit/f50df4f5eca4b4324ff18a49dcf8be3694482b51)。由於所有能運行 iOS 8 的裝置皆可升級至 iOS 9，此變動應無相容性問題，同時讓我們能移除專為 iOS 8 舊裝置設計的冗餘程式碼。

持續整合工具鏈已更新[至 Xcode 9.4](https://github.com/facebook/react-native/commit/c55bcd6ea729cdf57fc14a5478b7c2e3f6b2a94d)，確保所有 iOS 測試皆在 Apple 提供的最新開發工具環境下執行。

我們升級至 [Flow 0.75](https://github.com/facebook/react-native/commit/6264b6932a08e1cefd83c4536ff7839d91938730) 以採用[廣受開發者好評的新錯誤格式](https://twitter.com/dan_abramov/status/998610821096857602)，並為更多元件加入型別定義。若您尚未在專案中實施靜態型別檢查，建議使用 Flow 在編碼階段即時發現問題，而非等到執行時才除錯。

### 以及其他眾多改進...

例如 YellowBox 已被[全新實現的除錯工具取代](https://github.com/facebook/react-native/commit/d0219a0301e59e8b0ef75dbd786318d4b4619f4c)，大幅提升除錯體驗。

完整更新內容請參閱[版本日誌](https://github.com/react-native-community/react-native-releases/blob/master/CHANGELOG.md)，並務必查閱[升級指南](/docs/upgrading)以避免遷移至新版時發生問題。

---

最後提醒：從本週開始，React Native 核心團隊將恢復舉行每月例會。我們會確保讓大家及時了解會議討論內容，並將各位的反饋納入未來會議考量。

祝各位編碼愉快！

[Lorenzo](https://twitter.com/Kelset)、[Ryan](https://github.com/turnrye) 以及整個 [React Native 核心團隊](https://twitter.com/reactnative)

**附註：** 一如既往地提醒大家，由於仍有大量變更正在進行中，React Native 仍處於 0.x 版本階段——因此升級時請記住，確實可能仍有功能會崩潰或出現問題。在提交 issue 和 PR 時請保持互助精神，並務必遵守我們強制執行的[行為準則](https://code.fb.com/codeofconduct/)：螢幕另一端始終是活生生的人。