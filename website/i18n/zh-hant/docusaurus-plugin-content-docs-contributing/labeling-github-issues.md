---
title: Labeling GitHub Issues
---

[我們的標籤](https://github.com/facebook/react-native/issues/labels)大多帶有前綴，用以提示其用途。

您會立即注意到列表中有兩個主導性的標籤前綴：[API:](https://github.com/facebook/react-native/labels?utf8=%E2%9C%93&q=API%3A) 和 [Component:](https://github.com/facebook/react-native/labels?utf8=%E2%9C%93&q=Component%3A)。

這些通常表示與核心 React Native 函式庫中的 API 或元件相關的問題和拉取請求。這幫助我們一目了然地了解哪些元件急需文件或支援。

這些標籤由我們的[機器人](/contributing/bots-reference)自動添加，但如果機器人錯誤歸類了某個問題，請隨時調整它們。

- `p:` 類別的標籤表示與我們保持某種[關係](https://github.com/facebook/react-native/blob/main/ECOSYSTEM.md)的公司。例如，這些包括 Microsoft 和 Expo。這些標籤也是由我們的工具根據問題作者自動添加的。
- `DX:` 類別的標籤表示涉及開發者體驗的領域。用於標記對使用 React Native 的人產生負面影響的問題。
- `Tool:` 類別的標籤表示工具。例如 CocoaPods、Buck...
- `Resolution:` 標籤幫助我們溝通問題的狀態。是否需要更多資訊？在問題能夠進展之前需要做什麼？
- `Type:` 標籤由機器人根據拉取請求中的變更日誌欄位添加。它們也可能指非錯誤報告類型的問題。
- `Platform:` 標籤幫助我們識別問題影響的開發平台或目標作業系統。

如果不確定某個標籤的含義，請訪問 https://github.com/facebook/react-native/labels 並查看描述欄位。我們會盡力妥善記錄這些標籤。

### 標籤操作

應用以下標籤之一可能會觸發機器人互動。這些標籤的目的是通過在必要時提供預設回應來協助問題分類。

- 指示機器人留下帶有下一步驟評論的標籤：

  - `Needs: Issue Template`
  - `Needs: Environment Info`
  - `Needs: Verify on Latest Version`
  - `Needs: Repro`

- 指示機器人在留下解釋性評論後關閉問題的標籤：

  - `Resolution: For Stack Overflow`
  - `Type: Question`
  - `Type: Docs`

- 直接關閉問題且不留下評論的標籤：
  - `Type: Invalid`