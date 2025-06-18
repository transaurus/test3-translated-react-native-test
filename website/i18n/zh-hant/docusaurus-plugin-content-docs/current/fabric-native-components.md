---
id: fabric-native-components-introduction
title: Fabric Native Components Introduction
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import constants from '@site/core/TabsConstants';
import {FabricNativeComponentsAndroid,FabricNativeComponentsIOS} from './\_fabric-native-components';

# Native Components

If you want to build _new_ React Native Components that wrap around a [Host Component](https://reactnative.dev/architecture/glossary#host-view-tree-and-host-view) like a unique kind of [CheckBox](https://developer.android.com/reference/androidx/appcompat/widget/AppCompatCheckBox) on Android, or a [UIButton](https://developer.apple.com/documentation/uikit/uibutton?language=objc) on iOS, you should use a Fabric Native Component.

This guide will show you how to build Fabric Native Components, by implementing a web view component. The steps to doing this are:

1. Define a JavaScript specification using Flow or TypeScript.
2. Configure the dependencies management system to generate code from the provided spec and to be auto-linked.
3. Implement the Native code.
4. Use the Component in an App.

You're going to need a plain template generated application to use the component:

```bash
npx @react-native-community/cli@latest init Demo --install-pods false
```

## Creating a WebView Component

This guide will show you how to create a Web View component. We will be creating the component by using the Android's [`WebView`](https://developer.android.com/reference/android/webkit/WebView) component, and the iOS [`WKWebView`](https://developer.apple.com/documentation/webkit/wkwebview?language=objc) component.

Let's start by creating the folders structure to hold our component's code:

```bash
mkdir -p Demo/{specs,android/app/src/main/java/com/webview}
```

This gives you the following layout where you'll working:

```
Demo
├── android/app/src/main/java/com/webview
└── ios
└── specs
```

- The `android/app/src/main/java/com/webview` folder is the folder that will contain our Android code.
- The `ios` folder is the folder that will contain our iOS code.
- The `specs` folder is the folder that will contain the Codegen's specification file.

## 1. Define Specification for Codegen

Your specification must be defined in either [TypeScript](https://www.typescriptlang.org/) or [Flow](https://flow.org/) (see [Codegen](the-new-architecture/what-is-codegen) documentation for more details). This is used by Codegen to generate the C++, Objective-C++ and Java to connect your platform code to the JavaScript runtime that React runs in.

The specification file must be named `<MODULE_NAME>NativeComponent.{ts|js}` to work with Codegen. The suffix `NativeComponent` is not only a convention, it is actually used by Codegen to detect a spec file.

Use this specification for our WebView Component:

<Tabs groupId="language" queryString defaultValue={constants.defaultJavaScriptSpecLanguage} values={constants.javaScriptSpecLanguages}>
<TabItem value="typescript">

```typescript title="Demo/specs/WebViewNativeComponent.ts"
import type {
  CodegenTypes,
  HostComponent,
  ViewProps,
} from 'react-native';
import {codegenNativeComponent} from 'react-native';

type WebViewScriptLoadedEvent = {
  result: 'success' | 'error';
};

export interface NativeProps extends ViewProps {
  sourceURL?: string;
  onScriptLoaded?: CodegenTypes.BubblingEventHandler<WebViewScriptLoadedEvent> | null;
}

export default codegenNativeComponent<NativeProps>(
  'CustomWebView',
) as HostComponent<NativeProps>;
```

</TabItem>
<TabItem value="flow">

```ts title="Demo/RCTWebView/js/RCTWebViewNativeComponent.js":
// @flow strict-local

import type {CodegenTypes, HostComponent, ViewProps} from 'react-native';
import {codegenNativeComponent} from 'react-native';

type WebViewScriptLoadedEvent = $ReadOnly<{|
  result: "success" | "error",
|}>;

type NativeProps = $ReadOnly<{|
  ...ViewProps,
  sourceURL?: string;
  onScriptLoaded?: CodegenTypes.BubblingEventHandler<WebViewScriptLoadedEvent>?;
|}>;

export default (codegenNativeComponent<NativeProps>(
  'CustomWebView',
): HostComponent<NativeProps>);

```

</TabItem>
</Tabs>

This specification is composed of three main parts, excluding the imports:

- The `WebViewScriptLoadedEvent` is a supporting data type for the data the event needs to pass from native to JavaScript.
- The `NativeProps` is a definition of the props that we can set on the component.
- The `codegenNativeComponent` statement allows us to codegenerate the code for the custom component and that defines a name for the component used to match the native implementations.

As with Native Modules, you can have multiple specification files in the `specs/` directory. For more information about the types you can use, and the platform types these map to, see the [appendix](appendix.md#codegen-typings).

## 2. Configure Codegen to run

The specification is used by the React Native's Codegen tools to generate platform specific interfaces and boilerplate for us. To do this, Codegen needs to know where to find our specification and what to do with it. Update your `package.json` to include:

```json package.json
    "start": "react-native start",
    "test": "jest"
  },
  // highlight-start
  "codegenConfig": {
    "name": "AppSpec",
    "type": "components",
    "jsSrcsDir": "specs",
    "android": {
      "javaPackageName": "com.webview"
    },
    "ios": {
      "componentProvider": {
        "CustomWebView": "RCTWebView"
      }
    }
  },
  // highlight-end
  "dependencies": {
```

With everything wired up for Codegen, we need to prepare our native code to hook into our generated code.

Note that for iOS, we are declaratively mapping the name of the JS component that is exported by the spec (`CustomWebView`) with the iOS class that will implement the component natively.

## 2. 建置原生程式碼

現在是時候撰寫原生平台程式碼，當 React 需要渲染視圖時，平台能建立正確的原生視圖並將其顯示在畫面上。

您需要分別完成 Android 和 iOS 平台的實作。

:::note
本指南展示如何建立僅適用於新架構的原生元件。若需同時支援新架構與舊架構，請參閱我們的[向後相容性指南](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/backwards-compat.md)。

:::

<Tabs groupId="platforms" queryString defaultValue={constants.defaultPlatform}>
    <TabItem value="android" label="Android">
        <FabricNativeComponentsAndroid />
    </TabItem>
    <TabItem value="ios" label="iOS">
        <FabricNativeComponentsIOS />
    </TabItem>
</Tabs>

## 3. 使用您的原生元件

最後，您可以在應用程式中使用新元件。請更新生成的 `App.tsx` 檔案：

```javascript title="Demo/App.tsx"
import React from 'react';
import {Alert, StyleSheet, View} from 'react-native';
import WebView from './specs/WebViewNativeComponent';

function App(): React.JSX.Element {
  return (
    <View style={styles.container}>
      <WebView
        sourceURL="https://react.dev/"
        style={styles.webview}
        onScriptLoaded={() => {
          Alert.alert('Page Loaded');
        }}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    alignContent: 'center',
  },
  webview: {
    width: '100%',
    height: '100%',
  },
});

export default App;
```

這段程式碼建立了一個應用程式，使用我們創建的 `WebView` 元件來載入 `react.dev` 網站。

當網頁載入完成時，應用程式還會顯示一個提示框。

## 4. 使用 WebView 元件執行您的應用程式

<Tabs groupId="platforms" queryString defaultValue={constants.defaultPlatform}>
<TabItem value="android" label="Android">
```bash
yarn run android
```
</TabItem>
<TabItem value="ios" label="iOS">
```bash
yarn run ios
```
</TabItem>
</Tabs>

|                                      Android                                      |                                     iOS                                      |
| :-------------------------------------------------------------------------------: | :--------------------------------------------------------------------------: |
| <img style={{ "max-height": "600px" }} src="/docs/assets/webview-android.webp" /> | <img style={{"max-height": "600px" }} src="/docs/assets/webview-ios.webp" /> |