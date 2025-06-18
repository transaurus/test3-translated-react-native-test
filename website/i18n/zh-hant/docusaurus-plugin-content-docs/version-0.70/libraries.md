---
id: libraries
title: Using Libraries
author: Brent Vatne
authorURL: 'https://twitter.com/notbrent'
description: This guide introduces React Native developers to finding, installing, and using third-party libraries in their apps.
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

React Native provides a set of built-in [Core Components and APIs](./components-and-apis) ready to use in your app. You're not limited to the components and APIs bundled with React Native. React Native has a community of thousands of developers. If the Core Components and APIs don't have what you are looking for, you may be able to find and install a library from the community to add the functionality to your app.

## Selecting a Package Manager

React Native libraries are typically installed from the [npm registry](https://www.npmjs.com/) using a Node.js package manager such as [npm CLI](https://docs.npmjs.com/cli/npm) or [Yarn Classic](https://classic.yarnpkg.com/en/).

If you have Node.js installed on your computer then you already have the npm CLI installed. Some developers prefer to use Yarn Classic for slightly faster install times and additional advanced features like Workspaces. Both tools work great with React Native. We will assume npm for the rest of this guide for simplicity of explanation.

> 💡 The terms "library" and "package" are used interchangeably in the JavaScript community.

## Installing a Library

To install a library in your project, navigate to your project directory in your terminal and run the installation command. Let's try this with `react-native-webview`:

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install react-native-webview
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add react-native-webview
```

</TabItem>
</Tabs>

The library that we installed includes native code, and we need to link to our app before we use it.

## Linking Native Code on iOS

React Native uses CocoaPods to manage iOS project dependencies and most React Native libraries follow this same convention. If a library you are using does not, then please refer to their README for additional instruction. In most cases, the following instructions will apply.

Run `pod install` in our `ios` directory in order to link it to our native iOS project. A shortcut for doing this without switching to the `ios` directory is to run `npx pod-install`.

```bash
npx pod-install
```

Once this is complete, re-build the app binary to start using your new library:

```bash
npx react-native run-ios
```

## Linking Native Code on Android

React Native uses Gradle to manage Android project dependencies. After you install a library with native dependencies, you will need to re-build the app binary to use your new library:

```bash
npx react-native run-android
```

## Finding Libraries

[React Native Directory](https://reactnative.directory) is a searchable database of libraries built specifically for React Native. This is the first place to look for a library for your React Native app.

Many of the libraries you will find on the directory are from [React Native Community](https://github.com/react-native-community/) or [Expo](https://docs.expo.dev/versions/latest/).

Libraries built by the React Native Community are driven by volunteers and individuals at companies that depend on React Native. They often support iOS, tvOS, Android, Windows, but this varies across projects. Many of the libraries in this organization were once React Native Core Components and APIs.

Libraries built by Expo are all written in TypeScript and support iOS, Android, and `react-native-web` wherever possible.

After React Native Directory, the [npm registry](https://www.npmjs.com/) is the next best place if you can't find a library specifically for React Native on the directory. The npm registry is the definitive source for JavaScript libraries, but the libraries that it lists may not all be compatible with React Native. React Native is one of many JavaScript programming environments, including Node.js, web browsers, Electron, and more, and npm includes libraries that work for all of these environments.

## Determining Library Compatibility

### Does it work with React Native?

通常為其他平台專門構建的函式庫無法在 React Native 中運作。例如專為網頁開發的 `react-select` 以 `react-dom` 為目標，或是為 Node.js 設計並與電腦檔案系統互動的 `rimraf`。而像 `lodash` 這類僅使用 JavaScript 語言特性的函式庫則可在任何環境中運作。隨著經驗累積您會逐漸掌握判斷要領，在此之前最簡單的方式就是實際嘗試。若發現某套件無法在 React Native 中使用，可透過 `npm uninstall` 指令移除。

### 是否支援我的應用程式目標平台？

[React Native Directory](https://reactnative.directory) 提供依平台相容性篩選的功能，例如 iOS、Android、Web 和 Windows。若您想使用的函式庫未列於該目錄中，請參考該函式庫的 README 文件以獲取更多資訊。

### 是否相容於我的 React Native 版本？

函式庫的最新版本通常與最新版 React Native 相容。若您使用較舊版本，應查閱 README 文件以確認應安裝的函式庫版本。您可透過指令 `npm install <library-name>@<version-number>` 安裝特定版本，例如：`npm install @react-native-community/netinfo@^2.0.0`。