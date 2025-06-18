---
title: Introducing new iOS WebViews
author: Ramanpreet Nara
authorTitle: Software Engineer at Facebook
authorURL: 'https://github.com/rsnara'
tags: [engineering]
---

長期以來，Apple 一直不鼓勵使用 UIWebView，而推薦使用 WKWebView。在即將於未來幾個月發布的 iOS 12 中，[UIWebView 將正式被棄用](https://developer.apple.com/videos/play/wwdc2018/234/?time=104)。React Native 的 iOS WebView 實作大量依賴於 UIWebView 類別。因此，基於這些發展，我們為 WebView React Native 組件構建了一個新的原生 iOS 後端，該後端使用 WKWebView。

這些變更的最後部分已提交至[此提交](https://github.com/facebook/react-native/commit/33b353c97c31190439a22febbd3d2a9ead49d3c9)，並將在 0.57 版本中提供。

要選擇使用此新實作，請使用 [`useWebKit`](https://reactnative.dev/docs/0.63/webview#usewebkit) 屬性：

```js
<WebView
  useWebKit={true}
  source={{url: 'https://www.google.com'}}
/>
```

## 改進

`UIWebView` 沒有合法的方式來促進 WebView 中運行的 JavaScript 與 React Native 之間的通信。當消息從 WebView 發送時，我們依賴於一個 hack 來將它們傳遞給 React Native。簡而言之，我們將消息數據編碼到具有特殊方案的 url 中，並將 WebView 導航到該 url。在原生端，我們攔截並取消此導航，從 url 中解析數據，最後調用 React Native。這種實作容易出錯且不安全。我很高興地宣布，我們已經利用 `WKWebView` 的功能完全取代了它。

WKWebView 相對於 UIWebView 的其他優勢包括更快的 JavaScript 執行和多進程架構。詳情請參閱此[2014 WWDC](https://developer.apple.com/videos/play/wwdc2014/206)。

## 注意事項

如果您的組件使用以下屬性，則在切換到 WKWebView 時可能會遇到問題。目前，我們建議您避免使用這些屬性：

**行為不一致：**

`automaticallyAdjustContentInsets` 和 `contentInsets` ([提交](https://github.com/facebook/react-native/commit/bacfd9297657569006bab2b1f024ad1f289b1b27))

當您向 `WKWebView` 添加 contentInsets 時，它不會改變 `WKWebView` 的視口。視口大小與框架保持相同。對於 `UIWebView`，視口大小實際上會改變（如果內容插入為正，則會變小）。

`backgroundColor` ([提交](https://github.com/facebook/react-native/commit/215fa14efc2a817c7e038075163491c8d21526fd))

使用 WebView 的新 iOS 實作時，如果您使用此屬性，背景顏色可能會閃現。此外，`WKWebView` 渲染透明背景的方式與 `UIWebview` 不同。詳情請參閱提交描述。

**不支援：**

`scalesPageToFit` ([提交](https://github.com/facebook/react-native/commit/b18fddadfeae5512690a0a059a4fa80c864f43a3))

WKWebView 不支援 scalesPageToFit 屬性，因此我們無法在 WebView React Native 組件上實作此功能。