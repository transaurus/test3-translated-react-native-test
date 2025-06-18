---
title: 'React Native Monthly #6'
author: Tomislav Tenodi
authorTitle: Founder at Speck
authorURL: 'https://twitter.com/TomislavTenodi'
authorImageURL: 'https://pbs.twimg.com/profile_images/877237660225609729/bKFDwfAq.jpg'
authorTwitter: TomislavTenodi
tags: [engineering]
---

React Native 每月會議持續熱烈進行中！請務必查看本文底部關於下次會議的註記。

### Expo

- 恭喜 [Devin Abbott](https://github.com/dabbott) 和 [Houssein Djirdeh](https://twitter.com/hdjirdeh) 預先發布了《Full Stack React Native》書籍！該書透過構建多個小型應用程式引導你學習 React Native。
- 發布了首個（實驗性）版本的 [reason-react-native-scripts](https://github.com/react-community/reason-react-native-scripts)，幫助人們輕鬆嘗試 [ReasonML](https://reasonml.github.io/)。
- Expo SDK 24 已[發布](https://blog.expo.io/expo-sdk-v24-0-0-is-now-available-bfcac3b50d51)！它基於 [React Native 0.51](https://github.com/facebook/react-native/releases/tag/v0.51.0)，並包含一系列新功能和改進：獨立應用中的圖片打包（無需首次載入時緩存！）、圖片處理 API（裁剪、調整大小、旋轉、翻轉）、人臉檢測 API、新的發布頻道功能（設置頻道的活躍版本並回滾）、追蹤獨立應用構建的網頁儀表板，以及修復了 Android OpenGL 實現與 Android 多任務處理器的長期錯誤，僅舉幾例。
- 我們將從今年一月開始為 React Navigation 分配更多資源。我們堅信，僅使用 React 元件和如 Animated 及 `react-native-gesture-handler` 這樣的基礎功能來構建 React Native 導航是可能且理想的，我們對計劃中的一些改進感到非常興奮。如果你想為社區貢獻，請查看 [react-native-maps](https://github.com/react-community/react-native-maps) 和 [react-native-svg](https://github.com/react-native-community/react-native-svg)，它們都需要一些幫助！

### Infinite Red

- 我們已確定 [Chain React 大會](https://infinite.red/ChainReactConf) 的主題演講嘉賓：[Kent C. Dodds](https://twitter.com/kentcdodds) 和 [Tracy Lee](https://twitter.com/ladyleet)。我們將很快開放徵稿。
- [社區聊天](https://community.infinite.red/) 現有 1600 人參與。
- [React Native 通訊](https://reactnative.cc/) 現有 8500 名訂閱者。
- 目前正在研究使 React Native 抗崩潰的最佳實踐，報告將後續發布。
- 新增了從 [Solidarity](https://shift.infinite.red/effortless-environment-reports-d129d53eb405) 報告的功能。
- 發布了關於在 [React Native 和 Android](https://shift.infinite.red/simple-react-native-android-releases-319dc5e29605) 上發布的 HOW-TO 指南。

### Microsoft

- 已啟動一個[拉取請求](https://github.com/Microsoft/react-native-windows/pull/1419)，將 React Native Windows 的核心橋接遷移到 .NET Standard，使其實際上與操作系統無關。希望許多其他 .NET Core 平台可以擴展該橋接，提供自己的線程模型、JavaScript 運行時和 UIManager（考慮 JavaScriptCore、Xamarin.Mac、Linux Gtk# 和 Samsung Tizen 選項）。

### Wix

- [Detox](https://github.com/wix/detox)
  - 為了擴展端對端測試規模，我們希望縮短CI時間，目前正在開發Detox的並行化支援功能。
  - 已提交[拉取請求](https://github.com/facebook/react-native/pull/16948)以支援自訂版本建置，強化端對端測試的模擬功能。
- [DetoxInstruments](https://github.com/wix/DetoxInstruments)
  - 開發DetoxInstruments的核心功能面臨重大技術挑戰，需透過客製化JSCore實作來暫停JS執行緒以獲取即時JavaScript調用堆疊。在Wix應用內部測試中，分析器揭露了JS執行緒的關鍵效能洞察。
  - 該專案尚未達到穩定版本，但正積極開發中，預計不久後正式發布。
- [React Native Navigation](https://github.com/wix/react-native-navigation)
  - V2版本開發進度大幅加速，原本僅有1名開發者投入20%工時，現已擴編至3名全職開發者。
- Android效能優化
  - 將React Native內建的舊版JSCore替換為最新版本（基於webkitGTK專案尖端程式碼，搭配客製化JIT配置），使JS執行緒效能提升40%。下一步將編譯64位元版本，此項工作基於[Android版JSC建置腳本](https://github.com/SoftwareMansion/jsc-android-buildscripts)，可追蹤[當前進度](https://github.com/DanielZlotin/jsc-android-buildscripts/tree/tip)。

## 後續會議安排

會議討論聚焦於轉型為專題研討模式（例如導航系統、React Native模組獨立儲存庫遷移、文件規範等），以期對社群做出最大貢獻。此調整可能於下次會議實施，歡迎透過推特建議希望探討的主題。