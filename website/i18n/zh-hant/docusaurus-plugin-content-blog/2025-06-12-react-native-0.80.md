---
title: 'React Native 0.80 - React 19.1, JS API Changes, Freezing Legacy Arch and much more'
authors: [hezi, fabriziocucci, gabrieldonadel, chrfalch]
tags: [engineering]
date: 2025-06-12T16:01
---

# React Native 0.80 - React 19.1、JS API 變更、凍結舊版架構與更多更新

我們很高興宣布推出 React Native 0.80！

此版本將 React Native 內建的 React 版本更新至最新穩定版：19.1.0。

我們也針對 JS API 進行了一系列穩定性改進：深度導入現在會觸發警告，並提供新的選擇性 Strict TypeScript API，提供更精確且更安全的型別。

此外，React Native 的舊版架構現已正式凍結，您將開始看到針對即將在舊版架構完全淘汰後停止運作的 API 所發出的警告。

### 重點更新

- [JavaScript 深度導入棄用](/blog/2025/06/12/react-native-0.80#javascript-deep-imports-deprecation)
- [舊版架構凍結與警告](/blog/2025/06/12/react-native-0.80#legacy-architecture-freezing--warnings)
- [React 19.1.0](/blog/2025/06/12/react-native-0.80#react-1910)
- [實驗性功能 - React Native iOS 相依項現已預先建置](/blog/2025/06/12/react-native-0.80#experimental---react-native-ios-dependencies-are-now-prebuilt)

<!--truncate-->

## 重點更新詳情

### JavaScript 深度導入棄用

在此版本中，我們採取措施改進並穩定 React Native 的公開 JavaScript API。第一步是更好地界定哪些 API 可供應用程式和框架導入。為此，我們正式棄用 React Native 的深度導入（[參閱 RFC](https://github.com/react-native-community/discussions-and-proposals/pull/894)），並透過 ESLint 和 JS 控制台引入警告。

這些警告僅針對專案原始碼中的導入，並可[選擇退出](/docs/strict-typescript-api)。但請注意，我們計劃在未來的版本中移除 React Native API 的深度導入，應將其更新為根目錄導入。

```js
// Before - import from subpath
import {Alert} from 'react-native/Libraries/Alert/Alert';

// After - import from `react-native`
import {Alert} from 'react-native';
```

部分 API 未在根目錄導出，且將在沒有深度導入的情況下變得不可用。這是刻意為之，目的是減少 React Native API 的整體表面積。我們有一個開放的[意見回饋討論串](https://github.com/react-native-community/discussions-and-proposals/discussions/893)供使用者提出問題，並將與社群合作在（至少）接下來的兩個 React Native 版本中最終確定導出的 API。請分享您的意見！

了解更多關於此變更的詳細資訊，請參閱我們的專文：[邁向穩定的 JavaScript API](/blog/2025/06/12/moving-towards-a-stable-javascript-api)。

#### 選擇性 Strict TypeScript API

隨著上述公開 API 導出的重新定義，我們也在 0.80 版本中為 `react-native` 套件提供了一組新的 TypeScript 型別，稱為 Strict TypeScript API。

選擇啟用 Strict TypeScript API 是對 React Native 未來穩定 JavaScript API 的預覽。這些新型別具有以下特點：

1. **直接從原始碼生成** — 提高覆蓋率和正確性，因此您可以期待更強的相容性保證。
2. **限制於 React Native 的索引檔案** — 更嚴格地定義我們的公開 API，意味著在進行內部檔案變更時不會破壞 API。

我們將這些型別與現有型別一同發布，意味著您可以選擇在準備好時遷移。此外，如果您使用標準的 React Native API，許多應用程式應該**無需變更**即可通過驗證。我們強烈鼓勵早期採用者和新建立的應用程式透過 `tsconfig.json` 檔案選擇啟用。

當社群準備就緒時，Strict TypeScript API 將在未來成為我們的預設 API — 與深度導入移除同步進行。

進一步了解此變更，請參閱我們的專文：[邁向穩定的 JavaScript API](/blog/2025/06/12/moving-towards-a-stable-javascript-api)。

### 舊版架構凍結與警告

自 [0.76 版本](/blog/2024/10/23/the-new-architecture-is-here) 起，React Native 的新架構已成為預設選擇，我們也讀到許多 [成功案例](https://blog.kraken.com/product/engineering/how-kraken-fixed-performance-issues-via-incremental-adoption-of-the-react-native-new-architecture)，顯示專案與工具能從中獲得顯著效益。

[我們近期說明](https://github.com/reactwg/react-native-new-architecture/discussions/290)，現已將舊版架構視為凍結狀態。我們將不再為舊版架構開發新的錯誤修正或功能，且在版本發布過程中也不再測試舊版架構。

為使遷移過程更順暢，若您遇到錯誤或回歸問題，仍可選擇退出新架構。

然而，在 React Native 中同時維護兩種架構是極大的挑戰，會影響運行效能、應用程式大小及程式碼庫的維護。

因此，我們最終必須在未來某個時間點淘汰舊版架構。

在 0.80 版本中，我們新增了一系列警告，當您使用在新架構中無法運作的 API 時，這些警告會出現在 React Native DevTools 中。

建議您不要忽略這些警告，並考慮將應用程式與函式庫遷移至新架構，以迎接未來的變化。

![舊版架構警告](/blog/assets/0.80-legacy-arch-warnings.png)

您可以在我們近期於 App.js 發表的演講「[Life After Legacy: The New Architecture Future](https://www.youtube.com/live/K2JTTKpptGs?si=tRkT535f0GzguVGt&t=9011)」中，進一步了解這些變更。

### React 19.1.0

此版本的 React Native 搭載了最新的 React 穩定版：19.1.0。

您可以在 [發布說明](https://github.com/facebook/react/releases/tag/v19.1.0) 中閱讀 React 19.1.0 的所有新功能與錯誤修正。

:::warning

React 19.1.0 的一個重要功能是實現並改進了擁有者堆疊（owner stacks）。這是一項僅在開發階段使用的功能，可協助您識別導致特定錯誤的元件。

然而，我們注意到，若您使用預設啟用的 `@babel/plugin-transform-function-name` Babel 插件（React Native Babel Preset 的一部分），擁有者堆疊在 React Native 中可能無法如預期運作。我們將在未來的 React Native 版本中修正此問題。

:::

### 實驗性功能 - React Native iOS 相依項現已預先建置

若您曾建置 React Native iOS 應用程式，可能注意到首次原生建置會耗費一些時間：在較舊的機器上可能需要幾分鐘甚至更久。這是因為我們需要編譯整個 React Native iOS 程式碼及其所有相依項。

過去幾週，我們一直在實驗預先建置部分 React Native iOS 核心程式碼，類似於 Android 的做法，以減少首次運行 React Native 應用程式時的建置時間。

React Native 0.80 是首個能將部分 iOS 版 React Native 以預先建置形式發布的版本，有助於縮短建置時間。

在 React Native 的發布過程中，我們會產生一個名為 `ReactNativeDependencies.xcframework` 的 XCFramework，這是僅包含 React Native 所依賴的第三方相依項的預先建置版本。

我們實驗並測試了此 iOS 預先建置功能節省的時間。根據我們的基準測試（在 M4 機器上運行），使用預先建置的 iOS 建置速度比從原始碼建置快約 12%。

根據我們的經驗，我們也觀察到許多使用者的錯誤報告是由於 React Native 第三方依賴項的建置問題所導致（例如 [#39568](https://github.com/facebook/react-native/issues/39568)）。
預先建置這些第三方依賴項讓我們能為您處理建置過程，從而避免這些建置問題的發生。

請注意，我們並非預先建置整個 React Native：僅預先建置 Meta 未直接控制的函式庫，例如 Folly 和 GLog。

在未來的版本中，我們也將把 React Native 核心的其餘部分以預建置形式發布。

#### 如何使用此功能

此功能目前仍處於實驗階段，因此預設為關閉狀態。

若想啟用此功能，您可透過添加 `RCT_USE_RN_DEP` 環境變數來安裝 pods：

```bash
RCT_USE_RN_DEP=1 bundle exec pod install
```

或者，若想為所有開發成員啟用此功能，可修改 Podfile 如下：

```diff
if linkage != nil
  Pod::UI.puts "Configuring Pod with #{linkage}ally linked Frameworks".green
  use_frameworks! :linkage => linkage.to_sym
end

+ENV[‘`RCT_USE_RN_DEP`’] = ‘1’

target 'HelloWorld' do
  config = use_native_modules!
```

請將預建置可能導致的任何問題回報至[此討論串](https://github.com/react-native-community/discussions-and-proposals/discussions/912)。我們將全力調查這些問題，確保此功能對您的應用程式完全透明。

## 其他變更

### Android - 透過 IPO 實現更小的 APK 體積

此版本為所有使用 React Native 建置的 Android 應用程式帶來顯著的體積縮減。從 0.80 開始，我們為 React Native 和 Hermes 建置啟用了[程序間優化](https://en.wikipedia.org/wiki/Interprocedural_optimization)。

這項變更為所有 Android 應用節省了約 1MB 的空間。

![android apk 體積比較](/blog/assets/0.80-android-apk-size.png)

只需將 React Native 版本升級至 0.80 即可獲得此體積優化，無需對專案進行其他修改。

### 新應用程式畫面重新設計

若您未使用 Expo 而是採用 Community CLI 與模板，在此版本中我們已將新應用程式畫面移至[獨立套件](https://www.npmjs.com/package/@react-native/new-app-screen)並進行了視覺翻新。這減少了使用 Community 模板建立新應用時的初始樣板程式碼，同時為大螢幕設備提供更佳的使用體驗。

![新應用程式畫面重新設計](/blog/assets/0.80-new-community-template-landing.jpg)

### 關於 JSC 社群支援的公告

React Native 0.80 是最後一個提供官方 JSC 支援的版本。後續 JSC 支援將由社群維護的套件 `@react-native-community/javascriptcore` 提供。

若您錯過了先前的公告，可[在此閱讀詳細內容](/blog/2025/04/08/react-native-0.79#jsc-moving-to-community-package)

## 重大變更

### 在主套件新增 `"exports"` 欄位

作為 JS 穩定 API 變更的一部分，我們在 `react-native` 的 `package.json` 清單中引入了 [`"exports"` 欄位](https://nodejs.org/api/packages.html#package-entry-points)。

在 0.80 版本中，此映射預設仍會暴露**所有 JavaScript 子路徑**，因此不應造成重大相容性問題。但同時，這可能會微妙地影響 `react-native` 套件內模組的解析方式：

- 在 Metro 下，[平台特定擴展](https://metrobundler.dev/docs/package-exports#replacing-platform-specific-extensions)不會自動針對 `"exports"` 匹配進行擴展。我們已提供多個墊片模組來適應此變更（[#50426](https://github.com/facebook/react-native/pull/50426)）。
- 在 Jest 下，模擬深度導入的功能可能會受到影響，這可能需要更新測試案例。

### 其他重大變更

以下清單列舉了我們認為可能對您的產品代碼產生輕微影響、值得注意的其他重大變更：

### JavaScript

- 我們將 `eslint-plugin-react-hooks` 從 v4.6.0 升級至 v5.2.0（完整[變更記錄在此](https://github.com/facebook/react/blob/main/packages/eslint-plugin-react-hooks/CHANGELOG.md)）。`react-hooks` 的 lint 規則可能會產生新的錯誤提示，您需要修復或抑制這些錯誤。

### Android

- 此版本將 React Native 內建的 Kotlin 版本升級至 2.1.20。Kotlin 2.1 引入了預覽版的新語言功能，您可以在模組/元件中開始使用。詳細資訊請參閱[官方發布說明](https://kotlinlang.org/docs/whatsnew21.html)。
- 我們移除了 `StandardCharsets` 類別。該類別自 0.73 版起已被棄用，您應改用 `java.nio.charset.StandardCharsets` 類別。
- 我們將多個類別標記為內部使用。這些類別不屬於公共 API，不應直接存取。我們已通知或提交修補程式至受影響的函式庫：
  - `com.facebook.react.fabric.StateWrapperImpl`
  - `com.facebook.react.modules.core.ChoreographerCompat`
  - `com.facebook.react.modules.common.ModuleDataCleaner`
- 我們將多個類別從 Java 遷移至 Kotlin。如果您使用這些類別，部分參數的可空性和類型已變更，可能需要調整您的代碼：
  - `com.facebook.react.devsupport` 下的所有類別
  - `com.facebook.react.bridge.ColorPropConverter`
  - `com.facebook.react.views.textinput.ReactEditText`
  - `com.facebook.react.views.textinput.ReactTextInputManager`

### iOS

- 我們移除了 RCTUtils.h 中的 `RCTFloorPixelValue` 欄位 - `RCTFloorPixelValue` 方法在 React Native 中未被使用，因此我們將其完全刪除。

更多較小的重大變更請參閱 [0.80 版的變更記錄](https://github.com/facebook/react-native/blob/main/CHANGELOG.md#v0800)。

## 致謝

React Native 0.80 包含了來自 127 位貢獻者的超過 1167 次提交。感謝所有人的辛勤付出！

<!--alex ignore special white-->

我們要特別感謝在此版本中做出重大貢獻的社群成員：

- [Christian Falch](https://github.com/chrfalch) 為 React Native 依賴項的 iOS 預建置工作
- [Iwo Plaza](https://x.com/iwoplaza)、[Jakub Piasecki](https://x.com/breskin67) 和 [Dawid Małecki](https://github.com/coado) 對嚴格 TypeScript API 的貢獻

此外，我們也要感謝為此版本發布文檔撰寫功能的額外作者：

- [Riccardo Cipolleschi](https://github.com/cipolleschi) 撰寫了關於 React Native 依賴項 iOS 預建置的部分
- [Alex Hunt](https://x.com/huntie) 負責深度導入棄用、嚴格 TypeScript API 選用、新應用畫面重新設計
- [Nicola Corti](https://x.com/cortinico) 處理舊版架構凍結與警告

## 升級至 0.80

請使用 [React Native 升級助手](https://react-native-community.github.io/upgrade-helper/) 查看現有專案在 React Native 版本間的代碼變更，並參閱升級文件。

若要建立新專案：

若您使用 Expo，React Native 0.80 將在 Expo SDK 的 Canary 版本中獲得支援。關於如何在 Expo 中使用 React Native 0.80 的詳細說明，亦可參閱[專屬部落格文章](https://expo.dev/changelog/react-native-80)。

:::info

0.80 版現為 React Native 的最新穩定版本，0.77.x 版將轉為不受支援狀態。更多資訊請參閱 React Native 的支援政策。我們預計在近期發布 0.77 版的最終終止支援更新。

:::