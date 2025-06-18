---
title: Easier Upgrades Thanks to Git
author: Nicolas Cuillery
authorTitle: JavaScript consultant and trainer at Zenika
authorURL: 'https://twitter.com/ncuillery'
authorImageURL: 'https://fr.gravatar.com/userimage/78328995/184460def705a160fd8edadc04f60eaf.jpg?size=128'
authorTwitter: ncuillery
tags: [announcement]
---

升級 React Native 版本向來是個難題。你可能曾遇過類似以下情況：

![](/blog/assets/git-upgrade-conflict.png)

這些選項都不理想。覆寫檔案會導致本地修改遺失，不覆寫則無法取得最新更新。

今天我很榮幸介紹一款解決此問題的新工具。該工具名為 `react-native-git-upgrade`，背後運用 Git 技術在可能情況下自動解決衝突。

## 使用方式

> **必要條件**：Git 必須存在於 `PATH` 環境變數中。專案本身無需受 Git 管理。

全域安裝 `react-native-git-upgrade`：

```shell
$ npm install -g react-native-git-upgrade
```

或使用 [Yarn](https://yarnpkg.com/)：

```shell
$ yarn global add react-native-git-upgrade
```

接著在專案目錄內執行：

```shell
$ cd MyProject
$ react-native-git-upgrade 0.38.0
```

> 注意：請**不要**執行 'npm install' 來安裝新版 `react-native`。此工具需比對新舊專案模板才能正確運作。只需如上所示在舊版專案目錄內直接執行即可。

範例輸出：

![](/blog/assets/git-upgrade-output.png)

亦可直接執行 `react-native-git-upgrade` 不帶參數，即會升級至最新 React Native 版本。

我們會盡量保留你對 Android 與 iOS 建置檔案的修改，因此升級後無需執行 `react-native link`。

此實作設計盡可能降低侵入性。完全基於臨時目錄中即時建立的本地 Git 倉庫，不會干擾你的專案倉庫（無論使用何種版本控制系統：Git、SVN、Mercurial...或無）。若發生意外錯誤，原始碼將被還原。

## 運作原理

關鍵步驟在於產生 Git 補丁。該補丁包含 React Native 模板從當前版本到新版本之間的所有變更。

為取得此補丁，我們需要從 `node_modules` 目錄下 `react-native` 套件內嵌的模板生成應用程式（這些模板與 `react-native init` 指令使用的相同）。接著，當原生應用程式分別從當前版本與新版本的模板生成後，Git 便能產出符合專案的補丁（例如包含你的應用名稱）：

```
[...]

diff --git a/ios/MyAwesomeApp/Info.plist b/ios/MyAwesomeApp/Info.plist
index e98ebb0..2fb6a11 100644
--- a/ios/MyAwesomeApp/Info.plist
+++ b/ios/MyAwesomeApp/Info.plist
@@ -45,7 +45,7 @@
        <dict>
            <key>localhost</key>
            <dict>
-               <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
+               <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
            </dict>
        </dict>
[...]
```

最後只需將此補丁套用至原始碼檔案。舊版 `react-native upgrade` 流程會要求確認每個微小差異，而 Git 能透過三方合併演算法自動整合多數變更，最終僅留下熟悉的衝突標記：

```
    13B07F951A680F5B00A75B9A /* Release */ = {
      isa = XCBuildConfiguration;
      buildSettings = {
        ASSETCATALOG_COMPILER_APPICON_NAME = AppIcon;
<<<<<<< ours
        CODE_SIGN_IDENTITY = "iPhone Developer";
        FRAMEWORK_SEARCH_PATHS = (
          "$(inherited)",
          "$(PROJECT_DIR)/HockeySDK.embeddedframework",
          "$(PROJECT_DIR)/HockeySDK-iOS/HockeySDK.embeddedframework",
        );
=======
        CURRENT_PROJECT_VERSION = 1;
>>>>>>> theirs
        HEADER_SEARCH_PATHS = (
          "$(inherited)",
          /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include,
          "$(SRCROOT)/../node_modules/react-native/React/**",
          "$(SRCROOT)/../node_modules/react-native-code-push/ios/CodePush/**",
        );
```

這些衝突通常易於理解。標記 **ours** 代表「你的團隊」，而 **theirs** 可視為「React Native 團隊」。

## 為何要引入新全域套件？

React Native 自帶全域 CLI（[react-native-cli](https://www.npmjs.com/package/react-native-cli) 套件），會將指令委派給 `node_modules/react-native/local-cli` 目錄中的本地 CLI。

如前所述，此流程必須從當前 React Native 版本啟動。若將實作內嵌於 local-cli，使用舊版 React Native 時就無法享受此功能。例如，若此升級程式碼僅在 0.38.0 版釋出，你將無法從 0.29.2 升級至 0.38.0。

基於 Git 的升級方式大幅改善了開發者體驗，讓所有人都能使用這項功能至關重要。透過全域安裝的獨立套件 [react-native-git-upgrade](https://www.npmjs.com/package/react-native-git-upgrade)，無論您的專案使用哪個 React Native 版本，現在就能立即採用這項新功能。

另一個原因是 Martin Konicek 近期執行的 [Yeoman 移除計畫](https://twitter.com/martinkonicek/status/800730190141857793)。我們不希望為了比對舊版模板來建立修補檔，而讓這些 Yeoman 相依套件重新回到 `react-native` 套件中。

## 試用並提供意見回饋

總結來說，請盡情使用這項功能，並歡迎[提出改進建議、回報問題](https://github.com/facebook/react-native/issues)，特別是[發送 Pull Request](https://github.com/facebook/react-native/pulls)。每種開發環境與 React Native 專案都有差異，我們需要您的回饋來讓這項功能更完善。

### 致謝

特別感謝優秀的公司 [Zenika](https://www.zenika.com) 與 [M6 Web (封存頁面)](https://web.archive.org/web/20161230163633/http://www.groupem6.fr:80/le-groupe_en/activites/diversifications/m6-web.html)，若沒有他們的協助，這一切將無法實現！