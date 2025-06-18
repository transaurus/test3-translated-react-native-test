---
title: Meet Doctor, a new React Native command
author: Lucas Bento
authorTitle: React Native Community
authorURL: 'https://twitter.com/lbentosilva'
authorImageURL: 'https://avatars3.githubusercontent.com/u/6207220?s=460&v=4'
authorTwitter: lbentosilva
tags: [announcement]
---

在 React Native 社群 6 位貢獻者提交超過 20 個 pull request 後，我們興奮地推出 `react-native doctor` 指令，這項新功能將協助您進行環境初始化、疑難排解，並自動修復開發環境中的錯誤。`doctor` 指令的設計靈感主要來自 [Expo](https://expo.io/) 和 [Homebrew](https://brew.sh/) 的同名指令，並融入 [Jest](https://jestjs.io/) 風格的介面元素。

<!--truncate-->

實際運作畫面如下：

<p style={{textAlign: 'center'}}>
  <video width={700} controls="controls" autoPlay style={{borderRadius: 5}}>
    <source type="video/mp4" src="/img/homepage/DoctorCommand.mp4" />
  </video>
</p>

## 運作原理

目前 `doctor` 指令支援檢測 React Native 依賴的多數軟體與函式庫，例如 CocoaPods、Xcode 和 Android SDK。執行後會找出開發環境的問題，並提供自動修復選項。若無法自動修復，將顯示錯誤訊息與手動修復指南連結，如下所示：

<p style={{textAlign: 'center'}}>
  <img width={700} src="/img/DoctorManualInstallationMessage.png" alt="Doctor command with a link to help on Android SDK's installation" title="Doctor command with a link to help on Android SDK's installation" />
</p>

## 立即試用

`doctor` 指令已內建於 React Native 0.62 版本，但您無需升級即可立即體驗：

```sh
npx @react-native-community/cli doctor
```

## 目前支援的檢測項目

`doctor` 當前支援以下環境檢測：

- Node.js (>= 8.3)
- yarn (>= 1.10)
- npm (>= 4)
- Watchman (>= 4)，用於開發模式下監控檔案系統變更

Android 環境專屬檢測：

- Android SDK (>= 26)，Android 的軟體運行環境
- Android NDK (>= 19)，Android 原生開發工具包
- `ANDROID_HOME`，Android SDK 必需的環境變數

iOS 環境專屬檢測：

- Xcode (>= 10)，用於開發、建置與發布 iOS 應用的整合開發環境
- CocoaPods，iOS 應用程式依賴管理工具
- ios-deploy (選用)，CLI 內部用於在實體 iOS 裝置安裝應用的函式庫

## 致謝

特別感謝 React Native 社群成員的貢獻，尤其是 [@thymikee](https://github.com/thymikee)、[@thib92](https://github.com/thib92)、[@jmeistrich](https://github.com/jmeistrich)、[@tido64](https://github.com/tido64) 和 [@rickhanlonii](https://github.com/rickhanlonii)。