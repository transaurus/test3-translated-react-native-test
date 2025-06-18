---
title: 'Moving Towards a Stable JavaScript API (New Changes in 0.80)'
authors: [huntie, iwoplaza, jpiasecki, coado]
tags: [announcement]
date: 2025-06-12T16:00
---

在 React Native 0.80 中，我們將對 JavaScript API 進行兩項重大變更：棄用深度導入(deprecation of deep imports)與推出全新的嚴格 TypeScript API(Strict TypeScript API)。這些變更是為了精確定義我們的 API 架構，並為使用者與框架提供可靠的型別安全機制。

**重點摘要：**

- **棄用深度導入**：自 0.80 版本起，我們將對從 `react-native` 套件進行深度導入的行為發出棄用警告。
- **可選用的嚴格 TypeScript API**：我們正轉向基於原始碼生成的 TypeScript 型別定義，並在 TypeScript 下建立新的公共 API 基準。這些變更將帶來更強健且面向未來的型別準確性，屬於一次性重大變更。可透過專案 `tsconfig.json` 中的 `compilerOptions` [選擇啟用](/blog/2025/06/12/moving-towards-a-stable-javascript-api#strict-typescript-api)。
- 我們將與社群持續合作，確保這些變更能適用於所有開發者，之後才會在未來的 React Native 版本中預設啟用嚴格 TypeScript API。

<!--truncate-->

## 變更內容與原因

我們正著手改進並穩定 React Native 的公共 JavaScript API——也就是當你導入 `'react-native'` 時所獲得的介面。

長期以來，我們對此僅採取近似做法。React Native 原始碼使用 [Flow](https://flow.org/) 撰寫，但開源社群早已轉向 TypeScript，這正是公共 API 被使用與驗證相容性的方式。我們的型別定義過去（充滿熱情地）由[社群貢獻](https://www.npmjs.com/package/@types/react-native)，後來被合併至程式碼庫中。然而這些型別依賴人工維護且缺乏自動化工具，導致正確性缺口。

此外，我們的公共 JS API 在模組邊界定義上相當模糊——例如應用程式碼可存取內部 `'react-native/Libraries/'` 的深度導入路徑，但這些內部實作經常會隨更新而變動。

在 0.80 版本中，我們透過棄用深度導入與推出可選用的新型別生成基準（稱為**嚴格 TypeScript API**）來解決這些問題。這將為未來提供穩定的 React Native API 奠定基礎。

## 棄用從 `react-native` 進行深度導入

本次 API 變更的主要內容是棄用深度導入的使用（參見 [RFC](https://github.com/react-native-community/discussions-and-proposals/pull/894)），並在 ESLint 與 JS 控制台發出警告。請將值與型別的深度導入更新為 `react-native` 的根導入路徑。

```js title=""
// Before - import from subpath
import {Alert} from 'react-native/Libraries/Alert/Alert';

// After - import from `react-native`
import {Alert} from 'react-native';
```

此變更能將 JavaScript API 的總表面積縮減為固定可控制的匯出集合，以便在未來版本中實現穩定化。我們預計在 0.82 版本移除這些導入路徑。

:::info[API 意見回饋]

部分 API 未從根路徑匯出，棄用深度導入後將無法使用。我們設有**[開放意見討論串](https://github.com/react-native-community/discussions-and-proposals/discussions/893)**，將與社群共同完善公共 API 的匯出定義。歡迎提供意見！

:::

**選擇退出**

請注意，我們計劃在未來版本中從 React Native API 移除深度導入功能，建議將這些導入更新為根路徑形式。

<details>
<summary>**Opting out of warnings**</summary>

#### ESLint

Disable the `no-deep-imports` rule using `overrides`.

<!-- prettier-ignore -->
```js title=".eslintrc.js"
  overrides: [
    {
      files: ['*.js', '*.jsx', '*.ts', '*.tsx'],
      rules: {
        '@react-native/no-deep-imports': 0,
      },
    },
  ]
```

#### Console warnings

Pass the `disableDeepImportWarnings` option to `@react-native/babel-preset`.

<!-- prettier-ignore -->
```js title="babel.config.js"
module.exports = {
  presets: [
    ['module:@react-native/babel-preset', {disableDeepImportWarnings: true}]
  ],
};
```

Restart your app with `--reset-cache` to clear the Metro cache.

```sh title=""
npx @react-native-community/cli start --reset-cache
```

</details>

<details>
<summary>**Opting out of warnings (Expo)**</summary>

#### ESLint

Disable the `no-deep-imports` rule using `overrides`.

<!-- prettier-ignore -->
```js title=".eslintrc.js"
overrides: [
  {
    files: ['*.js', '*.jsx', '*.ts', '*.tsx'],
    rules: {
      '@react-native/no-deep-imports': 0,
    },
  },
];
```

#### Console warnings

Pass the `disableDeepImportWarnings` option to `babel-preset-expo`.

<!-- prettier-ignore -->
```js title="babel.config.js"
module.exports = function (api) {
  api.cache(true);
  return {
    presets: [['babel-preset-expo', {disableDeepImportWarnings: true}]],
  };
};
```

Restart your app with `--clear` to clear the Metro cache.

```sh name=""
npx expo start --clear
```

</details>

## 嚴格 TypeScript API（可選用）

嚴格 TypeScript API 是 `react-native` 套件中的新型別集合，可透過 `tsconfig.json` 選擇啟用。這些新型別將與現有 TS 型別並存，您可自行決定遷移時機。

新型別具有以下特性：

1. **直接從原始碼生成** — 提升覆蓋率與正確性，您將獲得更高的相容性保證。
2. **限制於 `react-native` 的 index 檔案** — 更嚴格定義公共 API，意味著當我們修改內部檔案時不會破壞 API。

當社群準備就緒時，嚴格 TypeScript API 將成為未來的預設 API — 與深層導入的移除同步進行。這意味著**現在開始啟用**是個好主意，您將為 React Native 未來穩定的 JS API 做好準備。

```json title="tsconfig.json"
{
  "extends": "@react-native/typescript-config",
  "compilerOptions": {
    ...
    "customConditions": ["react-native-strict-api"]
  }
}
```

:::note[底層機制]

這將指示 TypeScript 從我們新的 [`types_generated/`](https://www.npmjs.com/package/react-native?activeTab=code) 目錄解析 `react-native` 類型，而非先前手動維護的 [`types/`](https://www.npmjs.com/package/react-native?activeTab=code) 目錄。無需重啟 TypeScript 或您的編輯器。

:::

### 重大變更：禁止深層導入

如上所述，在嚴格 TypeScript API 下，類型現在僅能從主 `'react-native'` 導入路徑解析，根據前述的棄用政策強制執行[套件封裝](/blog/2023/06/21/package-exports-support)。

```tsx
// Before - import from subpath
import {Alert} from 'react-native/Libraries/Alert/Alert';

// After - MUST import from `react-native`
import {Alert} from 'react-native';
```

:::tip[關鍵優勢]

我們已將公共 API 範圍限定於 React Native 的 `index.js` 檔案輸出，該檔案經過仔細維護。這意味著程式碼庫中其他檔案的變更將不再成為破壞性變更。

:::

### 重大變更：部分類型名稱/結構已變更

類型現在從原始碼生成，而非手動維護。在此過程中：

- 我們已修正社群貢獻類型中累積的差異 — 同時增加了原始碼的類型覆蓋率。
- 我們刻意更新了部分類型名稱與結構，以簡化或減少歧義。

:::tip[關鍵優勢]

由於類型現在直接從 React Native 原始碼生成，您可以確信類型檢查器**始終準確**反映特定版本的 `react-native`。

:::

#### 範例：更嚴格的導出符號

`Linking` API 現在是單一 `interface`，而非兩個導出項。此模式適用於多個其他 API（[參閱文件](/docs/strict-typescript-api)）。

```tsx
// Before
import {Linking, LinkingStatic} from 'react-native';

function foo(linking: LinkingStatic) {}
foo(Linking);

// After
import {Linking} from 'react-native';

function foo(linking: Linking) {}
foo(Linking);
```

#### 範例：修正/更完整的類型

先前手動定義的類型可能遺漏某些部分。透過 Flow → TypeScript 的自動生成，這些漏洞已不復存在（且在原始碼層級受益於 Flow 對多平台程式碼的額外類型驗證）。

```tsx
import {Dimensions} from 'react-native';

// Before - Type error
// After - number | undefined
const {densityDpi} = Dimensions.get();
```

### 其他重大變更

請參閱我們文件中的[專用指南](/docs/strict-typescript-api)，其中詳述所有類型變更與遷移方法。

## 推行時程

我們理解任何 React Native 的破壞性變更都需要開發者花時間更新應用程式。

#### 現階段 — 選擇性啟用 (0.80)

`"react-native-strict-api"` 選項已在 0.80 版本穩定釋出。

- 這是一次性遷移。我們預計應用程式與函式庫會在未來幾個版本中依自身步調啟用。
- 無論採用哪種模式，您的應用程式在運行時都不會受影響 — 此變更僅涉及 TypeScript 分析。
- **同時**，我們將透過[專用反饋討論串](https://github.com/react-native-community/discussions-and-proposals/discussions/893)收集關於缺失 API 的意見。

:::tip[推薦]

嚴格 TypeScript API 將成為我們未來的預設 API。

如果您有時間，現在就值得在 `tsconfig.json` 中測試這個選擇加入功能，以確保您的應用程式或函式庫能適應未來。這將立即評估在嚴格 API 下，您的應用程式中是否有任何類型錯誤被引入。**可能完全沒有(!)** — 這樣的話，您就可以放心使用了。

:::

#### 未來 — 預設啟用嚴格 TypeScript API

未來，我們將要求所有程式碼庫使用我們的嚴格 API，並移除舊有的類型定義。

這個時間表將基於社群的回饋。至少在接下來的兩個 React Native 版本中，嚴格 API 將保持為選擇加入的功能。

## 常見問題

<details>
<summary>
**I'm using subpath imports today. What should I do?**
</summary>

Please migrate to the root `'react-native'` import path.

- Subpath imports (e.g. `'react-native/Libraries/Alert/Alert'`) are becoming private APIs. Without preventing access to implementation files inside React Native, we can’t offer a stable JavaScript API.
- We want our deprecation warnings to motivate community feedback, which can be raised via our [centralized discussion thread](https://github.com/react-native-community/discussions-and-proposals/discussions/893), if you believe we are not exposing code paths that are crucial for your app. Where justified, we may promote APIs to the index export.

</details>

<details>
<summary>
**I'm a library maintainer. How does this change impact me?**
</summary>

Both apps and libraries can opt in at their own pace, since `tsconfig.json` will only affect the immediate codebase.

- Typically, `node_modules` is excluded from validation by the TypeScript server in a React Native project. Therefore, your package's exported type definitions are the source of truth.

**💡 We want feedback!** As with changed subpath imports, if you encounter any integration issues with the Strict API, please let us know [on GitHub](https://github.com/react-native-community/discussions-and-proposals/discussions/893).

</details>

<details>
<summary>
**Does this guarantee a final API for React Native yet?**
</summary>

Sadly, not yet. In 0.80, we've made a tooling investment so that React Native's existing JS API baseline can be accurately consumed via TypeScript — enabling future stable changes. We're formalizing the existing API you know and love.

In the future, we will take action to finalise the APIs we currently offer in core — across each language surface. API changes will be communicated via RFCs/announcements, and typically a deprecation cycle.

</details>

<details>
<summary>
**Why isn't React Native written in TypeScript?**
</summary>

React Native is core infrastructure at Meta. We test every merged change across our Family of Apps, before they hit general open source availability.

At this scale and sensitivity, correctness matters. The bottom line is that Flow offers us greater performance and greater strictness than TypeScript, including specific [multi-platform support for React Native](https://flow.org/en/docs/react/multiplatform/).

</details>

## 致謝

這些變更得以實現，要感謝 [Iwo Plaza](https://x.com/iwoplaza)、[Jakub Piasecki](https://x.com/breskin67)、[Dawid Małecki](https://github.com/coado)、[Alex Hunt](https://x.com/huntie) 和 [Riccardo Cipolleschi](https://x.com/CipolleschiR)。

同時也感謝 [Pieter Vanderwerff](https://github.com/pieterv)、[Rubén Norte](https://github.com/rubennorte) 和 [Rob Hogan](https://x.com/robjhogan) 的額外協助與意見。

:::note[了解更多]

<div style={{display: 'flex'}}>
<p style={{flex: 1, marginRight: 40, marginBottom: 0}}>
<strong style={{ display: 'block', marginTop: 8, marginBottom: 8 }}>觀看演講！</strong>
<span style={{ display: 'block', marginBottom: 8 }}>我們在 <strong>App.js 2025</strong> 上深入探討了嚴格 TypeScript API 背後的動機和工作。</span>
**[在 YouTube 上觀看](https://www.youtube.com/live/UTaJlqhTk2g?si=SDRmj80kss7hXuGG&t=6520)**
</p>
<img
  src="/blog/assets/0.80-js-stable-api-appjs.jpg"
  style={{ flexShrink: 0, width: '200px', aspectRatio: '16/9', objectFit: 'contain' }}
  alt="App.js 2025 演講"
/>
</div>

:::