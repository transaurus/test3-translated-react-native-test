---
title: 'React Native Monthly #2'
author: Tomislav Tenodi
authorTitle: Product Manager at Shoutem
authorURL: 'https://github.com/tenodi'
authorImageURL: 'https://pbs.twimg.com/profile_images/877237660225609729/bKFDwfAq.jpg'
authorTwitter: TomislavTenodi
tags: [engineering]
---

React Native 每月會議持續進行中！本次會議我們邀請到 [Infinite Red](https://infinite.red/) 團隊參與，他們是 [Chain React 研討會](https://infinite.red/ChainReactConf) 的主辦方。由於多數與會者都在 Chain React 發表過演講，我們將會議延後一週舉行。大會演講影片已[上傳網路](https://www.youtube.com/playlist?list=PLFHvL21g9bk3RxJ1Ut5nR_uTZFVOxu522)，誠摯推薦您觀看。現在就來看看各團隊的最新動態。

## 與會團隊

第二次會議共有 9 個團隊參與：

- [Airbnb](https://github.com/airbnb)
- [Callstack](https://github.com/callstack-io)
- [Expo](https://github.com/expo)
- [Facebook](https://github.com/facebook)
- [GeekyAnts](https://github.com/GeekyAnts)
- [Infinite Red](https://github.com/infinitered)
- [Microsoft](https://github.com/microsoft)
- [Shoutem](https://github.com/shoutem)
- [Wix](https://github.com/wix)

## 會議紀要

以下是各團隊的分享內容：

### Airbnb

- 歡迎查看 [Airbnb 代碼庫](https://github.com/airbnb) 中的 React Native 相關專案。

### Callstack

- [Mike Grabowski](https://github.com/grabbou) 持續負責 React Native 每月版本發布工作，包含數個測試版。特別著手將 v0.43.5 版本發布至 npm，此版本將解決 Windows 用戶的阻塞問題！
- [Haul](https://github.com/callstack-io/haul) 專案進展雖緩慢但穩定，已有支援 HMR 的拉取請求提交，其他改進也已發布。近期獲得業界領導者採用，正考慮展開全職有償開發計劃。
- [Jest](https://github.com/facebook/jest) 團隊成員 [Michał Pierzchała](https://twitter.com/thymikee) 本月加入 Callstack，將協助維護 [Haul](https://github.com/callstack-io/haul)，並可能參與 [Metro Bundler](https://github.com/facebook/metro) 與 [Jest](https://github.com/facebook/jest) 開發。
- [Satyajit Sahoo](https://twitter.com/satya164) 正式加入團隊！
- 開源部門有多項新進展，特別著重將 Material Palette API 引入 React Native，並計劃發布原生 iOS 套件，實現與原生元件 1:1 的視覺與操作體驗。

### Expo

- 近期推出了 [Native Directory](https://native.directory)，旨在提升 React Native 生態系統中函式庫的可發現性與評估效率。核心問題在於：現有函式庫數量龐大，測試門檻高，需手動應用啟發式方法，且難以直觀辨識哪些是最佳選擇。同時也難以確認是否與 CRNA/Expo 相容。Native Directory 試圖解決這些問題。歡迎查看並[提交你的函式庫](https://github.com/react-community/native-directory)，完整清單見[此處](https://github.com/react-community/native-directory/blob/master/react-native-libraries.json)。這僅是我們的初步嘗試，希望該平台能由社群共同維護，而非僅限 Expo 團隊。若你認同其價值並願參與改進，請加入我們！
- 在 [Snack](https://snack.expo.io/) 中新增對 Expo SDK 19 的 npm 套件安裝支援（目前仍存在部分錯誤，歡迎回報問題）。結合 Native Directory，此功能可簡化僅含 JS 依賴或 [Expo SDK](https://github.com/expo/expo-sdk) 內建依賴的函式庫測試流程。試用範例：
  - [react-native-modal](https://snack.expo.io/ByBCD_2r-)
  - [react-native-animatable](https://snack.expo.io/SJfJguhrW)
  - [react-native-calendars](https://snack.expo.io/HkoXUdhr-)
- [發布 Expo SDK19](https://blog.expo.io/expo-sdk-v19-0-0-is-now-available-821a62b58d3d)，包含多項全面改進，現採用[更新版 Android JSC](https://github.com/SoftwareMansion/jsc-android-buildscripts)。
- 正與 [Alexander Kotliarskyi](https://github.com/frantic) 合作編寫應用程式使用者體驗優化指南，歡迎參與補充內容或協助撰寫：
  - 議題追蹤：[#14979](https://github.com/facebook/react-native/issues/14979)
  - 初始 PR：[#14993](https://github.com/facebook/react-native/pull/14993)
- 持續開發以下功能（預計部分將首度於 SDK20（八月）亮相）：音訊/視訊、相機、手勢操作（與 Software Mansion 合作開發 `react-native-gesture-handler`）、GL 相機整合，並將大幅改進現有功能。同時正為 Expo 客戶端構建背景工作基礎架構（如地理定位、音訊、通知處理等）。
- [Adam Miskiewicz](https://twitter.com/skevy) 在 [react-navigation](https://github.com/react-community/react-navigation) 中模擬 [UINavigationController](https://developer.apple.com/documentation/uikit/uinavigationcontroller) 轉場效果取得顯著進展，可參閱[其推文](https://twitter.com/skevy/status/884932473070735361)查看早期版本（即將發布）。另見其貢獻的 `MaskedViewIOS` [上游合併](https://github.com/facebook/react-native/commit/8ea6cea39a3db6171dd74838a6eea4631cf42bba)。若有能力且願意實作 Android 版 `MaskedView` 將極具價值！

### Facebook

- Facebook 內部正在探索將原生 [ComponentKit](https://componentkit.org/) 和 [Litho](https://fblitho.com/) 元件嵌入 React Native 的可能性。
- 非常歡迎對 React Native 做出貢獻！如果您想知道如何貢獻，["如何貢獻"指南](https://github.com/facebook/react-native-website/blob/master/CONTRIBUTING.md) 描述了我們的開發流程，並列出了發送第一個 pull request 的步驟。還有其他不需要編寫代碼的貢獻方式，例如分類問題或更新文檔。
  - 在撰寫本文時，React Native 有 **635** 個 [未解決問題](https://github.com/facebook/react-native/issues) 和 **249** 個 [未合併的 pull requests](https://github.com/facebook/react-native/pulls)。這對我們的維護者來說是壓倒性的，當問題在內部修復時，很難確保相關任務得到更新。
  - 我們不確定在保持社區滿意的同時，處理這個問題的最佳方法是什麼。一些（但不是全部！）選項包括關閉過時的問題、給予更多人管理問題的權限，以及自動關閉不符合問題模板的問題。我們寫了一份「維護者期望」指南來設定期望並避免意外。如果您有想法可以讓這個體驗對維護者更好，同時確保提出問題和 pull request 的人感到被傾聽和重視，請告訴我們！

### GeekyAnts

- 我們在 Chain React 上展示了適用於 React Native 文件的設計師工具。許多與會者加入了等候名單。
- 我們也在研究其他跨平台解決方案，如 [Google Flutter](https://flutter.io/)（即將進行重大比較）、[Kotlin Native](https://github.com/JetBrains/kotlin-native) 和 [Apache Weex](https://weex.incubator.apache.org/)，以了解架構差異以及我們可以從中學到什麼來提高 React Native 的整體性能。
- 我們已將大多數應用程序切換到 [react-navigation](https://github.com/react-community/react-navigation)，這提高了整體性能。
- 同時，我們宣布了 [NativeBase Market](https://market.nativebase.io/) —— 一個為開發者提供的 React Native 元件和應用市場。

### Infinite Red

- 我們想介紹 [Reactotron](https://github.com/infinitered/reactotron)。請查看 [介紹視頻](https://www.youtube.com/watch?v=tPBRfxswDjA)。我們將很快添加更多功能！
- 組織了 Chain React 會議。非常棒，感謝大家的參與！[視頻現已上線！](https://www.youtube.com/playlist?list=PLFHvL21g9bk3RxJ1Ut5nR_uTZFVOxu522)

### Microsoft

- [CodePush](https://github.com/Microsoft/code-push) 現已整合到 [Mobile Center](https://mobile.azure.com/)。現有用戶的工作流程不會有任何變化。
  - 一些用戶報告了重複應用程序的問題 —— 他們已經在 Mobile Center 上有一個應用程序。我們正在解決這些問題，但如果您有兩個應用程序，請告訴我們，我們可以為您合併它們。
- Mobile Center 現在支持 CodePush 的推送通知。我們還展示了如何將通知和 CodePush 結合用於 A/B 測試應用程序 —— 這是 ReactNative 架構獨有的功能。
- [VS Code](https://github.com/Microsoft/vscode) 有一個已知的 ReactNative 調試問題 —— 幾天後的下一個擴展版本將修復這個問題。
- 由於 Microsoft 內部還有許多其他團隊也在使用 React Native，我們將努力在下一次會議中獲得更好的代表性。

### Shoutem

- 已完成在 [Shoutem](https://shoutem.github.io/) 上簡化 React Native 開發流程的工作。現在開發 Shoutem 應用時可使用所有標準的 `react-native` 指令。
- 我們投入大量精力研究 React Native 效能分析的最佳實踐。現有[效能文件](/docs/performance)多數內容已過時，我們將盡力向官方文件提交更新請求，或至少透過部落格文章分享研究成果。
- 正將導航方案切換至 [react-navigation](https://github.com/react-community/react-navigation)，後續將提供使用反饋。
- 在工具包中發布了[新版 HTML 組件](https://github.com/shoutem/ui/tree/develop/html)，可將原始 HTML 轉換為 React Native 組件樹。

### Wix

- 已針對 [Metro Bundler](https://github.com/facebook/metro) 發起合併請求，整合 [react-native-repackager](https://github.com/wix/react-native-repackager) 功能。我們更新了 react-native-repackager 以支援生產環境使用的 RN 44 版本，目前用於 [detox](https://github.com/wix/detox) 的模擬測試基礎架構。
- 過去三週持續為 Wix 應用編寫 detox 測試案例。這是在超過 40 名工程師參與的大型專案中，如何減少人工 QA 的寶貴經驗。我們已解決 detox 的多項問題，新版套件剛發布。很高興向各位報告，目前測試案例完全符合「零波動政策」，持續保持穩定通過率。
- Detox 的 Android 版本進展順利，獲得開源社群大力協助，預計兩週內發布初期版本。
- 效能測試工具 [DetoxInstruments](https://github.com/wix/detoxinstruments) 的發展超出原先規劃。現計劃將其改為獨立工具，不再與 detox 緊密耦合，未來可通用於 iOS 應用效能分析，同時保留與 detox 的整合以執行自動化效能指標測試。

## 下次會議

下次會議訂於 2017 年 8 月 16 日舉行。由於這僅是第二次會議，我們想了解這些紀錄如何能幫助 React Native 社群。若有改進會議產出的建議，歡迎透過 [Twitter](https://twitter.com/TomislavTenodi) 聯繫我。