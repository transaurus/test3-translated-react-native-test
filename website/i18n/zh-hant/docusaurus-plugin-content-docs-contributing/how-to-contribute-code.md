---
title: How to Contribute Code
---

感謝您對貢獻 React Native 感興趣！從評論和分類問題，到審查和提交 PR，[我們歡迎所有形式的貢獻](/contributing/overview)。本文件將說明如何貢獻程式碼至 React Native。

若您希望立即開始貢獻程式碼，我們有一份 [`good first issues`](https://github.com/facebook/react-native/labels/good%20first%20issue) 清單，其中包含範圍相對有限的錯誤。
標記為 [`help wanted`](https://github.com/facebook/react-native/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22help+wanted+%3Aoctocat%3A%22+sort%3Aupdated-desc+) 的問題適合提交 PR。

## 必要條件

:::info
請參閱[環境設定指南](/docs/environment-setup)，根據您使用的平台和開發目標平台，設定所需工具和開發環境。
:::

## 開發工作流程

克隆 React Native 後，開啟目錄並執行 `yarn` 以安裝其依賴項。

現在您已準備好執行以下指令：

- `yarn start` 啟動 Metro 打包伺服器。
- `yarn lint` 檢查程式碼風格。
- `yarn format` 自動格式化您的程式碼。
- `yarn test` 執行基於 Jest 的 JavaScript 測試套件。
  - `yarn test --watch` 執行互動式 JavaScript 測試監視器。
  - `yarn test <pattern>` 執行符合檔案名稱模式的 JavaScript 測試。
- `yarn flow` 執行 [Flow](https://flowtype.org/) 型別檢查。
  - `yarn flow-check-android` 對 `*.android.js` 檔案執行完整 Flow 檢查。
  - `yarn flow-check-ios` 對 `*.ios.js` 檔案執行完整 Flow 檢查。
- `yarn test-typescript` 執行 [TypeScript](https://www.typescriptlang.org/) 型別檢查。
- `yarn test-ios` 執行 iOS 測試套件（需 macOS）。
- `yarn build` 建置所有設定的套件 — 通常此指令僅需由 CI 在發布前執行。
  - 需要建置的套件設定於 [scripts/build/config.js](https://github.com/facebook/react-native/blob/main/scripts/build/config.js)。

## 測試您的變更

測試有助於防止程式碼庫引入回歸問題。我們建議執行 `yarn test` 或上述平台特定指令碼，以確保您在變更時不會引入任何回歸問題。

GitHub 儲存庫透過 CircleCI [持續測試](/contributing/how-to-run-and-write-tests#continuous-testing)，測試結果可透過 [commits](https://github.com/facebook/react-native/commits/main) 和 pull request 上的 Checks 功能查看。

您可以在[如何執行和撰寫測試](/contributing/how-to-run-and-write-tests)頁面了解更多關於執行和撰寫測試的資訊。

## 程式碼風格

我們使用 Prettier 格式化 JavaScript 程式碼。這可節省您的時間和精力，因為您可以透過其編輯器整合自動修復格式問題，或手動執行 `yarn run prettier`。我們還使用 linter 捕捉程式碼中可能存在的風格問題。您可以執行 `yarn run lint` 檢查程式碼風格狀態。

然而，仍有部分風格是 linter 無法捕捉的，特別是 Java 或 Objective-C 程式碼。

### Objective-C

- `@property` 宣告後保留空格
- 每個 `if` 的括號位於同一行
- `- method`、`@interface` 和 `@implementation` 的括號位於下一行
- 盡量保持每行約 80 字元（有時無法達成...）
- `*` 運算子與變數名稱相連（例如 `NSObject *variableName;`）

### Java

- 若方法調用跨越多行，右括號應與最後一個參數位於同一行。
- 若方法標頭無法容納於一行，每個參數應獨立成行。
- 每行長度限制為100個字符

## 提交拉取請求

對React Native的程式碼貢獻通常以[拉取請求](https://help.github.com/en/articles/about-pull-requests)形式提交。向React Native提案變更的流程可歸納如下：

1. 複製React Native儲存庫並從`main`分支創建您的分支。
2. 若新增需測試的程式碼，請添加測試。
3. 若變更API，請更新文件。
4. 確保測試套件通過（可在本地或於開啟拉取請求後透過CI測試）。
5. 確認程式碼符合規範（例如執行`yarn lint --fix`）。
6. 將變更推送至您的複製儲存庫。
7. 向React Native儲存庫發起拉取請求。
8. 審閱並處理拉取請求上的評論。
9. 機器人可能提出建議，通常我們會要求您先解決這些問題，維護者才會審查您的程式碼。
10. 若尚未簽署，請提交[貢獻者許可協議（"CLA"）](#contributor-license-agreement)。

若一切順利，您的拉取請求將被合併。若未獲合併，維護者將盡力說明原因。

若這是您首次提交拉取請求，我們已建立[逐步指南協助您入門](/contributing/how-to-open-a-pull-request)。有關拉取請求處理的詳細資訊，請參閱[管理拉取請求頁面](managing-pull-requests)。

### 貢獻者許可協議

為接受您的拉取請求，您需提交[貢獻者許可協議（CLA）](/contributing/contribution-license-agreement)。此為一次性要求，適用於所有Meta開源專案。簽署僅需一分鐘，您可在等待依賴項安裝時完成。

## 授權條款

貢獻React Native即表示您同意，您的貢獻將依React Native儲存庫根目錄中的[授權條款](https://github.com/facebook/react-native/blob/main/LICENSE)文件授權。