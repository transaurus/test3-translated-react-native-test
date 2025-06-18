---
title: Using TypeScript with React Native
author: Ash Furrow
authorTitle: Software Engineer at Artsy
authorURL: 'https://github.com/ashfurrow'
authorImageURL: 'https://avatars2.githubusercontent.com/u/498212?s=460&v=4'
authorTwitter: ashfurrow
tags: [engineering]
---

JavaScript！我們都愛它。但有些人也熱愛[型別系統](https://en.wikipedia.org/wiki/Data_type)。幸運的是，我們有方法可以為JavaScript添加更嚴格的型別檢查。我最喜歡的是[TypeScript](https://www.typescriptlang.org)，但React Native原生支援[Flow](https://flow.org)。選擇哪種取決於個人偏好，它們各自有不同的方式為JavaScript添加型別魔法。今天，我們將探討如何在React Native應用中使用TypeScript。

本文以微軟的[TypeScript-React-Native-Starter](https://github.com/Microsoft/TypeScript-React-Native-Starter)倉庫作為指南。

**更新**：自本文撰寫以來，流程已變得更簡單。您只需執行以下命令即可替代本文描述的所有設定步驟：

```sh
npx react-native init MyAwesomeProject --template react-native-template-typescript
```

但Babel的TypeScript支援存在一些限制，上文連結的部落格有詳細說明。本文所述步驟仍然有效，且Artsy在生產環境仍使用[react-native-typescript-transformer](https://github.com/ds300/react-native-typescript-transformer)，但最快開始使用React Native與TypeScript的方式是執行上述命令。您之後仍可隨時切換方案。

無論如何，祝您使用愉快！原始部落格文章繼續如下。

## 必要條件

由於您可能在不同的平台開發，目標裝置類型也各異，基礎設定可能較複雜。您應先確保能在不使用TypeScript的情況下運行普通React Native應用。請依照[React Native官方網站指示](/docs/getting-started)開始。當您成功部署到裝置或模擬器後，即可開始建立TypeScript React Native應用。

您還需要安裝[Node.js](https://nodejs.org/en/)、[npm](https://www.npmjs.com)和[Yarn](https://yarnpkg.com/lang/en)。

## 初始化

當您嘗試建立普通React Native專案後，即可開始添加TypeScript。讓我們開始吧。

```sh
react-native init MyAwesomeProject
cd MyAwesomeProject
```

## 添加TypeScript

下一步是將TypeScript加入您的專案。以下指令將：

- 為專案添加TypeScript
- 添加[React Native TypeScript轉換器](https://github.com/ds300/react-native-typescript-transformer)
- 初始化空的TypeScript設定檔（我們稍後將進行配置）
- 添加空的React Native TypeScript轉換器設定檔（我們稍後將進行配置）
- 添加React和React Native的[型別定義](https://github.com/DefinitelyTyped/DefinitelyTyped)

好的，讓我們執行這些指令。

```sh
yarn add --dev typescript
yarn add --dev react-native-typescript-transformer
yarn tsc --init --pretty --jsx react
touch rn-cli.config.js
yarn add --dev @types/react @types/react-native
```

`tsconfig.json`檔案包含TypeScript編譯器的所有設定。上述指令建立的預設值大多沒問題，但請打開檔案並取消註解以下行：

```js
{
  /* Search the config file for the following line and uncomment it. */
  // "allowSyntheticDefaultImports": true,  /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
}
```

`rn-cli.config.js`包含React Native TypeScript轉換器的設定。打開它並添加以下內容：

```js
module.exports = {
  getTransformModulePath() {
    return require.resolve('react-native-typescript-transformer');
  },
  getSourceExts() {
    return ['ts', 'tsx'];
  },
};
```

## 遷移至TypeScript

將生成的`App.js`和`__tests_/App.js`檔案重新命名為`App.tsx`。`index.js`需保留`.js`副檔名。所有新檔案應使用`.tsx`副檔名（若檔案不含JSX則使用`.ts`）。

若您現在嘗試運行應用，可能會遇到`object prototype may only be an object or null`錯誤。這是因為在同一行同時導入React的默認導出和命名導出所致。請打開`App.tsx`並修改檔案頂部的導入語句：

```diff
-import React, { Component } from 'react';
+import React from 'react'
+import { Component } from 'react';
```

這部分與 Babel 和 TypeScript 如何與 CommonJS 模組交互的差異有關。未來兩者將穩定採用相同的行為模式。

此時，您應該能夠運行 React Native 應用程式。

## 添加 TypeScript 測試基礎設施

React Native 內建 [Jest](https://github.com/facebook/jest)，因此要在 TypeScript 環境中測試 React Native 應用程式，我們需將 [ts-jest](https://www.npmjs.com/package/ts-jest) 加入 `devDependencies`。

```sh
yarn add --dev ts-jest
```

接著開啟 `package.json` 並將 `jest` 欄位替換為以下內容：

```js
{
  "jest": {
    "preset": "react-native",
    "moduleFileExtensions": [
      "ts",
      "tsx",
      "js"
    ],
    "transform": {
      "^.+\\.(js)$": "<rootDir>/node_modules/babel-jest",
      "\\.(ts|tsx)$": "<rootDir>/node_modules/ts-jest/preprocessor.js"
    },
    "testRegex": "(/__tests__/.*|\\.(test|spec))\\.(ts|tsx|js)$",
    "testPathIgnorePatterns": [
      "\\.snap$",
      "<rootDir>/node_modules/"
    ],
    "cacheDirectory": ".jest/cache"
  }
}
```

這會配置 Jest 使用 `ts-jest` 來處理 `.ts` 和 `.tsx` 檔案。

## 安裝依賴項的型別宣告

為了獲得最佳的 TypeScript 開發體驗，我們需要讓型別檢查器理解依賴套件的結構與 API。有些函式庫會在發佈套件時包含 `.d.ts` 檔案（型別宣告/型別定義檔案），這些檔案能描述底層 JavaScript 的結構。對於其他函式庫，我們需明確安裝 `@types/` npm 範圍內的對應套件。

例如，此處我們需要 Jest、React、React Native 和 React Test Renderer 的型別定義。

```ts
yarn add --dev @types/jest @types/react @types/react-native @types/react-test-renderer
```

我們將這些宣告檔案套件保存為 _開發_ 依賴項，因為這是個僅在開發階段使用這些依賴項的 React Native _應用程式_。如果我們要發佈函式庫到 NPM，可能需要將部分型別依賴項設為常規依賴項。

您可參閱[此處了解更多關於取得 `.d.ts` 檔案的資訊](https://www.typescriptlang.org/docs/handbook/declaration-files/consumption.html)。

## 忽略更多檔案

為了版本控制，您需要開始忽略 `.jest` 資料夾。若使用 git，只需在 `.gitignore` 檔案中新增條目。

```config
# Jest
#
.jest/
```

作為檢查點，建議將檔案提交至版本控制系統。

```sh
git init
git add .gitignore # import to do this first, to ignore our files
git add .
git commit -am "Initial commit."
```

## 添加元件

讓我們在應用程式中添加一個元件。現在建立一個 `Hello.tsx` 元件。這是個教學用元件，並非實際開發中會寫的內容，但能展示如何在 React Native 中使用 TypeScript 處理非簡單案例。

建立 `components` 目錄並加入以下範例。

```ts
// components/Hello.tsx
import React from 'react';
import {Button, StyleSheet, Text, View} from 'react-native';

export interface Props {
  name: string;
  enthusiasmLevel?: number;
}

interface State {
  enthusiasmLevel: number;
}

export class Hello extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);

    if ((props.enthusiasmLevel || 0) <= 0) {
      throw new Error(
        'You could be a little more enthusiastic. :D',
      );
    }

    this.state = {
      enthusiasmLevel: props.enthusiasmLevel || 1,
    };
  }

  onIncrement = () =>
    this.setState({
      enthusiasmLevel: this.state.enthusiasmLevel + 1,
    });
  onDecrement = () =>
    this.setState({
      enthusiasmLevel: this.state.enthusiasmLevel - 1,
    });
  getExclamationMarks = (numChars: number) =>
    Array(numChars + 1).join('!');

  render() {
    return (
      <View style={styles.root}>
        <Text style={styles.greeting}>
          Hello{' '}
          {this.props.name +
            this.getExclamationMarks(this.state.enthusiasmLevel)}
        </Text>

        <View style={styles.buttons}>
          <View style={styles.button}>
            <Button
              title="-"
              onPress={this.onDecrement}
              accessibilityLabel="decrement"
              color="red"
            />
          </View>

          <View style={styles.button}>
            <Button
              title="+"
              onPress={this.onIncrement}
              accessibilityLabel="increment"
              color="blue"
            />
          </View>
        </View>
      </View>
    );
  }
}

// styles
const styles = StyleSheet.create({
  root: {
    alignItems: 'center',
    alignSelf: 'center',
  },
  buttons: {
    flexDirection: 'row',
    minHeight: 70,
    alignItems: 'stretch',
    alignSelf: 'center',
    borderWidth: 5,
  },
  button: {
    flex: 1,
    paddingVertical: 0,
  },
  greeting: {
    color: '#999',
    fontWeight: 'bold',
  },
});
```

哇！內容很多，讓我們分解說明：

- 我們渲染的是 `View` 和 `Button` 等元件，而非 `div`、`span`、`h1` 等 HTML 元素。這些是能跨平台運作的原生元件。
- 樣式是透過 React Native 提供的 `StyleSheet.create` 函式定義。React 的樣式表讓我們能用 Flexbox 控制佈局，並使用類似 CSS 的結構進行樣式設定。

## 添加元件測試

現在我們有了元件，來試著測試它。

我們已安裝 Jest 作為測試執行器。接下來要為元件編寫快照測試，先添加快照測試所需的附加套件：

```sh
yarn add --dev react-addons-test-utils
```

現在於 `components` 目錄中建立 `__tests__` 資料夾，並為 `Hello.tsx` 添加測試：

```ts
// components/__tests__/Hello.tsx
import React from 'react';
import renderer from 'react-test-renderer';

import {Hello} from '@site/Hello';

it('renders correctly with defaults', () => {
  const button = renderer
    .create(<Hello name="World" enthusiasmLevel={1} />)
    .toJSON();
  expect(button).toMatchSnapshot();
});
```

The first time the test is run, it will create a snapshot of the rendered component and store it in the `components/__tests__/__snapshots__/Hello.tsx.snap` file. When you modify your component, you'll need to update the snapshots and review the update for inadvertent changes. You can read more about testing React Native components [here](https://facebook.github.io/jest/docs/en/tutorial-react-native.html).

## Next Steps

Check out the official [React tutorial](https://reactjs.org/tutorial/tutorial.html) and state-management library [Redux](https://redux.js.org). These resources can be helpful when writing React Native apps. Additionally, you may want to look at [ReactXP](https://microsoft.github.io/reactxp/), a component library written entirely in TypeScript that supports both React on the web as well as React Native.

Have fun in a more type-safe React Native development environment!