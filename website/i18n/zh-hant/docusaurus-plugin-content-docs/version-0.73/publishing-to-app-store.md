---
id: publishing-to-app-store
title: Publishing to Apple App Store
---

發布流程與其他原生iOS應用程式相同，但需注意一些額外事項。

:::info
若使用Expo，請參閱Expo的[部署至應用商店指南](https://docs.expo.dev/distribution/app-stores/)來建置並提交應用至Apple App Store。本指南適用於任何React Native應用程式，可自動化部署流程。
:::

### 1. 配置發布方案

為App Store分發建置應用需在Xcode中使用`Release`方案。以`Release`建置的應用會自動禁用應用內開發者選單，避免用戶在生產環境誤觸。同時會將JavaScript程式碼本地打包，使應用能在未連接電腦的裝置上測試。

配置應用使用`Release`方案：前往**Product** → **Scheme** → **Edit Scheme**。在側邊欄選擇**Run**標籤，將Build Configuration下拉選單設為`Release`。

![](/docs/assets/ConfigureReleaseScheme.png)

#### 專業建議

當應用套件體積增長時，可能會在啟動畫面與主應用視圖間出現白屏閃爍。若發生此情況，可將以下程式碼加入`AppDelegate.m`以延長啟動畫面顯示時間。

```objectivec
  // Place this code after "[self.window makeKeyAndVisible]" and before "return YES;"
  UIStoryboard *sb = [UIStoryboard storyboardWithName:@"LaunchScreen" bundle:nil];
  UIViewController *vc = [sb instantiateInitialViewController];
  rootView.loadingView = vc.view;
```

每次針對實體裝置建置時（即使是Debug模式）都會生成靜態套件。若需節省時間，可在Xcode建置階段「Bundle React Native code and images」的shell腳本中加入以下內容來關閉Debug模式的套件生成：

```shell
 if [ "${CONFIGURATION}" == "Debug" ]; then
  export SKIP_BUNDLING=true
 fi
```

### 2. 建置發布版本

現在可透過快捷鍵<kbd>Cmd ⌘</kbd> + <kbd>B</kbd>或選單欄**Product** → **Build**來建置發布版本。完成後即可分發應用給測試人員或提交至App Store。

:::info
亦可使用`React Native CLI`執行此操作：在專案根目錄執行`npm run ios -- --mode="Release"`或`yarn ios --mode Release`。
:::

完成測試準備發布至App Store時，請依循以下步驟：

- 開啟終端機，進入應用程式的iOS資料夾並輸入`open .`
- 雙擊YOUR_APP_NAME.xcworkspace（將啟動Xcode）
- 點擊`Product` → `Archive`，確保裝置設為「Any iOS Device (arm64)」

:::note
請確認Bundle Identifier與Apple開發者後台創建的Identifiers完全一致。
:::

- 封存完成後，在封存視窗點擊`Distribute App`
- 選擇`App Store Connect`（若要發布至App Store）
- 點擊`Upload` → 確保勾選所有選項 → 點擊`Next`
- 根據需求選擇「自動管理簽名」或「手動管理簽名」
- 點擊`Upload`
- 完成後可在App Store Connect的TestFlight中找到

填寫必要資訊後，在Build區段選擇應用建置版本，點擊`Save` → `Submit For Review`提交審核。