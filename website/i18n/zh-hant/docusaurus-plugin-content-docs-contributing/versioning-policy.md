---
title: Versioning Policy
---

本頁面描述我們為 `react-native` 套件所遵循的版本控制政策。

我們會對每個 React Native 版本進行全面測試，包括手動與自動化測試，以確保品質不會倒退。

React Native 的 `stable` 頻道遵循以下所述的 0.x.y 發布政策。

React Native 也提供 `nightly` 發布頻道，以鼓勵對實驗性功能的早期反饋。

本頁面描述我們對 `react-native` 及 `@react-native` 範疇下套件的版本號碼處理方式。

## 穩定發布版本

React Native 會以固定節奏發布穩定版本。

我們遵循 0.x.y 版本控制架構：

- 重大變更會在新的次要版本中發布，即增加 x 數字（例如：0.78.0 至 0.79.0）。
- 新功能與 API 也會在新的次要版本中發布，即增加 x 數字（例如：0.78.0 至 0.79.0）。
- 關鍵錯誤修正會在新的修補版本中發布，即增加 y 數字（例如：0.78.1 至 0.78.2）。

穩定版本會定期發布，最新版本會在 NPM 上標記為 `latest`。

同一次要版本號下的一系列發布稱為**次要系列**（例如 0.76.x 是 0.76.0、0.76.1、0.76.2 等的次要系列）。

## 穩定性承諾

為支援使用者升級 React Native 版本，我們承諾維護最新的 3 個次要系列（例如當 0.78 為最新發布時，維護 0.78.x、0.77.x 和 0.76.x）。

我們會為這些版本定期發布更新與錯誤修正。

您可以在 [react-native-releases 工作小組](https://github.com/reactwg/react-native-releases/blob/main/docs/support.md) 閱讀更多關於我們支援政策的資訊。

### 重大變更

重大變更對所有人都不便，我們會盡量將其控制在最低限度。每個穩定版本中的重大變更都會在以下位置強調：

- [React Native 變更日誌](https://github.com/facebook/react-native/blob/main/CHANGELOG.md) 的 _Breaking_ 與 _Removed_ 章節
- 每篇發布部落格的 _Breaking Changes_ 章節

對於每個重大變更，我們承諾解釋背後原因、盡可能提供替代 API，並最小化對終端使用者的影響。

### 什麼是重大變更？

我們認為 React Native 的重大變更包括：

- 不相容的 API 變更（即變更或移除 API，導致您的程式碼因該變更而無法編譯/執行）。例如：
  - 任何需要您修改程式碼才能編譯的 JS/Java/Kotlin/Obj-c/C++ API 變更。
  - `@react-native/codegen` 中不向後相容的變更。
- 顯著的行為/執行時期變更。例如：
  - 某個屬性的佈局邏輯大幅變更。
- 開發體驗的顯著變更。例如：
  - 完全移除某個除錯功能。
- 任何我們傳遞依賴項的主要版本升級。例如：
  - 將 React 從 18.x 升級至 19.x
  - 將 Android 的 Target SDK 從 34 升級至 35）。
- 任何我們支援的平台版本範圍縮減。例如：
  - 將 Android 的 min SDK 從 21 提升至 23
  - 將最低 iOS 版本提升至 15.1。

我們不認為以下變更屬於重大變更：

- 修改帶有 `unstable_` 前綴的 API：這些 API 提供實驗性功能，我們對其最終形態尚未有十足把握。透過加上 `unstable_` 前綴發布，我們能更快迭代並盡早達成穩定 API。
- 變更私有或內部 API：這些 API 通常帶有 `internal_`、`private_` 前綴，或位於 `internal/`、`private/` 目錄/套件中。雖然部分 API 可能因工具限制而具有公開可見性，但我們不將其視為公開 API 的一部分，因此會直接變更而不另行通知。
  - 同理，若您存取內部屬性名稱如 `__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED` 或 `__reactInternalInstance$uk43rzhitjg`，我們不做任何保證，後果自負。
  - 標註為 `@FrameworkAPI` 的類別亦視為內部 API
- 變更工具/開發用 API：React Native 部分公開 API 專供框架與其他工具整合使用。例如某些 Metro API 或 React Native DevTools API 僅限其他框架或工具使用。此類 API 的變更會直接與受影響工具討論，不視為破壞性變更（我們不會在發布部落格中廣泛公告）。
- 開發警告：由於警告不影響運行時行為，我們可能在任何版本間新增或修改警告訊息。

若預期某項變更會對社群造成廣泛影響，我們仍會盡力為生態系統提供漸進式遷移路徑。

### 廢棄週期

隨著 React Native 持續發展演進，我們會編寫新 API 並有時需廢棄既有 API。這些 API 將經歷廢棄週期。

API 一旦被廢棄，仍會**持續提供**於**後續**穩定版本中。

例如：若某 API 在 React Native 0.76.x 被廢棄，它仍會存在於 0.77.x 版本，且不會早於 React Native 0.78.x 被移除。

若判斷生態系統需要更長時間遷移，我們可能延長特定廢棄 API 的保留期限。對此類 API 通常會提供警告訊息協助使用者遷移。

## 發布通道

React Native 仰賴活躍的開源社群提交錯誤報告、發起拉取請求與提案 RFC。為鼓勵回饋，我們支援多種發布通道。

:::note
本節內容主要與框架、函式庫或開發工具開發者相關。以 React Native 建置使用者介面應用為主的開發者，通常只需關注 latest 通道即可。
:::

### latest

`latest` 是穩定、符合語意化版本控制的 React Native 發布通道。透過 npm 安裝 React Native 時預設取得此通道。您當前使用的正是此通道。直接使用 React Native 的使用者介面應用程式皆採用此通道。

我們定期發布新版次要系列，並更新 `latest` 標籤指向最新穩定版本。

### next

在宣告新 React Native 版本穩定前，我們會發布一系列**候選版本**，從 RC0 開始。這些預發布版本遵循版本規範 `0.79.0-rc.0`，並在 NPM 上標記為 `next`。

當新分支切割完成且 RC 版本開始發布至 NPM 與 GitHub 時，建議測試您的函式庫/框架是否能相容 React Native 的 `next` 版本。

此舉能確保您的專案在未來 React Native 版本中仍可正常運作。

但請勿直接於使用者介面應用中使用預發布/RC 版本，因其尚未被視為生產環境就緒。

### nightly

我們也提供 `nightly` 發佈頻道。每日構建版本是從 [facebook/react-native](https://github.com/facebook/react-native) 的 `main` 分支每日發佈的。這些夜間版本被視為 React Native 的不穩定版本，不建議用於正式生產環境。

夜間版本遵循 `0.80.0-nightly-<DATE>-<SHA>` 的版本命名規則，其中 `<DATE>` 代表構建日期，`<SHA>` 則是發佈該版本所用提交的 SHA 值。

夜間版本僅供測試用途，我們不保證不同夜間版本之間的行為一致性。這些版本不遵循我們在 latest/next 頻道發佈時使用的 semver 協議。

建議設置 CI 工作流程，每天針對 react-native@nightly 版本測試您的函式庫，以確保您的函式庫能持續相容於未來的正式版本。