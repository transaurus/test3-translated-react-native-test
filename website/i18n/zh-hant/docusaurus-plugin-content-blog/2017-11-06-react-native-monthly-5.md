---
title: 'React Native Monthly #5'
author: Tomislav Tenodi
authorTitle: Founder at Speck
authorURL: 'https://github.com/tenodi'
authorImageURL: 'https://pbs.twimg.com/profile_images/877237660225609729/bKFDwfAq.jpg'
authorTwitter: TomislavTenodi
tags: [engineering]
---

React Native 每月會議持續進行中！來看看我們的團隊最近在做些什麼。

### Callstack

- 我們一直在進行 React Native CI 的工作。最重要的是，我們已從 Travis 遷移到 Circle，讓 React Native 擁有單一且統一的 CI 流程。
- 我們舉辦了 [Hacktoberfest - React Native 版本](https://blog.callstack.io/announcing-hacktoberfest-7313ea5ccf4f)，與參與者一起嘗試提交許多開源專案的 pull requests。
- 我們持續開發 [Haul](https://github.com/callstack/haul)。上個月，我們提交了兩個新版本，包括支援 webpack 3。我們計劃增加 [CRNA](https://github.com/react-community/create-react-native-app) 和 [Expo](https://github.com/expo/expo) 的支援，並改進 HMR。我們的開發路線圖公開在 issue tracker 上。如果您有任何建議或反饋，請告訴我們！

### Expo

- 發佈了 [Expo SDK 22](https://blog.expo.io/expo-sdk-v22-0-0-is-now-available-7745bfe97fc6)（使用 React Native 0.49）並更新了 [CRNA](https://github.com/react-community/create-react-native-app)。
  - 包含改進的啟動畫面 API、基礎 ARKit 支援、「DeviceMotion」API、iOS11 上的 SFAuthenticationSession 支援，以及[更多功能](https://blog.expo.io/expo-sdk-v22-0-0-is-now-available-7745bfe97fc6)。
- 您的 [snacks](https://snack.expo.io) 現在可以擁有多個 JavaScript 檔案，並且只需將圖片和其他資源拖曳到編輯器中即可上傳。
- 貢獻於 [react-navigation](https://github.com/react-community/react-navigation) 以增加對 iPhone X 的支援。
- 專注於使用 Expo 開發大型應用時遇到的問題。例如：
  - 對部署到多個環境（如 staging、production 和任意頻道）的一流支援。頻道將支援回滾和設定特定頻道的當前版本。如果您想成為早期測試者，請聯繫 [@expo_io](https://twitter.com/expo_io)。
  - 我們也在改進獨立應用程式的建置基礎設施，並增加在獨立應用程式中打包圖片和其他非程式碼資源的支援，同時保持透過空中更新資源的能力。

### Facebook

- 更好的 RTL 支援：
  - 我們引入了一些方向感知的樣式：
    - 位置：
      - (left|right) → (start|end)
    - 邊距：
      - margin(Left|Right) → margin(Start|End)
    - 內距：
      - padding(Left|Right) → padding(Start|End)
    - 邊框：
      - borderTop(Left|Right)Radius → borderTop(Start|End)Radius
      - borderBottom(Left|Right)Radius → borderBottom(Start|End)Radius
      - border(Left|Right)Width → border(Start|End)Width
      - border(Left|Right)Color → border(Start|End)Color
  - 在 RTL 中，「left」和「right」的意義在位置、邊距、內距和邊框樣式中被交換。在幾個月內，我們將移除這種行為，讓「left」始終表示「左」，「right」始終表示「右」。這些破壞性變更隱藏在一個標誌下。在您的 React Native 組件中使用 `I18nManager.swapLeftAndRightInRTL(false)` 來啟用它們。
- 正在為我們的內部原生模組進行 [Flow](https://github.com/facebook/flow) 類型檢查，並使用這些類型生成 Java 中的介面和 ObjC 中的協議，這些介面和協議必須由原生實現來實作。我們希望這些代碼生成工具最早能在明年開源。

### Infinite Red

- 新的開源工具，用於幫助 React Native 和其他專案。更多資訊請見[這裡](https://shift.infinite.red/solidarity-the-cli-for-developer-sanity-672fa81b98e9)。
- 正在改版 [Ignite](https://github.com/infinitered/ignite) 以發佈新的樣板（代號：Bowser）。

### Shoutem

- 改進 Shoutem 上的開發流程。我們希望簡化從創建應用到第一個自訂畫面的過程，使其變得非常容易，從而降低新 React Native 開發者的門檻。準備了幾場工作坊來測試新功能。我們還改進了 [Shoutem CLI](https://github.com/shoutem/cli) 以支援新的流程。
- [Shoutem UI](https://github.com/shoutem/ui) 進行了一些組件改進和錯誤修復。我們還檢查了與最新 React Native 版本的兼容性。
- Shoutem 平台收到了一些重要更新，新的整合功能作為 [開源擴展項目](https://github.com/shoutem/extensions) 的一部分提供。我們非常高興看到其他開發者對 Shoutem 擴展的積極開發。我們主動聯繫並提供有關其擴展的建議和指導。

## 下次會議

下次會議定於 2017 年 12 月 6 日星期三舉行。如果您對如何改進會議成果有任何建議，請隨時在 [Twitter](https://twitter.com/TomislavTenodi) 上聯繫我。