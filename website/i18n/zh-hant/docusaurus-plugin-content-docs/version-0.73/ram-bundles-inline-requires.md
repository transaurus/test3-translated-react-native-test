---
id: ram-bundles-inline-requires
title: RAM Bundles and Inline Requires
---

若您開發的是大型應用程式，可考慮採用隨機存取模組(RAM)套件格式並搭配行內載入(inline requires)。此方式特別適合具有大量畫面、但典型使用情境中未必會開啟這些畫面的應用程式。通常這類應用程式在啟動後相當時間內並不需要執行大量程式碼，例如包含複雜個人檔案頁面或較少使用功能的應用程式，但多數使用者操作僅涉及主畫面更新。透過RAM格式與行內載入（在實際需要時才載入），我們能優化套件的載入效率。

## JavaScript載入機制

在react-native執行JS程式碼前，必須先將程式碼載入記憶體並解析。若使用標準套件，當載入50MB的套件時，必須完整載入並解析全部50MB後才能執行任何程式碼。RAM套件的優化原理是：啟動時僅載入您實際需要的部分內容，後續再根據需求逐步載入套件的其他區段。

## 行內載入

行內載入會延遲模組或檔案的載入時機，直到真正需要該檔案時才執行。基礎範例如下：

```tsx title="VeryExpensive.tsx"
import React, {Component} from 'react';
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

即使未採用RAM格式，行內載入仍能改善啟動時間，因為VeryExpensive.js中的程式碼只會在首次被要求時才會執行。

## 啟用RAM格式

在iOS平台使用RAM格式會建立單一索引檔案，react native將逐個模組載入。Android平台預設會為每個模組建立獨立檔案，您可強制Android像iOS一樣建立單一檔案，但使用多個獨立檔案通常更具效能且記憶體消耗更低。

在Xcode中編輯「Bundle React Native code and images」建置階段以啟用RAM格式。於`../node_modules/react-native/scripts/react-native-xcode.sh`前添加`export BUNDLE_COMMAND="ram-bundle"`：

```
export BUNDLE_COMMAND="ram-bundle"
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

在Android平台需編輯`android/app/build.gradle`檔案啟用RAM格式。於`apply from: "../../node_modules/react-native/react.gradle"`前添加或修改`project.ext.react`區塊：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
]
```

若要在Android平台使用單一索引檔案，請添加以下設定：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
  extraPackagerArgs: ["--indexed-ram-bundle"]
]
```

:::info
若您使用[Hermes JS引擎](https://github.com/facebook/hermes)，則**不應**啟用RAM套件功能。Hermes載入位元組碼時，`mmap`會確保不會載入整個檔案。Hermes與RAM套件機制不相容，同時使用可能導致問題。
:::

## 預載與行內載入配置

使用RAM套件後，呼叫`require`會產生額外開銷。當遇到尚未載入的模組時，`require`需透過橋接器發送訊息。這對啟動階段影響最大，因為此時應用程式載入初始模組時可能會有大量require呼叫。所幸我們可配置部分模組進行預載，為此您需要實作某種形式的行內載入。

## 分析已載入模組

您可在根檔案(index.(ios|android).js)的初始導入語句後添加以下程式碼：

```js
const modules = require.getModules();
const moduleIds = Object.keys(modules);
const loadedModuleNames = moduleIds
  .filter(moduleId => modules[moduleId].isInitialized)
  .map(moduleId => modules[moduleId].verboseName);
const waitingModuleNames = moduleIds
  .filter(moduleId => !modules[moduleId].isInitialized)
  .map(moduleId => modules[moduleId].verboseName);

// make sure that the modules you expect to be waiting are actually waiting
console.log(
  'loaded:',
  loadedModuleNames.length,
  'waiting:',
  waitingModuleNames.length,
);

// grab this text blob, and put it in a file named packager/modulePaths.js
console.log(
  `module.exports = ${JSON.stringify(
    loadedModuleNames.sort(),
    null,
    2,
  )};`,
);
```

運行應用程式時，可透過控制台觀察已載入與待載入模組數量。建議檢視moduleNames內容確認是否有異常模組。請注意行內載入會在首次參照導入時觸發，您可能需要重構程式碼以確保啟動時僅載入必要模組。您可修改require上的Systrace物件來協助除錯有問題的require呼叫。

```js
require.Systrace.beginEvent = message => {
  if (message.includes(problematicModule)) {
    throw new Error();
  }
};
```

每個應用程式都不同，但合理的做法可能是只載入首屏所需的模組。當您確認無誤後，請將 loadedModuleNames 的輸出內容存入名為 `packager/modulePaths.js` 的檔案中。

## 更新 metro.config.js 設定檔

現在我們需要更新專案根目錄下的 `metro.config.js` 檔案，以使用新生成的 `modulePaths.js` 檔案：

<!-- prettier-ignore -->

```js
const {getDefaultConfig, mergeConfig} = require('@react-native/metro-config');
const fs = require('fs');
const path = require('path');
const modulePaths = require('./packager/modulePaths');

const config = {
  transformer: {
    getTransformOptions: () => {
      const moduleMap = {};
      modulePaths.forEach(modulePath => {
        if (fs.existsSync(modulePath)) {
          moduleMap[path.resolve(modulePath)] = true;
        }
      });
      return {
        preloadedModules: moduleMap,
        transform: {inlineRequires: {blockList: moduleMap}},
      };
    },
  },
};

module.exports = mergeConfig(getDefaultConfig(__dirname), config);
```

另請參閱 [**設定 Metro**](/docs/metro#configuring-metro)。

設定檔中的 `preloadedModules` 條目標記了哪些模組在建置 RAM bundle 時應被預先載入。當 bundle 載入時，這些模組會立即載入，甚至在執行任何 require 之前就已準備就緒。而 `blockList` 條目則標記了這些模組不應使用 inline require。由於它們已被預載，使用 inline require 並不會帶來效能優勢。實際上，生成的 JavaScript 程式碼每次參照這些導入時，反而會花費額外時間解析 inline require。

## 測試與效能改善評估

現在您應該已準備好使用 RAM 格式和 inline requires 來建置應用程式。請務必測量並比較啟用前後的啟動時間差異。