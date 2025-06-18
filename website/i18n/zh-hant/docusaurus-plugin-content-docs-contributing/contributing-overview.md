---
id: overview
title: Contributing Overview
description: How to contribute to React Native
---

<!-- alex disable simple simply -->

感謝您對貢獻 React Native 感興趣！無論是評論與分類議題、審查或提交 Pull Request，我們歡迎所有形式的貢獻。
我們的目標是建立一個超越主 React Native GitHub 儲存庫的活躍且包容的[合作夥伴、核心貢獻者與社群生態系統](https://github.com/facebook/react-native/blob/main/ECOSYSTEM.md)。

[開源指南](https://opensource.guide/)網站匯集了各類資源，適合個人、社群與企業學習如何運作及貢獻開源專案。

無論是貢獻者或開源新手，以下指南特別實用：

- [如何貢獻開源專案](https://opensource.guide/how-to-contribute/)
- [建立友善社群](https://opensource.guide/building-community/)

### 行為準則

請注意，所有貢獻者均須遵守[行為準則](https://github.com/facebook/react-native/blob/HEAD/CODE_OF_CONDUCT.md)。

## 版本政策

為完整理解 React Native 的版本管理，建議您查閱[版本政策](/contributing/versioning-policy)頁面。
該頁面說明哪些 React Native 版本受支援、發布頻率，以及您應根據自身情況選擇的版本。

## 貢獻方式

若您希望立即開始貢獻程式碼，我們有一份[新手友善議題清單](https://github.com/facebook/react-native/labels/good%20first%20issue)，這些錯誤的範圍相對有限。
當您累積更多經驗並展現出推動 React Native 發展的承諾，可能會獲得儲存庫的議題管理權限。

您也可以透過其他無需編寫程式碼的方式貢獻，以下是幾種協助途徑：

1. **回覆與處理開放議題。**

   我們每天收到大量議題，部分可能缺乏必要資訊。您可以引導提問者填寫議題模板、要求釐清細節，或指引他們至符合問題描述的現有議題。
   更多流程說明請參閱[分類 GitHub 議題](/contributing/triaging-github-issues)頁面。

2. **審查文件更新的 Pull Request。**

   審查[文件更新](https://github.com/facebook/react-native-website/pulls)可簡單至檢查拼字與文法。
   若發現文件中有需更清楚解釋之處，點擊文件頁面頂部的**編輯**按鈕即可開始貢獻。

3. **協助撰寫測試計畫。**

   提交至主儲存庫的部分 Pull Request 可能缺乏適當測試計畫。這些計畫能幫助審查者理解變更如何被測試，並加速貢獻被接受的流程。

每項任務都極具影響力，維護者將十分感謝您的協助。

### 我們的開發流程

我們使用 GitHub 議題與 Pull Request 來追蹤社群錯誤報告與貢獻。Meta 工程師的所有變更會透過與 Meta 內部原始碼控制的橋接同步至 [GitHub](https://github.com/facebook/react-native)。社群貢獻則透過 GitHub Pull Request 處理。

GitHub 上的變更一經核准，會先匯入 Facebook 內部原始碼控制系統，並針對 Facebook 程式碼庫進行測試。當變更在 Facebook 內部合併且通過測試後，最終會以單一提交同步回 GitHub。

您可透過以下文件進一步了解貢獻流程：

- [分類 GitHub 議題](/contributing/triaging-github-issues)
- [管理 Pull Request](/contributing/managing-pull-requests)

We also have a thriving community of contributors who would be happy to help you get set up. You can reach out to the React Native team through [@ReactNative](https://twitter.com/reactnative).

### Repositories

The main repository contains the React Native framework itself, and it is here where we keep track of bug reports and manage pull requests.

There are a few other repositories you might want to familiarize yourself with:

- **React Native website** which contains the source code for the website, including the documentation, located [in this repository](https://github.com/facebook/react-native-website).
- **Releases** conversations are happening [in this discussion repo](https://github.com/reactwg/react-native-releases/discussions).
- **Changelog** for the releases can be found [here](https://github.com/facebook/react-native/blob/main/CHANGELOG.md).
- **Discussions** about React Native take place in the [Discussions and Proposals](https://github.com/react-native-community/discussions-and-proposals) repository.
- **Discussions** about the new architecture of React Native take place in the [React Native New Architecture Working Group](https://github.com/reactwg/react-native-new-architecture) repository.
- **High-quality plugins** for React Native can be found throughout the [React Native Directory](https://reactnative.directory) website.

Browsing through these repositories should provide some insight into how the React Native open source project is managed.

## GitHub Issues

We use GitHub issues to track bugs exclusively. We have documented our issue handling processes in the [Triaging Issues Page](/contributing/triaging-github-issues).

### Security Bugs

Meta has a [bounty program](https://www.facebook.com/whitehat/) for the safe disclosure of security bugs. In those cases, please go through the process outlined on that page and do not file a public issue.

## Helping with Documentation

The React Native documentation is hosted as part of the React Native website repository. The website is built using [Docusaurus](https://docusaurus.io/). If there's anything you'd like to change in the docs, you can get started by clicking on the "Edit" button located on the upper right of most pages in the website.

If you are adding new functionality or introducing a change in behavior, we will ask you to update the documentation to reflect your changes.

### Contributing to the Blog

The React Native blog is generated [from the Markdown sources for the blog](https://github.com/facebook/react-native-website/tree/HEAD/website/blog).

Please open an issue in the React Native website repository or tag us on [@ReactNative on Twitter](https://twitter.com/reactnative) and get the go-ahead from a maintainer before writing an article intended for the React Native blog.
In most cases, you might want to share your article on your own blog or writing medium instead. It's worth asking, though, in case we find your article is a good fit for the blog.

We recommend referring to the `react-native-website` repository [Readme file](https://github.com/facebook/react-native-website#-contributing) to learn more about contributing to the website in general.

## Contributing Code

Code-level contributions to React Native generally come in the form of [pull requests](https://help.github.com/en/articles/about-pull-requests). These are done by forking the repo and making changes locally.

### Step-by-step Guide

Whenever you are ready to contribute code, check out our [step-by-step guide to sending your first pull request](/contributing/how-to-open-a-pull-request), or read the [How to Contribute Code](/contributing/how-to-contribute-code) page for more details.

### Tests

Tests help us prevent regressions from being introduced to the codebase. The GitHub repository is continuously tested using CircleCI, the results of which are available through the Checks functionality on [commits](https://github.com/facebook/react-native/commits/HEAD) and pull requests.

You can learn more about running and writing tests on the [How to Run and Write Tests](/contributing/how-to-run-and-write-tests) page.

## Community Contributions

Contributions to React Native are not limited to GitHub. You can help others by sharing your experience using React Native, whether that is through blog posts, presenting talks at conferences, or simply sharing your thoughts on Twitter and tagging [@ReactNative](https://twitter.com/reactnative).