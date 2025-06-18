---
title: 'React Native Monthly #3'
authors: [grabbou]
tags: [engineering]
---

React Native 每月會議持續進行！這個月的會議稍短，因為多數團隊正忙於產品發布。下個月，我們將在波蘭弗羅茨瓦夫舉行的 [React Native EU](https://react-native.eu/) 會議上見面。記得購票，期待與您現場相見！現在，讓我們看看各團隊的最新動態。

## 團隊

第三次會議共有 5 個團隊參與：

- [Callstack](https://github.com/callstack)
- [Expo](https://github.com/expo)
- [Facebook](https://github.com/facebook)
- [Microsoft](https://github.com/microsoft)
- [Shoutem](https://github.com/shoutem)

## 會議記錄

以下是各團隊的分享摘要：

### Callstack

- 近期開源了 [`react-native-material-palette`](https://github.com/callstack-io/react-native-material-palette)，該工具能從圖片提取主色調，協助打造視覺吸引力的應用。目前僅支援 Android，未來計劃擴展至 iOS。
- 我們已將 HMR 支援整合進 [`haul`](https://github.com/callstack-io/haul)，並加入多項新功能！請查看最新版本。
- React Native EU 2017 即將到來！下個月將是 React Native 與波蘭的主場！請把握最後購票機會 [這裡](https://react-native.eu/)。

### Expo

- 在 [Snack](https://snack.expo.io) 上發布了 npm 套件安裝支援。需注意 Expo 的限制——套件不能依賴未內建於 Expo 的原生 API。我們同時正開發 Snack 的多檔案支援與資源上傳功能。[Satyajit](https://github.com/satya164) 將在 [React Native Europe](https://react-native.eu/) 分享 Snack 相關內容。
- 發布 SDK20，新增相機、支付、安全儲存、磁力計、暫停/恢復檔案下載功能，並改進啟動/載入畫面。
- 持續與 [Krzysztof](https://github.com/kmagiera) 合作開發 [react-native-gesture-handler](https://github.com/kmagiera/react-native-gesture-handler)。歡迎試用並回報使用 PanResponder 或原生手勢識別器重建手勢時遇到的問題。
- 實驗 JSC 調試協議，並處理 [Canny](https://expo.canny.io/feature-requests) 上的多項功能請求。

### Facebook

- 上個月討論了 GitHub 問題追蹤器的管理，我們將持續改進專案維護性。
- 目前未解決問題數穩定維持在 600 左右。過去一個月，我們關閉了 690 個因長期無活動（定義為 60 天無評論）的問題，其中 58 個因維護者承諾修復或貢獻者提出充分理由被重新開啟。
- 我們計劃持續自動關閉停滯問題。目標是確保每個重要問題都能獲得處理，現階段仍需維護者協助分類，避免遺漏導致回歸或破壞性變更的問題（尤其影響新專案者）。有意協助者可參考維護者指南使用 Facebook GitHub Bot 分類問題與拉取請求，並加入 [issue task force](https://github.com/facebook/react-native/blob/master/bots/IssueCommands.txt) 邀請其他活躍成員參與！

### Microsoft

- 新版Skype應用程式基於React Native構建，旨在實現跨平台間最大程度的代碼共享。目前採用React Native的Skype應用已在Android和iOS應用商店上架。
- 在基於React Native開發Skype應用的過程中，我們會針對遇到的缺陷與功能缺失向React Native提交Pull Request。截至目前，我們已有[約70個PR被合併](https://github.com/facebook/react-native/pulls?utf8=%E2%9C%93&q=is%3Apr%20author%3Arigdern%20)。
- React Native讓我們能透過單一程式碼庫同時驅動Android與iOS版Skype應用。我們還希望利用該程式碼庫支援Skype網頁版應用。為實現此目標，我們開發並開源了建構於React/React Native之上的輕量層[ReactXP](https://microsoft.github.io/reactxp/blog/2017/04/06/introducing-reactxp.html)。ReactXP提供一組跨平台元件，當目標平台為iOS/Android時映射至React Native，目標為網頁時則映射至react-dom。ReactXP的目標與另一開源庫React Native for Web相似，兩者方法差異可參見[ReactXP常見問題](https://microsoft.github.io/reactxp/docs/faq.html)中的簡要說明。

### Shoutem

- 我們持續致力於改善和簡化使用[Shoutem](https://shoutem.github.io/)構建應用的開發者體驗。
- 已開始將所有應用遷移至react-navigation，但最終決定延後此計劃，直到更穩定版本發布或某個原生導航方案趨於穩定。
- 正在將所有[擴充功能](https://github.com/shoutem/extensions)及多數開源庫（[動畫](https://github.com/shoutem/animation)、[主題](https://github.com/shoutem/theme)、[UI元件](https://github.com/shoutem/ui)）更新至React Native 0.47.1版本。

## 下次會議

下次會議定於2017年9月13日星期三舉行。由於這僅是我們的第三次會議，我們希望了解這些紀錄如何使React Native社群受益。若您對改進會議成果有任何建議，歡迎透過[Twitter](https://twitter.com/grabbou)與我聯繫。