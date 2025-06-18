---
title: React Native Open Source Update March 2019
authors: [cpojer]
tags: [announcement]
---

We announced our [React Native Open Source roadmap](/blog/2018/11/01/oss-roadmap) in Q4 2018 after deciding to invest more in the React Native open source community.

For our first milestone, we focused on identifying and improving the most visible aspects of our community. Our goals were to reduce outstanding pull requests, reduce the project's surface area, identify leading user problems, and establish guidelines for community management.

In the past two months, we made more progress than we expected. Read on for more details:

### Pull Requests

In order to build a healthy community, we must respond quickly to code contributions. In past years, we de-prioritized reviewing community contributions and accumulated 280 pull requests (December 2018). In the first milestone, we reduced the number of open pull requests to ~65. Simultaneously, the average number of pull requests opened per day increased from 3.5 to 7 which means we have handled about [600 pull requests](https://github.com/facebook/react-native/pulls?page=24&q=is%3Apr+closed%3A%3E2018-12-01&utf8=%E2%9C%93) in the last three months.

We merged [almost two-thirds](https://github.com/facebook/react-native/pulls?utf8=%E2%9C%93&q=is%3Apr+closed%3A%3E2018-12-01+label%3A%22Merged%22+) and closed one-third of the pull requests. They were closed without being merged if they are obsolete or low quality, or if they unnecessarily increase the project's surface area. Most of the merged pull requests fixed bugs, improved cross-platform parity, or introduced new features. Notable contributions include improving type safety and the ongoing work to support AndroidX.

At Facebook, we run React Native from master, so we test all changes first before they make it into a React Native Release. Out of all the merged pull requests, only six caused issues: four only affected internal development and two were caught in the release candidate state.

One of the more visible community contributions was [the updated “RedBox” screen](https://github.com/facebook/react-native/pull/22242). It's a good example of how the community is making the developer experience friendlier.

### Lean Core

React Native currently has a very wide surface area with many unmaintained abstractions that we do not use a lot at Facebook. We are working on reducing the surface area in order to make React Native smaller and allow the community to take better care of abstractions that are mostly unused at Facebook.

In the first milestone, [we asked the community for help on the Lean Core project](https://twitter.com/reactnative/status/1093171521114247171). The response was overwhelming and we could barely keep up with all the progress. [Check out all the work completed in less than a month](https://github.com/facebook/react-native/issues/23313)!

What we are most excited about is that maintainers have jumped in fixing long standing issues, adding tests, and supporting long requested features. These modules are getting more support than they ever did within React Native, showing that this is a great step for the community. Examples of such projects are [WebView](https://github.com/react-native-community/react-native-webview) that has [received many pull requests](https://twitter.com/titozzz/status/1101283928026034176) since their extraction and the CLI that is now [maintained by members of the community](https://blog.callstack.io/the-react-native-cli-has-a-new-home-79b63838f0e6) and received much needed improvements and fixes.

### Leading User Problems

In December, we asked the community what they [disliked about React Native](https://github.com/react-native-community/discussions-and-proposals/issues/64). We aggregated the responses and [replied to each and every problem](https://github.com/react-native-community/discussions-and-proposals/issues/104). Fortunately, many of the issues that our community faces are also problems at Facebook. In our next milestone, we plan to address some of the main problems.

其中獲得最高票數的問題之一，是升級至新版React Native的開發者體驗。由於我們直接從master分支運行React Native，這並非我們自身會遇到的問題。值得慶幸的是，社群成員已主動著手解決此問題：

- Callstack的[Michał Pierzchała](https://github.com/thymikee)透過整合[rn-diff-purge](https://github.com/react-native-community/rn-diff-purge)，[改進了react-native upgrade指令](https://github.com/react-native-community/react-native-cli/pull/176/files)。我們也更新了官網，移除了過時的升級指引。
- [我們計劃預設推薦iOS專案使用CocoaPods](https://github.com/facebook/react-native/pull/23563)，這將減少升級React Native時的專案文件變動。此舉能簡化第三方模組的安裝與連結流程，在Lean Core計畫背景下更顯重要，因預期專案將預設連結更多模組。

### 0.59版本發布

若沒有React Native社群，特別是[Mike Grabowski](https://github.com/grabbou)與[Lorenzo Sciandra](https://github.com/kelset)的協助，我們將無法完成版本發布。我們計劃改善發布管理流程並從現在起更深入參與：

- 我們將與社群成員合作，為每個主要版本撰寫部落格文章。
- 我們將直接在CLI中顯示版本升級時的破壞性變更。
- 我們將縮短發布週期，正探索增加自動化測試與改進手動測試計畫的方法。

這些計劃多數將納入即將發布的[React Native 0.59版本](https://github.com/facebook/react-native/releases/tag/v0.59.0-rc.3)。0.59版將包含React Hooks、Android版64位元JavaScriptCore，以及眾多效能與功能改進。目前該版本已作為候選發布版推出，預計兩週內趨於穩定。

### 後續步驟

未來兩個月，我們將持續管理pull requests以[維持進度](https://k03lwm00zo.codesandbox.io/)，同時開始減少未解決的GitHub issues數量。透過Lean Core計畫持續精簡React Native的範疇，並著手解決5項最高票的社群問題。待社群準則定案後，我們將轉而關注官網與文件改進。

我們非常期待三月份能在Facebook倫敦辦公室接待十多位社群貢獻者，共同推動這些計畫。感謝您使用React Native，期待您能感受到我們2019年的改進成果。數月後我們將再次更新進度，_期間也將持續合併您的pull requests!_ ⚛️✌️