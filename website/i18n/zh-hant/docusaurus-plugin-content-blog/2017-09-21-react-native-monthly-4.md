---
title: 'React Native Monthly #4'
authors: [grabbou]
tags: [engineering]
---

React Native 月度會議持續進行中！以下是各團隊的會議記錄：

### Callstack

- [React Native EU](https://react-native.eu) 已圓滿落幕。來自 33 個國家、超過 300 位與會者齊聚弗羅茨瓦夫。演講影片可於 [YouTube 頻道](https://www.youtube.com/channel/UCUNE_g1mQPuyW975WjgjYxA/videos)觀看。
- 我們正逐步恢復會議後的開源專案進度。值得一提的是，我們正在開發新版本的 [react-native-opentok](https://github.com/callstack/react-native-opentok)，該版本將修復多數現有問題。

### GeekyAnts

我們致力於降低開發者進入 React Native 的門檻，具體措施包括：

- 在 [React Native EU](https://react-native.eu) 大會上發布了 [BuilderX.io](https://builderx.io/)。BuilderX 是一款直接操作 JavaScript 檔案（目前僅支援 React Native）的設計工具，可生成美觀、易讀且可編輯的程式碼。
- 推出 [ReactNativeSeed.com](https://reactnativeseed.com/)，提供多種 React Native 專案樣板。這些樣板包含 TypeScript 和 Flow 資料類型選項，並整合 MobX、Redux 和 mobx-state-tree 等狀態管理方案，同時支援 CRNA 與純 React-Native 技術堆疊。

### Expo

- 即將發布 SDK 21，新增對 react-native 0.48.3 的支援，並包含多項 Expo SDK 的錯誤修復、穩定性改進與新功能，例如影片錄製、新版啟動畫面 API、支援 `react-native-gesture-handler` 以及強化的錯誤處理機制。
- 關於 [react-native-gesture-handler](https://github.com/kmagiera/react-native-gesture-handler)，[Software Mansion](https://swmansion.com/) 的 [Krzysztof Magiera](https://github.com/kmagiera) 持續推進該專案，我們協助進行測試並資助部分開發時程。該庫將整合至 Expo SDK21，屆時開發者可透過 Snack 輕鬆試用，我們期待看到大家的創意應用。
- 關於強化的錯誤記錄/處理機制，詳見 [此份 Expo 內部 PR 摘要](https://gist.github.com/brentvatne/00407710a854627aa021fdf90490b958)（特別是「問題 2」段落），以及 [此提交](https://github.com/expo/xdl/commit/1d62eca293dfb867fc0afc920c3dad94b7209987) 中對 npm 標準庫模組導入失敗的處理改進。React Native 上游尚有許多改進錯誤訊息的空間，我們將持續提交相關 PR，也歡迎社群共同參與。
- [native.directory](https://native.directory/) 持續擴充中，您可透過 [GitHub 儲存庫](https://github.com/react-community/native-directory) 提交專案。
- 我們將參與北美多場黑客松，包括 [PennApps](https://pennapps.com/)、[Hack The North](https://hackthenorth.com/)、[HackMIT](https://hackmit.org/)，以及即將舉行的 [MHacks](https://mhacks.org/)。

### Facebook

- 正在改進 Android 平台的 `<Text>` 與 `<TextInput>` 元件（包括 `<TextInput>` 原生自動調整高度、深層嵌套 `<Text>` 元件的佈局問題、程式碼結構優化及效能提升）。
- 我們持續招募願意協助處理議題與 PR 審查的貢獻者。

### Microsoft

- 為 CodePush 發布程式碼簽署功能。React Native 開發者現可為 CodePush 中的應用程式套件進行簽署，詳見 [公告](https://microsoft.github.io/code-push/articles/CodeSigningAnnouncement.html)。
- 正進行 CodePush 與 Mobile Center 的完整整合，同時評估測試/崩潰報告整合方案。

## 下次會議

下一次會議定於2017年10月10日星期三舉行。由於這只是我們的第四次會議，我們想知道這些筆記如何讓React Native社群受益。如果您對如何改進會議成果有任何建議，歡迎透過[Twitter](https://twitter.com/grabbou)與我聯繫。