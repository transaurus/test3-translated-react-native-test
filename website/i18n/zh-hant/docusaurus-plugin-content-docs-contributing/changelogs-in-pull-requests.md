---
title: Changelogs in Pull Requests
---

您拉取請求中的變更日誌條目可視為變更的「簡要說明」：這些變更是否影響 Android？是否為重大變更？是否有新增功能？

使用標準化格式提供變更日誌能協助發行協調者撰寫發行說明。請在拉取請求描述中包含變更日誌。若拉取請求被合併，您的描述將作為提交訊息使用。

### 格式

變更日誌條目需遵循以下格式：

```
## Changelog:

[Category] [Type] - Message
```

「類別」欄位可為以下之一：

- **Android**：影響 Android 的變更。
- **iOS**：影響 iOS 的變更。
- **General**：不屬於其他類別的變更。
- **Internal**：與開發者無關的內部變更。

「類型」欄位可為以下之一：

- **Breaking**：重大變更。
- **Added**：新增功能。
- **Changed**：現有功能變更。
- **Deprecated**：即將移除的功能。
- **Removed**：已移除的功能。
- **Fixed**：錯誤修正。
- **Security**：安全性漏洞修補。

最後，「訊息」欄位需簡要說明「變更內容與原因」，讓 React Native 使用者了解重要變更。

更多細節請參閱：[如何撰寫好的變更日誌？](https://keepachangelog.com/en/1.0.0/#how) 與 [為何需要維護變更日誌？](https://keepachangelog.com/en/1.0.0/#why)

### 範例

- `[General] [Added] - 為 ScrollView 元件新增 snapToOffsets 屬性`
- `[General] [Fixed] - 修正 ScrollView 元件中 snapToInterval 的多項問題`
- `[iOS] [Fixed] - 修復 RCTImagePicker 的當機問題`

### 常見問題

#### 若我的拉取請求同時包含 Android 與 JavaScript 的變更？

請使用 Android 類別。

#### 若我的拉取請求同時包含 Android 與 iOS 的變更？

若變更透過單一拉取請求提交，請使用 General 類別。

#### 若我的拉取請求同時包含 Android、iOS 與 JavaScript 的變更？

若變更透過單一拉取請求提交，請使用 General 類別。

#### 如果...？

任何變更日誌條目都比沒有好。若不確定類別是否正確，請在「訊息」欄位簡潔描述變更內容。

這些條目會由 [`@rnx-kit/rn-changelog-generator`](https://github.com/microsoft/rnx-kit/tree/main/incubator/rn-changelog-generator) 腳本用於建立初稿，再由發行協調者編輯。

您的說明將用於將變更正確歸類至最終發行說明中。