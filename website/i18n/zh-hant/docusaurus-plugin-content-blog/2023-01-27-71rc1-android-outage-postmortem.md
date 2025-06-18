---
title: 'React Native 0.71-RC0 Android outage postmortem'
authors: [cortinico, kelset]
tags: [engineering]
date: 2023-01-27
---

現在 0.71 版本[已發佈](/blog/2023/01/12/version-071)，我們希望分享關於 2022 年 11 月 4 日發佈首個 React Native 0.71 候選版本時，導致所有 React Native 版本 Android 構建中斷事件的關鍵資訊。

參與處理此事件的貢獻者近期參加了事後檢討會議，詳細討論事件經過、從中學到的教訓，以及我們將採取哪些措施來避免未來發生類似的中斷情況。

<!--truncate-->

## 事件經過

2022 年 11 月 4 日，我們在多個公開儲存庫發佈了 React Native 的 `0.71.0-rc0` 版本，這是 0.71 的首個候選版本。

此候選版本中的一項重大變更是改為將構建產物發佈至 Maven Central，而非從原始碼構建，以提升構建速度。具體實現方式詳見 [RFC#508](https://github.com/react-native-community/discussions-and-proposals/pull/508) 和[相關討論](https://github.com/reactwg/react-native-new-architecture/discussions/105)。

遺憾的是，由於我們從模板初始化新專案的方式，這導致所有使用舊版本的 Android 用戶構建失敗，因為他們會開始下載 `0.71.0-rc0` 的新構建產物，而非專案實際使用的版本（例如 `0.68.0`）。

## 事件原因

React Native 模板提供的 `build.gradle` 檔案包含對 React Native Android 函式庫的依賴聲明如下：
`implementation("com.facebook.react:react-native:+")`。

關鍵在於，此依賴中的 `+` 部分（屬於 [Gradle 動態版本](https://docs.gradle.org/current/userguide/dynamic_versions.html)）會指示 Gradle 選取最高可用版本的 React Native。使用 Gradle 動態版本被視為反模式，因其會導致構建結果難以重現。

我們已知動態版本可能引發的問題，因此在 `0.71` 版本中清理了新應用模板，移除了所有 `+` 依賴。然而，使用舊版 React Native 的用戶仍在使用含 `+` 的版本。

這導致 React Native 版本低於 `0.71.0-rc.0` 的構建會查詢所有儲存庫以獲取最高可用版本。由於新推送至 Maven Central 的 0.71.0-rc.0 成為最高可用版本，低於 0.71.0-rc.0 的 React Native 版本構建開始使用 0.71.0-rc.0 的構建產物。本地構建版本（如 `0.68.0`）與 Maven Central 構建產物版本（`0.71.0-rc.0`）的不匹配導致這些構建失敗。

此事件的進一步技術細節亦可參閱 [GitHub 議題](https://github.com/facebook/react-native/issues/35210)。

## 緩解與解決措施

我們在 11 月 4 日識別出問題後，社群立即找到並分享了手動解決方案，可將 React Native 固定至特定版本以修正錯誤。

隨後在 11 月 5 日至 6 日的週末期間，發佈團隊為所有舊版 React Native（回溯至 0.63）發佈了修補版本，自動應用修正方案，使用者可更新至已修復的 React Native 版本。

同時，我們[聯繫了 Sonatype](https://issues.sonatype.org/browse/OSSRH-86006) 請求移除有問題的構建產物。

11 月 8 日當所有構建產物從 Maven Central 完全移除後，事件獲得徹底解決。

## 事件時間軸

_本節為事件簡要時間軸，所有時間均為 GMT/UTC +0 時區_

- 11月4日 下午5:06: [發布0.71-RC0版本](https://github.com/facebook/react-native/releases/tag/v0.71.0-rc.0)。
- 11月4日 下午6:20: [首次報告建構問題](https://github.com/facebook/react-native/issues/35204)。
- 11月4日 下午7:45: [社群確認問題根源](https://github.com/facebook/react-native/issues/35204#issuecomment-1304090948)。
- 11月4日 晚上9:39: [發布臨時解決方案，Expo團隊為所有用戶部署修復](https://github.com/facebook/react-native/issues/35204#issuecomment-1304281740)。
- 11月5日 凌晨3:04: [開設新議題通報狀態與應變措施](https://github.com/facebook/react-native/issues/35210)。
- 11月6日 下午4:11: [向SonaType提交移除問題構件的請求](https://issues.sonatype.org/browse/OSSRH-86006?focusedCommentId=1216303&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-1216303)。
- 11月6日 下午4:40: [@reactnative官方推特首次發文](https://twitter.com/reactnative/status/1589296764678705155)確認問題並附議題連結。
- 11月6日 晚上7:05: 決議回溯修復React Native至0.63版本。
- 11月7日 凌晨12:47: 最後一個修補版本[0.63.5發布](https://github.com/facebook/react-native/releases/tag/v0.63.5)。
- 11月8日 晚上8:04: Maven Central上的問題構件[完全移除](https://issues.sonatype.org/browse/OSSRH-86006?focusedCommentId=1216303&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#comment-1216303)。
- 11月10日 上午11:51: [事件相關議題正式關閉](https://github.com/facebook/react-native/issues/35210#issuecomment-1310170361)。

## 經驗教訓

雖然觸發此事件的條件自React Native 0.12.0以來就已存在，但我們將強化開發與發布的基礎架構。以下是我們從中學到的教訓，以及未來改進流程與基礎建設的具體行動方案。

### 事件應變策略

此事件暴露了我們在處理React Native開源議題時的應變策略缺口。

社群在2小時內迅速找到臨時解決方案。由於缺乏對問題影響範圍的可視性，加上修復舊版本的複雜性，我們當時依賴受影響者自行在GitHub議題中發現解決方案。

我們耗時48小時才認知到此問題的廣泛影響，意識到不能僅依賴開發者自行查找GitHub議題。必須優先實施能自動修復專案的主動緩解措施。

我們將重新評估「依賴開發者手動應用解決方案」與「部署自動修復」的決策流程，並探索提升生態系統健康狀態即時監測的方案。

### 版本支援政策

如[rn-versions工具](https://rn-versions.github.io/)所示，為涵蓋事件發生時90%以上的React Native開發者，我們必須向下兼容修補至0.63版本。

這歸因於React Native歷來繁瑣的升級體驗。我們正著手改善升級流程，以緩解生態系統碎片化問題。

新版React Native的發布絕不應影響舊版用戶，我們為此造成的工作流程中斷深表歉意。

同樣地，我們也想強調保持依賴套件和React Native版本更新的重要性，以獲得我們所引入的改進和安全保障。這次事件發生時，官方[發布支援政策](https://github.com/reactwg/react-native-releases#releases-support-policy)正在制定中，尚未被廣泛宣傳或強制執行。

未來，我們將透過我們的溝通管道傳達支援政策，並考慮[在npm上棄用舊版React Native](https://docs.npmjs.com/deprecating-and-undeprecating-packages-or-package-versions)。

### 改進第三方函式庫的測試與最佳實踐

這次事件凸顯了改進發布測試和提供更好指引給第三方函式庫的重要性。

在測試方面，發布回溯至`0.63.x`版本的過程充滿挑戰，這是由於我們缺乏現有穩定版本發布時的自動化和測試機制。我們認識到發布和測試基礎設施的重要性，未來將進一步投入資源加強這方面。

具體來說，我們現在鼓勵並支援將第三方函式庫測試作為[React Native發布流程](https://github.com/reactwg/react-native-releases/discussions/41)的一部分。我們也在[核心貢獻者Discord伺服器](https://github.com/facebook/react-native/blob/main/ECOSYSTEM.md#core-contributors)中新增了一些頻道和角色。

除此之外，我們開始與Callstack（[create-react-native-library](https://github.com/callstack/react-native-builder-bob/tree/main/packages/create-react-native-library)的維護者）進行更緊密的合作，以改進函式庫模板並確保其遵循所有必要的最佳實踐來與React Native專案整合。新版本的`create-react-native-library`現在完全相容於0.71專案，同時仍保持向後相容性。

## 結論

我們要為這次事件對全球開發者工作流程造成的干擾致歉。如上所述，我們已開始採取行動強化基礎建設，並將持續進行更多改進工作。

我們希望分享這些見解能幫助大家更了解這次事件，並能運用我們的經驗在您自己的工具和專案中實踐更好的方法。

最後，我們要再次感謝Sonatype協助我們移除有問題的構件，以及我們的社群和發布團隊，他們不眠不休地盡快解決了這個問題。