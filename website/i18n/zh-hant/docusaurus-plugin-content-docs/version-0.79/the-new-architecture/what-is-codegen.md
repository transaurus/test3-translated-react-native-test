# 什麼是 Codegen？

**Codegen** 是一個用來避免撰寫大量重複程式碼的工具。使用 **Codegen** 並非強制性的：你可以手動撰寫所有生成的程式碼。然而，**Codegen** 會生成一些基礎架構程式碼，這可以為你節省大量時間。

React Native 在每次建構 iOS 或 Android 應用程式時，會自動調用 **Codegen**。有時，你可能會想要手動執行 **Codegen** 腳本，以了解實際生成了哪些類型和檔案：這在開發 Turbo Native Modules 和 Fabric Native Components 時是一個常見的情境。

<!-- TODO: Add links to TM and FC -->

## Codegen 如何運作

**Codegen** 是一個與 React Native 應用程式緊密耦合的過程。**Codegen** 腳本位於 `react-native` NPM 套件中，應用程式在建構時會調用這些腳本。

**Codegen** 會從你在 `package.json` 中指定的目錄開始，爬取專案中的資料夾，尋找包含自訂模組和元件規格（或稱 specs）的特定 JS 檔案。規格檔案是以類型化方言撰寫的 JS 檔案：React Native 目前支援 Flow 和 TypeScript。

每當 **Codegen** 找到一個規格檔案時，它會生成與之相關的樣板程式碼。**Codegen** 會生成一些 C++ 黏合程式碼，然後再生成平台特定的程式碼，Android 使用 Java，iOS 則使用 Objective-C++。