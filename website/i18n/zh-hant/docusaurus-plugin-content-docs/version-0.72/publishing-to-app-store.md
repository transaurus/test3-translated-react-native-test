---
id: publishing-to-app-store
title: Publishing to Apple App Store
---

發布流程與其他原生 iOS 應用程式相同，但需注意一些額外事項。

:::info
若使用 Expo，請參閱 Expo 的[部署至應用商店指南](https://docs.expo.dev/distribution/app-stores/)，了解如何建置並提交應用程式至 Apple App Store。本指南適用於任何 React Native 應用程式，可自動化部署流程。
:::

### 1. 啟用應用程式傳輸安全性

應用程式傳輸安全性（ATS）是 iOS 9 引入的安全功能，會拒絕所有未透過 HTTPS 發送的 HTTP 請求。這可能導致 HTTP 流量被阻擋，包括開發用的 React Native 伺服器。為簡化開發流程，React Native 專案預設會停用對 `localhost` 的 ATS。

在為生產環境建置應用程式前，應重新啟用 ATS。請從 `Info.plist` 檔案的 `NSExceptionDomains` 字典中移除 `localhost` 條目，並將 `NSAllowsArbitraryLoads` 設為 `false`。該檔案位於 `ios/` 資料夾中。您也可透過 Xcode 重新啟用 ATS：開啟目標屬性中的 Info 面板，編輯「App Transport Security Settings」條目。

:::note
若您的應用程式需在生產環境存取 HTTP 資源，請了解如何設定專案的 ATS。
:::

### 2. 設定發布方案

為 App Store 分發建置應用程式時，需在 Xcode 中使用 `Release` 方案。以 `Release` 建置的應用程式會自動停用應用內開發者選單，防止使用者在生產環境中誤觸。同時會將 JavaScript 本地打包，讓您能在未連接電腦的情況下於裝置上測試應用程式。

要設定應用程式以 `Release` 方案建置，請前往 **Product** → **Scheme** → **Edit Scheme**。在側邊欄選擇 **Run** 標籤，然後將 Build Configuration 下拉選單設為 `Release`。

![](/docs/assets/ConfigureReleaseScheme.png)

#### 專業建議

當應用程式套件體積增長時，您可能會在啟動畫面與根應用程式視圖之間看到空白閃爍。若發生此情況，可將以下代碼加入 `AppDelegate.m`，以在過渡期間保持啟動畫面顯示。

```objectivec
  // Place this code after "[self.window makeKeyAndVisible]" and before "return YES;"
  UIStoryboard *sb = [UIStoryboard storyboardWithName:@"LaunchScreen" bundle:nil];
  UIViewController *vc = [sb instantiateInitialViewController];
  rootView.loadingView = vc.view;
```

每次針對實體裝置建置時（即使是 Debug 模式），都會產生靜態套件。若要節省時間，可在 Debug 模式停用套件生成：於 Xcode Build Phase 的「Bundle React Native code and images」shell 腳本中加入以下內容：

```shell
 if [ "${CONFIGURATION}" == "Debug" ]; then
  export SKIP_BUNDLING=true
 fi
```

### 3. 建置發布版應用程式

現在您可以透過按下 <kbd>Cmd ⌘</kbd> + <kbd>B</kbd> 或從選單欄選擇 **Product** → **Build** 來建置發布版應用程式。建置完成後，即可將應用程式分發給測試人員並提交至 App Store。

:::info
您也可使用 `React Native CLI` 執行此操作，透過 `--mode` 選項設定為 `Release`（例如在專案根目錄執行：`npm run ios -- --mode="Release"` 或 `yarn ios --mode Release`）。
:::

完成測試並準備發布至 App Store 時，請遵循本指南後續步驟。

- 開啟終端機，導航至應用程式的 iOS 資料夾並輸入 `open .`。
- 雙擊 YOUR_APP_NAME.xcworkspace，這將啟動 Xcode。
- 點擊 `Product` → `Archive`。請確保將裝置設為「Any iOS Device (arm64)」。

:::note
請檢查您的 Bundle Identifier，確保與 Apple Developer Dashboard 中建立的 Identifiers 完全一致。
:::

- 歸檔完成後，在歸檔視窗中點擊「分發應用程式」。
- 現在點擊「App Store Connect」（若您想發佈至App Store）。
- 點擊「上傳」→ 確保所有核取方塊都已勾選，點擊「下一步」。
- 根據您的需求選擇「自動管理簽名」或「手動管理簽名」。
- 點擊「上傳」。
- 現在您可以在App Store Connect的TestFlight中找到它。

現在填寫必要的資訊，在「建置版本」區段中選擇應用程式的建置版本，點擊「儲存」→「提交審核」。