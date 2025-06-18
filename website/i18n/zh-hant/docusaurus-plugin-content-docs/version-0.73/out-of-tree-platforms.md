---
id: out-of-tree-platforms
title: Out-of-Tree Platforms
---

React Native 不僅適用於 Android 和 iOS 裝置——我們的合作夥伴與社群還維護了將 React Native 帶到其他平台的專案，例如：

**來自合作夥伴**

- [React Native macOS](https://github.com/microsoft/react-native-macos) - 適用於 macOS 和 Cocoa 的 React Native。
- [React Native Windows](https://github.com/microsoft/react-native-windows) - 適用於微軟通用 Windows 平台 (UWP) 的 React Native。

**來自社群**

- [React Native tvOS](https://github.com/react-native-tvos/react-native-tvos) - 適用於 Apple TV 和 Android TV 裝置的 React Native。
- [React Native Web](https://github.com/necolas/react-native-web) - 使用 React DOM 在網頁上運行的 React Native。
- [React Native Skia](https://github.com/react-native-skia/react-native-skia) - 使用 [Skia](https://skia.org/) 作為渲染器的 React Native。目前支援 Linux 和 macOS。

## 建立你自己的 React Native 平台

目前從頭開始建立 React Native 平台的過程尚未有完善的文件記錄——即將推出的新架構 ([Fabric](/blog/2018/06/14/state-of-react-native-2018)) 的目標之一就是讓維護平台變得更加容易。

### 打包

從 React Native 0.57 開始，你可以將你的 React Native 平台註冊到 React Native 的 JavaScript 打包工具 [Metro](https://facebook.github.io/metro/) 中。這意味著你可以向 `npx react-native bundle` 傳遞 `--platform example` 參數，它會尋找帶有 `.example.js` 後綴的 JavaScript 檔案。

要向 RNPM 註冊你的平台，你的模組名稱必須符合以下模式之一：

- `react-native-example` - 它會搜尋所有以 `react-native-` 開頭的頂層模組
- `@org/react-native-example` - 它會搜尋任何作用域下以 `react-native-` 開頭的模組
- `@react-native-example/module` - 它會搜尋所有名稱以 `@react-native-` 開頭的作用域下的模組

你還必須在你的 `package.json` 中加入以下條目：

```json
{
  "rnpm": {
    "haste": {
      "providesModuleNodeModules": ["react-native-example"],
      "platforms": ["example"]
    }
  }
}
```

`"providesModuleNodeModules"` 是一個會被加入 Haste 模組搜尋路徑的模組陣列，而 `"platforms"` 是一個會被加入有效平台後綴的平台陣列。