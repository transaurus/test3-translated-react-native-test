---
title: Managing Pull Requests
---

審查一個拉取請求可能需要相當長的時間。在某些情況下，進行審查所需的時間甚至可能比撰寫和提交變更的時間還要長！因此，有必要進行一些初步工作，以確保每個拉取請求處於適合審查的良好狀態。

一個拉取請求應包含三個主要部分：

- 摘要。這有助於我們理解變更的動機。
- 變更日誌。這有助於我們撰寫發布說明。同時也是對變更的簡要總結。
- 測試計劃。這可能是拉取請求中最重要的部分。測試計劃應是一個可重現的逐步指南，以便審查者能夠驗證您的變更是否按預期工作。對於用戶可見的變更，附上截圖或影片也是個好主意。

任何拉取請求都可能需要對 React Native 的某些領域有更深入的理解，而您可能對此並不熟悉。即使您覺得自己不是審查某個拉取請求的合適人選，您仍然可以通過添加標籤或向作者請求更多信息來提供幫助。

## 審查拉取請求

拉取請求需要使用 GitHub 的審查功能進行審查和批准後才能合併。雖然任何人都可以審查和批准拉取請求，但我們通常只有在審查來自[貢獻者](https://github.com/facebook/react-native/blob/main/ECOSYSTEM.md)之一時，才會認為拉取請求已準備好合併。

<!-- alex ignore clearly -->

假設您找到了一個您有信心審查的拉取請求。請使用 GitHub 的審查功能，並清晰且禮貌地傳達任何建議的變更。

可以考慮從那些被標記為缺少變更日誌或測試計劃的拉取請求開始。

- [看似缺少變更日誌的拉取請求](https://github.com/facebook/react-native/pulls?utf8=%E2%9C%93&q=is%3Apr+is%3Aopen+label%3A%22Missing+Changelog%22+) - 查看並嘗試通過編輯拉取請求來添加變更日誌。完成後，移除「Missing Changelog」標籤。
- [缺少測試計劃的拉取請求](https://github.com/facebook/react-native/pulls?q=is%3Apr+label%3A%22Missing+Test+Plan%22+is%3Aclosed) - 打開拉取請求並尋找測試計劃。如果測試計劃看起來足夠充分，移除「Missing Test Plan」標籤。如果沒有測試計劃，或者看起來不完整，添加一條評論禮貌地請求作者考慮添加測試計劃。

拉取請求必須通過所有測試才能合併。這些測試會在每次提交到 `main` 分支和拉取請求時運行。幫助我們準備拉取請求進行審查的一個快速方法是[搜索那些未能通過預提交測試的拉取請求](https://github.com/facebook/react-native/pulls?utf8=%E2%9C%93&q=is%3Apr+is%3Aopen+label%3A%22CLA+Signed%22+status%3Afailure+)，並確定是否需要進行修訂。失敗的測試通常會列在線程底部，位於「Some checks were not successful」下方。

- 快速瀏覽 [main 分支的最新測試運行結果](https://circleci.com/gh/facebook/react-native/tree/main)。`main` 是否顯示為綠色（通過）？如果是：
  - 測試失敗是否可能與此拉取請求中的變更有關？請要求作者進行調查。
  - 即使 `main` 目前顯示為綠色，仍需考慮拉取請求中的提交可能是基於 `main` 分支損壞時的某個提交點。若您認為可能是這種情況，請要求作者將其變更重新基於 `main` 分支進行重訂（rebase），以納入他們開始處理拉取請求後可能已修復的任何內容。
- 若 `main` 顯示為損壞（失敗），請尋找任何 [標記為「CI Test Failure」的問題](https://github.com/facebook/react-native/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22%E2%9D%8CCI+Test+Failure%22+)。
  - 若找到與 `main` 上失敗相關的問題，請返回拉取請求並感謝作者提出這些變更，並告知他們測試失敗可能與其特定變更無關（別忘了連結回 CI Test Failure 問題，這將幫助作者知道何時可以重新運行測試）。
  - 若找不到描述您在 `main` 上觀察到問題的現有 CI Test Failure 問題，請提交新問題並使用「CI Test Failure」標籤讓其他人知道 `main` 已損壞（參見 [此問題](https://github.com/facebook/react-native/issues/23108) 作為範例）。

## 我們如何優先處理拉取請求

Meta 的 React Native 團隊成員致力於快速審查拉取請求，大多數 PR 會在一週內獲得回應。

## 拉取請求如何被合併？

React Native 的 GitHub 倉庫實際上是 Meta 單一倉庫（monorepo）中某個子目錄的鏡像。因此，拉取請求並非以傳統方式合併。相反，它們需要以「差異」（["diff"](https://www.phacility.com/phabricator/differential/)）形式導入 Meta 的內部代碼審查系統。

導入後，變更將通過一系列測試。其中部分測試為「合併阻斷測試」（land-blocking），意味著必須成功才能合併差異內容。Meta 始終從 `main` 分支運行 React Native，某些變更可能需要 Facebook 員工在合併前為您的拉取請求附加內部變更。例如，若您重命名模組名稱，必須在同一變更中更新 Facebook 內部所有呼叫點才能合併。若差異成功合併，變更最終將由 [ShipIt](https://github.com/facebook/fbshipit) 作為單一提交同步回 GitHub。

Meta 員工使用 GitHub 的專用瀏覽器擴充功能，可透過兩種方式導入拉取請求：一是「落地至 fbsource」（landed to fbsource），表示將導入並自動核准產生的差異，若無失敗，變更最終將同步回 `main`；二是「導入至 Phabricator」（imported to Phabricator），表示將變更複製到內部差異，需進一步審查和核准才能合併。

<figure>
  <img src="/img/importing-pull-requests.png" />
  <figcaption>Screenshot of the custom browser extension. The button "Import to fbsource" is used to import a Pull Request internally.</figcaption>
</figure>

## 機器人

審查和處理拉取請求時，您可能會遇到少數 GitHub 機器人帳號留下的評論。這些機器人旨在協助拉取請求審查流程。詳見 [機器人參考文件](/contributing/bots-reference) 以了解更多。

## 拉取請求標籤

- `Merged`：應用於已關閉的 PR，表示其變更已納入核心倉庫。此標籤是必要的，因為拉取請求不會直接在 GitHub 上合併。相反，包含 PR 變更的補丁會被導入並排入代碼審查隊列。核准後，將這些變更應用於 Meta 內部單一倉庫的結果會作為新提交同步回 GitHub。GitHub 不會將該提交歸因於原始 PR，因此需要此標籤來傳達 PR 的真實狀態。
- `Blocked on FB`：PR 已導入，但變更尚未應用。