---
id: typescript
title: Using TypeScript
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

[TypeScript][ts] 是一種透過添加型別定義來擴展 JavaScript 的語言，類似於 [Flow][flow]。雖然 React Native 本身使用 Flow 構建，但它預設同時支援 TypeScript 和 Flow。

## 開始使用 TypeScript

若您要開始一個新專案，有以下幾種方式可以選擇。

您可以使用 [TypeScript 範本][ts-template]：

```shell
npx react-native init MyApp --template react-native-template-typescript
```

:::note

如果上述指令執行失敗，可能是因為您的系統全域安裝了舊版的 `react-native` 或 `react-native-cli`。解決方法是先解除安裝 CLI：

- `npm uninstall -g react-native-cli` 或 `yarn global remove react-native-cli`

然後再次執行 `npx` 指令。
或者，您也可以使用以下指令來啟動範本專案：

- `npx react-native --ignore-existing init MyApp --template react-native-template-typescript`

:::

您可以使用 [Expo][expo]，它維護了 TypeScript 範本，或在專案中添加 `.ts` 或 `.tsx` 檔案時會提示您自動安裝並配置 TypeScript：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npx create-expo-app --template
```

</TabItem>
<TabItem value="yarn">

```shell
yarn create expo-app --template
```

</TabItem>
</Tabs>

或者使用 [Ignite][ignite]，它也提供了 TypeScript 範本：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install -g ignite-cli
ignite new MyTSProject
```

</TabItem>
<TabItem value="yarn">

```shell
yarn global add ignite-cli
ignite new MyTSProject
```

</TabItem>
</Tabs>

## 在現有專案中添加 TypeScript

1. 將 TypeScript 以及 React Native 和 Jest 的型別定義添加到您的專案中。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install -D typescript @types/jest @types/react @types/react-native @types/react-test-renderer @tsconfig/react-native
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add -D typescript @types/jest @types/react @types/react-native @types/react-test-renderer @tsconfig/react-native
```

</TabItem>
</Tabs>

2. 添加 TypeScript 配置文件。在專案根目錄下創建一個 `tsconfig.json`：

```json
{
  "extends": "@tsconfig/react-native/tsconfig.json"
}
```

3. 創建一個 `jest.config.js` 文件來配置 Jest 以使用 TypeScript

```js
module.exports = {
  preset: 'react-native',
  moduleFileExtensions: [
    'ts',
    'tsx',
    'js',
    'jsx',
    'json',
    'node',
  ],
};
```

4. 將一個 JavaScript 文件重命名為 `*.tsx`

> 您應該保持 `./index.js` 入口文件不變，否則在打包生產版本時可能會遇到問題。

5. 執行 `yarn tsc` 來對新的 TypeScript 文件進行型別檢查。

## TypeScript 與 React Native 的協作原理

開箱即用，將文件轉換為 JavaScript 的功能與非 TypeScript 的 React Native 專案一樣，都是透過 [Babel 基礎設施][babel] 實現的。我們建議您僅使用 TypeScript 編譯器進行型別檢查。如果您要將現有的 TypeScript 代碼移植到 React Native，使用 Babel 而非 TypeScript 時會有 [一兩個注意事項][babel-7-caveats]。

## React Native + TypeScript 的實際樣貌

您可以透過 `React.Component<Props, State>` 為 React 組件的 [Props](props) 和 [State](state) 提供介面，這將在使用 JSX 處理該組件時提供型別檢查和編輯器自動完成功能。

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

## 實用建議資源

- [TypeScript 手冊][ts-handbook]
- [React 關於 TypeScript 的文檔][react-ts]
- [React + TypeScript 速查表][cheat] 提供了關於如何將 React 與 TypeScript 結合使用的詳細概述

## 在 TypeScript 中使用自訂路徑別名

要在 TypeScript 中使用自訂路徑別名，您需要配置 Babel 和 TypeScript 以支援這些別名。方法如下：

1. 編輯您的 `tsconfig.json` 以設置 [自訂路徑映射][path-map]。設定 `src` 根目錄下的任何內容無需前置路徑即可引用，並允許透過 `tests/File.tsx` 訪問任何測試文件：

```diff {2-7}
    "target": "esnext",
+     "baseUrl": ".",
+     "paths": {
+       "*": ["src/*"],
+       "tests": ["tests/*"],
+       "@components/*": ["src/components/*"],
+     },
    }
```

2. 將 [`babel-plugin-module-resolver`][bpmr] 作為開發依賴添加到你的專案中：

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

3. 最後，配置你的 `babel.config.js`（注意 `babel.config.js` 的語法與 `tsconfig.json` 不同）：

```diff {3-13}
{
  plugins: [
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

[tsplay]: https://www.typescriptlang.org/play?strictNullChecks=false&jsx=3#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wG4BYAKFEljgG8AhAVxhggDsAaOAZRgCeAGyS8AFkiQweAFSQAPaXABqwJAHcAvnGy4CRdDAC0HFDGAA3JGSpUFteILBI4ABRxgAznAC8DKnBwpiBIAFxwnjBQwBwA5hSUgQBGKJ5IAKIcMGLMnsCpIAAySFZCAPzhHMwgSUhQCZq2lGickXAAEkhCQhDhyIYAdABiAMIAPO4QXgB8vnAAFPRBKCE8KWmZ2bn5nkUlXXMADHCaAJS+s-QBcC0cbQDaSFk5eQXFpTxpMJsvO3ulAF05v0MANcqIYGYkPN1hlnts3vshKcEtdbm1OABJDhoIghLJzebnHyzL4-BG7d5deZPLavSlIuAAajgAEYUWjWvBOAARJC4pD4+B+IkXCJScn0-7U2m-RGlOCzY5lOCyinSoRwIxsuDhQ4cyicu7wWIS+RoIQrMzATgAWRQUAA1t4RVUQCMxA7PJVqrUoMTZm6PV7FXBlXAAIJQKAoATzIOeqDeFnsgYAKwgMXm+AAhPhzuF8DZDYk4EQYMwoBwFtdAmNVBoIoIRD56JFhEhPANbpCYnVNNNa4E4GM5Iomx3W+2RF3YkQpDFYgOh8OOl0evR8ARGqXV4F6MEkDu98P6KbvubLSBrXaHc6afCpVTkce92MAPRjmCD3fD+tqdQfxPOsWDYTgVz3cwYBbAAibEBVSFw1SlGCINXdA0E7PIkmAIRgEEQoUFqIQfBgmIBSFVDfxPTh3Cw1ssRxPFaVfYCbggHooFIpIhGYJAqLY98gOAsZQPYDg0OHKDYL5BC0lVR8-gEti4AwrDgBwvCCKIrpSIAE35ZismUtjaKITxPAYjhZKMmBWOAlpONIog9JMvchIgj8G0AocvIA4SDU0VFmi5CcZzmfgO3ESQYG7AwYGhK5Sx7FA+ygcIktXTARHkcJWS4IcUDw2IOExBKQG9OAYMwrI6hggrfzTXJzEwAQRk4BKsnCaraTq65NAawI5xixcMqHTAOt4YAAC8wjgAAmQ5BuHCasgAdSQYBYjEGBCySDi9P0ZbAmvKBYhiPKADZloGqgzmC+xoHgAzMBQZghHgTpuggBIgA