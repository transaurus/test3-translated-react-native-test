---
id: app-extensions
title: App Extensions
---

應用程式擴充功能讓您能在主應用程式之外提供自訂功能與內容。iOS上有不同類型的應用程式擴充功能，這些都在[《App Extension Programming Guide》](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)中有詳細說明。本指南將簡要介紹如何在iOS上利用這些擴充功能。

## 擴充功能中的記憶體使用

由於這些擴充功能是在常規應用沙盒之外載入的，很可能會同時載入多個擴充功能。如您所料，這些擴充功能有嚴格的記憶體使用限制。開發時請務必注意這些限制。強烈建議在實體裝置上測試您的應用程式，開發擴充功能時更是如此：開發者經常發現擴充功能在iOS模擬器上運作良好，但實際裝置上的用戶卻回報無法載入。

我們強烈建議觀看Conrad Kramer關於[《擴充功能中的記憶體使用》](https://www.youtube.com/watch?v=GqXMqn6MXrM)的演講，以深入了解此主題。

### 今日小工具

今日小工具的記憶體限制為16 MB。使用React Native實現的今日小工具可能因記憶體使用量過高而運作不穩定。若小工具顯示「無法載入」訊息，即表示超出記憶體限制：

![](/docs/assets/TodayWidgetUnableToLoad.jpg)

務必在實體裝置上測試擴充功能，但請注意這可能仍不足夠，特別是處理今日小工具時。除錯模式的建置版本更容易超出記憶體限制，而發行模式的建置版本不會立即失敗。強烈建議使用[Xcode Instruments](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/index.html)分析實際記憶體使用情況，因為發行建置很可能已接近16 MB限制。在此情況下，執行常見操作（如從API獲取資料）可能瞬間超過限制。

若要實驗React Native今日小工具的極限，可嘗試擴充[react-native-today-widget](https://github.com/matejkriz/react-native-today-widget/)中的範例專案。

### 其他應用程式擴充功能

Other types of app extensions have greater memory limits than the Today widget. For instance, Custom Keyboard extensions are limited to 48 MB, and Share extensions are limited to 120 MB. Implementing such app extensions with React Native is more viable. One proof of concept example is [react-native-ios-share-extension](https://github.com/andrewsardone/react-native-ios-share-extension).