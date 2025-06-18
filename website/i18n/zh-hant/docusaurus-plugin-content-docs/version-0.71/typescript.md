---
id: typescript
title: Using TypeScript
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

[TypeScript][ts] 是一種透過添加類型定義來擴展 JavaScript 的語言。新的 React Native 專案預設以 TypeScript 為目標，但同時也支援 JavaScript 和 Flow。

## 開始使用 TypeScript

由 [React Native CLI](/docs/environment-setup#creating-a-new-application) 或熱門模板如 [Ignite][ignite] 建立的新專案將預設使用 TypeScript。

TypeScript 也可與 [Expo][expo] 一起使用，後者維護了 TypeScript 模板，或在專案中添加 `.ts` 或 `.tsx` 文件時會提示自動安裝並配置 TypeScript。

```shell
npx create-expo-app --template
```

## 在現有專案中添加 TypeScript

1. 將 TypeScript、類型定義和 ESLint 插件添加到您的專案中。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install -D @tsconfig/react-native @types/jest @types/react @types/react-test-renderer typescript
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add --dev @tsconfig/react-native @types/jest @types/react @types/react-test-renderer typescript
```

</TabItem>
</Tabs>

:::note
此命令會添加所有依賴項的最新版本。版本可能需要調整以匹配專案中現有的套件。您可以使用 [React Native Upgrade Helper](https://react-native-community.github.io) 等工具來查看 React Native 提供的版本。
:::

2. 添加 TypeScript 配置文件。在專案根目錄下創建一個 `tsconfig.json`：

```json
{
  "extends": "@tsconfig/react-native/tsconfig.json"
}
```

3. 將 JavaScript 文件重命名為 `*.tsx`

> 您應保持 `./index.js` 入口文件不變，否則在打包生產版本時可能會遇到問題。

4. 運行 `yarn tsc` 來對新的 TypeScript 文件進行類型檢查。

## 使用 JavaScript 而非 TypeScript

React Native 預設新應用使用 TypeScript，但仍可使用 JavaScript。副檔名為 `.jsx` 的文件將被視為 JavaScript 而非 TypeScript，且不會進行類型檢查。JavaScript 模組仍可被 TypeScript 模組導入，反之亦然。

## TypeScript 與 React Native 的運作方式

開箱即用，TypeScript 源碼在打包時會由 [Babel][babel] 轉換。我們建議僅使用 TypeScript 編譯器進行類型檢查。這是新創建應用中 `tsc` 的預設行為。如果您有現有的 TypeScript 代碼移植到 React Native，使用 Babel 而非 TypeScript 時有[一兩個注意事項][babel-7-caveats]。

## React Native + TypeScript 的樣貌

您可以透過 `React.Component<Props, State>` 為 React 組件的 [Props](props) 和 [State](state) 提供介面，這將在使用 JSX 處理該組件時提供類型檢查和編輯器自動完成功能。

```tsx title="components/Hello.tsx"
import React from 'react';
import {Button, StyleSheet, Text, View} from 'react-native';

export type Props = {
  name: string;
  baseEnthusiasmLevel?: number;
};

const Hello: React.FC<Props> = ({
  name,
  baseEnthusiasmLevel = 0,
}) => {
  const [enthusiasmLevel, setEnthusiasmLevel] = React.useState(
    baseEnthusiasmLevel,
  );

  const onIncrement = () =>
    setEnthusiasmLevel(enthusiasmLevel + 1);
  const onDecrement = () =>
    setEnthusiasmLevel(
      enthusiasmLevel > 0 ? enthusiasmLevel - 1 : 0,
    );

  const getExclamationMarks = (numChars: number) =>
    numChars > 0 ? Array(numChars + 1).join('!') : '';

  return (
    <View style={styles.container}>
      <Text style={styles.greeting}>
        Hello {name}
        {getExclamationMarks(enthusiasmLevel)}
      </Text>
      <View>
        <Button
          title="Increase enthusiasm"
          accessibilityLabel="increment"
          onPress={onIncrement}
          color="blue"
        />
        <Button
          title="Decrease enthusiasm"
          accessibilityLabel="decrement"
          onPress={onDecrement}
          color="red"
        />
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  greeting: {
    fontSize: 20,
    fontWeight: 'bold',
    margin: 16,
  },
});

export default Hello;
```

您可以在 [TypeScript 遊樂場][tsplay] 中進一步探索語法。

## 哪裡可以找到有用的建議

- [TypeScript 手冊][ts-handbook]
- [React 關於 TypeScript 的文檔][react-ts]
- [React + TypeScript 速查表][cheat] 提供了如何使用 React 與 TypeScript 的良好概述

## 在 TypeScript 中使用自定義路徑別名

要在 TypeScript 中使用自定義路徑別名，您需要設置路徑別名以同時適用於 Babel 和 TypeScript。方法如下：

1. 編輯您的 `tsconfig.json` 以設置[自定義路徑映射][path-map]。將 `src` 根目錄下的任何內容設置為無需前置路徑引用即可使用，並允許通過 `tests/File.tsx` 訪問任何測試文件：

```diff
{
-  "extends": "@tsconfig/react-native/tsconfig.json"
+  "extends": "@tsconfig/react-native/tsconfig.json",
+  "baseUrl": ".",
+  "paths": {
+    "*": ["src/*"],
+    "tests": ["tests/*"],
+    "@components/*": ["src/components/*"],
+  },
}
```

2. 將 [`babel-plugin-module-resolver`][bpmr] 作為開發套件添加到您的專案中：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install --save-dev babel-plugin-module-resolver
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add --dev babel-plugin-module-resolver
```

</TabItem>
</Tabs>

3. 最後，配置您的 `babel.config.js`（請注意，`babel.config.js` 的語法與 `tsconfig.json` 不同）：

```diff
{
  presets: ['module:metro-react-native-babel-preset'],
+  plugins: [
+    [
+       'module-resolver',
+       {
+         root: ['./src'],
+         extensions: ['.ios.js', '.android.js', '.js', '.ts', '.tsx', '.json'],
+         alias: {
+           tests: ['./tests/'],
+           "@components": "./src/components",
+         }
+       }
+    ]
  ]
}
```

[react-ts]: https://reactjs.org/docs/static-type-checking.html#typescript

[ts]: https://www.typescriptlang.org/

[flow]: https://flow.org

[ts-template]: https://github.com/react-native-community/react-native-template-typescript

[babel]: /docs/javascript-environment#javascript-syntax-transformers

[babel-7-caveats]: https://babeljs.io/docs/en/next/babel-plugin-transform-typescript

[cheat]: https://github.com/typescript-cheatsheets/react-typescript-cheatsheet#reacttypescript-cheatsheets

[ts-handbook]: https://www.typescriptlang.org/docs/handbook/intro.html

[path-map]: https://www.typescriptlang.org/docs/handbook/module-resolution.html#path-mapping

[bpmr]: https://github.com/tleunen/babel-plugin-module-resolver

[expo]: https://expo.io

[ignite]: https://github.com/infinitered/ignite

[tsplay]: https://www.typescriptlang.org/play?strictNullChecks=false&jsx=3#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wG4BYAKFEljgG8AhAVxhggDsAaOAZRgCeAGyS8AFkiQweAFSQAPaXABqwJAHcAvnGy4CRdDAC0HFDGAA3JGSpUFteILBI4ABRxgAznAC8DKnBwpiBIAFxwnjBQwBwA5hSUgQBGKJ5IAKIcMGLMnsCpIAAySFZCAPzhHMwgSUhQCZq2lGickXAAEkhCQhDhyIYAdABiAMIAPO4QXgB8vnAAFPRBKCE8KWmZ2bn5nkUlXXMADHCaAJS+s-QBcC0cbQDaSFk5eQXFpTxpMJsvO3ulAF05v0MANcqIYGYkPN1hlnts3vshKcEtdbm1OABJDhoIghLJzebnHyzL4-BG7d5deZPLavSlIuAAajgAEYUWjWvBOAARJC4pD4+B+IkXCJScn0-7U2m-RGlOCzY5lOCyinSoRwIxsuDhQ4cyicu7wWIS+RoIQrMzATgAWRQUAA1t4RVUQCMxA7PJVqrUoMTZm6PV7FXBlXAAIJQKAoATzIOeqDeFnsgYAKwgMXm+AAhPhzuF8DZDYk4EQYMwoBwFtdAmNVBoIoIRD56JFhEhPANbpCYnVNNNa4E4GM5Iomx3W+2RF3YkQpDFYgOh8OOl0evR8ARGqXV4F6MEkDu98P6KbvubLSBrXaHc6afCpVTkce92MAPRjmCD3fD+tqdQfxPOsWDYTgVz3cwYBbAAibEBVSFw1SlGCINXdA0E7PIkmAIRgEEQoUFqIQfBgmIBSFVDfxPTh3Cw1ssRxPFaVfYCbggHooFIpIhGYJAqLY98gOAsZQPYDg0OHKDYL5BC0lVR8-gEti4AwrDgBwvCCKIrpSIAE35ZismUtjaKITxPAYjhZKMmBWOAlpONIog9JMvchIgj8G0AocvIA4SDU0VFmi5CcZzmfgO3ESQYG7AwYGhK5Sx7FA+ygcIktXTARHkcJWS4IcUDw2IOExBKQG9OAYMwrI6hggrfzTXJzEwAQRk4BKsnCaraTq65NAawI5xixcMqHTAOt4YAAC8wjgAAmQ5BuHCasgAdSQYBYjEGBCySDi9PwZbAmvKBYhiPKADZloGqgzmC+xoHgAzMBQZghHgTpuggBIgA