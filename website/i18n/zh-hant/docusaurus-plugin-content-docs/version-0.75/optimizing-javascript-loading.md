---
id: optimizing-javascript-loading
title: Optimizing JavaScript loading
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

解析和執行 JavaScript 程式碼需要記憶體和時間。因此，隨著應用程式的增長，延遲載入程式碼直到首次需要時才執行往往很有幫助。React Native 預設啟用了一些標準優化功能，您也可以在自己的程式碼中採用特定技術來幫助 React 更有效率地載入應用程式。對於非常大型的應用程式，還有一些進階的自動優化方案（各有其取捨考量）。

## 建議：使用 Hermes 引擎

Hermes 是新版 React Native 應用的預設引擎，並針對程式碼載入效率進行了高度優化。在正式版建置中，JavaScript 程式碼會預先完整編譯為位元組碼。位元組碼是按需載入到記憶體的，無需像純 JavaScript 那樣進行解析。

:::info
詳細了解如何在 React Native 中使用 Hermes [請參閱此處](./hermes)。
:::

## 建議：延遲載入大型元件

若某個包含大量程式碼/依賴項的元件在應用初始渲染時不太可能被使用，您可以使用 React 的 [`lazy`](https://react.dev/reference/react/lazy) API 來延遲載入其程式碼，直到首次渲染時才執行。通常建議對應用中的畫面級元件進行延遲載入，這樣新增畫面時就不會增加應用啟動時間。

:::info
詳細了解如何[使用 Suspense 延遲載入元件](https://react.dev/reference/react/lazy#suspense-for-code-splitting)（包含程式碼範例），請參閱 React 官方文件。
:::

### 技巧：避免模組副作用

若您的元件模組（或其依賴項）具有_副作用_（例如修改全域變數或在元件外部訂閱事件），延遲載入元件可能會改變應用行為。React 應用中的大多數模組都不應有任何副作用。

```tsx title="SideEffects.tsx"
import Logger from './utils/Logger';

//  🚩 🚩 🚩 Side effect! This must be executed before React can even begin to
// render the SplashScreen component, and can unexpectedly break code elsewhere
// in your app if you later decide to lazy-load SplashScreen.
global.logger = new Logger();

export function SplashScreen() {
  // ...
}
```

## 進階：內聯呼叫 `require`

有時您可能希望延遲載入某些程式碼直到首次使用時才執行，但不想使用 `lazy` 或非同步的 `import()`。您可以在檔案頂部使用 [`require()`](https://metrobundler.dev/docs/module-api/#require) 函數來替代靜態的 `import` 語法實現此目的。

```tsx title="VeryExpensive.tsx"
import {Component} from 'react';
import {Text} from 'react-native';
// ... import some very expensive modules

export default function VeryExpensive() {
  // ... lots and lots of rendering logic
  return <Text>Very Expensive Component</Text>;
}
```

```tsx title="Optimized.tsx"
import {useCallback, useState} from 'react';
import {TouchableOpacity, View, Text} from 'react-native';
// Usually we would write a static import:
// import VeryExpensive from './VeryExpensive';

let VeryExpensive = null;

export default function Optimize() {
  const [needsExpensive, setNeedsExpensive] = useState(false);
  const didPress = useCallback(() => {
    if (VeryExpensive == null) {
      VeryExpensive = require('./VeryExpensive').default;
    }

    setNeedsExpensive(true);
  }, []);

  return (
    <View style={{marginTop: 20}}>
      <TouchableOpacity onPress={didPress}>
        <Text>Load</Text>
      </TouchableOpacity>
      {needsExpensive ? <VeryExpensive /> : null}
    </View>
  );
}
```

## 進階：自動內聯 `require` 呼叫

若使用 React Native CLI 建置應用，`require` 呼叫（不包括 `import`）將會自動為您內聯處理，此優化同時作用於您的程式碼和使用的第三方套件（`node_modules`）。

```tsx
import {useCallback, useState} from 'react';
import {TouchableOpacity, View, Text} from 'react-native';

// This top-level require call will be evaluated lazily as part of the component below.
const VeryExpensive = require('./VeryExpensive').default;

export default function Optimize() {
  const [needsExpensive, setNeedsExpensive] = useState(false);
  const didPress = useCallback(() => {
    setNeedsExpensive(true);
  }, []);

  return (
    <View style={{marginTop: 20}}>
      <TouchableOpacity onPress={didPress}>
        <Text>Load</Text>
      </TouchableOpacity>
      {needsExpensive ? <VeryExpensive /> : null}
    </View>
  );
}
```

:::info
部分 React Native 框架會禁用此行為。特別是在 Expo 專案中，`require` 呼叫預設不會被內聯。您可以透過編輯專案的 Metro 配置檔案，在 [`getTransformOptions`](https://metrobundler.dev/docs/configuration#gettransformoptions) 中設定 `inlineRequires: true` 來啟用此優化。
:::

### 內聯 `require` 的潛在問題

內聯處理 `require` 呼叫會改變模組的評估順序，甚至可能導致某些模組_永遠不被_評估。由於 JavaScript 模組通常被設計為無副作用的，自動執行此優化通常是安全的。

若您的某個模組確實具有副作用（例如初始化日誌機制或修補全域 API），則可能會出現意外行為甚至崩潰。在這些情況下，您可能需要將特定模組排除在此優化之外，或完全停用該功能。

若要**完全停用 `require` 呼叫的自動內聯處理**：

更新您的 `metro.config.js` 檔案，將 `inlineRequires` 轉換器選項設為 `false`：

```tsx title="metro.config.js"
module.exports = {
  transformer: {
    async getTransformOptions() {
      return {
        transform: {
          inlineRequires: false,
        },
      };
    },
  },
};
```

若僅需**將特定模組排除在 `require` 內聯處理之外**：

有兩個相關的轉換器選項：`inlineRequires.blockList` 和 `nonInlinedRequires`。請參閱程式碼片段以了解如何使用每個選項的範例。

```tsx title="metro.config.js"
module.exports = {
  transformer: {
    async getTransformOptions() {
      return {
        transform: {
          inlineRequires: {
            blockList: {
              // require() calls in `DoNotInlineHere.js` will not be inlined.
              [require.resolve('./src/DoNotInlineHere.js')]: true,

              // require() calls anywhere else will be inlined, unless they
              // match any entry nonInlinedRequires (see below).
            },
          },
          nonInlinedRequires: [
            // require('react') calls will not be inlined anywhere
            'react',
          ],
        },
      };
    },
  },
};
```

詳見 Metro 的 [`getTransformOptions` 文件](https://metrobundler.dev/docs/configuration#gettransformoptions)，以獲取更多關於設置和微調內聯 `require` 的詳細資訊。

## 進階：使用隨機存取模組包（非 Hermes）

:::info
**不支援 [使用 Hermes 時](#use-hermes)。** Hermes 字節碼與 RAM 包格式不相容，且在各種使用場景下提供相同（或更好）的性能。
:::

隨機存取模組包（亦稱 RAM 包）與上述技術協同工作，以限制需要解析並載入記憶體的 JavaScript 代碼量。每個模組儲存為獨立的字串（或檔案），僅在模組需要執行時才進行解析。

RAM 包可以物理上分割為多個檔案，或使用 _索引_ 格式，即在單一檔案中包含多個模組的查找表。

<Tabs groupId="platform" queryString defaultValue={constants.defaultPlatform} values={constants.platforms}>
<TabItem value="android">

On Android enable the RAM format by editing your `android/app/build.gradle` file. Before the line `apply from: "../../node_modules/react-native/react.gradle"` add or amend the `project.ext.react` block:

```
project.ext.react = [
  bundleCommand: "ram-bundle",
]
```

Use the following lines on Android if you want to use a single indexed file:

```
project.ext.react = [
  bundleCommand: "ram-bundle",
  extraPackagerArgs: ["--indexed-ram-bundle"]
]
```

</TabItem>
<TabItem value="ios">

On iOS, RAM bundles are always indexed ( = single file).

Enable the RAM format in Xcode by editing the build phase "Bundle React Native code and images". Before `../node_modules/react-native/scripts/react-native-xcode.sh` add `export BUNDLE_COMMAND="ram-bundle"`:

```
export BUNDLE_COMMAND="ram-bundle"
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

</TabItem>
</Tabs>

詳見 Metro 的 [`getTransformOptions` 文件](https://metrobundler.dev/docs/configuration#gettransformoptions)，以獲取更多關於設置和微調 RAM 包構建的詳細資訊。