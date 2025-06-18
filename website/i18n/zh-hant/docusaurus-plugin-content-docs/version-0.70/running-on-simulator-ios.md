---
id: running-on-simulator-ios
title: Running On Simulator
---

## 啟動模擬器

當你的 React Native 專案初始化完成後，可以在新建立的專案目錄中執行 `npx react-native run-ios`。如果一切設定正確，你應該很快就能看到新應用程式在 iOS 模擬器中運行。

## 指定裝置

你可以使用 `--simulator` 參數指定模擬器運行的裝置，後面接上裝置名稱的字串。預設為 `"iPhone 13"`。如果你想在 iPhone SE（第 2 代）上運行應用程式，請執行 `npx react-native run-ios --simulator='iPhone SE (2nd generation)'`。

裝置名稱對應於 Xcode 中可用的裝置列表。你可以通過在控制台中執行 `xcrun simctl list devices` 來檢查可用的裝置。

### 指定裝置版本

如果你安裝了多個 iOS 版本，還需要指定適當的版本。例如，執行 `npx react-native run-ios --simulator='iPhone 13 Pro (15.5)'` 以指定 iOS 版本。

## 指定 UDID

你可以指定從 `xcrun simctl list devices` 命令返回的裝置 UDID。例如，執行 `npx react-native run-ios --udid='AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA'`。