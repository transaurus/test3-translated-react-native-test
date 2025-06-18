---
title: San Francisco Meetup Recap
authors: [hectorramos]
hero: '/blog/img/rnmsf-august-2016-hero.jpg'
tags: [events]
---

上週我有幸參加了在Zynga舊金山辦公室舉辦的[React Native Meetup](https://www.meetup.com/React-Native-San-Francisco/photos/27168649/#452793854)。現場約有200人參與，這是一個絕佳的機會，讓我能認識附近同樣對React Native感興趣的開發者。

![](/blog/assets/rnmsf-august-2016-hero.jpg)

我特別想深入了解像Zynga、Netflix和Airbnb這樣的公司是如何使用React和React Native的。當晚的議程如下：

- 使用React進行快速原型開發
- 為React Native設計API
- 彌合差距：在現有代碼庫中使用React Native

但首先，活動以簡短的介紹和近期新聞回顧開場：

- 你知道React Native現在是[GitHub上最受歡迎的Java倉庫](https://twitter.com/jamespearce/status/759637111880359937)嗎？
- [rnpm](https://github.com/rnpm/rnpm)現在已成為React Native核心的一部分！你可以使用`react-native link`來替代`rnpm link`，以[安裝帶有原生依賴的庫](/docs/linking-libraries-ios)。
- React Native Meetup社區正在迅速壯大！現在全球各地有超過4,800名開發者參與各種React Native Meetup小組。

如果[這些Meetup](https://www.meetup.com/find/?allMeetups=false&keywords=react+native&radius=Infinity&userFreeform=San+Francisco%2C+CA&mcId=z94105&mcName=San+Francisco%2C+CA&sort=recommended&eventFilter=mysugg)在你附近舉辦，我強烈推薦參加！

## 在Zynga使用React進行快速原型開發

第一輪新聞之後，由我們當晚的主辦方Zynga做了簡短介紹。Abhishek Chadha談到了他們如何使用React在移動設備上快速原型開發新體驗，並演示了一個類似Draw Something的應用原型。他們採用了與React Native類似的方法，通過橋接提供對原生API的訪問。這一點在Abhishek使用設備相機拍攝觀眾照片並在某人的頭上畫了一頂帽子時得到了展示。

## 在Netflix為React Native設計API

接下來是當晚的第一場主題演講。Netflix的高級軟件工程師[Clarence Leung](https://twitter.com/clarler)帶來了關於為React Native設計API的演講。他首先指出，開發者可能會接觸到的兩類主要庫：一是像標籤欄和日期選擇器這樣的組件，二是提供對原生服務（如相冊或應用內支付）訪問的庫。在為React Native構建庫時，有兩種主要方法：

- 提供特定於平台的組件
- 構建一個跨平台庫，為Android和iOS提供相似的API

每種方法都有其考量，你需要根據自己的需求來決定哪種方式最適合。

**方法一**

作為特定於平台組件的例子，Clarence談到了React Native核心中的DatePickerIOS和DatePickerAndroid。在iOS上，日期選擇器是作為UI的一部分渲染的，可以輕鬆嵌入現有視圖中，而在Android上，日期選擇器是以模態方式呈現的。在這種情況下，提供獨立的組件是有意義的。

**方法二**

另一方面，照片選擇器在Android和iOS上的處理方式相似。雖然有一些細微差別——例如，Android不會像iOS那樣將照片分組到「自拍」等文件夾中——但這些可以輕鬆通過`if`語句和`Platform`組件來處理。

無論您最終選擇哪種方法，最小化API表面積並構建應用特定的函式庫都是個好主意。舉例來說，iOS的應用內購買框架支援一次性消費性購買與可續訂訂閱。如果您的應用只需要支援消費性購買，或許可以在跨平台函式庫中直接捨棄訂閱功能。

![](/blog/assets/rnmsf-august-2016-netflix.jpg)

Clarence的演講結束後進行了簡短的問答環節。其中一個有趣的細節是，Netflix為這些函式庫編寫的React Native程式碼中，約有80%可在Android和iOS平台共用。

## 彌合鴻溝：在現有程式碼庫中使用React Native

當晚最後一場演講由Airbnb的[Leland Richardson](https://twitter.com/intelligibabble)主講，聚焦於如何在現有程式碼庫中整合React Native。我深知從零開始用React Native開發新應用有多簡單，因此對Airbnb在既有原生應用中採用React Native的經驗特別感興趣。

Leland首先談到「綠地開發」與「棕地開發」的區別。綠地開發意指無需考慮既有架構的全新專案，而棕地專案則需考量現有專案需求、開發流程及各團隊的不同需求。

進行綠地開發時，React Native CLI會自動建立同時包含Android和iOS的單一儲存庫，一切都能無縫運作。Airbnb採用React Native的第一個挑戰在於：他們的Android與iOS應用原本各自擁有獨立儲存庫。採用多儲存庫架構的公司，在導入React Native前必須先克服這個障礙。

為解決此問題，Airbnb先為React Native程式碼建立了新儲存庫。他們透過持續整合伺服器，將Android和iOS儲存庫的內容鏡像至此新儲存庫。在測試執行與套件組合建置完成後，建置產出物會同步回傳至Android與iOS儲存庫。這種做法讓行動工程師能在不改變開發環境的情況下處理原生程式碼，無需安裝npm、啟動封包伺服器或手動建置JavaScript套件組合。而實際編寫React Native程式碼的工程師，則因直接操作React Native儲存庫，無需擔心跨平台同步問題。

這種做法存在明顯缺點：無法進行原子性更新。需要同時修改原生與JavaScript程式碼的變更，必須提交三個獨立拉取請求，且都需謹慎合併。為避免衝突，當主分支在建置期間發生變更時，持續整合系統會阻止將變更回傳至Android與iOS儲存庫。這在提交頻率高的時段（例如發布新版本時）會導致嚴重延遲。

Airbnb後續已轉向單一儲存庫架構。幸運的是此方案原本就在考量中，當Android與iOS團隊熟悉React Native後，便順利加速了轉換進程。

這解決了多數分散式儲存庫帶來的問題。Leland也指出，此做法會對版本控制伺服器造成較大負荷，對規模較小的公司可能是個問題。

![](/blog/assets/rnmsf-august-2016-airbnb.jpg)

### 導航難題

演講後半段聚焦於React Native的核心議題：導航系統的解決方案。Leland探討了React Native生態中眾多的導航函式庫（包括官方與第三方方案），提到NavigationExperimental雖看似前景看好，最終卻不符合他們的用例需求。

事實上，現有導航函式庫皆無法完美支援棕地應用。這類應用要求導航狀態必須完全由原生應用掌控。例如當使用者在React Native視圖中操作時，若會話突然過期，原生應用必須能立即接管並彈出登入畫面。

Airbnb 也希望能避免在過渡期間以 JavaScript 版本取代原生導覽列，因為效果可能會顯得突兀。起初他們將應用範圍限制在模態呈現的視圖，但這顯然在更廣泛採用 React Native 時會產生問題。

他們決定需要開發自己的函式庫，該庫命名為 `airbnb-navigation`。由於與 Airbnb 程式碼庫高度耦合，目前尚未開源，但他們希望能在年底前發布。

以下簡要說明該函式庫 API 的關鍵設計要點：

- 需預先註冊所有場景
- 每個場景均以獨立 `RCTRootView` 呈現，各平台使用原生元件（例如 iOS 採用 `UINavigationController`）
- 場景中的主 `ScrollView` 需包裹於 `ScrollScene` 元件內，可繼承原生行為（如 iOS 點擊狀態列返回頂部）
- 場景轉場由原生處理，無需擔心效能問題
- 自動支援 Android 返回鍵
- 透過無 UI 的 Navigator.Config 元件，可實現基於 View Controller 的導覽列樣式控制

需注意的設計限制：

- 導覽列難以用 JavaScript 自訂（因屬原生元件），此為刻意設計以符合嚴格的導覽列原生需求
- 經橋接傳遞的 ScreenProps 需序列化/反序列化，傳輸大量數據時須謹慎
- 導覽狀態由原生應用掌控（核心需求），因此 Redux 等工具無法操作導覽狀態

問答環節中，Leland 表示 Airbnb 對 React Native 整體滿意。團隊對 Code Push 的熱修復機制感興趣，工程師也喜愛 Live Reload 功能帶來的開發效率提升。

## 活動總結

活動尾聲分享的 React Native 相關動態：

- Deco 宣布推出 [React Native 應用展示牆](https://www.decosoftware.com/showcase)，邀請與會者提交作品
- 特別提及近期完成的[文件全面改版](/blog/2016/07/06/toward-better-documentation)
- Deco IDE 共同創建者 Devin Abbott 將開設 [React Native 入門課程](https://www.decosoftware.com/course)

![](/blog/assets/rnmsf-august-2016-docs.jpg)

技術社群聚會是交流學習的絕佳場合。期待未來參與更多 React Native 活動，若您也參與其中，歡迎交流使用體驗與改進建議！