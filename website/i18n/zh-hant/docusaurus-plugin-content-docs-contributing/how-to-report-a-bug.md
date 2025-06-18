---
title: How to Report a Bug
---

回報 React Native 的錯誤是開始貢獻專案的最佳方式之一。我們使用 [GitHub issues](https://github.com/facebook/react-native/issues) 作為處理新錯誤報告的主要管道。

在開啟新錯誤報告前，請先[搜尋該錯誤是否已存在](https://github.com/facebook/react-native/issues?q=sort%3Aupdated-desc%20is%3Aissue)於我們的問題追蹤系統中。多數情況下，這是最快找到解決方案的方式，因為可能已有其他人遇到相同問題。

若在問題追蹤系統中找不到您的錯誤，您可以開啟新報告。建立新問題時，請確保：

- 添加問題描述。
- 遵循[問題模板](https://github.com/facebook/react-native/issues/new?template=bug_report.yml)的指示。
- 註明您使用的 React Native 版本。
- 添加 `npx @react-native-community/cli info` 指令的輸出結果。
- 若適用，請附上問題的螢幕截圖和影片。

所有錯誤報告都應包含**重現步驟**：能幫助我們理解問題並進行除錯的必要程式碼。

:::warning

由於我們收到大量問題報告，重現步驟是**強制要求**。沒有重現步驟的問題將無法調查，很可能會被關閉。

:::

## 提供重現步驟

重現步驟的目的是提供一種能_重現_您錯誤的方法。沒有重現步驟，我們將無法理解錯誤，也無法修復它。

重現步驟應盡量**精簡**：依賴項越少越好（理想情況下除了 `react-native` 外沒有其他依賴），這有助於我們更好地隔離錯誤。
當 GitHub 上有人要求提供重現步驟時，他們**並非**在要求您提供所有原始碼。

您需要建立一個能重現相同崩潰/錯誤/問題的**最小化**專案。

這個過程至關重要，因為通常問題會在建立重現步驟的過程中被解決。透過建立重現步驟，更容易判斷問題是與您的特定設定相關，還是 React Native 本身的錯誤。

由於 React Native 的流量龐大，我們僅接受以下其中一種有效的重現步驟：

1. 對於大多數錯誤：提交 Pull Request 修改 [RNTesterPlayground.js](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/Playground/RNTesterPlayground.js) 來重現您的錯誤。
2. 若錯誤與 UI 相關：提供一個 [Snack](https://snack.expo.dev)。
3. 若錯誤與建置/升級相關：使用我們的[重現模板](https://github.com/react-native-community/reproducer-react-native/generate)建立專案。

### RNTesterPlayground.js

提供重現步驟的最佳方式是針對 React Native 開啟 Pull Request 來修改 [`RNTesterPlayground.js`](https://github.com/facebook/react-native/blob/main/packages/rn-tester/js/examples/Playground/RNTesterPlayground.js) 檔案。

:::tip

這種重現方式會讓您的程式碼在 `react-native` 的 `main` 分支上執行，是我們調查和修復錯誤的**最快速**途徑。

:::

`RNTesterPlayground.js` 檔案位於 RN-Tester 應用程式內，這是我們的參考應用程式。您可以在其[專屬的 README 檔案](https://github.com/facebook/react-native/blob/main/packages/rn-tester/README.md)中閱讀更多關於它如何運作以及如何建置的資訊。

此類重現步驟的範例可參考：[Reproduce modal layout issues #50704](https://github.com/facebook/react-native/pull/50704/)。

編輯完 `RNTesterPlayground.js` 後，您將能在 RNTester 的 **Playground** 分頁中看到程式碼執行結果：

![第一步](/docs/assets/RNTesterPlayground.png)

### Expo Snack

對於大多數與 UI 相關的錯誤，您可以使用 [Expo Snack](https://snack.expo.dev/) 來重現問題。

透過 Expo Snack，您能在瀏覽器中直接執行 React Native 程式碼，並立即看到渲染結果。

當您成功在 Expo Snack 重現問題後，點擊 **儲存** 按鈕取得可分享的連結，並將其附加至問題報告中。

### 重現模板

對於大多數與建置相關的錯誤，您應使用 [社群提供的重現模板](https://github.com/react-native-community/reproducer-react-native) 來重現問題。

此模板會建立一個小型專案，該專案使用 React Native Community CLI 執行，可用於展示建置問題。

模板已預設整合 GitHub Actions 的 CI 流程，這有助於發現您可能遇到的建置問題。

使用此模板的步驟：

1. 在 GitHub 點擊 [使用此模板](https://github.com/new?template_name=reproducer-react-native&template_owner=react-native-community) 按鈕，以模板為基礎建立新專案。
2. 將新建立的儲存庫克隆至本地端。
3. 進行修改以重現您的問題。
4. 將儲存庫連結附加至您建立的錯誤報告中。