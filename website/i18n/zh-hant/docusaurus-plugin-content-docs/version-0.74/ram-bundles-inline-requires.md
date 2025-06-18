---
id: ram-bundles-inline-requires
title: RAM Bundles and Inline Requires
---

若您的應用程式規模較大，可考慮採用隨機存取模組(RAM)套件格式並使用內聯引入(inline requires)。這對於具有大量畫面、但在典型使用情境中可能永遠不會開啟的應用程式特別有用。通常這類優化適合啟動後長時間不需使用大量程式碼的應用程式，例如應用程式包含複雜的個人檔案畫面或較少使用的功能，但多數使用情境僅涉及主畫面更新。透過RAM格式和實際使用時才引入的功能(內聯引入)，我們能優化套件載入效率。

## JavaScript載入機制

在react-native執行JS程式碼前，必須先將程式碼載入記憶體並解析。標準套件若載入50mb的套件，必須完整載入並解析全部50mb後才能執行。RAM套件的優化原理是：啟動時僅載入實際需要的部分，後續再根據需求漸進式載入其他部分。

## 內聯引入

內聯引入會延遲模組或檔案的引入時機，直到實際需要該檔案時才執行。基本範例如下：

```tsx title="VeryExpensive.tsx"
import React, {Component} from 'react';
import {Text} from 'react-native';
// ... import some very expensive modules

// You may want to log at the file level to verify when this is happening
console.log('VeryExpensive component loaded');

export default class VeryExpensive extends Component {
  // lots and lots of code
  render() {
    return <Text>Very Expensive Component</Text>;
  }
}
```

```tsx title="Optimized.tsx"
import React, {Component} from 'react';
import {TouchableOpacity, View, Text} from 'react-native';

let VeryExpensive = null;

export default class Optimized extends Component {
  state = {needsExpensive: false};

  didPress = () => {
    if (VeryExpensive == null) {
      VeryExpensive = require('./VeryExpensive').default;
    }

    this.setState(() => ({
      needsExpensive: true,
    }));
  };

  render() {
    return (
      <View style={{marginTop: 20}}>
        <TouchableOpacity onPress={this.didPress}>
          <Text>Load</Text>
        </TouchableOpacity>
        {this.state.needsExpensive ? <VeryExpensive /> : null}
      </View>
    );
  }
}
```

即使不使用RAM格式，內聯引入也能改善啟動時間，因為VeryExpensive.js中的程式碼只會在首次被引入時執行。

## 啟用RAM格式

在iOS平台，RAM格式會建立單一索引檔案，react native將逐個模組載入。Android平台預設會為每個模組建立獨立檔案。您可強制Android像iOS一樣建立單一檔案，但使用多個檔案通常更具效能且記憶體消耗更少。

在Xcode中編輯「Bundle React Native code and images」建置階段以啟用RAM格式。於`../node_modules/react-native/scripts/react-native-xcode.sh`前添加`export BUNDLE_COMMAND="ram-bundle"`：

```
export BUNDLE_COMMAND="ram-bundle"
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

在Android平台編輯`android/app/build.gradle`檔案啟用RAM格式。於`apply from: "../../node_modules/react-native/react.gradle"`前添加或修改`project.ext.react`區塊：

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
若使用[Hermes JS引擎](https://github.com/facebook/hermes)，**不應**啟用RAM套件功能。Hermes載入位元碼時，`mmap`會確保不會載入整個檔案。Hermes與RAM套件機制不相容，同時使用可能導致問題。
:::

## 預載與內聯引入配置

使用RAM套件後，呼叫`require`會產生額外開銷。當遇到尚未載入的模組時，`require`需透過橋接器發送訊息。這對啟動階段影響最大，因為此時會集中執行最多數量的require呼叫。所幸我們可配置部分模組進行預載，為此您需要實作某種形式的內聯引入。

## 分析已載入模組

在根檔案(index.(ios|android).js)的初始引入後可添加以下程式碼：

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

運行應用程式時，可透過控制台查看已載入/待載入模組數量。建議檢查moduleNames確認是否有異常模組。請注意內聯引入會在首次引用時觸發，可能需要重構程式碼以確保啟動時僅載入必要模組。您可修改require中的Systrace物件協助除錯有問題的引入。

```js
require.Systrace.beginEvent = message => {
  if (message.includes(problematicModule)) {
    throw new Error();
  }
};
```

每個應用程式的情況不同，但合理的做法是僅載入首屏所需的模組。當確認無誤後，將 `loadedModuleNames` 的輸出內容存入名為 `packager/modulePaths.js` 的檔案中。

## 更新 metro.config.js

現在我們需要更新專案根目錄下的 `metro.config.js`，以使用新生成的 `modulePaths.js` 檔案：

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

配置中的 `preloadedModules` 條目標記了哪些模組應在建置 RAM bundle 時預先載入。當 bundle 載入時，這些模組會立即載入，甚至在執行任何 require 之前。`blockList` 條目則標記了哪些模組不應使用內聯 require。由於它們已被預載，使用內聯 require 並不會帶來效能優勢。實際上，生成的 JavaScript 每次解析這些導入時，反而會花費額外時間處理內聯 require。

## 測試與衡量改進效果

現在您應該已準備好使用 RAM 格式和內聯 require 來建置應用程式。請務必測量並比較啟用前後的啟動時間。