---
id: ram-bundles-inline-requires
title: RAM Bundles and Inline Requires
---

若您的應用程式規模龐大，可考慮採用隨機存取模組(RAM)套件格式並搭配行內(inline) require。這對於具有大量畫面、但典型使用情境中未必會開啟的應用程式特別有用，尤其適合啟動後長時間不需載入大量程式碼的場景。例如應用程式包含複雜的個人檔案畫面或較少使用的功能，但多數使用階段僅涉及主畫面更新。透過RAM格式與行內require(在實際使用時才載入)，我們能優化套件載入效率。

## JavaScript載入機制

在react-native執行JS程式碼前，必須先將程式碼載入記憶體並解析。傳統套件若載入50mb的套件，必須完整載入並解析全部50mb後才能執行。RAM套件的優化原理是：啟動時僅載入實際需要的部分，隨需求逐步載入套件的其他區段。

## 行內Requires

行內require會延遲模組或檔案的載入時機，直到真正需要時才執行。基礎範例如下：

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

即使未採用RAM格式，行內require仍能改善啟動時間，因為VeryExpensive.js中的程式碼只會在首次被require時執行。

## 啟用RAM格式

在iOS平台，RAM格式會建立單一索引檔案，react native將逐個模組載入。Android預設會為每個模組建立獨立檔案，雖可強制改為iOS的單檔模式，但多檔模式通常效能更佳且記憶體消耗更低。

在Xcode中編輯「Bundle React Native code and images」建置階段以啟用RAM格式。於`../node_modules/react-native/scripts/react-native-xcode.sh`前添加`export BUNDLE_COMMAND="ram-bundle"`：

```
export BUNDLE_COMMAND="ram-bundle"
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

Android平台需編輯`android/app/build.gradle`檔案，在`apply from: "../../node_modules/react-native/react.gradle"`前新增或修改`project.ext.react`區塊：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
]
```

若要在Android使用單一索引檔案，請添加下列設定：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
  extraPackagerArgs: ["--indexed-ram-bundle"]
]
```

:::info
若使用[Hermes JS引擎](https://github.com/facebook/hermes)，**不應**啟用RAM套件功能。Hermes載入位元組碼時，`mmap`會確保不會載入整個檔案。Hermes與RAM套件機制不相容，混用可能導致問題。
:::

## 預載與行內Requires配置

使用RAM套件後，呼叫`require`會產生額外開銷——當遇到尚未載入的模組時，require需透過bridge傳送訊息。這對啟動階段影響最大，因初始模組載入時會密集呼叫require。我們可透過配置預載模組與行內require來優化。

## 模組載入分析

在根檔案(index.(ios|android).js)的初始import後添加：

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

運行應用程式時，可透過控制台觀察已載入與待載入模組數量。建議檢查moduleNames確認是否有非預期模組。注意行內require會在首次引用時觸發，可能需要重構程式碼以確保啟動時僅載入必要模組。可透過修改require上的Systrace物件協助除錯異常require。

```js
require.Systrace.beginEvent = message => {
  if (message.includes(problematicModule)) {
    throw new Error();
  }
};
```

每個應用程式的情況不同，但合理的做法是僅載入首屏所需的模組。當您確認模組清單後，請將 loadedModuleNames 的輸出內容存入名為 `packager/modulePaths.js` 的檔案中。

## 更新 metro.config.js 設定

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

設定中的 `preloadedModules` 條目用於標記建置 RAM bundle 時應預先載入的模組。當 bundle 載入時，這些模組會立即載入，甚至在執行任何 require 之前。而 `blockList` 條目則表示這些模組不應使用內聯 require。由於它們已被預載，使用內聯 require 不會帶來效能優勢。實際上生成的 JavaScript 程式碼每次解析這些導入時，反而會花費額外時間處理內聯 require。

## 測試與效能改善評估

現在您應該已準備好使用 RAM 格式和內聯 require 來建置應用程式。請務必測量並比較啟用前後的啟動時間差異。