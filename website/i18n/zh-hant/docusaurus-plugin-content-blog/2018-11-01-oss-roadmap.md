---
title: Open Source Roadmap
author: Héctor Ramos
authorTitle: Engineer at Facebook
authorURL: 'https://hectorramos.com/about'
authorImageURL: 'https://s.gravatar.com/avatar/f2223874e66e884c99087e452501f2da?s=128'
authorTwitter: hectorramos
tags: [announcement]
---

![](/blog/assets/oss-roadmap-hero.jpg)

今年，React Native 團隊致力於大規模的 [React Native 架構重構](https://github.com/react-native-community/discussions-and-proposals/issues/4)。正如 Sophie 在她的 [React Native 現狀文章](/blog/2018/06/14/state-of-react-native-2018) 中提到的，我們已擬定計劃以更好地支持 Facebook 外部蓬勃發展的 React Native 用戶與協作者群體。現在是時候分享更多我們的工作細節了。在此之前，我想先闡明我們對開源版 React Native 的長期願景。

我們對 React Native 的願景是...

- **健康的 GitHub 儲存庫。** 問題與拉取請求能在合理時間內得到處理。
  - 提高測試覆蓋率。
  - 從 Facebook 代碼庫同步的提交不應破壞開源測試。
  - 更多有意義的社群貢獻。
- **穩定的 API，** 讓與開源依賴項的對接更輕鬆。
  - Facebook 使用與開源相同的公開 API。
  - React Native 版本遵循語意化版本控制。
- **活躍的生態系統。** 由社群維護的高品質 ViewManager、原生模組與多平台支援。
- **完善的文檔。** 專注於幫助用戶打造高品質體驗，並提供最新的 API 參考文件。

我們已確定以下重點領域來實現此願景。

## ✂️ 精簡核心

我們的目標是透過移除非核心與未使用的元件來 [減少 React Native 的表面積](https://github.com/react-native-community/discussions-and-proposals/issues/6)。我們將把非核心元件轉交給社群以加速其發展。縮減表面積將使管理對 React Native 的貢獻更加容易。

[`WebView`](https://github.com/react-native-community/discussions-and-proposals/blob/master/proposals/0001-webview.md) 是我們移交給社群的元件範例。我們正在開發一種工作流程，讓內部團隊在這些元件從儲存庫移除後仍能繼續使用。我們已識別出 [數十個其他元件](https://github.com/react-native-community/discussions-and-proposals/issues/6)，將交由社群負責維護。

## 🎁 開源內部工具與 🛠 更新工具鏈

Facebook 產品團隊的 React Native 開發體驗可能與開源社群大不相同。開源社群中流行的工具在 Facebook 內部可能並未使用，而某些情況下 Facebook 團隊已習慣使用外部不存在內部工具。這些差異可能為我們開源新架構工作帶來挑戰。

我們將致力於開源部分內部工具，同時加強對開源社群熱門工具支援。以下是我們將處理的非完整專案清單：

- 開源 JSI 並讓社群能引入自己的 JavaScript 虛擬機，取代現有源自 RN 最初版本的 JavaScriptCore。我們將在未來文章中詳細介紹 JSI，在此期間您可透過 [Parashuram 在 React Conf 的演講](https://www.youtube.com/watch?v=UcqRXTriUVI) 了解更多。
- 支援 Android 64 位元函式庫。
- 啟用新架構下的除錯功能。
- 改進對 CocoaPods、Gradle、Maven 及新 Xcode 建置系統的支援。

## ✅ 測試基礎設施

當 Facebook 工程師發布代碼時，若通過所有測試即被視為可安全合併。這些測試能識別變更是否可能破壞我們自己的 React Native 介面。然而，Facebook 使用 React Native 的方式存在差異，這使我們可能在不知情情況下破壞開源版 React Native。

我們將強化內部測試，確保其執行環境盡可能接近開源環境。這有助於防止破壞這些測試的代碼進入開源版本。我們也將建立基礎設施來改善 GitHub 上核心儲存庫的測試，讓未來的拉取請求能輕鬆包含測試。

結合精簡後的程式碼範圍，這將讓貢獻者能更有信心地快速合併拉取請求 (pull requests)。

## 📜 公開 API

Facebook 將透過與開源相同的公開 API 使用 React Native，以減少非預期的破壞性變更。我們已開始轉換內部呼叫點來解決此問題。我們的目標是收斂至穩定的公開 API，從而實現 1.0 版語意化版本控制 (semantic versioning) 的採用。

## 📣 溝通

React Native 是 [GitHub 上貢獻者數量最多的開源專案之一](https://octoverse.github.com/#top-and-trending-projects)。這讓我們非常高興，我們希望能持續保持。我們將繼續推動提升透明度與開放討論等促進貢獻者參與的計畫。文件是新接觸 React Native 的人首先會接觸到的內容，但過去並非優先事項。我們希望改善這點，從恢復自動生成的 API 參考文件開始，並建立更多專注於打造[優質使用者體驗](/docs/improvingux)的內容，以及改進我們的[版本發布說明](https://github.com/react-native-community/react-native-releases/issues/47)。

## 時間軸

我們計劃在未來一年左右陸續完成這些專案。部分工作已在進行中，例如 [JSI 已開源發布](https://github.com/facebook/react-native/compare/e337bcafb0b017311c37f2dbc24e5a757af0a205...8427f64e06456f171f9df0316c6ca40613de7a20)。其他如縮減程式碼範圍等專案則需要更長時間完成。我們將盡力向社群同步進度。歡迎加入 [討論與提案](https://github.com/react-native-community/discussions-and-proposals) 儲存庫，這是 React Native 社群發起的計畫，促成了本路線圖中討論的多項計畫。