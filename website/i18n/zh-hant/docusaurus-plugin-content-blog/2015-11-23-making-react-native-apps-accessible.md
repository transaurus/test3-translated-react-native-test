---
title: Making React Native apps accessible
author: Georgiy Kassabli
authorTitle: Software Engineer at Facebook
authorURL: 'https://www.facebook.com/georgiy.kassabli'
authorImageURL: 'https://scontent-sea1-1.xx.fbcdn.net/v/t1.0-1/c0.0.160.160/p160x160/1978838_795592927136196_1205041943_n.jpg?_nc_log=1&oh=d7a500fdece1250955a4d27b0a80fee2&oe=59E8165A'
hero: '/blog/assets/blue-hero.png'
tags: [engineering]
---

隨著 React 在網頁端和 React Native 在行動端的推出，我們為開發者提供了一個建構產品的新前端框架。打造穩健產品的關鍵之一，是確保所有人都能使用它，包括視力受損或其他障礙的使用者。React 和 React Native 的無障礙功能 API 能讓任何 React 驅動的體驗，都能被使用輔助技術的人操作，例如為盲人和視障者設計的螢幕閱讀器。

本文將聚焦於 React Native 應用程式。我們將 React 無障礙功能 API 設計得與 Android 和 iOS API 的外觀和操作感相似。如果您曾為 Android、iOS 或網頁開發過無障礙應用程式，應該會對 React AX API 的框架和術語感到熟悉。舉例來說，您可以將 UI 元素設為「可存取」（從而暴露給輔助技術），並使用「accessibilityLabel」為元素提供字串描述：

```
<View accessible={true} accessibilityLabel=”This is simple view”>
```

讓我們透過 Facebook 自家 React 驅動的產品 **廣告管理員應用程式**，來逐步解析 React AX API 的進階應用實例。

<footer>
  <a
    href="https://code.facebook.com/posts/435862739941212/making-react-native-apps-accessible/"
    className="btn">Read more</a>
</footer>

> 此為節錄內容。完整文章請見 [Facebook Code](https://code.facebook.com/posts/435862739941212/making-react-native-apps-accessible/)。