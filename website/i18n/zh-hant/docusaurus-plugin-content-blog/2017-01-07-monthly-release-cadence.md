---
title: 'A Monthly Release Cadence: Releasing December and January RC'
author: Eric Vicenti
authorTitle: Engineer at Facebook
authorURL: 'https://twitter.com/EricVicenti'
authorImageURL: 'https://secure.gravatar.com/avatar/077ad5372b65567fe952a99f3b627048?s=128'
authorTwitter: EricVicenti
tags: [announcement]
---

在React Native推出後不久，我們開始每兩週發布一次版本，以幫助社群採用新功能，同時保持生產環境的版本穩定性。在Facebook內部，我們需要每兩週為iOS生產應用穩定程式碼庫，因此決定以相同節奏開源發布。如今許多Facebook應用改為每週發布（尤其是Android平台）。由於我們每週從master分支直接發布，必須保持其高度穩定性，雙週發布節奏甚至已不再惠及內部貢獻者。

我們經常收到社群反饋表示難以跟上這種發布節奏。像[Expo](https://expo.io/)這樣的工具不得不跳過隔次發布，才能管理版本的快速變更。顯然雙週發布並未真正服務好社群需求。

### 現改為每月發布

我們很高興宣布新的月度發布週期，以及經過整個12月穩定的2016年12月版本`v0.40`已準備就緒（請務必[更新iOS原生模組的標頭檔](https://github.com/facebook/react-native/releases/tag/v0.40.0)）。

雖然可能因避開週末或處理突發問題而微調日期，但現在您可以預期：每月首日提供候選版本，月底發布正式版。

### 使用當月版本獲得最佳支援

一月候選版本已可試用，您可[在此查看新功能](https://github.com/facebook/react-native/releases/tag/v0.41.0-rc.0)。

要掌握變更動向並向React Native貢獻者提供更好反饋，請盡可能使用當月候選版本。每個版本月底發布時，其包含的變更已在Facebook生產環境應用運行超過兩週。

您可透過新的[react-native-git-upgrade](/blog/2016/12/05/easier-upgrades)指令輕鬆升級應用：

```
npm install -g react-native-git-upgrade
react-native-git-upgrade 0.41.0-rc.0
```

我們希望這個更簡潔的方案能幫助社群更輕鬆追蹤React Native的變更，並盡快採用新版本！

（感謝[Martin Konicek](https://github.com/mkonicek)提出此計劃，以及[Mike Grabowski](https://github.com/grabbou)實現它）