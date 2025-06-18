---
id: local-library-setup
title: Local libraries setup
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

本地函式庫是指包含視圖或模組且僅限於應用程式內部使用的程式庫，並未發佈至註冊表中。與傳統的視圖和模組設置不同，本地函式庫與應用程式的原生代碼是解耦的。

本地函式庫創建於 `android/` 和 `ios/` 資料夾之外，並利用自動連結（autolinking）功能與應用程式整合。包含本地函式庫的專案結構可能如下所示：

```plaintext
MyApp
├── node_modules
├── modules <-- folder for your local libraries
│ └── awesome-module <-- your local library
├── android
├── ios
├── src
├── index.js
└── package.json
```

由於本地函式庫的代碼存在於 `android/` 和 `ios/` 資料夾之外，未來升級 React Native 版本、複製到其他專案等操作會更加容易。

我們將使用 [create-react-native-library](https://callstack.github.io/react-native-builder-bob/create) 工具來創建本地函式庫。此工具包含所有必要的模板。

### 開始使用

在 React Native 應用程式的根目錄下執行以下命令：

```shell
npx create-react-native-library@latest awesome-module
```

其中 `awesome-module` 是新模組的名稱。完成提示後，專案根目錄下會新增一個名為 `modules` 的資料夾，其中包含新創建的模組。

### 連結

預設情況下，生成的函式庫會自動連結至專案：使用 Yarn 時採用 `link:` 協議，使用 npm 時則採用 `file:` 協議：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>

<TabItem value="npm">

```json
"dependencies": {
  "awesome-module": "file:./modules/awesome-module"
}
```

</TabItem>
<TabItem value="yarn">

```json
"dependencies": {
  "awesome-module": "link:./modules/awesome-module"
}
```

</TabItem>
</Tabs>

這會在 `node_modules` 下建立一個符號連結，使自動連結功能生效。

### 安裝依賴項

要連結模組，需先安裝依賴項：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>

<TabItem value="npm">

```shell
npm install
```

</TabItem>
<TabItem value="yarn">

```shell
yarn install
```

</TabItem>
</Tabs>

### 在應用程式中使用模組

要在應用程式中使用該模組，可透過其名稱直接導入：

```js
import {multiply} from 'awesome-module';
```