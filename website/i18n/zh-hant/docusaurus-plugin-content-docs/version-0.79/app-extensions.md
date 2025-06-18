---
id: app-extensions
title: App Extensions
---

應用程式擴充功能讓您能在主應用程式之外提供自訂功能與內容。iOS 上有不同類型的應用程式擴充功能，這些都在 [App Extension Programming Guide](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1) 中有詳細說明。本指南將簡要介紹如何在 iOS 上利用應用程式擴充功能。

## 擴充功能中的記憶體使用

由於這些擴充功能是在常規應用沙盒之外載入的，很可能會同時載入多個應用程式擴充功能。如您所料，這些擴充功能有較小的記憶體使用限制。在開發應用程式擴充功能時請牢記這一點。強烈建議始終在實際裝置上測試您的應用程式，開發擴充功能時更是如此：開發者經常發現擴充功能在 iOS 模擬器中運作良好，但實際裝置上的用戶卻回報擴充功能無法載入。

我們強烈建議您觀看 Conrad Kramer 關於 [擴充功能中的記憶體使用](https://www.youtube.com/watch?v=GqXMqn6MXrM) 的演講，以深入了解此主題。

### 今日小工具

今日小工具的記憶體限制為 16 MB。實際上，使用 React Native 實現的今日小工具可能運作不穩定，因為記憶體使用量往往過高。如果您的今日小工具顯示「無法載入」訊息，則表示可能已超出記憶體限制：

![](/docs/assets/TodayWidgetUnableToLoad.jpg)

務必在真實裝置上測試您的應用程式擴充功能，但請注意這可能仍不足夠，尤其是處理今日小工具時。除錯配置的建置版本更容易超出記憶體限制，而發行配置的建置版本不會立即失敗。我們強烈建議使用 [Xcode Instruments](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html) 分析實際記憶體使用情況，因為您的發行配置建置很可能已接近 16 MB 限制。在這種情況下，執行常見操作（例如從 API 獲取數據）可能迅速超過 16 MB 限制。

若要實驗 React Native 今日小工具實現的限制，請嘗試擴展 [react-native-today-widget](https://github.com/matejkriz/react-native-today-widget/) 中的範例專案。

### 其他應用程式擴充功能

其他類型的應用程式擴充功能比今日小工具具有更高的記憶體限制。例如，自訂鍵盤擴充功能限制為 48 MB，分享擴充功能限制為 120 MB。使用 React Native 實現這類應用程式擴充功能更為可行。一個概念驗證範例是 [react-native-ios-share-extension](https://github.com/andrewsardone/react-native-ios-share-extension)。