---
title: The GAAD Pledge - One Year Later
authors: [alexmarlette]
tags: [announcement]
---

自Facebook承諾[GAAD宣言](https://diamond.la/GAADPledge/)要使React Native具備無障礙功能已過一年，此專案的進展已超出我們的預期。我們興奮地宣布這項專案將持續至2021年，並想向各位更新目前的進展。在去年徹底分析React Native的無障礙功能缺口後，我們便開始著手填補這些缺口。

我們最初有90個待解決的缺口分析問題，從2021年3月專案在GitHub上啟動至今：

- 社群已關閉11個問題。

- React Native團隊評估並關閉了19個問題。

- 合併了9個拉取請求。

- 合併了1個拉取請求至React Native文件。

我們想認可並感謝React Native社群在過去一年中為使React Native更具無障礙性所做出的重大進展。每一位貢獻者的努力都在改進React Native無障礙功能上起到了作用。

<!--truncate-->

## 修復項目

透過9個拉取請求，已在多個組件中修復了兩類問題，並在API中新增了一項功能。

- 已在七個組件中解決了禁用狀態的問題

- 在兩個組件中解決了選中狀態的問題

- 在React Native API中新增了查詢AccessibilityManager.getRecommendedTimeoutMillis()的功能。

### 禁用狀態宣告及禁用功能

在缺口分析中發現的最普遍問題之一是某些組件不會宣告或禁用功能。現在有七個組件會宣告其禁用狀態或禁用點擊功能。

宣告禁用時

- `Button` - [#31001](https://github.com/facebook/react-native/pull/31001)

- `Images` - [#31252](https://github.com/facebook/react-native/pull/31252)

- `ImageBackground` - [#31252](https://github.com/facebook/react-native/pull/31252)

當組件具有禁用屬性時，禁用點擊功能

- `Button` - [#31001](https://github.com/facebook/react-native/pull/31001)

- `Text` - [React Native團隊提交](https://github.com/facebook/react-native/commit/33ff4445dcf858cd5e6ba899163fd2a76774b641)

- `Pressable` - [React Native團隊提交](https://github.com/facebook/react-native/commit/1c7d9c8046099eab8db4a460bedc0b2c07ed06df)

- `TouchableHighlight` - [#31135](https://github.com/facebook/react-native/pull/31135)

- `TouchableOpacity` - [#31108](https://github.com/facebook/react-native/pull/31108)

- `TouchableNativeFeedback` - [#31224](https://github.com/facebook/react-native/pull/31224)

- `TouchableWithoutFeedback` - [#31297](https://github.com/facebook/react-native/pull/31297)

### 選中狀態宣告

有些組件在獲得焦點時不會宣告其選中狀態。現在當組件獲得焦點且AccessibilityState設為選中或組件變更為選中時，此行為已得到修正。

宣告選中時

- `Button` - [#31001](https://github.com/facebook/react-native/pull/31001)

- `TextInput` - [#31144](https://github.com/facebook/react-native/pull/31144)

### 無障礙超時設定

先前在Android上無法查詢無障礙超時設定。此修復新增了查詢`AccessibilityManager.getRecommendedTimeoutMillis()`的功能。這會查詢「採取行動的時間」，即UI元素自動關閉或自動進展前的時間。

## 文件新增內容

React Native 文件必須更新以反映每個 API 新增或變更的內容。[React Native 文件新增的部分](https://reactnative.dev/docs/next/accessibilityinfo#getrecommendedtimeoutmillis-android)涵蓋了 AccessibilityInfo 中新增的 `getRecommendedTimeoutMillis()` 方法。

## 社群參與

我們要感謝以下所有提交並合併 pull request 的貢獻者，以及審查和評論 issue 的人員。

### 已合併的 Pull Requests

- [@huzaifaaak](https://twitter.com/huzaifaaak) 關閉了 3 個 issue：
  - [為按鈕無障礙功能新增 disabled prop 的語音回饋支援 #31001](https://github.com/facebook/react-native/pull/31001)
  - [無障礙/按鈕測試 #31189](https://github.com/facebook/react-native/pull/31189)
- [@natural_clar](https://twitter.com/natural_clar) 關閉了 1 個 issue：
  - [功能：當 `TouchableHighlight` 禁用時設定無障礙狀態為 disabled #31135](https://github.com/facebook/react-native/pull/31135)
- [fabriziobertoglio1987](https://github.com/fabriziobertoglio1987) 關閉了 2 個 issue：
  - [[Android] 當 `TextInput` 元件被選中時未宣告選中狀態 #31144](https://github.com/facebook/react-native/pull/31144)
  - [無障礙修復：圖片未宣告「disabled」狀態 #31252](https://github.com/facebook/react-native/pull/31252)
- [@kyamashiro73](https://twitter.com/kyamashiro73) 關閉了 1 個 issue：
  - [為 `TouchableNativeFeedback` 無障礙功能新增 disabled prop 的語音回饋支援 #31224](https://github.com/facebook/react-native/pull/31224)
- [@grgr-dkrk](https://twitter.com/dkrk0901) 關閉了 1 個 issue 並新增至 React Native 文件：
  - [在 AccessibilityInfo 中新增 `getRecommendedTimeoutMillis` #31063](https://github.com/facebook/react-native/pull/31063)
  - [功能：在 accessibilityInfo 中新增 `getRecommendedTimeoutMillis` 章節 #2581](https://github.com/facebook/react-native-website/pull/2581)
- [@crloscuesta](https://twitter.com/crloscuesta) 關閉了 1 個 issue：
  - [當 `TouchableWithoutFeedback` 禁用時設定無障礙狀態為 disabled #31297](https://github.com/facebook/react-native/pull/31297)
- [@chakrihacker](https://twitter.com/chakrihacker) 關閉了 1 個 issue：
  - [當無障礙功能禁用時停用 `TouchableOpacity` #31108](https://github.com/facebook/react-native/pull/31108)

感謝以其他方式投入時間的社群成員！

[Simek](https://github.com/Simek)、[saurabhkacholiya](https://github.com/saurabhkacholiya)、[meehawk](https://github.com/meehawk)、[intergalacticspacehighway](https://github.com/intergalacticspacehighway)、[chrisglein](https://github.com/chrisglein)、[jychiao](https://github.com/jychiao) 和 [Waltari10](https://github.com/Waltari10)

## 參與其中！

我們已經取得了長足的進步，但尚未完成。我們需要您的支持來達成目標。Facebook 的 React Native 團隊承諾支援處理 gap analysis issue 的貢獻者。他們將繼續回應無障礙功能 issue 的評論並審查 pull request。React Native 團隊也在處理一些最困難的 gap analysis issue。這項工作包括將 accessibilityRoles 正確翻譯成其他語言，以及為特定元件指定錯誤文字。

加入我們一起解決剩下的問題。[改進 React Native 無障礙功能專案看板](https://github.com/facebook/react-native/projects/15)上仍有開放的無障礙功能 issue。[Checked/Unchecked 狀態](https://github.com/facebook/react-native/issues/30843)、[集合的進入/退出](https://github.com/facebook/react-native/issues/30861) 和 [集合中的位置](https://github.com/facebook/react-native/issues/30977) 等 issue 是現有和新貢獻者為 React Native 無障礙功能做出貢獻的絕佳機會。

### 了解更多

閱讀關於差距分析如何進行的詳細內容，請參閱 [Facebook 技術部落格](https://tech.fb.com/react-native-accessibility/)；或關於 GitHub 議題的啟動，請參閱 [React Native 部落格](https://reactnative.dev/blog/2021/03/08/GAAD-React-Native-Accessibility)。