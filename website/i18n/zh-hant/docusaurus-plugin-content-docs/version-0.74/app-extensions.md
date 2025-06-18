---
id: app-extensions
title: App Extensions
---

應用程式擴充功能讓您能在主應用程式外提供自訂功能與內容。iOS 上有不同類型的應用程式擴充功能，這些都在《[應用程式擴充功能程式設計指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)》中涵蓋。本指南將簡要說明如何在 iOS 上利用應用程式擴充功能。

## 擴充功能中的記憶體使用

由於這些擴充功能是在常規應用程式沙箱外載入，很可能會同時載入多個應用程式擴充功能。如您所料，這些擴充功能有較小的記憶體使用限制。開發應用程式擴充功能時請牢記這一點。強烈建議在實際裝置上測試您的應用程式，開發擴充功能時更是如此：開發者經常發現擴充功能在 iOS 模擬器中運作良好，卻收到使用者回報擴充功能無法在實際裝置上載入。

我們強烈建議您觀看 Conrad Kramer 關於《[擴充功能中的記憶體使用](https://www.youtube.com/watch?v=GqXMqn6MXrM)》的演講，以深入了解此主題。

### 今日小工具

今日小工具的記憶體限制為 16 MB。實際上，使用 React Native 實現的今日小工具可能因記憶體使用量過高而運作不穩定。若您的今日小工具顯示「無法載入」訊息，即表示可能超出記憶體限制：

![](/docs/assets/TodayWidgetUnableToLoad.jpg)

務必在實際裝置上測試您的應用程式擴充功能，但請注意這可能仍不足夠，尤其是處理今日小工具時。除錯配置的建置版本較容易超出記憶體限制，而發布配置的建置版本不會立即失敗。我們強烈建議使用 [Xcode Instruments](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html) 分析實際記憶體使用情況，因為您的發布配置建置很可能已接近 16 MB 限制。在此類情況下，執行常見操作（例如從 API 獲取資料）可能迅速超過 16 MB 限制。

若要實驗 React Native 今日小工具實現的限制，可嘗試擴展 [react-native-today-widget](https://github.com/matejkriz/react-native-today-widget/) 中的範例專案。

### 其他應用程式擴充功能

其他類型的應用程式擴充功能比今日小工具擁有更高的記憶體限制。例如，自訂鍵盤擴充功能限制為 48 MB，分享擴充功能限制為 120 MB。使用 React Native 實現此類擴充功能更為可行。一個概念驗證範例是 [react-native-ios-share-extension](https://github.com/andrewsardone/react-native-ios-share-extension)。