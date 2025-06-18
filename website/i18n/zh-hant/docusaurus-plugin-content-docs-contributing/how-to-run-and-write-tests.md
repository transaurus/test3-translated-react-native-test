---
title: How to Run and Write Tests
---

## 執行測試

本節說明如何以貢獻者身份測試您對 React Native 的修改。若尚未完成，請先依照[使用原生程式碼建置專案](/docs/environment-setup)的步驟設定開發環境。

### JavaScript 測試

執行 JavaScript 測試套件最簡單的方式，是在 React Native 程式碼庫根目錄下執行以下指令：

```bash
yarn test
```

此指令會使用 [Jest](https://jestjs.io) 執行測試。

您還需確保程式碼通過 [Flow](https://flowtype.org/) 類型檢查與 lint 檢測：

```bash
yarn flow
yarn lint
```

### iOS 測試

請遵循 `packages/rn-tester` 目錄中的 [README.md](https://github.com/facebook/react-native/blob/main/packages/rn-tester/README.md) 說明。

接著返回 React Native 程式碼庫根目錄執行 `yarn`，此指令會設定 JavaScript 相依套件。

此時您可透過以下腳本執行 iOS 測試（請在程式碼庫根目錄執行）：

```bash
./scripts/objc-test.sh test
```

亦可使用 Xcode 執行 iOS 測試。開啟 `RNTester/RNTesterPods.xcworkspace` 後，按 <kbd>Command + U</kbd> 或從選單列選擇 `Product` > `Test` 即可本地執行測試。

Xcode 的 Test Navigator 也支援執行單一測試，快捷鍵為 <kbd>Command + 6</kbd>。

:::note
`objc-test.sh` 會確保測試環境正確設定以執行所有測試，同時會停用已知不穩定或損壞的測試。使用 Xcode 執行測試時請注意此點，若發現非預期失敗，請先檢查該測試是否在 `objc-test.sh` 中被停用。
:::

#### iOS Podfile/Ruby 測試

若您修改了 `Podfile` 配置，可執行 Ruby 測試來驗證這些變更。

執行 Ruby 測試指令如下：

```bash
cd scripts
sh run_ruby_tests.sh
```

### Android 測試

Android 單元測試不需模擬器，而是在本地機器的 JVM 上執行。

執行 Android 單元測試的腳本如下（請在程式碼庫根目錄執行）：

```bash
./gradlew test
```

## 撰寫測試

當您修復錯誤或為 React Native 新增功能時，建議添加對應的測試案例。根據修改內容不同，適合的測試類型也會有所差異。

### JavaScript 測試

JavaScript 測試檔案位於 `__test__` 目錄中，與被測試檔案處於相同層級。基礎範例可參考 [`TouchableHighlight-test.js`][js-jest-test]，更多教學請參閱 Jest 的[測試 React Native 應用程式][jest-tutorial]指南。

[js-jest-test]: https://github.com/facebook/react-native/blob/main/Libraries/Components/Touchable/__tests__/TouchableHighlight-test.js

[jest-tutorial]: https://jestjs.io/docs/en/tutorial-react-native

### iOS 整合測試

React Native 提供測試工具，可簡化需跨橋接器通訊的原生與 JS 元件整合測試。

兩大核心元件為 `RCTTestRunner` 與 `RCTTestModule`。`RCTTestRunner` 負責設定 React Native 環境，並提供在 Xcode 中以 `XCTestCase` 執行測試的方法（最簡易的是 `runTest:module`）。`RCTTestModule` 則會以 `NativeModules.TestModule` 形式暴露給 JavaScript 使用。

測試本身是用 JavaScript 編寫的，必須在完成時調用 `TestModule.markTestCompleted()`，否則測試會超時並失敗。

測試失敗主要通過拋出 JavaScript 異常來表示。也可以使用 `runTest:module:initialProps:expectErrorRegex:` 或 `runTest:module:initialProps:expectErrorBlock:` 來測試錯誤條件，這些方法會預期拋出錯誤並驗證錯誤是否符合提供的條件。

請參閱以下範例用法和整合點：

- [`IntegrationTestHarnessTest.js`][f-ios-test-harness]
- [`RNTesterIntegrationTests.m`][f-ios-integration-tests]
- [`IntegrationTestsApp.js`][f-ios-integration-test-app]

[f-ios-test-harness]: https://github.com/facebook/react-native/blob/main/IntegrationTests/IntegrationTestHarnessTest.js

[f-ios-integration-tests]: https://github.com/facebook/react-native/blob/main/RNTester/RNTesterIntegrationTests/RNTesterIntegrationTests.m

[f-ios-integration-test-app]: https://github.com/facebook/react-native/blob/main/IntegrationTests/IntegrationTestsApp.js

### iOS 快照測試

一種常見的整合測試是快照測試。這些測試會渲染一個組件，並使用 `TestModule.verifySnapshot()` 將屏幕快照與參考圖片進行比對，背後使用的是 [`FBSnapshotTestCase`](https://github.com/facebook/ios-snapshot-test-case) 庫。參考圖片是通過在 `RCTTestRunner` 上設置 `recordMode = YES` 然後運行測試來記錄的。

32 位元和 64 位元系統以及不同操作系統版本之間的快照會略有不同，因此建議強制測試在[正確配置](https://github.com/facebook/react-native/blob/main/scripts/.tests.env)下運行。

強烈建議模擬所有網絡數據以及其他可能出問題的依賴項。請參閱 [`SimpleSnapshotTest`](https://github.com/facebook/react-native/blob/main/IntegrationTests/SimpleSnapshotTest.js) 了解基本範例。

如果你在拉取請求中進行了影響快照測試的更改（例如在快照範例中添加了一個新的案例），則需要重新記錄快照參考圖片。

為此，請在 [RNTester/RNTesterSnapshotTests.m](https://github.com/facebook/react-native/blob/136666e2e7d2bb8d3d51d599fc1384a2f68c43d3/RNTester/RNTesterIntegrationTests/RNTesterSnapshotTests.m#L29) 中將 `recordMode` 標誌更改為 `_runner.recordMode = YES;`，重新運行失敗的測試，然後將記錄模式切換回 `NO`，提交/更新你的拉取請求並等待 CircleCI 構建是否通過。

### Android 單元測試

當你處理可以單獨由 Java/Kotlin 代碼測試的代碼時，最好添加一個 Android 單元測試。Android 單元測試位於 `packages/react-native/ReactAndroid/src/test/`。

建議瀏覽這些測試以了解良好的單元測試應該是什麼樣子。

## 持續測試

我們使用 [CircleCI][config-circleci] 自動運行開源測試。每當拉取請求中添加提交時，CircleCI 都會運行這些測試，以幫助維護者了解代碼更改是否引入了回歸。這些測試也會在提交到 `main` 和 `*-stable` 分支時運行，以跟踪這些分支的健康狀況。

[config-circleci]: https://github.com/facebook/react-native/blob/main/.circleci/config.yml

還有另一組測試在 Meta 的內部測試基礎設施中運行。其中一些測試是由 React Native 的內部使用者定義的整合測試（例如 Facebook 應用中 React Native 表面的單元測試）。

這些測試會在每次提交到 Facebook 源代碼控制中託管的 React Native 副本時運行。它們也會在拉取請求導入到 Facebook 的源代碼控制時運行。

若這些測試失敗，您需要聯繫 Meta 的內部人員協助檢視。由於只有 Meta 員工能導入 pull request，負責導入的人員應能協助處理相關細節。

:::note
**在本機執行 CI 測試：**
多數開源協作者依賴 CircleCI 查看測試結果。若您希望使用與 CircleCI 相同的配置在本機驗證變更，可透過 CircleCI 提供的[命令行介面](https://circleci.com/docs/local-cli)在本地執行任務。
:::

### 常見問題

#### 如何升級 CI 測試使用的 Xcode 版本？

升級至新版 Xcode 時，請先確認該版本[受 CircleCI 支援](https://circleci.com/docs/testing-ios#supported-xcode-versions)。

您還需更新測試環境配置，確保測試在 CircleCI 機器上預裝的 iOS 模擬器中執行。

此資訊亦可查閱 [CircleCI 的 Xcode 版本參考文件](https://circleci.com/docs/2.0/testing-ios/#supported-xcode-versions)，點選所需版本後查看「Runtimes」段落。

接著請編輯以下兩個檔案：

- `.circleci/config.yml`

  修改 `macos:` 段落下的 `xcode:` 行（搜尋 `_XCODE_VERSION`）。

- `scripts/.tests.env`

  將 `IOS_TARGET_OS` 環境變數調整為對應的 iOS Runtime 版本。

若您計劃在 GitHub 上合併此變更，請務必通知 Meta 員工，因為他們在導入您的 pull request 時，需同步更新內部 Sandcastle RN OSS iOS 測試中 `react_native_oss.py` 的 `_XCODE_VERSION` 值。