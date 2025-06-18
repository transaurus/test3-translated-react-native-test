---
id: ram-bundles-inline-requires
title: RAM Bundles and Inline Requires
---

若您的應用程式規模龐大，可考慮採用隨機存取模組(RAM)套件格式並搭配行內(inline)引入。這對於包含大量畫面但典型使用情境中未必會開啟的應用程式特別有用，尤其適合啟動後長時間不需使用大量程式碼的場景。例如應用程式內含複雜的個人檔案頁面或較少使用的功能，但多數使用階段僅需造訪主畫面查看更新。透過RAM格式與行內引入這些功能模組(實際使用時才載入)，我們能有效優化套件載入效率。

## JavaScript載入機制

在react-native執行JS程式碼前，必須先將程式碼載入記憶體並解析。傳統套件若載入50MB的套件，必須完整載入並解析全部內容後才能執行。RAM套件的優化原理在於：啟動時僅載入實際需要的50MB部分內容，後續再按需漸進式載入其他模組。

## 行內引入

行內引入會延遲模組或檔案的引入時機，直到真正需要時才載入。基礎範例如下：

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

即使未採用RAM格式，行內引入仍能改善啟動時間，因為VeryExpensive.js內的程式碼僅在首次被引入時才會執行。

## 啟用RAM格式

在iOS平台使用RAM格式會建立單一索引檔案，react native將逐個模組載入。Android平台預設會為每個模組建立獨立檔案，雖可強制建立如iOS的單一檔案，但多檔案模式通常更具效能且記憶體消耗更低。

在Xcode中編輯「Bundle React Native code and images」建置階段以啟用RAM格式。於`../node_modules/react-native/scripts/react-native-xcode.sh`前加入`export BUNDLE_COMMAND="ram-bundle"`：

```
export BUNDLE_COMMAND="ram-bundle"
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

Android平台請編輯`android/app/build.gradle`檔案，在`apply from: "../../node_modules/react-native/react.gradle"`前新增或修改`project.ext.react`區塊：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
]
```

若需使用單一索引檔案，請在Android加入以下設定：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
  extraPackagerArgs: ["--indexed-ram-bundle"]
]
```

:::info
若使用[Hermes JS引擎](https://github.com/facebook/hermes)，則**不應**啟用RAM套件功能。Hermes載入位元碼時，`mmap`會確保不會載入整個檔案。Hermes與RAM套件機制不相容，混用可能導致問題。
:::

## 預載與行內引入配置

使用RAM套件後，呼叫`require`會產生額外開銷。當遇到尚未載入的模組時，`require`需透過橋接層傳送訊息。這對啟動階段影響最大，因初始模組載入時會密集呼叫require。我們可透過預載部分模組來優化，需配合實作某種形式的行內引入。

## 模組載入分析

在根檔案(index.(ios|android).js)的初始引入後加入以下程式碼：

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

運行應用程式時，可透過控制台觀察已載入與待載入模組數量。建議檢視moduleNames確認是否有異常模組。請注意行內引入會在首次參照時觸發，可能需要重構程式碼以確保啟動時僅載入必要模組。可透過修改require上的Systrace物件協助除錯有問題的引入。

```js
require.Systrace.beginEvent = message => {
  if (message.includes(problematicModule)) {
    throw new Error();
  }
};
```

每個應用程式都不同，但合理的做法是僅載入首屏所需的模組。當您確認無誤後，可將 loadedModuleNames 的輸出內容存入名為 `packager/modulePaths.js` 的檔案中。

## 更新 metro.config.js 設定檔

現在我們需要更新專案根目錄下的 `metro.config.js` 檔案，以使用新生成的 `modulePaths.js` 檔案：

```js
const modulePaths = require('./packager/modulePaths');
const resolve = require('path').resolve;
const fs = require('fs');

// Update the following line if the root folder of your app is somewhere else.
const ROOT_FOLDER = resolve(__dirname, '..');

const config = {
  transformer: {
    getTransformOptions: () => {
      const moduleMap = {};
      modulePaths.forEach(path => {
        if (fs.existsSync(path)) {
          moduleMap[resolve(path)] = true;
        }
      });
      return {
        preloadedModules: moduleMap,
        transform: {inlineRequires: {blockList: moduleMap}},
      };
    },
  },
  projectRoot: ROOT_FOLDER,
};

module.exports = config;
```

設定檔中的 `preloadedModules` 參數用於指定建構 RAM bundle 時應標記為預載的模組。當 bundle 載入時，這些模組會立即載入，甚至在任何 require 執行前就已完成。`blockList` 參數則標示這些模組不應使用內聯 require。由於它們已被預載，使用內聯 require 不會帶來效能優勢。實際上，生成的 JavaScript 程式碼每次參照這些導入時，反而會額外耗費時間解析內聯 require。

## 測試與衡量改進效果

現在您應該已準備好使用 RAM 格式和內聯 require 來建構應用程式。請務必測量並比較啟用前後的啟動時間差異。