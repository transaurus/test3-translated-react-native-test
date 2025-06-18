---
title: 'React Native Monthly #1'
author: Tomislav Tenodi
authorTitle: Product Manager at Shoutem
authorURL: 'https://github.com/tenodi'
authorImageURL: 'https://pbs.twimg.com/profile_images/877237660225609729/bKFDwfAq.jpg'
authorTwitter: TomislavTenodi
tags: [engineering]
---

在 [Shoutem](https://shoutem.github.io/)，我們很幸運能從最初就開始使用 React Native。我們決定從第一天起就成為這個驚人社群的一部分。很快地，我們意識到幾乎不可能跟上社群成長和改進的速度。這就是為什麼我們決定組織每月會議，讓所有主要的 React Native 貢獻者能簡短分享他們的努力和計劃。

## 每月會議

我們在 2017 年 6 月 14 日舉行了第一次月度會議。React Native 月度會議的使命簡單明瞭：**改善 React Native 社群**。團隊分享各自的努力能促進線下的協作。

## 團隊

在第一次會議中，有 8 個團隊加入了我們：

- [Airbnb](https://github.com/airbnb)
- [Callstack](https://github.com/callstack-io)
- [Expo](https://github.com/expo)
- [Facebook](https://github.com/facebook)
- [GeekyAnts](https://github.com/GeekyAnts)
- [Microsoft](https://github.com/microsoft)
- [Shoutem](https://github.com/shoutem)
- [Wix](https://github.com/wix)

我們希望有更多的核心貢獻者加入未來的會議！

## 筆記

由於團隊的計劃可能對更廣泛的受眾感興趣，我們將在 React Native 部落格上分享這些內容。以下是他們的計劃：

### Airbnb

- 計劃為 `View` 和 `AccessibilityInfo` 原生模組添加一些 A11y（無障礙）API。
- 將研究在 Android 的原生模組中添加一些 API，以允許指定它們運行的線程。
- 一直在研究潛在的初始化性能改進。
- 一直在研究在「unbundle」之上使用更複雜的打包策略。

### Callstack

- 正在研究通過使用 [Detox](https://github.com/wix/detox) 進行端到端測試來改進發布流程。拉取請求應該很快就會提交。
- 他們一直在努力的 Blob 拉取請求已經合併，後續的拉取請求即將到來。
- 在內部項目中增加 [Haul](https://github.com/callstack-io/haul) 的採用，以查看其與 [Metro Bundler](https://github.com/facebook/metro-bundler) 相比的表現。正在與 webpack 團隊合作改進多線程性能。
- 在內部，他們已經實施了更好的基礎設施來管理開源項目。計劃在接下來的幾週內推出更多內容。
- React Native Europe 會議正在籌備中，目前還沒有什麼有趣的內容，但大家都被邀請了！
- 暫時從 [react-navigation](https://github.com/react-community/react-navigation) 退後一步，研究替代方案（特別是原生導航）。

### Expo

- 正在努力使在 [Snack](https://snack.expo.io/) 中安裝 npm 模組成為可能，這對庫作者在文檔中添加示例很有用。
- 正在與 [Krzysztof](https://github.com/kmagiera) 和 [Software Mansion](https://github.com/software-mansion) 的其他人合作，進行 Android 上的 JSC 更新和手勢處理庫的開發。
- [Adam Miskiewicz](https://github.com/skevy) 正在將他的注意力轉向 [react-navigation](https://github.com/react-community/react-navigation)。
- [Create React Native App](https://github.com/react-community/create-react-native-app) 已包含在文檔的 [入門指南](/docs/getting-started) 中。Expo 希望鼓勵庫作者清楚地解釋他們的庫是否與 CRNA 兼容，如果是，則解釋如何設置。

### Facebook

- React Native 的打包工具現已獨立為 [Metro Bundler](https://github.com/facebook/metro) 專案。倫敦的 Metro Bundler 團隊正積極解決社群需求，提升模組化以支援 React Native 以外的使用場景，並加快對問題與 PR 的回應速度。
- 未來幾個月，React Native 團隊將著重優化基礎元件的 API。預計將改善佈局異常、無障礙功能與 Flow 類型檢查等問題。
- 團隊也計劃在今年提升核心模組化程度，透過重構全面支援 Windows 和 macOS 等第三方平台。

### GeekyAnts

- 團隊正在開發代號為 Builder 的 UI/UX 設計工具，可直接操作 `.js` 檔案。目前僅支援 React Native，功能類似 Adobe XD 和 Sketch。
- 致力實現「在編輯器中載入現有 React Native 應用 → 視覺化設計修改 → 直接保存至 JS 檔案」的工作流程。
- 目標是縮短設計師與開發者之間的鴻溝，讓兩者能在同個程式庫協作。
- 此外，[NativeBase](https://github.com/GeekyAnts/NativeBase) 近期已突破 5,000 GitHub 星標。

### Microsoft

- [CodePush](https://github.com/Microsoft/code-push) 已整合至 [Mobile Center](https://mobile.azure.com/)，這是強化與分發、分析等服務協作的第一步。詳見[公告](https://microsoft.github.io/code-push/articles/CodePushOnMobileCenter.html)。
- 正在修復 [VS Code](https://github.com/Microsoft/vscode) 的除錯功能錯誤，新版即將發布。
- 評估使用 [Detox](https://github.com/wix/detox) 進行整合測試，研究透過 JSC Context 在當機報告中獲取變數資訊。

### Shoutem

- 簡化 Shoutem 應用與 React Native 生態工具的整合，未來可直接使用 React Native 指令執行 [Shoutem](https://shoutem.github.io/) 建立的應用。
- 研究 React Native 效能分析工具，將分享配置過程中發現的關鍵洞察。
- 開發更流暢的 React Native 與現有原生應用整合方案，計劃公開內部研發的概念以獲取社群反饋。

### Wix

- 內部全面採用 [Detox](https://github.com/wix/detox)，推動 Wix 應用邁向「零人工 QA」。目前已有數十名開發者在生產環境中使用，促使工具快速成熟。
- 擴展 [Metro Bundler](https://github.com/facebook/metro) 功能，支援在建置時覆寫任意副檔名（如 "e2e" 或 "detox"），用於 E2E 模擬。已發布 [react-native-repackager](https://github.com/wix/react-native-repackager)，現正準備 PR。
- 開發開源專案 [DetoxInstruments](https://github.com/wix/DetoxInstruments) 實現效能測試自動化。
- 與 KPN 貢獻者合作開發 Android 版 Detox，支援實體裝置。
- 規劃「Detox 平台化」，支援建置需自動化模擬器/裝置的工具，例如 [Storybook](https://github.com/storybooks/react-native-storybook) 或整合測試方案。

## 下次會議

會議將每四周舉行一次。下一次會議定於2017年7月12日召開。由於我們才剛開始舉辦此會議，我們想知道這些筆記如何能讓React Native社群受益。如果您對我們接下來會議應涵蓋的內容，或是如何改進會議產出有任何建議，歡迎[在Twitter上聯繫我](https://twitter.com/TomislavTenodi)。