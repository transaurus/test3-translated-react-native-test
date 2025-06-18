---
title: 'Package Exports Support in React Native'
authors: [huntie]
tags: [announcement, metro]
date: 2023-06-21
---

# React Native 中的套件匯出支援

隨著 [React Native 0.72](/blog/2023/06/21/0.72-metro-package-exports-symlinks) 的發布，我們的 JavaScript 建置工具 Metro 現在包含對 `package.json` 中 [`"exports"`](https://nodejs.org/docs/latest-v18.x/api/packages.html#exports) 欄位的測試版支援。當[啟用](/blog/2023/06/21/package-exports-support#enabling-package-exports-beta)後，它新增了以下功能：

- [React Native 專案將能直接使用更多 npm 套件](/blog/2023/06/21/package-exports-support#for-app-developers)
- [套件能定義其 API 並針對 React Native 的新功能](/blog/2023/06/21/package-exports-support#for-package-maintainers-preview)
- [套件解析的一些破壞性變更（在邊緣情況下）](/blog/2023/06/21/package-exports-support#breaking-changes)

在這篇文章中，我們將介紹套件匯出的運作方式，以及這些變更對身為 React Native 應用程式開發者或套件維護者的您意味著什麼。

<!-- truncate -->

## 什麼是套件匯出？

套件匯出是 Node.js 12.7.0 引入的現代方法，讓 npm 套件能指定**進入點**——套件子路徑的映射，這些子路徑可以被外部導入並解析到哪些檔案。

支援 `"exports"` 改善了 React Native 專案與更廣泛的 JavaScript 生態系統的互動（[目前約有 16.6k 個套件使用](https://github.com/search?q=path%3A%2A%2A%2Fpackage.json+%22%5C%22access%5C%22%3A+%5C%22public%5C%22%22+%22%5C%22exports%5C%22%22+NOT+path%3A%2A%2A%2Fnode_modules+NOT+is%3Afork+NOT+is%3Aarchived&type=code)），並為套件作者提供了一套標準化功能集，讓跨平台套件能針對 React Native。

[`"exports"`](https://nodejs.org/docs/latest-v19.x/api/packages.html#exports) 可以與 [`"main"`](https://nodejs.org/docs/latest-v19.x/api/packages.html#main) 一起使用，或完全取代它。

```json
{
  "name": "@storybook/addon-actions",
  "main": "./dist/index.js",
  ...
  "exports": {
    ".": {
      "node": "./dist/index.js",
      "import": "./dist/index.mjs",
      "default": "./dist/index.js"
    },
    "./preview": {
      "import": "./dist/preview.mjs",
      "default": "./dist/preview.js"
    },
    ...
    "./package.json": "./package.json"
  }
}
```

以下是一些應用程式代碼，通過導入 `@storybook/addon-actions` 的不同子路徑來使用上述套件。

```js
import {action} from '@storybook/addon-actions';
// -> '@storybook/addon-actions/dist/index.js'

import {action} from '@storybook/addon-actions/preview';
// -> '@storybook/addon-actions/dist/preview.js'

import helpers from '@storybook/addon-actions/src/preset/addArgsHelpers';
// Inaccessible - not listed in "exports"!
```

套件匯出的主要功能包括：

- **套件封裝**：只有 `"exports"` 中定義的子路徑才能從套件外部導入——讓套件能控制其公共 API。
- **子路徑別名**：套件可以定義自訂子路徑，這些子路徑映射到不同的檔案位置（包括通過[子路徑模式](https://nodejs.org/docs/latest-v19.x/api/packages.html#subpath-patterns)）——允許在保留公共 API 的同時重新定位檔案。
- **條件式匯出**：一個子路徑可能根據環境解析到不同的底層檔案。例如，針對 `"node"`、`"browser"` 或 `"react-native"` 運行時——取代 [`"browser"` 欄位規範](https://github.com/defunctzombie/package-browser-field-spec)。

:::note
`"exports"` 的完整功能詳見 [Node.js 套件進入點規範](https://nodejs.org/docs/latest-v19.x/api/packages.html#package-entry-points)。

由於這些功能與現有的 React Native 概念（如[平台特定擴展](/docs/platform-specific-code)）重疊，且 `"exports"` 已在 npm 生態系統中存在一段時間，我們聯繫了 React Native 社群，以確保我們的實作能滿足開發者的需求（[PR](https://github.com/react-native-community/discussions-and-proposals/pull/534)、[最終 RFC](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0534-metro-package-exports-support.md)）。
:::

## For app developers

Package Exports can be enabled today, in beta.

- Imports against packages that depend on Package Exports features (such as [**Firebase**](https://www.npmjs.com/package/firebase) and [**Storybook**](https://www.npmjs.com/search?q=%40storybook)) should now work as designed.
- React Native for Web projects using Metro will now be able to use the `"browser"` conditional export, removing the need for workarounds.

Enabling Package Exports brings a few [edge-case breaking changes](#breaking-changes) that may affect specific projects, and which you can [test today](#validating-changes-in-your-project).

**In a future React Native release, Package Exports will be enabled by default**. In a chicken-and-egg situation, React Native apps were previously a holdout for some packages to migrate to `"exports"` — or used our `"react-native"` root field escape hatch. Supporting these features in Metro will allow the ecosystem to move forward.

### Enabling Package Exports (beta)

Package Exports can be enabled in your app's [**metro.config.js**](https://github.com/facebook/react-native/blob/0.72-stable/packages/react-native/template/metro.config.js) file via the [`resolver.unstable_enablePackageExports`](https://metrobundler.dev/docs/configuration/#unstable_enablepackageexports-experimental) option.

```js
const config = {
  // ...
  resolver: {
    unstable_enablePackageExports: true,
  },
};
```

Metro exposes two further resolver options which configure how conditional exports behave:

- [`unstable_conditionNames`](https://metrobundler.dev/docs/configuration/#unstable_conditionnames-experimental) — The set of [condition names](https://nodejs.org/docs/latest-v19.x/api/packages.html#community-conditions-definitions) to assert when resolving conditional exports. By default, we match `['require', 'import', 'react-native']`.
- [`unstable_conditionsByPlatform`](https://metrobundler.dev/docs/configuration/#unstable_conditionsbyplatform-experimental) — The additional condition names to assert when resolving for a given platform target. By default, this matches `'browser'` when the platform is `'web'`.

:::tip
**Remember to use the React Native [Jest preset](https://github.com/facebook/react-native/blob/main/template/jest.config.js#L2)!** Jest includes support for Package Exports by default. In tests, you can override which `customExportConditions` are resolved using the [`testEnvironmentOptions`](https://jestjs.io/docs/configuration#testenvironmentoptions-object) option.

**If you are using TypeScript**, resolution behaviour can be matched by setting [`moduleResolution: 'bundler'`](https://www.typescriptlang.org/tsconfig#moduleResolution) and [`resolvePackageJsonImports: false`](https://www.typescriptlang.org/tsconfig#resolvePackageJsonExports) within your project's `tsconfig.json`.
:::

#### Validating changes in your project

For existing projects, we recommend that early adopters follow these steps to see if resolution changes occur after enabling `unstable_enablePackageExports`. This is a one-time process. It's likely that there will be no changes at all, but we'd like developers to opt in with certainty.

<details>
<summary>💡 Validating changes in your project</summary>

:::note
If you are not using Yarn, substitute `yarn` for `npx` (or the relevant tool used in your project).
:::

1. Get all resolved dependencies (before changes):

   ```sh
   # Replace index.js with your entry file if needed, such as App.js
   yarn metro get-dependencies index.js --platform android --output before.txt
   ```

   - **Expo CLI**: Run `npx expo customize metro.config.js` if your project doesn't have a `metro.config.js` file yet.
   - For full coverage, substitute `--platform android` for the other platforms in use by your app (e.g. `ios`, `web`).

2. Enable `resolver.unstable_enablePackageExports` in `metro.config.js`.
3. Get all resolved dependencies (after changes):

   ```sh
   yarn metro get-dependencies index.js --platform android --output after.txt
   ```

4. Compare!

   ```sh
   diff before.txt after.txt
   ```

</details>

### Breaking changes

We decided on an implementation of Package Exports in Metro that is spec-compliant (necessitating some breaking changes), but backwards compatible otherwise (helping apps with existing imports to migrate gradually).

The key breaking change is that when `"exports"` is provided by a package, it will be consulted first (before any other `package.json` fields) — and a matched subpath target will be used directly.

- Metro will not expand [`sourceExts`](https://metrobundler.dev/docs/configuration/#sourceexts) against the import specifier.
- Metro will not resolve [platform-specific extensions](/docs/platform-specific-code) against the target file.

For more details, please see all [**breaking changes**](https://metrobundler.dev/docs/package-exports#summary-of-breaking-changes) in the Metro docs.

### 套件封裝採取寬鬆策略

當 Metro 遇到未列在 `"exports"` 中的子路徑時，**會退回傳統解析方式**。此相容性設計旨在降低既有 React Native 專案中已允許的導入操作所產生的摩擦。

Metro 不會拋出錯誤，而是記錄警告訊息。

```sh
warn: You have imported the module "foo/private/fn.js" which is not listed in
the "exports" of "foo". Consider updating your call site or asking the package
maintainer(s) to expose this API.
```

:::note
我們計劃在未來實作嚴格的套件封裝模式，以符合 Node 的預設行為。因此**建議所有開發者處理這些警告**（若被使用者回報）。
:::

## 給套件維護者的預覽說明

:::info
根據我們的[推出計劃](#the-future-stable-exports-enabled-by-default)，套件匯出功能將在下一版 React Native (0.73) 為多數專案預設啟用。

**我們目前沒有計劃在短期內移除對 `"main"` 欄位及其他現有套件解析功能的支援。**
:::

套件匯出功能讓您能限制套件內部存取，並為函式庫提供更可預測的能力來鎖定 React Native 與 React Native for Web。

### 若您目前已使用 `"exports"`

若您的套件同時使用 `"exports"` 與現有的 `"react-native"` 根欄位，請注意上述使用者的[重大變更](#breaking-changes)。對在 Metro 啟用此功能的使用者而言，模組解析時會優先考量 `"exports"`。

實際上，我們預期使用者的主要變動將來自套件封裝的 `"exports"` 規範——透過警告機制來強制執行應用程式中無法存取的子路徑。

### 遷移至 `"exports"`

**為套件新增 `"exports"` 欄位完全屬於選配**。現有套件解析功能對未使用 `"exports"` 的套件會維持相同行為——我們沒有計劃移除此行為。

我們認為 `"exports"` 的新功能為 React Native 套件維護者提供了極具吸引力的功能組合。

- **強化套件 API 管控**：現在是審視套件模組 API 的好時機，可透過匯出的子路徑別名正式定義。這能防止使用者存取內部 API，減少錯誤發生面。
- **條件式匯出**：若您的套件目標是 React Native for Web（即 `"react-native"` 和 `"browser"`），現在套件能控制這些條件的解析順序（參見下個標題）。

若決定導入 `"exports"`，**建議將其視為重大變更**。我們在 Metro 文件中準備了[**遷移指南**](https://metrobundler.dev/docs/package-exports#migration-guide-for-package-maintainers)，內容包含如何取代平台特定擴充等功能。

:::note
**請勿依賴 Metro 實作的寬鬆行為**。雖然 Metro 保持向後相容，但套件應遵循規格文件記載的 `"exports"` 標準及其他工具的嚴格實作方式。
:::

### 新增的 `"react-native"` 條件

我們已將 `"react-native"` 納入社群條件（用於條件式匯出）。這代表 React Native 框架與其他已知運行環境如 `"node"` 和 `"deno"` 並列（參見 [RFC](https://github.com/nodejs/node/pull/45367)）。

> [社群條件定義——**`"react-native"`**](https://nodejs.org/docs/latest-v19.x/api/packages.html#community-conditions-definitions)
>
> _將由 React Native 框架匹配（全平台適用）。若目標為 React Native for Web，應在此條件前指定 "browser"。_

這取代了先前的 `"react-native"` 根欄位。過去解析優先順序由專案決定的方式，[在使用 React Native for Web 時會產生歧義](https://github.com/expo/router/issues/37#issuecomment-1275925758)。透過 `"exports"`，_套件能明確定義條件進入點的解析順序_——消除此歧義。

```json
  "exports": {
    "browser": "./dist/index-browser.js",
    "react-native": "./dist/index-react-native.js",
    "default": "./dist/index.js"
  }
```

:::note
我們選擇不引入 `"android"` 和 `"ios"` 條件，原因是現有平台選擇方法已廣泛使用，且跨框架實現此行為的複雜性過高。請改用 [`Platform.select()`](/docs/platform#select) API。
:::

## 未來規劃：預設啟用的穩定版 `"exports"`

在下一個 React Native 版本中，我們計劃移除此功能的 `unstable_` 前綴（在完成預定的效能優化與錯誤修復後），並將預設啟用 Package Exports 解析功能。

當 `"exports"` 全面啟用後，我們便能推動 React Native 生態系向前發展——例如更新核心套件以更明確區分公開模組與內部模組。

![Package Exports 支援的推出時程圖](/blog/assets/package-exports-rollout.png)

## 致謝

感謝 React Native 社群成員對 RFC 提供的反饋：[@SimenB](https://github.com/SimenB)、[@tido64](https://github.com/tido64)、[@byCedric](https://github.com/byCedric)、[@thymikee](https://github.com/thymikee)。

特別感謝 Meta 的 [@motiz88](https://github.com/motiz88) 和 [@robhogan](https://github.com/robhogan) 對此功能開發的支持。