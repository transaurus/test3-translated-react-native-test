---
id: running-on-simulator-ios
title: Running On Simulator
---

## 啟動模擬器

當你初始化 React Native 專案後，可以在新建立的專案目錄中執行 `npx react-native run-ios`。如果一切設定正確，你應該很快就能看到新應用程式在 iOS 模擬器中運行。

## 指定裝置

你可以使用 `--simulator` 參數指定模擬器運行的裝置，後面接上裝置名稱的字串。預設為 `"iPhone 14"`。如果想在 iPhone SE（第 3 代）上運行應用程式，請執行 `npx react-native run-ios --simulator='iPhone SE (3rd generation)'`。

裝置名稱對應 Xcode 中可用的裝置列表。你可以透過在控制台執行 `xcrun simctl list devices` 來查看可用的裝置。

### 指定裝置版本

如果安裝了多個 iOS 版本，還需要指定相應的版本。例如執行 `npx react-native run-ios --simulator='iPhone 14 Pro (16.0)'` 來指定 iOS 版本。

## 指定 UDID

你可以指定從 `xcrun simctl list devices` 命令返回的裝置 UDID。例如執行 `npx react-native run-ios --udid='AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA'`。