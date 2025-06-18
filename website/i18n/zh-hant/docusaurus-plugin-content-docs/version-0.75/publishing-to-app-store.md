---
id: publishing-to-app-store
title: Publishing to Apple App Store
---

發布流程與其他原生 iOS 應用程式相同，但需注意一些額外考量事項。

:::info
若您使用 Expo，請參閱 Expo 的 [部署至應用商店](https://docs.expo.dev/distribution/app-stores/) 指南來建置並提交應用程式至 Apple App Store。本指南適用於任何 React Native 應用程式以自動化部署流程。
:::

### 1. 配置發布方案

為在 App Store 分發而建置應用程式需在 Xcode 中使用 `Release` 方案。以 `Release` 建置的應用程式會自動停用應用內開發者選單，防止使用者在生產環境中誤觸。同時會將 JavaScript 本地打包，因此您可在未連接電腦時將應用程式安裝至裝置進行測試。

要配置應用程式使用 `Release` 方案建置，請前往 **Product** → **Scheme** → **Edit Scheme**。在側邊欄選擇 **Run** 標籤頁，然後將 Build Configuration 下拉選單設為 `Release`。

![](/docs/assets/ConfigureReleaseScheme.png)

#### 專業建議

當您的應用程式套件體積增長時，可能會在啟動畫面與根應用程式視圖顯示之間看到空白閃屏。若發生此情況，可將以下代碼加入 `AppDelegate.m` 以在過渡期間保持啟動畫面顯示。

```objectivec
  // Place this code after "[self.window makeKeyAndVisible]" and before "return YES;"
  UIStoryboard *sb = [UIStoryboard storyboardWithName:@"LaunchScreen" bundle:nil];
  UIViewController *vc = [sb instantiateInitialViewController];
  rootView.loadingView = vc.view;
```

即使處於 Debug 模式，每次針對實體裝置時都會建置靜態套件。若想節省時間，可透過在 Xcode Build Phase 的 `Bundle React Native code and images` 殼層腳本中加入以下內容來關閉 Debug 模式下的套件生成：

```shell
 if [ "${CONFIGURATION}" == "Debug" ]; then
  export SKIP_BUNDLING=true
 fi
```

### 2. 建置發布版應用程式

現在您可以透過按下 <kbd>Cmd ⌘</kbd> + <kbd>B</kbd> 或從選單欄選擇 **Product** → **Build** 來建置發布版應用程式。完成發布建置後，即可將應用程式分發給測試人員並提交至 App Store。

:::info
您也可使用 `React Native CLI` 執行此操作，透過 `--mode` 選項設定值為 `Release`（例如在專案根目錄執行：`npm run ios -- --mode="Release"` 或 `yarn ios --mode Release`）。
:::

完成測試並準備發布至 App Store 後，請依照本指南操作。

- 開啟終端機，導航至應用程式的 iOS 資料夾並輸入 `open .`。
- 雙擊 YOUR_APP_NAME.xcworkspace，這將啟動 Xcode。
- 點擊 `Product` → `Archive`。請確保將裝置設定為「Any iOS Device (arm64)」。

:::note
請檢查您的 Bundle Identifier 是否與 Apple Developer Dashboard 中建立的 Identifiers 完全一致。
:::

- 封存完成後，在封存視窗中點擊 `Distribute App`。
- 點擊 `App Store Connect`（若您要發布至 App Store）。
- 點擊 `Upload` → 確保所有核取方塊已勾選，點擊 `Next`。
- 根據需求選擇 `Automatically manage signing` 或 `Manually manage signing`。
- 點擊 `Upload`。
- 現在您可在 App Store Connect 的 TestFlight 中找到該建置版本。

填寫必要資訊後，在 Build 區段選擇應用程式建置版本，點擊 `Save` → `Submit For Review`。

### 4. 螢幕截圖

Apple Store 要求提供最新裝置的螢幕截圖。相關裝置規格參考請見 [此處](https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications/)。請注意若已提供其他尺寸的截圖，部分顯示尺寸的截圖並非必要。