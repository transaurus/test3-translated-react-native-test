---
title: 'Use a framework to build React Native apps'
authors: [cortinico]
tags: [announcement]
date: 2024-06-25
---

在 [React Conf](https://www.youtube.com/live/0ckOUBiuxVY?si=pU4qP4eB5iWfY0IG&t=2320) 上，我們更新了關於開始構建 React Native 應用的最佳工具指南：**React Native 框架**——一個包含所有必要 API 的工具箱，讓您能夠構建生產就緒的應用。

使用 React Native 框架（例如 Expo）現在是創建新應用的**推薦**方法。

在這篇部落格文章中，我們將詳細介紹這些框架是什麼，以及對於您作為 React Native 開發者開始新項目意味著什麼。

<!-- truncate  -->

## 什麼是 React Native 框架？

如果您一直在構建生產應用，您可能知道有一系列常見問題遲早需要解決。

無論是在網頁還是原生應用上構建任何應用，您可能希望用戶能夠在不同屏幕之間導航、獲取數據並存儲用戶狀態。但對於原生應用來說，還有更多需要處理的問題：您需要工具來升級 React Native 版本之間的原生代碼、管理所有依賴的兼容版本，以及處理原生構建工具。

沒有合適的工具，將應用從想法帶到生產環境需要付出巨大努力。

我們希望您專注於為用戶編寫漂亮的應用和功能，而不是反覆解決這些常見問題。

這就是為什麼我們認為，您體驗 React Native 的最佳方式是通過一個提供所有必要工具的工具箱框架來構建生產就緒的應用。

我們發現**您要麼使用一個框架，要麼在構建自己的框架**。

構建自己的框架並沒有錯，通過為路由、導航、部署等問題制定自己的解決方案。像 Meta 和 Microsoft 這樣的大公司內部構建自己的框架，以深度集成到他們的棕地應用中。但我們相信大多數人使用現有框架會更好。

如果您一直在網頁上使用 React，您可能熟悉類似的[生產級 React 框架](https://react.dev/learn/start-a-new-react-project#production-grade-react-frameworks)概念。

截至目前，唯一推薦的 React Native 社區框架是 [Expo](https://docs.expo.dev/)。Expo 的團隊從 React Native 早期就開始投資於 React Native 生態系統，我們認為 Expo 提供的開發者體驗是目前最好的。

:::note

Expo 框架是並將保持免費和開源，而 Expo Application Services (EAS) 是一個可選的付費服務。

:::

<!--alex ignore he-she retext-equality-->

如果您最近沒有使用過 Expo，請不要錯過 [Kadi @ Expo 的這場演講](https://www.youtube.com/live/0ckOUBiuxVY?si=N-WSfmAJSMfd6wDL&t=3888)，她展示了 2024 年您可以用 Expo 做什麼。

我們還更新了網站上的[入門頁面](https://reactnative.dev/docs/environment-setup)以反映這一推薦。

## 框架將如何影響您？

如果您已經在使用推薦的框架（如 Expo），那麼您已經準備好了！

如果您想將現有應用遷移到 Expo，可以在[官方 Expo 網站](https://docs.expo.dev/bare/overview/)上找到相關說明。Expo 提供了許多好處，例如更輕鬆的[升級 React Native 版本](https://docs.expo.dev/workflow/upgrading-expo-sdk-walkthrough/)方式、更好的開發者體驗等等。

However, if you can't or don't want to migrate to Expo, that's fine too. Using React Native without an official framework will continue to be supported. The tools you’ve been using such as React Native Community CLI, Template and [Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) will keep on working as usual.

The `react-native init` command has moved out of core and is now accessible via:

```
npx @react-native-community/cli@latest init
```

and on GitHub at [react-native-community/cli](https://github.com/react-native-community/cli).

If you’re a React Native library developer, we collected a list of recommendations on which APIs to use. [Read more in the RFC](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0759-react-native-frameworks.md#what-do-we-recommend-to-react-native-library-developers).

## Further reading

If you’re interested in learning more about the reasoning behind this decision, we invite you to read the [RFC0759: React Native Frameworks](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0759-react-native-frameworks.md#what-do-we-recommend-to-react-native-library-developers). This RFC is a result of a multi-month effort involving countless discussions and brainstorming among different partners and players of the React Native ecosystem.

While Expo today is the only recommended framework, the RFC also contains [guidelines](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0759-react-native-frameworks.md#becoming-a-react-native-framework) on how to become a recommended framework, as we hope to see more competition and innovation in this space.

Moreover, you should check out the talk [useFrameworks()](https://www.youtube.com/watch?v=lifGTznLBcw) at App.js 2024 where we presented this RFC and the necessary changes in a short format.

We believe that by clarifying the respective responsibilities of React Native Core and the Frameworks, we can foster a healthier ecosystem and drive growth & innovation for React Native.