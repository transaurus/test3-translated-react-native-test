---
id: publishing-to-app-store
title: Publishing to Apple App Store
---

發布流程與其他原生iOS應用程式相同，但需注意一些額外事項。

:::info
若您使用Expo，請參閱Expo的[部署至應用商店指南](https://docs.expo.dev/distribution/app-stores/)，以構建並提交您的應用至Apple App Store。本指南適用於任何React Native應用程式，可自動化部署流程。
:::

### 1. 配置發布方案

為App Store分發構建應用需在Xcode中使用`Release`方案。以`Release`構建的應用將自動禁用應用內開發者選單，防止用戶在生產環境中誤觸。同時會將JavaScript代碼本地打包，使您能在未連接電腦的情況下於設備上測試應用。

配置應用使用`Release`方案：前往**Product** → **Scheme** → **Edit Scheme**。在側邊欄選擇**Run**標籤，將Build Configuration下拉選單設為`Release`。

![](/docs/assets/ConfigureReleaseScheme.png)

#### 專業建議

當應用包體積增大時，您可能會在啟動畫面與主應用視圖之間看到空白閃屏。若出現此情況，可將以下代碼加入`AppDelegate.m`以保持啟動畫面顯示過渡期間。

```objectivec
  // Place this code after "[self.window makeKeyAndVisible]" and before "return YES;"
  UIStoryboard *sb = [UIStoryboard storyboardWithName:@"LaunchScreen" bundle:nil];
  UIViewController *vc = [sb instantiateInitialViewController];
  rootView.loadingView = vc.view;
```

即使處於Debug模式，每次針對實體設備時都會構建靜態包。若要節省時間，可在Xcode Build Phase的`Bundle React Native code and images`的shell腳本中加入以下內容來關閉Debug模式下的包生成：

```shell
 if [ "${CONFIGURATION}" == "Debug" ]; then
  export SKIP_BUNDLING=true
 fi
```

### 2. 構建發布版本

現在可通過按<kbd>Cmd ⌘</kbd> + <kbd>B</kbd>或選擇選單欄的**Product** → **Build**來構建發布版本。完成後即可將應用分發給測試人員或提交至App Store。

:::info
亦可使用`React Native CLI`執行此操作，透過`--mode`選項設為`Release`（例如在專案根目錄執行：`npm run ios -- --mode="Release"`或`yarn ios --mode Release`）。
:::

完成測試準備發布至App Store時，請遵循本指南後續步驟。

- 啟動終端機，導航至應用的iOS資料夾並輸入`open .`
- 雙擊YOUR_APP_NAME.xcworkspace，將啟動Xcode
- 點擊`Product` → `Archive`。請確保設備設為"Any iOS Device (arm64)"

:::note
請檢查Bundle Identifier是否與Apple Developer Dashboard中建立的Identifiers完全一致。
:::

- 歸檔完成後，在歸檔視窗點擊`Distribute App`
- 選擇`App Store Connect`（若要發布至App Store）
- 點擊`Upload` → 確保所有複選框已勾選 → 點擊`Next`
- 根據需求選擇`Automatically manage signing`或`Manually manage signing`
- 點擊`Upload`
- 現在您可在App Store Connect的TestFlight中找到該版本

填寫必要資訊後，在Build區段選擇應用構建版本，點擊`Save` → `Submit For Review`。

### 4. 螢幕截圖

Apple Store要求提供最新設備的螢幕截圖。相關設備規格參考請見[此處](https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications/)。請注意若已提供某些顯示尺寸的截圖，則其他尺寸非必填。