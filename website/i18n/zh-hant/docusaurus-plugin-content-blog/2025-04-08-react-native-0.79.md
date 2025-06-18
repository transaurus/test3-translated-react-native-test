---
title: 'React Native 0.79 - Faster tooling and much more'
authors: [alanjhughes, shubham, fabriziocucci, cortinico]
tags: [engineering]
date: 2025-04-08
---

# React Native 0.79 - Faster tooling, faster startup and much more

Today we are excited to release React Native 0.79!

This release ships with performance improvements on various fronts, as well as several bugfixes. First, Metro is now faster to start thanks to deferred hashing, and has stable support for package exports. Startup time in Android will also be improved thanks to changes in the JS bundle compressions and much more.

### Highlights

- [New Metro Features](/blog/2025/04/08/react-native-0.79#metro-faster-startup-and-package-exports-support)
- [JSC moving to a Community Package](/blog/2025/04/08/react-native-0.79#jsc-moving-to-community-package)
- [iOS: Swift-Compatible Native Modules registration](/blog/2025/04/08/react-native-0.79#ios-swift-compatible-native-modules-registration)
- [Android: Faster App Startup](/blog/2025/04/08/react-native-0.79#android-faster-app-startup)
- [Removal of Remote JS Debugging](/blog/2025/04/08/react-native-0.79#removal-of-remote-js-debugging)

<!--truncate-->

## Highlights

### Metro: Faster startup and package exports support

This release ships with [Metro 0.82](https://github.com/facebook/metro/releases/tag/v0.82.0).This version uses deferred hashing to improve the speed of first `yarn start` typically by over 3x (more in larger projects and monorepos) making your development experience and CI builds faster on a daily basis.

![metro startup comparison](/blog/assets/0.79-metro-startup-comparison.gif)

Also in Metro 0.82, we’re promoting `package.json` `"exports"` and `"imports"` field resolution to stable. `"exports"` resolution was [introduced in React Native 0.72](/blog/2023/06/21/package-exports-support), and `"imports"` support was added in a community contribution - both will now be enabled by default for all the projects on React Native 0.79.

This improves compatibility with modern npm dependencies, and opens up new, standards-compliant ways to organise your projects.

:::warning[Breaking change]

While we've been testing `package.json` `"exports"` in the community for a while, this switchover can be a breaking change for certain packages and project setups.

In particular, we're aware of user reported incompatibilities for some popular packages including Firebase and AWS Amplify, and are working to get these fixed at source.

If you're encountering issues:

- Please update to the Metro [0.81.5 hotfix](https://github.com/facebook/metro/releases/tag/v0.81.5), or set [`resolver.unstable_enablePackageExports = false`](https://metrobundler.dev/docs/configuration/#unstable_enablepackageexports-experimental) to opt out.
- See [expo/expo#36551](https://github.com/expo/expo/discussions/36551) for affected packages and future updates.

:::

### JSC moving to Community Package

As part of our effort to reduce the API surface of React Native, we're in the process of moving the JavaScriptCore (JSC) engine to a community-maintained package: `@react-native-community/javascriptcore`

This change will not affect users that are using Hermes.

Starting with React Native 0.79, you can use a community supported version of JSC by following the [installation instructions in the readme](https://github.com/react-native-community/javascriptcore#installation). The JSC version provided by React Native core will still be available in 0.79, but we’re planning to remove it [in the near future](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0836-lean-core-jsc.md).

將JSC移至社群維護的套件將使我們能更頻繁地更新JSC版本，並提供您最新功能。社群維護的JSC將遵循與React Native不同的發佈週期。

### iOS：相容Swift的原生模組註冊

在此版本中，我們重新設計了將原生模組註冊到React Native運行時的方式。新方法遵循與元件相同的途徑，詳見[官方文件](/docs/next/the-new-architecture/using-codegen#configuring-codegen)。

從此版本開始，您可以透過修改`package.json`檔案來註冊模組。我們在`ios`屬性中新增了`modulesProvider`欄位：

```diff
"codegenConfig": {
     "ios": {
+       "modulesProvider": {
+         "JS Name for the module": "ObjC Module provider for the pure C++ TM or a class conforming to RCTTurboModule"
+     }
    }
}
```

Codegen將負責從您的`package.json`檔案生成所有相關程式碼。

若您使用純C++原生模組，則需遵循以下建議配置：

<details>
<summary>Configure pure C++Native Modules in your app</summary>

For pure C++ Native Modules, you need to add a new ObjectiveC++ class to glue together the C++ Native Module with the rest of the App:

```objc title="CppNativeModuleProvider.h"
#import <Foundation/Foundation.h>
#import <ReactCommon/RCTTurboModule.h>

NS_ASSUME_NONNULL_BEGIN

@interface <YourNativeModule>Provider : NSObject <RCTModuleProvider>

@end
```

```objc title="CppNativeModuleProvider.mm"
NS_ASSUME_NONNULL_END

#import "<YourNativeModule>Provider.h"
#import <ReactCommon/CallInvoker.h>
#import <ReactCommon/TurboModule.h>
#import "<YourNativeModule>.h"

@implementation NativeSampleModuleProvider

- (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:
    (const facebook::react::ObjCTurboModule::InitParams &)params
{
  return std::make_shared<facebook::react::NativeSampleModule>(params.jsInvoker);
}
```

</details>

透過這種新方法，我們統一了應用程式開發者和函式庫維護者的原生模組註冊流程。函式庫可在其`package.json`中指定相同屬性，Codegen將處理後續工作。

此方法解決了我們在0.77版本中引入的限制，該限制曾阻礙純C++原生模組與Swift`AppDelegate`的註冊。如您所見，這些變更均未修改`AppDelegate`，生成的程式碼將適用於以Swift和Objective-C實現的`AppDelegate`。

### Android：更快的應用啟動

我們還推出了一項變更，可大幅改善Android應用的啟動時間。

從此版本開始，我們將不再壓縮APK內的JavaScript套件。先前Android系統需在應用啟動前解壓縮JavaScript套件，這導致應用啟動時明顯變慢。

自本版本起，我們預設將以未壓縮形式提供JavaScript套件，因此您的Android應用啟動速度通常會更快。

[Margelo](https://margelo.com)團隊在Discord應用上測試此功能後獲得顯著效能提升：Discord的互動時間(TTI)減少了400毫秒，僅透過一行變更就實現了12%的加速（測試設備為三星A14）。

另一方面，以未壓縮形式儲存套件會導致應用在用戶設備上佔用更多空間。若您對此有顧慮，可透過修改`app/build.gradle`檔案中的`enableBundleCompression`屬性來切換此行為。

```kotlin title="app/build.gradle"
react {
  // ...
  // If you want to compress the JS bundle (slower startup, less
  // space consumption)
  enableBundleCompression = true
  // If don't you want to compress the JS bundle (faster startup,
  // higher space consumption)
  enableBundleCompression = false

  // Default is `false`
}
```

請注意，此版本中APK大小將會增加，但用戶在從網路下載APK時不會承擔額外的下載大小成本，因為APK在下載時會被壓縮。

## 重大變更

### 移除遠端JS偵錯

作為持續改進偵錯體驗的一部分，我們移除了透過Chrome進行的遠端JS偵錯功能。此傳統偵錯方法已在[React Native 0.73中被棄用並改為運行時選擇加入](/blog/2023/12/06/0.73-debugging-improvements-stable-symlinks#remote-javascript-debugging)。請使用[React Native開發者工具](/docs/react-native-devtools)進行現代化且可靠的偵錯。

這也意味著React Native不再相容[react-native-debugger](https://github.com/jhen0409/react-native-debugger)社群專案。對於想使用第三方偵錯擴充功能（如Redux DevTools）的開發者，我們推薦使用[Expo開發者工具外掛](https://github.com/expo/dev-plugins)，或整合這些工具的獨立版本。

詳見[此專文討論](https://github.com/react-native-community/discussions-and-proposals/discussions/872)。

### 內部模組更新為`export`語法

作為現代化 JavaScript 程式碼庫的一部分，我們已將 `react-native` 中多個實作模組更新為統一使用 `export` 語法，取代原有的 `module.exports`。

我們總共更新了約 **46 個 API**，詳細清單可參閱[更新日誌](https://github.com/facebook/react-native/blob/main/CHANGELOG.md#v0790)。

此變更對現有導入方式有細微影響：

<details>
<summary>**Case 1: Default export**</summary>

```diff
  // CHANGED - require() syntax
- const ImageBackground = require('react-native/Libraries/Image/ImageBackground');
+ const ImageBackground = require('react-native/Libraries/Image/ImageBackground').default;

// Unchanged - import syntax
import ImageBackground from 'react-native/Libraries/Image/ImageBackground';

// RECOMMENDED - root import
import {ImageBackground} from 'react-native';

```

</details>

<details>

<summary>**Case 2: Secondary exports**</summary>

There are very few cases of this pattern, again unaffected when using the root `'react-native'` import.

```diff
  // Unchanged - require() syntax
  const BlobRegistry = require('react-native/Libraries/Blob/BlobRegistry');

  // Unchanged - require() syntax with destructuring
  const {register, unregister} = require('react-native/Libraries/Blob/BlobRegistry');

  // CHANGED - import syntax as single object
- import BlobRegistry from 'react-native/Libraries/Blob/BlobRegistry';
+ import * as BlobRegistry from 'react-native/Libraries/Blob/BlobRegistry';


  // Unchanged - import syntax with destructuring
  import {register, unregister} from 'react-native/Libraries/Blob/BlobRegistry';

  // RECOMMENDED - root import
  import {BlobRegistry} from 'react-native';
```

</details>

我們預期此變更的影響範圍極小，特別是對於使用 TypeScript 和 `import` 語法的專案。請檢查並修正任何類型錯誤以更新您的程式碼。

:::tip

**強烈建議使用根路徑 `react-native` 導入**

作為通用建議，我們強烈建議從根路徑 `'react-native'` 導入，以避免未來非必要的破壞性變更。在下個版本中，我們將棄用深層導入，作為明確定義 React Native 公共 JavaScript API 的一部分（參閱 [RFC](https://github.com/react-native-community/discussions-and-proposals/pull/894)）。

:::

### 其他破壞性變更

以下清單列舉了可能對產品程式碼產生輕微影響的其他破壞性變更，值得特別注意。

- **陰影框與濾鏡中無單位長度的失效**：
  - 為使 React Native 更符合 CSS/Web 規範，我們不再支援在 `box-shadow` 和 `filter` 中使用無單位長度。這意味著若您曾使用如 `1 1 black` 的陰影設定，現在將無法渲染。您應改用帶單位的數值，例如 `1px 1px black`。
- **從 normalize-color 移除錯誤的 hwb() 語法支援**：
  - 為符合 CSS/Web 規範，我們現在限制 `hwb()` 的某些無效語法。過去 React Native 曾支援逗號分隔值（如 `hwb(0, 0%, 100%)`），現已不再支援（應遷移至 `hwb(0 0% 100%)`）。詳情請參閱[此提交](https://github.com/facebook/react-native/commit/676359efd9e478d69ad430cff213acc87b273580)。
- **Libraries/Core/ExceptionsManager 導出方式更新**：
  - 作為現代化 React Native JS API 的一部分，我們將 <code>[ExceptionsManager](https://github.com/facebook/react-native/blob/0.79-stable/packages/react-native/Libraries/Core/ExceptionsManager.js)</code> 改為導出預設的 `ExceptionsManager` 物件，並將 `SyntheticError` 作為次要導出。

## 致謝

React Native 0.79 版本包含來自 100 位貢獻者的超過 944 次提交。感謝所有人的辛勤付出！

我們特別感謝在此版本中貢獻顯著的社群成員：

- [Marc Rousavy](https://github.com/mrousavy) 開發並撰寫「Android：更快的應用啟動」功能文件
- [Kudo Chien](https://github.com/Kudo) 與 [Oskar Kwaśniewski](https://github.com/okwasniewski) 負責 `@react-native-community/javascriptcore` 套件及「JSC 遷移至社群套件」章節
- [James Lawson](https://github.com/facebook/metro/pull/1302) 在 [Metro](https://github.com/facebook/metro/pull/1302) 中新增子路徑解析支援

此外，我們也感謝為此版本發布文件撰寫功能的作者：

- [Rob Hogan](https://github.com/robhogan) 撰寫「新 Metro 功能」章節
- [Alex Hunt](https://github.com/huntie) 負責「移除遠端 JS 偵錯」與「內部模組更新為 export 語法」章節
- [Riccardo Cipolleschi](https://github.com/cipolleschi) 實作 iOS 原生模組註冊功能

## 升級至 0.79

請使用 [React Native Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 查看現有專案在 React Native 版本間的程式碼變更，並參閱升級文件。

建立新專案：

```sh
npx @react-native-community/cli@latest init MyProject --version latest
```

若您使用 Expo，React Native 0.79 將在即將發布的 Expo SDK 53 中作為預設的 React Native 版本獲得支援。

:::info

0.79 現為 React Native 的最新穩定版本，0.76.x 版本將不再獲得支援。更多資訊請參閱 [React Native 的支援政策](https://github.com/reactwg/react-native-releases/blob/main/docs/support.md)。我們預計在近期發布 0.76 版本的最終終止支援更新。

:::