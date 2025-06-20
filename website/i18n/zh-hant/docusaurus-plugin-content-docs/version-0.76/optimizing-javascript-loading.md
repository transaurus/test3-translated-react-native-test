---
id: optimizing-javascript-loading
title: Optimizing JavaScript loading
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

Parsing and running JavaScript code requires memory and time. Because of this, as your app grows, it's often useful to delay loading code until it's needed for the first time. React Native comes with some standard optimizations that are on by default, and there are techniques you can adopt in your own code to help React load your app more efficiently. There are also some advanced automatic optimizations (with their own tradeoffs) that are suitable for very large apps.

## Recommended: Use Hermes

Hermes is the default engine for new React Native apps, and is highly optimized for efficient code loading. In release builds, JavaScript code is fully compiled to bytecode ahead of time. Bytecode is loaded to memory on-demand and does not need to be parsed like plain JavaScript does.

:::info
Read more about using Hermes in React Native [here](./hermes).
:::

## Recommended: Lazy-load large components

If a component with a lot of code/dependencies is not likely to be used when initially rendering your app, you can use React's [`lazy`](https://react.dev/reference/react/lazy) API to defer loading its code until it's rendered for the first time. Typically, you should consider lazy-loading screen-level components in your app, so that adding new screens to your app does not increase its startup time.

:::info
Read more about [lazy-loading components with Suspense
](https://react.dev/reference/react/lazy#suspense-for-code-splitting), including code examples, in React's documentation.
:::

### Tip: Avoid module side effects

Lazy-loading components can change the behavior of your app if your component modules (or their dependencies) have _side effects_, such as modifying global variables or subscribing to events outside of a component. Most modules in React apps should not have any side effects.

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

## Advanced: Call `require` inline

Sometimes you may want to defer loading some code until you use it for the first time, without using `lazy` or an asynchronous `import()`. You can do this by using the [`require()`](https://metrobundler.dev/docs/module-api/#require) function where you would otherwise use a static `import` at the top of the file.

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

## Advanced: Automatically inline `require` calls

If you use the React Native CLI to build your app, `require` calls (but not `import`s) will automatically be inlined for you, both in your code and inside any third-party packages (`node_modules`) you use.

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
Some React Native frameworks disable this behavior. In particular, in Expo projects, `require` calls are not inlined by default. You can enable this optimization by editing your project's Metro config and setting `inlineRequires: true` in [`getTransformOptions`](https://metrobundler.dev/docs/configuration#gettransformoptions).
:::

### Pitfalls of inline `require`s

Inlining `require` calls changes the order in which modules are evaluated, and can even cause some modules to _never_ be evaluated. This is usually safe to do automatically, because JavaScript modules are often written to be side-effect-free.

If one of your modules does have side effects - for example, if it initializes some logging mechanism, or patches a global API used by the rest of your code - then you might see unexpected behavior or even crashes. In those cases, you may want to exclude certain modules from this optimization, or disable it entirely.

To **disable all automatic inlining of `require` calls:**

Update your `metro.config.js` to set the `inlineRequires` transformer option to `false`:

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

To only **exclude certain modules from `require` inlining:**

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
**不支援 [使用 Hermes](#use-hermes) 的情況。** Hermes 位元組碼與 RAM 包格式不相容，且在所有使用場景中提供相同（或更好）的性能。
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