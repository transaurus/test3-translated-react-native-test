---
title: Toward Hermes being the Default
authors: [huxpro]
tags: [announcement]
---

自[我們於2019年宣布推出Hermes](https://engineering.fb.com/2019/07/12/android/hermes/)以來，它在社群中的採用率持續攀升。維護熱門React Native元框架的[Expo](https://expo.dev/)團隊，近期[宣布實驗性支援](https://blog.expo.dev/expo-sdk-42-579aee2348b6) [Hermes](https://blog.expo.dev/expo-sdk-43-beta-is-now-available-47dc54a8d29f)，這項功能原是[Expo使用者最常要求的功能之一](https://expo.canny.io/feature-requests/p/enabling-hermes)。熱門行動資料庫[Realm](https://realm.io/)的團隊也於近期發布了對Hermes的[alpha版支援](https://github.com/realm/realm-js/issues/3940)。在這篇文章中，我們想重點介紹過去兩年來，我們為推動Hermes成為React Native「最佳」JavaScript引擎所取得的一些最令人興奮的進展。展望未來，我們相信隨著這些改進及更多即將到來的優化，我們能讓Hermes成為所有平台上React Native的預設JavaScript引擎。

<!--truncate-->

## 為React Native優化

Hermes的定義性特徵在於其提前執行編譯工作，這意味著啟用Hermes的React Native應用程式會預先載入經過優化的位元組碼，而非普通的JavaScript原始碼。這大幅減少了使用者啟動產品所需的工作量。來自Facebook和社群應用的測量數據表明，啟用Hermes通常能將產品的TTI（或[互動時間](https://web.dev/interactive/)）指標減少近一半。

話雖如此，我們一直在從許多其他方面改進Hermes，使其作為專為React Native打造的JavaScript引擎更加出色。

### 為Fabric構建新的垃圾回收器

隨著新React Native架構中即將推出的[Fabric](https://github.com/react-native-community/discussions-and-proposals/issues/4)渲染器，將有可能在UI線程上同步調用JavaScript。然而，這意味著如果JavaScript線程執行時間過長，可能會導致明顯的UI幀率下降並阻塞使用者輸入。由React [Fiber](https://reactjs.org/docs/faq-internals.html#what-is-react-fiber)啟用的[並行渲染](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html)將通過將渲染工作拆分為多個區塊來避免排程長時間的JavaScript任務。然而，JavaScript線程還有另一個常見的延遲來源——當JavaScript引擎必須「停止世界」以執行垃圾回收（GC）時。

Hermes中先前的預設垃圾回收器[GenGC](https://hermesengine.dev/docs/gengc/)是一個單線程的分代垃圾回收器。新生代使用典型的半空間複製策略，而老生代則使用標記-壓縮策略，使其非常擅長積極地將記憶體返還給操作系統。由於其單線程特性，GenGC的缺點是會導致長時間的GC暫停。在像Facebook for Android這樣複雜的應用上，我們觀察到平均暫停時間為200毫秒，或在p99時為1.4秒。考慮到Facebook for Android龐大且多樣化的使用者群體，我們甚至見過長達7秒的暫停。

為了解決這個問題，我們實作了一個全新的「大部分並行」垃圾回收器，名為[Hades](https://hermesengine.dev/docs/hades)。Hades的年輕代回收方式與GenGC完全相同，但其老年代則採用「開始時快照」風格的標記-清除回收器，這能大幅減少GC暫停時間，因為大部分工作都在背景執行緒中進行，不會阻擋引擎主執行緒執行JavaScript程式碼。**我們的統計數據顯示，Hades在64位元裝置上的p99.9暫停時間僅48毫秒（比GenGC快34倍！）**，而在32位元裝置上約為88毫秒（此時它以單執行緒的「增量式」GC模式運作）。這些暫停時間的改善可能會以整體吞吐量為代價，因為需要更昂貴的寫入屏障、基於自由列表的較慢記憶體配置（相對於指標遞增配置器），以及增加的堆碎片化。我們認為這些是合理的權衡，且透過合併與其他記憶體優化措施，我們成功實現了整體更低的記憶體消耗，後續將詳細說明。

### 直擊效能痛點

應用程式的啟動時間對許多產品的成功至關重要，我們持續為React Native突破界限。對於Hermes實作的任何新JavaScript功能，我們都會仔細監控其對生產效能的影響，確保不會導致指標倒退。在Facebook，我們目前正在實驗[Metro中專為Hermes設計的Babel轉換設定檔](https://github.com/facebook/react-native/blob/main/packages/react-native-babel-preset/src/configs/main.js#L41)，用Hermes的原生ESNext實作取代十多個Babel轉換。我們在許多介面觀察到**18-25%的TTI改善**與**整體位元組碼大小減少**，預期開源社群也能看到類似成果。

除了啟動效能，我們發現記憶體佔用是React Native應用程式（尤其是[虛擬實境](https://reactnative.dev/blog/2021/08/26/many-platform-vision#expanding-to-new-platforms)）的改進機會。得益於JavaScript引擎的低階控制能力，我們透過細微調整實現了多輪記憶體優化：

1. 過去所有JavaScript值都以64位元NaN-boxing編碼標記值表示，用於在64位元架構上儲存浮點數雙精度與指標。但這在實際應用中浪費空間，因為多數數字是小整數（SMI），且客戶端應用的JavaScript堆通常不會超過4GiB。為此，我們引入新的32位元編碼，其中SMI與指標以29位元儲存（因指標為8位元對齊，可假設最低3位元始終為零），其餘JS數字則封裝到堆中。**這使JavaScript堆大小減少約30%。**
2. 不同類型的JavaScript物件在堆中以不同GC管理單元表示。透過極致優化這些單元的標頭記憶體布局，**我們再降低約15%記憶體使用量**。

Hermes的關鍵決策之一是不實作[即時編譯器(JIT)](https://en.wikipedia.org/wiki/Just-in-time_compilation)，因為我們認為對多數React Native應用而言，額外的暖機成本與二進位檔/記憶體佔用並不划算。多年來，我們投入大量精力優化直譯器效能與編譯器最佳化，使Hermes的吞吐量在React Native工作負載上能與其他引擎競爭。我們持續透過識別各處效能瓶頸（直譯器調度循環、堆疊布局、物件模型、GC等）來提升吞吐量，請期待後續版本的數據！

### 垂直整合的先鋒實踐

在Facebook，我們偏好將專案集中於單一[monorepo](https://en.wikipedia.org/wiki/Monorepo)。透過讓引擎（Hermes）與宿主（React Native）緊密協同迭代，我們開創了許多垂直整合的可能性，例如：

- Hermes 支援[使用 Chrome 除錯工具進行裝置上的 JavaScript 除錯](https://reactnative.dev/docs/hermes#debugging-js-on-hermes-using-google-chromes-devtools)，透過實作 [Chrome DevTools 協定](https://chromedevtools.github.io/devtools-protocol/)。這比傳統的「[遠端 JS 除錯](https://reactnative.dev/docs/debugging#chrome-developer-tools)」（透過應用內代理在桌面版 Chrome 中執行 JS）更優越，因為它支援同步原生呼叫的除錯，並確保一致的執行環境。與 React DevTools、Metro、Inspector 等工具整合後，Hermes 除錯器現已成為 [Flipper](https://reactnative.dev/blog/2020/03/26/version-0.62) 的一部分，提供一站式的開發者體驗。
- React Native 應用初始化路徑中分配的物件通常具有較長的生命週期，且不符合分代式 GC 所依賴的_分代假設_。因此，我們[在 React Native 中配置 Hermes](https://github.com/facebook/react-native/blob/main/ReactAndroid/src/main/java/com/facebook/hermes/reactexecutor/OnLoad.cpp#L37-L42)，將前 32MiB 直接分配至老年代（稱為_預先晉升_），以避免觸發 GC 暫停並延遲 TTI。
- 新的 React Native 架構高度依賴 [JSI（JavaScript 介面）](https://github.com/react-native-community/discussions-and-proposals/issues/91)，這是一個輕量級、通用的 API，用於將 JavaScript 引擎嵌入 C++ 程式。由於維護 JS 引擎的團隊同時負責 JSI API 的實作，我們有信心提供最可靠的整合方案，並在 Facebook 的規模下經過效能與實戰驗證。
- 確保 JavaScript 並行原語（如 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)）與平台並行原語（如 [微任務](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API/Microtask_guide)）在語意上正確且高效，對 React 並行渲染與 React Native 應用的未來至關重要。過去，React Native 中的 Promise 是透過非標準的 [`setImmediate`](https://developer.mozilla.org/en-US/docs/Web/API/Window/setImmediate) API [進行填充](https://github.com/facebook/react-native/blob/main/Libraries/Core/polyfillPromise.js#L37)。我們正致力於透過 JSI 提供 JS 引擎原生的 Promise 與微任務，並引入網頁標準的新成員 [`queueMicrotask`](https://developer.mozilla.org/en-US/docs/Web/API/queueMicrotask) 至平台，以更好地支援現代非同步 JavaScript 程式碼。

## 攜手整個社群

Hermes 對 Facebook 來說表現卓越，但我們的工作尚未完成，直到整個生態系統都能使用 Hermes 來驅動各種體驗，讓所有人充分發揮其功能與潛力。

### 擴展至新平台

Hermes 最初僅針對 Android 平台的 React Native 開源。此後，我們欣喜地看到社群成員將 Hermes 支援擴展至 [React Native 生態系統涵蓋的眾多其他平台](https://reactnative.dev/blog/2021/08/26/many-platform-vision)。

[Callstack](https://callstack.com/) 主導了在 [React Native 0.64 中將 Hermes 引入 iOS](https://reactnative.dev/blog/2021/03/12/version-0.64) 的工作。他們撰寫了[系列文章](https://callstack.com/blog/bringing-hermes-to-ios-in-react-native/)並舉辦了[播客](https://callstack.com/podcasts/react-native-0-64-with-hermes-for-ios-ep-5)，分享實現過程。根據他們的基準測試，在 Mattermost 應用中，Hermes 能**在 iOS 上持續帶來約 40% 的啟動效能提升與約 18% 的記憶體減少**，且僅增加 2.4 MiB 的應用大小開銷。建議您[親眼見證實際效果](https://callstack.com/blog/hermes-performance-on-ios/)。

微軟正致力於將 [Hermes 引入 React Native for Windows 和 macOS](https://microsoft.github.io/react-native-windows/docs/hermes)。[在 Microsoft Build 2020](https://youtu.be/QMFbrHZnvvw?t=389) 上，微軟分享 Hermes 的記憶體影響（[工作集](https://en.wikipedia.org/wiki/Working_set)）比 React Native for Windows 上的 Chakra 引擎低 13%。最近，在一些合成基準測試中，他們發現 Hermes 0.8（搭載 Hades 及前述的 SMI 和指標壓縮優化）**比其他引擎少用 30%-40% 的記憶體**。毫不意外，基於 React Native 構建的 [桌面版 Messenger](https://www.messenger.com/desktop) 視訊通話體驗，同樣由 Hermes 驅動。

最後但同樣重要的是，Hermes 也驅動了所有在 Oculus 上使用 React 技術家族構建的虛擬實境體驗，包括 Oculus Home。

### 支援我們的社群

我們承認仍有部分障礙阻止社群某些成員採用 Hermes，我們承諾將為這些缺失的功能建立支援。我們的目標是實現完整功能，使 Hermes 成為大多數 React Native 應用的正確選擇。以下是社群如何已經塑造了 Hermes 的路線圖：

<!-- alex ignore just fellowship -->

- [`Proxy` 和 `Reflect`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Meta_programming) 最初被排除在 Hermes 之外，因為 Facebook 並未使用它們。我們也擔心添加 Proxy 會影響屬性查找性能，即使未使用 Proxy 時亦然。但由於 [MobX](https://mobx.js.org/README.html) 和 [Immer](https://immerjs.github.io/immer/) 等熱門函式庫的需求，Proxy 迅速成為 [Hermes 最受期待的功能](https://github.com/facebook/hermes/issues/33)。經過仔細評估，我們決定為社群實現此功能，並以極低成本完成。由於這是我們未使用的功能，我們依賴社群驗證其穩定性。我們先將 Proxy 設為可選功能進行測試，並為 [v0.4](https://github.com/facebook/hermes/issues/33#issuecomment-668374607) 和 [v0.5](https://github.com/facebook/hermes/issues/33#issuecomment-668374607) 版本發布了 npm 套件，[從 v0.7 開始預設啟用](https://github.com/facebook/hermes/releases/tag/v0.7.0)。
- [ECMAScript 國際化 API 規範（ECMA-402，或稱 `Intl`）](https://hermesengine.dev/docs/intl) 是 [第二受期待的功能](https://github.com/facebook/hermes/issues/23)。`Intl` 是一組龐大的 API，通常需要包含 **6MB** 的 [Unicode CLDR](https://cldr.unicode.org/index) 資料。這就是為何 [FormatJS（即 `react-intl`）](https://github.com/formatjs/formatjs) 等 polyfill 和 [社群 JSC 的國際化版本](https://github.com/react-native-community/jsc-android-buildscripts#international-variant) 等 JS 引擎如此龐大。為避免大幅增加 Hermes 的二進位檔案大小，我們決定採用另一種策略：利用並映射作業系統函式庫提供的 ICU 功能，代價是不同平台間可能存在一些（通常較小的）行為差異。
  - 微軟合作在 Android 上實現了此功能。它幾乎涵蓋了 ECMA-402 至 ES2020 的所有內容，**體積影響僅 3%（每個 ABI 57-62K）**。我們在 [Twitter 上進行了投票](https://twitter.com/tmikov/status/1336442786694893568)，結果顯示強烈支持預設包含 `Intl`，因此我們從 [v0.8 版本](https://github.com/facebook/hermes/releases/tag/v0.8.0) 開始提供此功能。
  - Facebook 贊助了 [Major League Hacking](https://mlh.io/) 推出 [遠端開源獎學金計劃](https://news.mlh.io/welcoming-facebook-back-as-a-sponsor-of-the-2020-2021-mlh-fellowship-08-12-2020)。去年，我們發布了 [Hermes 取樣分析器](https://reactnative.dev/docs/profile-hermes)。今年，我們的獎學金獲得者將與 Hermes、React Native 和 Callstack 的成員合作，為 iOS 添加 Hermes `Intl` 支持。敬請期待！
- 我們感謝社群與我們合作發現影響社群的問題。
  - 有人幫助我們識別了關鍵的規範差異，例如 [ES2019 修訂的 `Array.prototype.sort` 穩定性問題](https://github.com/facebook/hermes/issues/212)。此問題已修復，將在下一版本中提供。
  - 有人發現我們的預設堆大小限制過小，導致 [不必要的 GC 壓力](https://github.com/facebook/hermes/issues/295) 和 [OOM 崩潰](https://github.com/facebook/hermes/issues/511)，影響了許多不熟悉 Hermes GC 配置的用戶。因此，我們將預設值從 512MiB 增加到 3GiB，以滿足大多數用戶的需求。
  - 也有人報告，我們特製的 `Function.prototype.toString` 實作 [導致某些函式庫在進行不當功能檢測時性能下降](https://github.com/facebook/hermes/issues/471#issuecomment-820123463)，並 [阻礙用戶進行原始碼注入](https://github.com/facebook/hermes/issues/114#issuecomment-887106990)。這促使我們更加堅定：Hermes 應盡可能不阻礙開發者，並尊重實際開發實踐。

## 總結

總結來說，我們的願景是讓 Hermes 成為所有 React Native 平台的預設 JavaScript 引擎。我們已開始朝此方向努力，並希望聽取各位對此方向的意見。

為生態系統做好順利採用的準備對我們極為重要。我們鼓勵您試用 Hermes，並在我們的 [GitHub 儲存庫](https://github.com/facebook/hermes) 提交任何反饋、問題、功能請求及不相容性的報告。

## 致謝

我們要衷心感謝 Hermes 團隊、React Native 團隊，以及來自 React Native 社群的眾多貢獻者，感謝他們為改進 Hermes 所做的努力。

<!-- alex ignore white -->

我個人還想特別感謝（按字母順序）Eli White、Luna Wei、Neil Dhar、Tim Yung、Tzvetan Mikov 等人在撰寫過程中提供的協助。