---
id: publishing-to-app-store
title: Publishing to Apple App Store
---

發布流程與其他原生 iOS 應用程式相同，但需注意一些額外事項。

:::info
若使用 Expo，請參閱 Expo 的[部署至應用商店指南](https://docs.expo.dev/distribution/app-stores/)來建置並提交應用至 Apple App Store。本指南適用於任何 React Native 應用程式，可自動化部署流程。
:::

### 1. 啟用應用程式傳輸安全性

應用程式傳輸安全性（ATS）是 iOS 9 引入的安全功能，會拒絕所有未透過 HTTPS 傳送的 HTTP 請求。這可能導致 HTTP 流量被阻擋，包括開發用的 React Native 伺服器。為簡化開發流程，React Native 專案預設會停用對 `localhost` 的 ATS 限制。

在為生產環境建置應用程式前，應重新啟用 ATS：移除 `Info.plist` 檔案中 `NSExceptionDomains` 字典的 `localhost` 條目，並將 `NSAllowsArbitraryLoads` 設為 `false`。該檔案位於 `ios/` 資料夾。亦可透過 Xcode 重新啟用 ATS：開啟目標屬性中的 Info 面板，編輯「App Transport Security Settings」項目。

:::note
若應用程式需在生產環境存取 HTTP 資源，請學習如何設定專案的 ATS。
:::

### 2. 設定發布方案

為 App Store 分發建置應用程式時，需使用 Xcode 的 `Release` 方案。採用 `Release` 方案建置的應用程式會自動停用開發者選單，避免使用者在生產環境誤觸。同時會將 JavaScript 程式碼本地打包，使應用程式可在未連接電腦的裝置上測試。

要設定應用程式使用 `Release` 方案建置，請前往 **Product** → **Scheme** → **Edit Scheme**。在側邊欄選擇 **Run** 標籤，然後將 Build Configuration 下拉選單設為 `Release`。

![](/docs/assets/ConfigureReleaseScheme.png)

#### 專業建議

當應用程式套件體積增長時，可能會在啟動畫面與主應用畫面切換時看到空白閃屏。若發生此情況，可將以下程式碼加入 `AppDelegate.m`，使啟動畫面在過渡期間持續顯示。

```objectivec
  // Place this code after "[self.window makeKeyAndVisible]" and before "return YES;"
  UIStoryboard *sb = [UIStoryboard storyboardWithName:@"LaunchScreen" bundle:nil];
  UIViewController *vc = [sb instantiateInitialViewController];
  rootView.loadingView = vc.view;
```

每次針對實體裝置建置時（即使是 Debug 模式）都會產生靜態套件。若要節省時間，可在 Debug 模式停用套件生成：於 Xcode 的 Build Phase「Bundle React Native code and images」中的 shell 腳本加入以下內容：

```shell
 if [ "${CONFIGURATION}" == "Debug" ]; then
  export SKIP_BUNDLING=true
 fi
```

### 3. 建置發布版應用程式

現在可透過按下 `⌘B` 或從選單選擇 **Product** → **Build** 來建置發布版應用程式。建置完成後，即可分發應用程式給測試人員或提交至 App Store。

:::info
亦可使用 `React Native CLI` 執行此操作：透過 `--mode` 選項設定值為 `Release`（例如 `npx react-native run-ios --mode Release`）。
:::

完成測試並準備發布至 App Store 時，請按照本指南後續步驟操作。

- 開啟終端機，導航至應用程式的 iOS 資料夾並輸入 `open .`。
- 雙擊 YOUR_APP_NAME.xcworkspace，此操作會啟動 Xcode。
- 點擊 `Product` → `Archive`。請確認裝置設定為「Any iOS Device (arm64)」。

:::note
請檢查 Bundle Identifier 是否與 Apple Developer Dashboard 中建立的 Identifiers 完全一致。
:::

- 歸檔完成後，在歸檔視窗中點擊「分發應用程式」。
- 現在點擊「App Store Connect」（若您希望發佈至App Store）。
- 點擊「上傳」→ 確保所有核取方塊均已勾選，點擊「下一步」。
- 根據需求選擇「自動管理簽名」或「手動管理簽名」。
- 點擊「上傳」。
- 現在您可以在App Store Connect的TestFlight下找到該版本。

現在填寫必要資訊，在「建置版本」區段中選擇應用程式的建置版本，點擊「儲存」→「提交審核」。