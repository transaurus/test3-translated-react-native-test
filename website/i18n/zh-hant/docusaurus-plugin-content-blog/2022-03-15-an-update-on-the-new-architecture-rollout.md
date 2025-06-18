---
title: An update on the New Architecture Rollout
authors: [cortinico]
tags: [announcement]
date: 2022-03-15
---

各位好，
[如先前公告](/blog/2022/01/21/react-native-h2-2021-recap#the-new-architecture-rollout-and-releases)：

> 2022年將成為開源新架構的元年

如果您尚未有時間研究React Native新架構（Fabric渲染器與TurboModule系統），現在正是最佳時機！

我們希望與社群分享一些準備好的計畫與資源，確保所有人都能參與這項重要變革。

<!--truncate-->

### 工作小組

近期我們在GitHub成立了[React Native新架構工作小組](https://github.com/reactwg/react-native-new-architecture)，這是一個專門用於協調與支援新架構在生態系統中推廣的「僅限討論」倉庫。

我們將此工作小組視為社群成員能**交流**、分享**想法**，並**討論**新架構採用過程中挑戰的空間。此外，我們將透過此工作小組向更廣泛的社群**分享**資訊與更新，確保透明度。

為保持討論聚焦，我們決定讓此工作小組**公開可讀**，但**僅限核准用戶**參與撰寫。

若您希望加入討論，可[填寫此表單](https://forms.gle/8emgdwFZXuzEpyyn9)**申請或提名**您認為能為討論帶來價值的人選。

**歡迎所有人**申請加入對話。

如同所有討論平台，我們要再次強調**尊重**與接納他人觀點的重要性。請務必閱讀我們的[**行為準則**](https://github.com/reactwg/react-native-new-architecture/blob/main/CODE_OF_CONDUCT.md)（若您尚未閱讀）。

### 遷移指南

經過多輪審查與反饋後，我們終於合併了**遷移指南**（原稱《手冊》）。您可在[新架構工作小組](https://github.com/reactwg/react-native-new-architecture#guides)中找到。

這份遷移指南將以逐步教學方式，展示**如何建立自訂Fabric元件或TurboModule**，並說明如何**調整現有應用程式或函式庫**以採用新架構。

此外，我們要提醒您官網新增的[架構專區](/architecture/overview)。該處收錄多篇深入解析React Native內部機制的文章，特別是[Fabric章節](/architecture/fabric-renderer)能幫助您理解新架構下的渲染管線。

最後，請考慮[在工作小組](https://github.com/reactwg/react-native-new-architecture/discussions/7)**分享您對這些文件的意見**。我們持續收集開發者反饋，確保提供的內容最符合您的需求。

未來幾個月，我們將持續精進並新增更多文件來協助您。

### 新架構範本

React Native **0.68.0**即將發布。此版本是新架構推廣的關鍵里程碑，因為它首次在**新應用範本**中內建了**選擇性啟用開關**。

這意味著您只需修改範本中的**一行代碼**即可試用新架構。我們還為範本添加了大量**註解與文件**，確保您無需額外閱讀就能開箱即用。我們希望透過**減少需編寫的代碼量**，幫助您更輕鬆地採用新架構。

<!-- alex ignore simple -->

在接下來的版本中，我們將持續更新模板，使其更加精簡且易於使用。

要在任一平台上啟用新架構，您可以：

- 在 iOS 上，於 `ios` 資料夾內執行 `RCT_NEW_ARCH_ENABLED=1 bundle exec pod install`。
- 在 Android 上，透過以下方式將 `newArchEnabled` 屬性設為 `true`：
  - 修改 `android/gradle.properties` 檔案中的對應行。
  - 設定環境變數 `ORG_GRADLE_PROJECT_newArchEnabled=true`。
  - 使用 `-PnewArchEnabled=true` 參數呼叫 Gradle。

接著您可以用 `yarn react-native run-android` 或 `run-ios` 執行應用程式，此時將啟用 Fabric 和 TurboModules。

請嘗試使用此新模板，並[回報任何遇到的錯誤或異常行為](https://github.com/reactwg/react-native-new-architecture/discussions/5)。過去幾個月我們努力修復了許多錯誤和建置失敗，這些問題若沒有社群的持續回饋和測試將**難以發現**。

### 第三方函式庫生態系統

若沒有**第三方函式庫作者與維護者**的全面支援，社群將無法順利遷移至新架構。

我們理解這可能是個繁瑣的過程，也明白同時支援**舊架構與新架構**使用者的重要性。未來幾個月，我們將重點協助函式庫開發者完成遷移。

如果您是**函式庫開發者**，[我們邀請您在「新架構工作小組」中發布更新](https://github.com/reactwg/react-native-new-architecture/discussions/categories/libraries)，說明**函式庫的遷移狀態**。這有助於吸引早期採用者，並讓我們了解是否有函式庫遇到阻礙。

若您是**函式庫使用者**，可以[在此發布訊息](https://github.com/reactwg/react-native-new-architecture/discussions/6)請求遷移特定函式庫。若發現某函式庫成為多數使用者的阻礙，我們將嘗試聯繫維護者了解未遷移的原因。

最後，我們要特別感謝 Software Mansion 發布支援雙架構的 [`react-native-screens`](https://github.com/software-mansion/react-native-screens) 新版本。他們還發表了部落格文章（[為 react-native-screens 引入 Fabric](https://blog.swmansion.com/introducing-fabric-to-react-native-screens-fd17bf18858e)），**分享遷移歷程**。希望這個故事能激勵並協助您完成遷移。

### 版本發布

0.68 預發布版本的工作實現了[我們上半年定義的改進版發布流程](/blog/2022/01/19/version-067#improvements-to-release-process)中的多項內容。

我們很高興分享，在 0.68 版本中我們成功達成：

- 順利將發布工作移交至內部輪值制度。這主要得益於[改進的發布流程文件](/contributing/overview)，降低了流程的關鍵人物風險。
- 與合作夥伴啟動[副駕駛輪值制度](https://github.com/reactwg/react-native-releases/blob/main/docs/roles-and-responsibilities.md)的討論。此舉旨在提升流程透明度，並指引合作夥伴投資支援 React Native 發布與生態系統的方向。
- [從社群招募多名版本測試與支援人員](https://github.com/reactwg/react-native-releases/discussions/11)。上半年我們發出協助請求後獲得熱烈響應！測試者與支援者的回饋**協助我們修復關鍵錯誤**與回歸問題，特別是新架構相關的部分。感謝所有參與測試的夥伴！

在 React Native 0.69 版本中，我們將持續完善此流程，目標是讓合作夥伴能更早提供版本發布訊號並完成副駕駛員的入職培訓。一如既往，[我們非常歡迎任何反饋意見](https://github.com/reactwg/react-native-releases/discussions)。若您想加入成為版本測試員或支援者，[請點此報名](https://forms.gle/fPuPE1MZRDGWNqpd6)。

### 邁向將 Hermes 設為預設引擎

新架構推廣的關鍵點之一，就是採用新的 JavaScript 引擎：**Hermes**。

隨著新 React Native 架構的推出，我們將**把 Hermes 設為預設引擎**。這意味著所有新文件和模板都會預設啟用 Hermes。

請注意，我們將持續與社群合作，確保**其他引擎**（如 JSC (JavaScript Core)）**仍受支援**。您仍可選擇使用偏好的引擎，但需**手動停用 Hermes**。

為提升 Hermes 的穩定性，我們正著手改變 Hermes 的**發布模式**。具體而言，我們希望讓 Hermes 的發布流程**更貼近** React Native 的發布流程。

這將使我們能發布與綁定 JS 引擎**完全相容**的 React Native 版本。您無須再處理難以除錯和理解的運行時崩潰及 Hermes 相容性問題。

此外，這將**縮短**取得 Hermes **改進功能**和錯誤修復的週期，讓我們能更**迅速回應** React Native 使用者的需求。

未來幾個月我們將分享更多相關資訊。在此期間，歡迎[加入工作小組的討論](https://github.com/reactwg/react-native-new-architecture/discussions/4)。

若您尚未嘗試 Hermes，現在正是時候。請務必回報您遇到的任何問題或阻礙。

以上就是本次的內容總結。

我要感謝 Andrei、Aleksandar、Dmitry、Eli、Luna、Héctor 和 Neil 審閱這篇部落格文章，並為這些工作提供寶貴貢獻。

期待**閱讀您的遷移經驗分享**。