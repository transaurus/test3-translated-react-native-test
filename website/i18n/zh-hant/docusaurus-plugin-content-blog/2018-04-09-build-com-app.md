---
title: Built with React Native - The Build.com app
author: Garrett McCullough
authorTitle: Senior Mobile Engineer
authorURL: 'https://twitter.com/gwmccull'
authorImageURL: 'https://pbs.twimg.com/profile_images/955503100785172486/UrMKkQXc_400x400.jpg'
authorTwitter: gwmccull
tags: [showcase]
---

[Build.com](https://www.build.com/), headquartered in Chico, California, is one of the largest online retailers for home improvement items. The team has had a strong web-centric business for 18 years and began thinking about a mobile App in 2015. Building unique Android and iOS apps wasn’t practical due to our small team and limited native experience. Instead, we decided to take a risk on the very new React Native framework. Our initial commit was on August 12, 2015 using React Native v0.8.0! We were live in both App Stores on October 15, 2016. Over the last two years, we’ve continued to upgrade and expand the app. We are currently on React Native version 0.53.0.

You can check out the app at [https://www.build.com/app](https://www.build.com/app).

<p align="center">
  <img src="/blog/assets/build-com-blog-image.jpg" />
</p>

## Features

Our app is full featured and includes everything that you’d expect from an e-commerce app: product listings, search and sorting, the ability to configure complex products, favorites, etc. We accept standard credit card payment methods as well as PayPal, and Apple Pay for our iOS users.

A few standout features you might not expect include:

1. 3D models available for around 40 products with 90 finishes
2. Augmented Reality (AR) to allow the user to see how lights and faucets will look in their home at 98% size accuracy. The Build.com React Native App is featured in the Apple App Store for AR Shopping! AR is now available for Android and iOS!
3. Collaborative project management features that allow people to put together shopping lists for the different phases of their project and collaborate around selection

We’re working on many new and exciting features that will continue to improve our app experience including the next phase of Immersive Shopping with AR.

## Our Development Workflow

Build.com allows each dev to choose the tools that best suit them.

- IDEs include Atom, IntelliJ, VS Code, Sublime, Eclipse, etc.
- For Unit testing, developers are responsible for creating Jest unit tests for any new components and we’re working to increase the coverage of older parts of the app using `jest-coverage-ratchet`.
- We use Jenkins to build out our beta and release candidates. This process works well for us but still requires significant work to create the release notes and other artifacts.
- Integration Testing include a shared pool of testers that work across desktop, mobile and web. Our automation engineer is building out our suite of automated integration tests using Java and Appium.
- Other parts of the workflow include a detailed eslint configuration, custom rules that enforce properties needed for testing, and pre-push hooks that block offending changes.

## Libraries Used in the App

The Build.com app relies on a number of common open source libraries including: Redux, Moment, Numeral, Enzyme and a bunch of React Native bridge modules. We also use a number of forked open source libraries; forked either because they were abandoned or because we needed custom features. A quick count shows around 115 JavaScript and native dependencies. We would like to explore tools that remove unused libraries.

We're in the process of adding static typing via TypeScript and looking into optional chaining. These features could help us with solving a couple classes of bugs that we still see:

- Data that is the wrong type
- Data that is undefined because an object didn’t contain what we expected

## Open Source Contributions

Since we rely so heavily on open source, our team is committed to contributing back to the community. Build.com allows the team to open source libraries that we've built and encourages us contribute back to the libraries that we use.

We’ve released and maintained a number of React Native libraries:

- `react-native-polyfill`
- `react-native-simple-store`
- `react-native-contact-picker`

我們也貢獻了許多函式庫，包括：React 和 React Native、`react-native-schemes-manager`、`react-native-swipeable`、`react-native-gallery`、`react-native-view-transformer`、`react-native-navigation`。

## 我們的旅程

過去幾年，我們見證了 React Native 及其生態系統的快速成長。早期時，似乎每個 React Native 版本都會修復一些錯誤，但又會引入幾個新的問題。例如，Android 上的遠端 JS 除錯功能曾失效數月之久。幸運的是，2017 年後情況變得穩定許多。

### 導航函式庫

我們反覆面臨的一大挑戰是導航函式庫。很長一段時間，我們使用 Expo 的 ex-nav 函式庫。它運作良好，但最終被棄用。然而，當時我們正處於密集的功能開發階段，因此抽時間更換導航函式庫並不可行。這意味著我們必須分叉該函式庫並進行修補，以支援 React 16 和 iPhone X。最終，我們得以遷移到 [`react-native-navigation`](https://github.com/wix/react-native-navigation)，希望它能持續獲得支援。

### 橋接模組

另一個重大挑戰是橋接模組。剛開始時，許多關鍵橋接功能缺失。我的一位隊友編寫了 `react-native-contact-picker`，因為我們的應用需要存取 Android 通訊錄選擇器。我們也遇到許多橋接模組因 React Native 的變更而損壞的情況。例如，React Native v40 中有一個破壞性變更，當我們升級應用時，我不得不提交 PR 來修復 3 到 4 個尚未更新的函式庫。

## 展望未來

隨著 React Native 持續發展，我們對社群的願望清單包括：

- 穩定並改進導航函式庫
- 維護 React Native 生態系統中函式庫的支援
- 改善為專案添加原生函式庫和橋接模組的體驗

React Native 社群中的公司和個人一直積極貢獻時間和精力來改進我們共用的工具。如果你尚未參與開源，希望你考慮改進你所使用函式庫的程式碼或文件。有許多文章可以幫助你入門，而且可能比你想象的容易得多！