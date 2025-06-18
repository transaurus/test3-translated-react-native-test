---
id: publishing-to-app-store
title: Publishing to Apple App Store
---

發布流程與其他原生 iOS 應用程式相同，但需注意一些額外事項。

:::info
若使用 Expo，請參閱 Expo 的 [部署至應用商店指南](https://docs.expo.dev/distribution/app-stores/) 來建置並提交應用至 Apple App Store。本指南適用於任何 React Native 應用程式，可自動化部署流程。
:::

### 1. 配置發布方案

為 App Store 分發建置應用需在 Xcode 中使用 `Release` 方案。`Release` 建置的應用會自動禁用應用內開發者選單，防止用戶在生產環境中誤觸。同時會將 JavaScript 程式碼本地打包，使應用能在未連接電腦的裝置上測試。

配置應用使用 `Release` 方案：前往 **Product** → **Scheme** → **Edit Scheme**。在側邊欄選擇 **Run** 標籤，將 Build Configuration 下拉選單設為 `Release`。

![](/docs/assets/ConfigureReleaseScheme.png)

#### 專業建議

當應用套件體積增長時，可能會在啟動畫面與主應用視圖之間出現短暫白屏。若發生此情況，可將以下程式碼加入 `AppDelegate.m` 以保持啟動畫面顯示過渡期間的連續性。

```objectivec
  // Place this code after "[self.window makeKeyAndVisible]" and before "return YES;"
  UIStoryboard *sb = [UIStoryboard storyboardWithName:@"LaunchScreen" bundle:nil];
  UIViewController *vc = [sb instantiateInitialViewController];
  rootView.loadingView = vc.view;
```

靜態套件會在每次針對實體裝置建置時生成（即使是 Debug 模式）。若要節省時間，可在 Xcode Build Phase 的 `Bundle React Native code and images` shell 腳本中加入以下內容來關閉 Debug 模式下的套件生成：

```shell
 if [ "${CONFIGURATION}" == "Debug" ]; then
  export SKIP_BUNDLING=true
 fi
```

### 2. 建置發布版應用

現在可透過快捷鍵 <kbd>Cmd ⌘</kbd> + <kbd>B</kbd> 或選單欄 **Product** → **Build** 來建置發布版應用。完成發布建置後，即可分發應用給測試人員並提交至 App Store。

:::info
也可使用 `React Native CLI` 執行此操作：在專案根目錄執行 `npm run ios -- --mode="Release"` 或 `yarn ios --mode Release`（透過 `--mode` 參數指定 `Release` 模式）。
:::

完成測試準備發布至 App Store 時，請依循本指南後續步驟。

- 開啟終端機，導航至應用程式的 iOS 資料夾並輸入 `open .`
- 雙擊 YOUR_APP_NAME.xcworkspace 檔案（將啟動 Xcode）
- 點選 `Product` → `Archive`，確認裝置設定為 "Any iOS Device (arm64)"

:::note
請檢查 Bundle Identifier 是否與 Apple Developer Dashboard 中建立的 Identifiers 完全一致。
:::

- 封存完成後，在封存視窗點選 `Distribute App`
- 選擇 `App Store Connect`（若要發布至 App Store）
- 點選 `Upload` → 確認勾選所有選項後點擊 `Next`
- 根據需求選擇 `Automatically manage signing` 或 `Manually manage signing`
- 點擊 `Upload`
- 完成後可在 App Store Connect 的 TestFlight 中找到該建置版本

填寫必要資訊後，在 Build 區段選擇應用建置版本，點擊 `Save` → `Submit For Review` 提交審核。

### 4. 螢幕截圖

Apple Store 要求提供最新裝置的螢幕截圖。規格參考請見 [官方文件](https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications/)。需注意若已提供某些顯示尺寸的截圖，其他尺寸可能非必填項目。