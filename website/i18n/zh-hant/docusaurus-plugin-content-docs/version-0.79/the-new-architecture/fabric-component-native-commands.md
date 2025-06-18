import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

# 在原生元件上呼叫原生函式

在[撰寫新原生元件的基礎指南](/docs/fabric-native-components-introduction)中，您已了解如何建立新元件、如何從JS端傳遞屬性至原生端，以及如何從原生端發送事件至JS端。

自訂元件也能以命令式呼叫原生程式碼實作的函式，以實現更進階的功能，例如以程式方式重新載入網頁。

本指南將透過新概念「原生指令(Native Commands)」教您如何達成此目的。

本指南以[原生元件](/docs/fabric-native-components-introduction)指南為基礎，假設您已熟悉該內容並了解[Codegen](/docs/next/the-new-architecture/what-is-codegen)。

## 1. 更新元件規格

第一步是更新元件規格以宣告`NativeCommand`。

<Tabs groupId="language" queryString defaultValue={constants.defaultJavaScriptSpecLanguage} values={constants.javaScriptSpecLanguages}>
<TabItem value="typescript">

Update the `WebViewNativeComponent.ts` as it follows:

```diff title="Demo/specs/WebViewNativeComponent.ts"
import type {HostComponent, ViewProps} from 'react-native';
import type {BubblingEventHandler} from 'react-native/Libraries/Types/CodegenTypes';
import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';
+import codegenNativeCommands from 'react-native/Libraries/Utilities/codegenNativeCommands';

type WebViewScriptLoadedEvent = {
  result: 'success' | 'error';
};

export interface NativeProps extends ViewProps {
  sourceURL?: string;
  onScriptLoaded?: BubblingEventHandler<WebViewScriptLoadedEvent> | null;
}

+interface NativeCommands {
+    reload: (viewRef: React.ElementRef<HostComponent<NativeProps>>) => void;
+}

+export const Commands: NativeCommands = codegenNativeCommands<NativeCommands>({
+    supportedCommands: ['reload'],
+});

export default codegenNativeComponent<NativeProps>(
  'CustomWebView',
) as HostComponent<NativeProps>;
```

</TabItem>
<TabItem value="flow">

Update the `WebViewNativeComponent.js` as it follows:

```diff title="Demo/specs/WebViewNativeComponent.js"
// @flow strict-local

import type {HostComponent, ViewProps} from 'react-native';
import type {BubblingEventHandler} from 'react-native/Libraries/Types/CodegenTypes';
import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';
+import codegenNativeCommands from 'react-native/Libraries/Utilities/codegenNativeCommands';

type WebViewScriptLoadedEvent = $ReadOnly<{|
  result: "success" | "error",
|}>;

type NativeProps = $ReadOnly<{|
  ...ViewProps,
  sourceURL?: string;
  onScriptLoaded?: BubblingEventHandler<WebViewScriptLoadedEvent>?;
|}>;

+interface NativeCommands {
+    reload: (viewRef: React.ElementRef<HostComponent<NativeProps>>) => void;
+}

+export const Commands: NativeCommands = codegenNativeCommands<NativeCommands>({
+    supportedCommands: ['reload'],
+});

export default (codegenNativeComponent<NativeProps>(
  'CustomWebView',
): HostComponent<NativeProps>);

```

</TabItem>
</Tabs>

這些變更需要您：

1. 從`react-native`匯入`codegenNativeCommands`函式。這會指示Codegen需為`NativeCommands`生成程式碼
2. 定義包含要在原生端呼叫方法的介面。所有原生指令的第一個參數必須是`React.ElementRef`類型
3. 匯出`Commands`變數，該變數是呼叫`codegenNativeCommands`並傳入支援指令清單的結果

:::warning
在TypeScript中，`React.ElementRef`已被棄用。正確類型應使用`React.ComponentRef`。但由於Codegen的錯誤，使用`ComponentRef`會導致應用程式崩潰。我們已準備好修復程式，但需發布新版本的React Native才能套用。
:::

## 2. 更新應用程式程式碼以使用新指令

現在您可以在應用程式中使用該指令。

<Tabs groupId="language" queryString defaultValue={constants.defaultJavaScriptSpecLanguage} values={constants.javaScriptSpecLanguages}>
<TabItem value="typescript">

Open the `App.tsx` file and modify it as it follows:

```diff title="App.tsx"
import React from 'react';
-import {Alert, StyleSheet, View} from 'react-native';
-import WebView from '@site/versioned_docs/version-0.79/specs/WebViewNativeComponent';
+import {Alert, StyleSheet, Pressable, Text, View} from 'react-native';
+import WebView, {Commands} from '@site/versioned_docs/version-0.79/specs/WebViewNativeComponent';

function App(): React.JSX.Element {
+    const webViewRef = React.useRef<React.ElementRef<typeof View> | null>(null);
+
+    const refresh = () => {
+        if (webViewRef.current) {
+            Commands.reload(webViewRef.current);
+        }
+    };

  return (
    <View style={styles.container}>
      <WebView
+       ref={webViewRef}
        sourceURL="https://react.dev/"
        style={styles.webview}
        onScriptLoaded={() => {
          Alert.alert('Page Loaded');
        }}
      />
+      <View style={styles.tabbar}>
+        <Pressable onPress={refresh} style={styles.button}>
+            {({pressed}) => (
+                !pressed ? <Text style={styles.buttonText}>Refresh</Text> : <Text style={styles.buttonTextPressed}>Refresh</Text>) }
+        </Pressable>
+      </View>
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
-    height: '100%',
+    height: '90%',
  },
+  tabbar: {
+    flex: 1,
+    backgroundColor: 'gray',
+    width: '100%',
+    alignItems: 'center',
+    alignContent: 'center',
+  },
+  button: {
+    margin: 10,
+  },
+  buttonText: {
+    fontSize: 20,
+    fontWeight: 'bold',
+    color: '#00D6FF',
+    width: '100%',
+  },
+  buttonTextPressed: {
+    fontSize: 20,
+    fontWeight: 'bold',
+    color: '#00D6FF77',
+    width: '100%',
+  },
});

export default App;
```

</TabItem>
<TabItem value="flow">

Open the `App.tsx` file and modify it as it follows:

```diff title="App.jsx"
import React from 'react';
-import {Alert, StyleSheet, View} from 'react-native';
-import WebView from '@site/versioned_docs/version-0.79/specs/WebViewNativeComponent';
+import {Alert, StyleSheet, Pressable, Text, View} from 'react-native';
+import WebView, {Commands} from '@site/versioned_docs/version-0.79/specs/WebViewNativeComponent';

function App(): React.JSX.Element {
+    const webViewRef = React.useRef<React.ElementRef<typeof View> | null>(null);
+
+    const refresh = () => {
+        if (webViewRef.current) {
+            Commands.reload(webViewRef.current);
+        }
+    };

  return (
    <View style={styles.container}>
      <WebView
+       ref={webViewRef}
        sourceURL="https://react.dev/"
        style={styles.webview}
        onScriptLoaded={() => {
          Alert.alert('Page Loaded');
        }}
      />
+      <View style={styles.tabbar}>
+        <Pressable onPress={refresh} style={styles.button}>
+            {({pressed}) => (
+                !pressed ? <Text style={styles.buttonText}>Refresh</Text> : <Text style={styles.buttonTextPressed}>Refresh</Text>) }
+        </Pressable>
+      </View>
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
-    height: '100%',
+    height: '90%',
  },
+  tabbar: {
+    flex: 1,
+    backgroundColor: 'gray',
+    width: '100%',
+    alignItems: 'center',
+    alignContent: 'center',
+  },
+  button: {
+    margin: 10,
+  },
+  buttonText: {
+    fontSize: 20,
+    fontWeight: 'bold',
+    color: '#00D6FF',
+    width: '100%',
+  },
+  buttonTextPressed: {
+    fontSize: 20,
+    fontWeight: 'bold',
+    color: '#00D6FF77',
+    width: '100%',
+  },
});

export default App;
```

</TabItem>
</Tabs>

相關變更如下：

1. 從規格檔案匯入`Commands`常數。該指令物件讓我們能呼叫原生端的方法
2. 使用`useRef`宣告`WebView`自訂原生元件的參照。需將此參照傳遞給原生指令
3. 實作`refresh`函式。此函式檢查WebView的參照是否為null，若非null則呼叫指令
4. 新增可按壓元件，讓使用者點擊按鈕時呼叫指令

其餘變更是常規的React變更，用於新增`Pressable`及美化視圖樣式。

## 3. 重新執行Codegen

更新規格且程式碼準備好使用指令後，接著需實作原生程式碼。但在編寫原生程式碼前，必須重新執行Codegen以生成原生程式碼所需的新類型。

<Tabs groupId="platforms" queryString defaultValue={constants.defaultPlatform}>
<TabItem value="android" label="Android">
Codegen is executed through the `generateCodegenArtifactsFromSchema` Gradle task:

```bash
cd android
./gradlew generateCodegenArtifactsFromSchema

BUILD SUCCESSFUL in 837ms
14 actionable tasks: 3 executed, 11 up-to-date
```

This is automatically run when you build your Android application.
</TabItem>
<TabItem value="ios" label="iOS">
Codegen is run as part of the script phases that's automatically added to the project generated by CocoaPods.

```bash
cd ios
bundle install
bundle exec pod install
```

The output will look like this:

```shell
...
Framework build type is static library
[Codegen] Adding script_phases to ReactCodegen.
[Codegen] Generating ./build/generated/ios/ReactCodegen.podspec.json
[Codegen] Analyzing /Users/me/src/TurboModuleExample/package.json
[Codegen] Searching for codegen-enabled libraries in the app.
[Codegen] Found TurboModuleExample
[Codegen] Searching for codegen-enabled libraries in the project dependencies.
[Codegen] Found react-native
...
```

</TabItem>
</Tabs>

## 4. 實作原生程式碼

現在是時候實作原生端變更，讓您的JS能直接呼叫原生視圖上的方法。

<Tabs groupId="platforms" queryString defaultValue={constants.defaultPlatform}>
<TabItem value="android" label="Android">

To let your view respond to the Native Command, you only have to modify the ReactWebViewManager.

If you try to build right now, the build will fail, because the current `ReactWebViewManager` does not implement the new `reload` method.
To fix the build error, let's modify the `ReactWebViewManager` to implement it.

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```diff title="ReactWebViewManager.java"

//...
  @ReactProp(name = "sourceUrl")
  @Override
  public void setSourceURL(ReactWebView view, String sourceURL) {
    if (sourceURL == null) {
      view.emitOnScriptLoaded(ReactWebView.OnScriptLoadedEventResult.error);
      return;
    }
    view.loadUrl(sourceURL, new HashMap<>());
  }

+  @Override
+  public void reload(ReactWebView view) {
+    view.reload();
+  }

  public static final String REACT_CLASS = "CustomWebView";
//...
```

</TabItem>
<TabItem value="kotlin">

```diff title="ReactWebViewManager.kt"
  @ReactProp(name = "sourceUrl")
  override fun setSourceURL(view: ReactWebView, sourceURL: String?) {
    if (sourceURL == null) {
      view.emitOnScriptLoaded(ReactWebView.OnScriptLoadedEventResult.error)
      return;
    }
    view.loadUrl(sourceURL, emptyMap())
  }

+  override fun reload(view: ReactWebView) {
+    view.reload()
+  }

  companion object {
    const val REACT_CLASS = "CustomWebView"
  }
```

</TabItem>
</Tabs>

In this case, it's enough to call directly the `view.reload()` method because our ReactWebView inherits from the Android's `WebView` and it has a reload method directly available. If you are implementing a custom function, that is not available in your custom view, you might also have to implement the required method in the Android's View that is managed by the React Native's `ViewManager`.

</TabItem>
<TabItem value="ios" label="iOS">

To let your view respond to the Native Command, we need to implement a couple of methods on iOS.

Let's open the `RCTWebView.mm` file and let's modify it as it follows:

```diff title="RCTWebView.mm"
  // Event emitter convenience method
  - (const CustomWebViewEventEmitter &)eventEmitter
  {
  return static_cast<const CustomWebViewEventEmitter &>(*_eventEmitter);
  }

+  - (void)handleCommand:(const NSString *)commandName args:(const NSArray *)args
+  {
+  RCTCustomWebViewHandleCommand(self, commandName, args);
+  }
+
+  - (void)reload
+  {
+  [_webView reloadFromOrigin];
+  }

  + (ComponentDescriptorProvider)componentDescriptorProvider
  {
  return concreteComponentDescriptorProvider<CustomWebViewComponentDescriptor>();
  }
```

To make your view respond to the Native Commands, you need to apply the following changes:

1. Add a `handleCommand:args` function. This function is invoked by the components infrastructure to handle the commands. The function implementation is similar for every component: you need to call an `RCT<componentNameInJS>HandleCommand` function that is generated by Codegen for you. The `RCT<componentNameInJS>HandleCommand` perform a bunch of validation, verifying that the command that we need to invoke is among the supported ones and that the parameters passed matches the one expected. If all the checks pass, the `RCT<componentNameInJS>HandleCommand` will then invoke the proper native method.
2. Implement the `reload` method. In this example, the `reload` method calls the `reloadFromOrigin` function of the WebKit's WebView.

</TabItem>
</Tabs>

## 5. 執行您的應用程式

最後，您可以用常規指令執行應用程式。當應用程式運行時，點擊重新整理按鈕即可看到頁面重新載入。

| <center>Android</center>                                                                         | <center>iOS</center>                                                                         |
| ------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------- |
| <center><img src="/docs/assets/native-commands-android.gif" height="75%" width="75%" /></center> | <center><img src="/docs/assets/native-commands-ios.gif" height="75%" width="75%" /></center> |