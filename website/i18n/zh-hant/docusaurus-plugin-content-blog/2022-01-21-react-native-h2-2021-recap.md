---
title: React Native - H2 2021 Recap
authors: [cortinico]
tags: [announcement]
---

在我們仍為 [React Native 0.67 版本發佈](/blog/2022/01/19/version-067)感到興奮之際，我們希望花點時間**慶祝**社群在過去半年的成就，並分享 React Native 未來的**發展藍圖**。

<!--truncate-->

具體而言，2021 下半年對我們和社群來說都是 [充滿驚喜的半年](/blog/2021/08/19/h2-2021#pushing-the-technology-forward)，我們有機會更深入投資開源生態系統。我們翻新了部分流程，並從零開始建立新流程，這些都將幫助您、我們和社群享受**更優質**的 React Native 體驗。

## 程式庫健康狀態

在 2021 下半年，我們投入資源處理程式庫多年來累積的_開源技術債_。特別的是，我們主要聚焦於**拉取請求(Pull Requests)**。我們建立了內部流程，確保所有新提交的拉取請求都能及時獲得處理。

雖然這並非完整清單，但我們想特別強調貢獻者提交的一些**具影響力**的 PR：

- **無障礙功能**
  - [#31630](https://github.com/facebook/react-native/pull/31630) `由 [@anaskhraza](https://github.com/anaskhraza) 新增 Flatlist 集合的進入/退出支援`
- **崩潰修復**
  - [#29452](https://github.com/facebook/react-native/pull/29452) `由 [@fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 修復 - TextInput Drawable 避免空指針異常運行時錯誤`
- **顯示問題**
  - [#31777](https://github.com/facebook/react-native/pull/31777) `由 [@intergalacticspacehighway](https://github.com/intergalacticspacehighway) 修復：TouchableNativeFeedback 波紋效果從上次觸摸位置開始的問題`
  - [#31789](https://github.com/facebook/react-native/pull/31789) `由 [@tomekzaw](https://github.com/tomekzaw) 修復 Android 上大於 64 KB 的二進制對象支援`
  - [#31007](https://github.com/facebook/react-native/pull/31007) `由 [@fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 修復 selectionColor 無法樣式化 Android TextInput 選擇手柄的問題`
  - [#32398](https://github.com/facebook/react-native/pull/32398) `由 [@oblador](https://github.com/oblador) 修復 Android 邊框定位回歸問題`
  - [#29099](https://github.com/facebook/react-native/pull/29099) `由 [@fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 修復 [Android] 允許設置單獨（左、上、右、下）虛線/點線邊框`
  - [#29117](https://github.com/facebook/react-native/pull/29117) `由 [@fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 修復 [Android] 字體重量數值問題`
- **交互問題**
  - [#28995](https://github.com/facebook/react-native/pull/28995) `由 [@fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 修復 [Android] TextInput 光標在 placeholder 為 null 時跳至右側的問題`
  - [#28952](https://github.com/facebook/react-native/pull/28952) `由 [@fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 修復 [Android] FlatList 中不可選文本的問題`
  - [#29046](https://github.com/facebook/react-native/pull/29046) `由 [@fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 修復 [Android] 數字鍵不觸發 onKeyPress 事件的問題`
  - [#31500](https://github.com/facebook/react-native/pull/31500) `由 [@intergalacticspacehighway](https://github.com/intergalacticspacehighway) 修復 #29319 - iOS 模態框關閉問題`
  - [#32179](https://github.com/facebook/react-native/pull/32179) `由 [@xiankuncheng](https://github.com/xiankuncheng) 修復：多行 TextInput 在移動光標時出現「抖動」的問題`
  - [#29039](https://github.com/facebook/react-native/pull/29039) `由 [@hsource](https://github.com/hsource) 修復 Android 上點擊父邊界外視圖無效的問題`
- **性能優化**
  - [#31764](https://github.com/facebook/react-native/pull/31764) `由 [@Adlai-Holler](https://github.com/Adlai-Holler) 優化 iOS 上的字體處理`
  - [#32536](https://github.com/facebook/react-native/pull/32536) `由 [@Somena1](https://github.com/Somena1) 修復分屏模式下不重建應用組件`
- **測試相關**
  - [#31401](https://github.com/facebook/react-native/pull/31401) `由 [@NickGerleman](https://github.com/NickGerleman) 新增 VirtualizedList 渲染特性的單元測試`

其中部分 PR 解決了同時影響 Meta 和整個開源社區的問題，從相關已關閉議題的反應數量可見一斑。

還有許多其他我們想特別提及的PR，我們要再次**感謝**所有花時間幫助我們解決錯誤並改進React Native的人們。

## 社群參與

在上半年開始時，我們設定了一個目標，要**加強與社群的溝通**並建立持續此行為的流程。以下是我們在2021年下半年的一些互動：

<!--alex ignore gross-->

- 我們有機會參與[React Native EU](https://www.react-native.eu/)，並由[Joshua Gross](https://twitter.com/joshuaisgross)帶來一場演講 - [將Fabric渲染器帶入「Facebook」應用](https://www.youtube.com/watch?v=xKOkILSLs0Q&t=3987s)
- 我們在Reddit上舉辦了一場[「問我們任何事」(AUA)](https://www.reddit.com/r/reactnative/comments/pzdo1r/react_native_team_aua_thursday_oct_14_9am_pt/)，收到了超過100個問題！AUA對我們來說是一個了解社群參與度的好機會，對你們來說也是一個提出任何問題的好機會。如果你還沒看過，一定要去看看那些回答，因為其中一些非常有見地
- 我們分享了我們的[多平台願景](https://reactnative.dev/blog/2021/08/26/many-platform-vision)，提供了[Android 12和iOS 15的注意事項指南](https://reactnative.dev/blog/2021/09/01/preparing-your-app-for-iOS-15-and-android-12)，以及[Hermes成為React Native默認JS引擎的進展和願景](https://reactnative.dev/blog/2021/10/26/toward-hermes-being-the-default)！
- 我們的[Kevin Gozali](https://twitter.com/fkgozali)出現在[React Native Radio播客的一集](https://reactnativeradio.com/episodes/rnr-222-the-new-architecture-with-kevin-gozali-from-the-rn-core-team)中，談論了新架構。
- 在[ReactConf 2021](https://conf.reactjs.org/)上，[Rick Hanlon](https://twitter.com/rickhanlonii)分享了React和React Native的統一多平台願景。此外，[Eric Rozell](https://twitter.com/EricRozell)和[Steven Moyes](https://twitter.com/moyessa)展示了React Native Desktop在支持Meta和Microsoft應用方面的驚人進展，並展示了多平台願景的實踐。

除了在2021年下半年分享更多更新外，我們也比以往任何時候都更**依賴**我們的社群。我們依賴貢獻者在試用新架構材料的早期草稿時提供的關鍵反饋。同時，我們在調試關鍵發布問題和改進方面也得到了社群的專業知識的大力支持。

我們的社群為React Native帶來了豐富的知識，我們需要繼續培養這一點。

## 新架構的推出與發布

2022年將是**開源新架構**的一年。

我們一直在努力交付將新架構推廣到應用和庫所需的基礎設施。我們邀請了一些合作夥伴和核心貢獻者/庫維護者來完善我們對新架構的支持，以獲得早期反饋。

我們現在正在準備在我們的網站上發布一個新的指南：[新架構入門](https://github.com/facebook/react-native-website/pull/2879)。這將是我們在2022年將發布的一系列材料的起點，這些材料將幫助你遷移/開始你的新架構項目。

此外，我們想強調[**提供反饋**的重要性](https://github.com/facebook/react-native-website/pull/2879)。我們仍在完成最後的細節，你的意見將幫助每個人更無縫地採用新架構。

**版本發布**在新架構的推廣過程中扮演著關鍵角色。我們在上半年的目標是確保任何阻礙版本發布的問題不會停滯不前。我們通過[釐清並改進流程與責任歸屬](https://github.com/facebook/react-native/wiki/Releases)來解決這個問題，以實現更好的問責制。我們的版本協調工作現在在一個[專屬的討論存儲庫](https://github.com/reactwg/react-native-releases/discussions)中進行，並提供了[更清晰的版本問題報告機制](https://github.com/facebook/react-native/issues/new?assignees=&labels=Needs%3A+Triage+%3Amag%3A%2CType%3A+Upgrade+Issue&template=upgrade-regression-form.yml)。

在2022年上半年，我們將繼續迭代版本發布的責任分工，以支持新架構的推廣。如果您想幫助測試發布候選版本或[參與改進工作](https://github.com/facebook/react-native/projects/18)，歡迎[加入討論](https://github.com/reactwg/react-native-releases/discussions/categories/improvements)！

## 邁向移動端及其他平台

正如您在[ReactConf的演講陣容](https://conf.reactjs.org/)中所見，React Native不僅僅局限於Android和iOS。

早在2021年，我們就分享了我們的[多平台願景](https://reactnative.dev/blog/2021/08/26/many-platform-vision)，並且在桌面和VR平台上成功推廣了React Native。

我們期待將**平台特定的模式**整合到React Native的體驗中。

最後，我們要再次感謝社區在2021年下半年給予的巨大支持。看到貢獻者們在GitHub上齊心協力、互相支持，修復錯誤、分享知識並幫助我們將React Native交付給數百萬用戶，這總是令人驚嘆。

敬請期待，並展望**更加精彩的2022年** 🎉！