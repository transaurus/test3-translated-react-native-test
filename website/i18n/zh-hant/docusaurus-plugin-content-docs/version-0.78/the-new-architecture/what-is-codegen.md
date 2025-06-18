# 什麼是 Codegen？

**Codegen** 是一個用來避免撰寫大量重複程式碼的工具。使用 **Codegen** 並非強制性的：你可以手動編寫所有生成的程式碼。然而，**Codegen** 會生成框架程式碼，這可以為你節省大量時間。

React Native 在每次構建 iOS 或 Android 應用時會自動調用 **Codegen**。偶爾，你可能會想要手動運行 **Codegen** 腳本，以了解實際生成哪些類型和文件：這在開發 Turbo Native Modules 和 Fabric Native Components 時是一個常見的情境。

<!-- TODO: Add links to TM and FC -->

## Codegen 如何運作

**Codegen** 是一個與 React Native 應用緊密耦合的過程。**Codegen** 腳本位於 `react-native` NPM 套件中，應用在構建時會調用這些腳本。

**Codegen** 會從你在 `package.json` 中指定的目錄開始，爬取專案中的文件夾，尋找包含自訂模組和元件規格（或 specs）的特定 JS 文件。規格文件是以類型化方言編寫的 JS 文件：React Native 目前支援 Flow 和 TypeScript。

每當 **Codegen** 找到一個規格文件時，它會生成與之相關的樣板程式碼。**Codegen** 會生成一些 C++ 黏合程式碼，然後生成平台特定的程式碼，使用 Java 為 Android 生成，使用 Objective-C++ 為 iOS 生成。