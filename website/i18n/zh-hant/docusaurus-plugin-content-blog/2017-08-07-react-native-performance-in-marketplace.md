---
title: React Native Performance in Marketplace
author: Aaron Chiu
authorTitle: Software Engineer at Facebook
authorURL: 'https://www.facebook.com/aaronechiu'
authorFBID: 1057500063
authorTwitter: AaaChiuuu
tags: [engineering]
---

React Native 在 Facebook 系列的多個應用程式中被廣泛使用，包括主 Facebook 應用程式中的頂層標籤。本文的重點是一個高度可見的產品——[Marketplace](https://newsroom.fb.com/news/2016/10/introducing-marketplace-buy-and-sell-with-your-local-community/)。該產品已在十幾個國家推出，讓用戶能夠發現其他用戶提供的產品和服務。

2017 年上半年，通過 Relay 團隊、Marketplace 團隊、Mobile JS Platform 團隊和 React Native 團隊的共同努力，我們將 Marketplace 的「互動時間」（Time to Interaction, TTI）在 Android [2010-11 年級設備](https://code.facebook.com/posts/307478339448736/year-class-a-classification-system-for-android/)上縮短了一半。Facebook 歷來將這些設備視為低端 Android 設備，它們在任何平台或設備類型上的 TTI 都是最慢的。

典型的 React Native 啟動過程大致如下：

![](/blog/assets/RNPerformanceStartup.png)

> 免責聲明：比例不代表實際情況，會根據 React Native 的配置和使用方式而有所不同。

我們首先初始化 React Native 核心（即「Bridge」），然後運行產品特定的 JavaScript 代碼，這些代碼決定了 React Native 將在「原生處理時間」中渲染哪些原生視圖。

### 不同的方法

我們早期犯的一個錯誤是讓 [Systrace 和 CTScan](https://code.facebook.com/posts/747457662026706/performance-instrumentation-for-android-apps/) 主導我們的性能優化工作。這些工具在 2016 年幫助我們發現了很多低垂的果實，但我們發現 Systrace 和 CTScan **並不能代表實際生產場景**，也無法模擬真實環境中的情況。時間分配的比例經常不準確，有時甚至完全偏離實際。極端情況下，我們預期只需幾毫秒的操作實際上可能耗費數百甚至數千毫秒。話雖如此，CTScan 仍然有用，我們發現它能在大約三分之一的性能退化問題進入生產環境之前捕捉到它們。

在 Android 上，我們將這些工具的不足歸因於以下事實：1) React Native 是一個多線程框架；2) Marketplace 與許多複雜視圖（如 Newsfeed 和其他頂層標籤）共存；3) 計算時間差異極大。因此，今年上半年，我們幾乎完全讓生產環境的測量和細分數據來驅動我們的決策和優先級排序。

### 生產環境檢測之路

表面上看，檢測生產環境似乎很簡單，但實際上這是一個相當複雜的過程。我們經歷了多次迭代週期，每次耗時 2-3 週；這是由於從提交代碼到主分支，再到將應用推送到 Play Store，最後收集足夠的生產樣本以確保我們的工作可靠，整個過程存在延遲。每個迭代週期都涉及確認我們的細分數據是否準確、粒度是否合適，以及它們是否能正確累加為總時間。我們不能依賴 alpha 和 beta 版本，因為它們無法代表一般用戶群體。本質上，我們非常細緻地構建了一個基於數百萬樣本聚合的、極其準確的生產環境追蹤系統。

我們之所以仔細驗證每一毫秒的細分數據是否正確累加到其父指標，是因為我們很早就意識到檢測中存在漏洞。事實證明，我們最初的細分數據沒有考慮到線程跳轉導致的停頓。線程跳轉本身並不耗時，但跳轉到已經在處理任務的繁忙線程則非常耗時。我們最終通過在適當的時機插入 `Thread.sleep()` 調用來在本地重現這些阻塞問題，並通過以下方式修復了它們：

1. 移除對 AsyncTask 的依賴，
2. 取消在 UI 線程上強制初始化 ReactContext 和 NativeModules 的做法，以及
3. 移除在初始化時測量 ReactRootView 的依賴。

這些線程阻塞問題的解決，總共讓啟動時間減少了超過 25%。

Production metrics also challenged some of our prior assumptions. For example, we used to pre-load many JavaScript modules on the startup path under the assumption that co-locating modules in one bundle would reduce their initialization cost. However, the cost of pre-loading and co-locating these modules far outweighed the benefits. By re-configuring our inline require blacklists and removing JavaScript modules from the startup path, we were able to avoid loading unnecessary modules such as Relay Classic (when only [Relay Modern](https://relay.dev/docs/new-in-relay-modern) was necessary). Today, our `RUN_JS_BUNDLE` breakdown is over 75% faster.

We also found wins by investigating product-specific native modules. For example, by lazily injecting a native module's dependencies, we reduced that native module's cost by 98%. By removing the contention of Marketplace startup with other products, we reduced startup by an equivalent interval.

The best part is that many of these improvements are broadly applicable to all screens built with React Native.

## Conclusion

People assume that React Native startup performance problems are caused by JavaScript being slow or exceedingly high network times. While speeding up things like JavaScript would bring down TTI by a non-trivial sum, each of these contribute a much smaller percentage of TTI than was previously believed.

The lesson so far has been to _measure, measure, measure!_ Some wins come from moving run-time costs to build time, such as Relay Modern and [Lazy NativeModules](https://github.com/facebook/react-native/commit/797ca6c219b2a44f88f10c61d91e8cc21e2f306e). Other wins come from avoiding work by being smarter about parallelizing code or removing dead code. And some wins come from large architectural changes to React Native, such as cleaning up thread blockages. There is no grand solution to performance, and longer-term performance wins will come from incremental instrumentation and improvements. Do not let cognitive bias influence your decisions. Instead, carefully gather and interpret production data to guide future work.

## Future plans

In the long term, we want Marketplace TTI to be comparable to similar products built with Native, and, in general, have React Native performance on par with native performance. Further more, although this half we drastically reduced the bridge startup cost by about 80%, we plan to bring the cost of the React Native bridge close to zero via projects like [Prepack](https://prepack.io/) and more build time processing.