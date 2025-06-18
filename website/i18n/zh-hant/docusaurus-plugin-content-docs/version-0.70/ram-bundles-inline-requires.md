---
id: ram-bundles-inline-requires
title: RAM Bundles and Inline Requires
---

若您的應用程式規模龐大，可考慮採用隨機存取模組 (RAM) 套件格式並搭配行內 require。此方式特別適用於具有大量畫面、但典型使用情境中未必會開啟的應用程式。通常對於啟動後短時間內不需載入大量程式碼的應用程式效益顯著，例如應用程式包含複雜的個人檔案畫面或較少使用的功能，但多數使用情境僅涉及主畫面更新。透過 RAM 格式與行內 require（實際使用時才載入），我們能優化套件載入效率。

## JavaScript 載入機制

在 react-native 執行 JS 程式碼前，必須先將程式碼載入記憶體並解析。傳統套件若載入 50MB 的套件，需完整載入並解析全部 50MB 後才能開始執行。RAM 套件的優化原理是：啟動時僅載入實際需要的部分，後續再依需求漸進式載入套件其他部分。

## 行內 Require

行內 require 會延遲模組或檔案的載入時機，直到實際需要時才執行。基礎範例如下：

```js title='VeryExpensive.js'
import React, {Component} from 'react';
import {Text} from 'react-native';
// ... import some very expensive modules

export default function VeryExpensive() {
  // ... lots and lots of rendering logic
  return <Text>Very Expensive Component</Text>;
}
```

```js title='Optimized.js'
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

即使未採用 RAM 格式，行內 require 仍能改善啟動時間，因為 VeryExpensive.js 內的程式碼只會在首次被 require 時執行。

## 啟用 RAM 格式

在 iOS 上使用 RAM 格式會建立單一索引檔案，react native 將逐個模組載入。Android 預設會為每個模組建立獨立檔案，您可強制 Android 建立類似 iOS 的單一檔案，但使用多個獨立檔案通常效能更佳且記憶體消耗更低。

在 Xcode 中編輯「Bundle React Native code and images」建置階段以啟用 RAM 格式。於 `../node_modules/react-native/scripts/react-native-xcode.sh` 前新增 `export BUNDLE_COMMAND="ram-bundle"`：

```
export BUNDLE_COMMAND="ram-bundle"
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

在 Android 上編輯 `android/app/build.gradle` 檔案以啟用 RAM 格式。於 `apply from: "../../node_modules/react-native/react.gradle"` 前新增或修改 `project.ext.react` 區塊：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
]
```

若要在 Android 上使用單一索引檔案，請加入以下設定：

```
project.ext.react = [
  bundleCommand: "ram-bundle",
  extraPackagerArgs: ["--indexed-ram-bundle"]
]
```

:::info
若使用 [Hermes JS 引擎](https://github.com/facebook/hermes)，則**不應**啟用 RAM 套件功能。Hermes 載入位元組碼時，`mmap` 會確保不會載入整個檔案。Hermes 與 RAM 套件併用可能導致問題，因兩者機制不相容。
:::

## 預載與行內 Require 設定

啟用 RAM 套件後，呼叫 `require` 會產生額外開銷。當遇到尚未載入的模組時，`require` 需透過橋接器傳送訊息。這對啟動階段影響最大，因初始模組載入時會集中大量 require 呼叫。我們可透過設定預載部分模組來改善，此時需實作某種形式的行內 require。

## 分析已載入模組

在根檔案 (index.(ios|android).js) 的初始導入後加入以下程式碼：

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

執行應用程式時，可透過控制台觀察已載入與待載入模組數量。建議檢查 moduleNames 內容是否有異常模組。請注意行內 require 會在首次參照導入時觸發，可能需要重構程式碼以確保啟動時僅載入必要模組。您可修改 require 上的 Systrace 物件協助除錯異常 require。

```js
require.Systrace.beginEvent = message => {
  if (message.includes(problematicModule)) {
    throw new Error();
  }
};
```

每個應用程式都不同，但合理的做法可能是僅載入首屏所需的模組。當您確認模組清單後，請將 loadedModuleNames 的輸出內容存入名為 `packager/modulePaths.js` 的檔案中。

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

設定檔中的 `preloadedModules` 參數用於指定建構 RAM bundle 時應預先載入的模組。當 bundle 載入時，這些模組會在所有 require 執行前立即載入。而 `blockList` 參數則標記這些模組不應使用 inline require——由於它們已被預載，使用 inline require 不會帶來效能提升，反而會讓生成的 JavaScript 在每次引用導入時額外耗費解析時間。

## 測試與效能改善評估

現在您應該已準備好使用 RAM 格式與 inline requires 來建置應用程式。請務必測量並比較啟用前後的啟動時間差異。