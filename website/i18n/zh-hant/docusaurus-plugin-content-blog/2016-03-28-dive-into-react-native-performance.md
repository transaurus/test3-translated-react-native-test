---
title: Dive into React Native Performance
author: Pieter De Baets
authorTitle: Software Engineer at Facebook
authorURL: 'https://github.com/javache'
authorImageURL: 'https://avatars1.githubusercontent.com/u/5676?v=3&s=460'
authorTwitter: javache
tags: [engineering]
---

React Native 讓你能夠使用 JavaScript，透過 React 和 Relay 的宣告式程式設計模型來建構 Android 和 iOS 應用程式。這使得程式碼更加簡潔、易於理解；能夠快速迭代而無需編譯週期；並且可以輕鬆地在多個平台之間共享程式碼。你可以更快地發布應用，並專注於真正重要的細節，讓你的應用看起來和使用起來都很棒。優化效能是其中的重要部分。以下是我們如何讓 React Native 應用啟動速度提升兩倍的故事。

## 為什麼要這麼趕？

應用運行得更快，內容就能更快載入，這意味著人們有更多時間與之互動，而流暢的動畫則讓應用使用起來更加愉快。在新興市場，[2011 年的手機](https://code.facebook.com/posts/952628711437136/classes-performance-and-network-segmentation-on-android/)和[2G 網路](https://newsroom.fb.com/news/2015/10/news-feed-fyi-building-for-all-connectivity/)是主流，專注於效能可以決定一個應用是否可用。

自從在 [iOS](https://reactjs.org/blog/2015/03/26/introducing-react-native.html) 和 [Android](https://code.facebook.com/posts/1189117404435352/react-native-for-android-how-we-built-the-first-cross-platform-react-native-app/) 上發布 React Native 以來，我們一直在改進列表視圖的滾動效能、記憶體效率、UI 響應速度以及應用啟動時間。啟動時間決定了應用的第一印象，並且考驗了框架的所有部分，因此這是最有回報且最具挑戰性的問題。

<footer>
  <a
    href="https://code.facebook.com/posts/895897210527114/dive-into-react-native-performance/"
    className="btn">Read more</a>
</footer>

> 這是節選。閱讀完整文章請前往 [Facebook Code](https://code.facebook.com/posts/895897210527114/dive-into-react-native-performance/)。