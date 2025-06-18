import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 iOS 應用的關鍵在於：

1. 建立正確的目錄結構
2. 安裝必要的 NPM 依賴項
3. 在 Podfile 配置中添加 React Native
4. 為首個 React Native 畫面編寫 TypeScript 程式碼
5. 使用 `RCTRootView` 將 React Native 與 iOS 程式碼整合
6. 執行打包器並查看應用運作情況來測試整合效果

## 使用社群範本

遵循本指南時，建議以 [React Native 社群範本](https://github.com/react-native-community/template/)作為參考。該範本包含一個**最簡 iOS 應用**，可幫助您理解如何將 React Native 整合至現有 iOS 應用。

## 必要條件

請先完成[開發環境設定指南](set-up-your-environment)並參閱[無框架使用 React Native](getting-started-without-a-framework)來配置 iOS 版 React Native 應用的開發環境。
本指南同時假設您已掌握 iOS 開發基礎知識，例如建立 `UIViewController` 和編輯 `Podfile` 檔案。

### 1. 設定目錄結構

為確保流程順暢，請先為整合式 React Native 專案建立新資料夾，然後**將現有 iOS 專案**移至 `/ios` 子資料夾。

## 2. 安裝 NPM 依賴項

進入根目錄並執行以下指令：

```shell
curl -O https://raw.githubusercontent.com/react-native-community/template/refs/heads/0.78-stable/template/package.json
```

這會將[社群範本中的 package.json 檔案](https://github.com/react-native-community/template/blob/0.78-stable/template/package.json)複製到您的專案。

接著執行以下指令安裝 NPM 套件：

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

安裝過程會建立新的 `node_modules` 資料夾，此資料夾儲存建置專案所需的所有 JavaScript 依賴項。

請將 `node_modules/` 加入您的 `.gitignore` 檔案（可參考[社群預設範本](https://github.com/react-native-community/template/blob/0.78-stable/template/_gitignore)）。

### 3. 安裝開發工具

### Xcode 命令列工具

安裝命令列工具。在 Xcode 選單中選擇 **Settings... (或 Preferences...)**，進入 Locations 面板後，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令列工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### CocoaPods

[CocoaPods](https://cocoapods.org) 是 iOS 和 macOS 開發的套件管理工具，我們用它將實際的 React Native 框架程式碼本地化加入您的專案。

建議透過 [Homebrew](https://brew.sh/) 安裝 CocoaPods：

```shell
brew install cocoapods
```

## 4. 將 React Native 加入應用

### 配置 CocoaPods

配置 CocoaPods 需要兩個檔案：

- **Gemfile**：定義所需的 Ruby 依賴項
- **Podfile**：定義依賴項的正確安裝方式

對於 **Gemfile**，請進入專案根目錄並執行此指令：

```sh
curl -O https://raw.githubusercontent.com/react-native-community/template/refs/heads/0.78-stable/template/Gemfile
```

這會從範本下載 Gemfile。

:::note
If you created your project with Xcode 16, you need to update the Gemfile as it follows:

```diff
-gem 'cocoapods', '>= 1.13', '!= 1.15.0', '!= 1.15.1'
+gem 'cocoapods', '1.16.2'
gem 'activesupport', '>= 6.1.7.5', '!= 7.1.0'
-gem 'xcodeproj', '< 1.26.0'
+gem 'xcodeproj', '1.27.0'
```

Xcode 16 generates a project in a slightly different ways from previous versions of Xcode, and you need the latest CocoaPods and Xcodeproj gems to make it work properly.
:::

Similarly, for the **Podfile**, go to the `ios` folder of your project and run

```sh
curl -O https://raw.githubusercontent.com/react-native-community/template/refs/heads/0.78-stable/template/ios/Podfile
```

Please use the Community Template as a reference point for the [Gemfile](https://github.com/react-native-community/template/blob/0.78-stable/template/Gemfile) and for the [Podfile](https://github.com/react-native-community/template/blob/0.78-stable/template/ios/Podfile).

:::note
Remember to change [this line](https://github.com/react-native-community/template/blob/0.78-stable/template/ios/Podfile#L17).
:::

Now, we need to run a couple of extra commands to install the Ruby gems and the Pods.
Navigate to the `ios` folder and run the following commands:

```sh
bundle install
bundle exec pod install
```

The first command will install the Ruby dependencies and the second command will actually integrate the React Native code in your application so that your iOS files can import the React Native headers.

## 5. Writing the TypeScript Code

Now we will actually modify the native iOS application to integrate React Native.

The first bit of code we will write is the actual React Native code for the new screen that will be integrated into our application.

### Create a `index.js` file

First, create an empty `index.js` file in the root of your React Native project.

`index.js` is the starting point for React Native applications, and it is always required. It can be a small file that `import`s other file that are part of your React Native component or application, or it can contain all the code that is needed for it.

Our `index.js` should look as follows (here the [Community template file as reference](https://github.com/react-native-community/template/blob/0.78-stable/template/index.js)):

```js
import {AppRegistry} from 'react-native';
import App from './App';

AppRegistry.registerComponent('HelloWorld', () => App);
```

### Create a `App.tsx` file

Let's create an `App.tsx` file. This is a [TypeScript](https://www.typescriptlang.org/) file that can have [JSX](<https://en.wikipedia.org/wiki/JSX_(JavaScript)>) expressions. It contains the root React Native component that we will integrate into our iOS application ([link](https://github.com/react-native-community/template/blob/0.78-stable/template/App.tsx)):

```tsx
import React from 'react';
import {
  SafeAreaView,
  ScrollView,
  StatusBar,
  StyleSheet,
  Text,
  useColorScheme,
  View,
} from 'react-native';

import {
  Colors,
  DebugInstructions,
  Header,
  ReloadInstructions,
} from 'react-native/Libraries/NewAppScreen';

function App(): React.JSX.Element {
  const isDarkMode = useColorScheme() === 'dark';

  const backgroundStyle = {
    backgroundColor: isDarkMode ? Colors.darker : Colors.lighter,
  };

  return (
    <SafeAreaView style={backgroundStyle}>
      <StatusBar
        barStyle={isDarkMode ? 'light-content' : 'dark-content'}
        backgroundColor={backgroundStyle.backgroundColor}
      />
      <ScrollView
        contentInsetAdjustmentBehavior="automatic"
        style={backgroundStyle}>
        <Header />
        <View
          style={{
            backgroundColor: isDarkMode
              ? Colors.black
              : Colors.white,
            padding: 24,
          }}>
          <Text style={styles.title}>Step One</Text>
          <Text>
            Edit <Text style={styles.bold}>App.tsx</Text> to
            change this screen and see your edits.
          </Text>
          <Text style={styles.title}>See your changes</Text>
          <ReloadInstructions />
          <Text style={styles.title}>Debug</Text>
          <DebugInstructions />
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  title: {
    fontSize: 24,
    fontWeight: '600',
  },
  bold: {
    fontWeight: '700',
  },
});

export default App;
```

Here the [Community template file as reference](https://github.com/react-native-community/template/blob/0.78-stable/template/App.tsx)

## 5. Integrating with your iOS code

We now need to add some native code in order to start the React Native runtime and tell it to render our React components.

### Requirements

React Native intialization is now unbound to any specific part of an iOS app.

React Native can be initialized using a class called `RCTReactNativeFactory`, that takes care of handling the React Native lifecycle for you.

初始化該類後，您可以選擇提供一個 `UIWindow` 物件來啟動 React Native 視圖，或是要求工廠生成一個 `UIView`，以便載入到任何 `UIViewController` 中。

在以下範例中，我們將創建一個能載入 React Native 視圖作為其 `view` 的 ViewController。

#### 創建 ReactViewController

從模板創建新檔案（<kbd>⌘</kbd>+<kbd>N</kbd>），並選擇 Cocoa Touch Class 模板。

請確保在「Subclass of」欄位中選擇 `UIViewController`。

<Tabs groupId="ios-language" queryString defaultValue={constants.defaultAppleLanguage} values={constants.appleLanguages}>
<TabItem value="objc">

Now open the `ReactViewController.m` file and apply the following changes

```diff title="ReactViewController.m"
#import "ReactViewController.h"
+#import <React/RCTBundleURLProvider.h>
+#import <RCTReactNativeFactory.h>
+#import <RCTDefaultReactNativeFactoryDelegate.h>
+#import <RCTAppDependencyProvider.h>


@interface ReactViewController ()

@end

+@interface ReactNativeFactoryDelegate: RCTDefaultReactNativeFactoryDelegate
+@end

-@implementation ReactViewController
+@implementation ReactViewController {
+  RCTReactNativeFactory *_factory;
+  id<RCTReactNativeFactoryDelegate> _factoryDelegate;
+}

 - (void)viewDidLoad {
     [super viewDidLoad];
     // Do any additional setup after loading the view.
+    _factoryDelegate = [ReactNativeFactoryDelegate new];
+    _factoryDelegate.dependencyProvider = [RCTAppDependencyProvider new];
+    _factory = [[RCTReactNativeFactory alloc] initWithDelegate:_factoryDelegate];
+    self.view = [_factory.rootViewFactory viewWithModuleName:@"HelloWorld"];
 }

@end

+@implementation ReactNativeFactoryDelegate
+
+- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge
+{
+  return [self bundleURL];
+}
+
+- (NSURL *)bundleURL
+{
+#if DEBUG
+  return [RCTBundleURLProvider.sharedSettings jsBundleURLForBundleRoot:@"index"];
+#else
+  return [NSBundle.mainBundle URLForResource:@"main" withExtension:@"jsbundle"];
+#endif
+}

@end

```

</TabItem>
<TabItem value="swift">

Now open the `ReactViewController.swift` file and apply the following changes

```diff title="ReactViewController.swift"
import UIKit
+import React
+import React_RCTAppDelegate
+import ReactAppDependencyProvider

class ReactViewController: UIViewController {
+  var reactNativeFactory: RCTReactNativeFactory?
+  var reactNativeFactoryDelegate: RCTReactNativeFactoryDelegate?

  override func viewDidLoad() {
    super.viewDidLoad()
+    reactNativeFactoryDelegate = ReactNativeDelegate()
+    reactNativeFactoryDelegate!.dependencyProvider = RCTAppDependencyProvider()
+    reactNativeFactory = RCTReactNativeFactory(delegate: reactNativeFactoryDelegate!)
+    view = reactNativeFactory!.rootViewFactory.view(withModuleName: "HelloWorld")

  }
}

+class ReactNativeDelegate: RCTDefaultReactNativeFactoryDelegate {
+    override func sourceURL(for bridge: RCTBridge) -> URL? {
+      self.bundleURL()
+    }
+
+    override func bundleURL() -> URL? {
+      #if DEBUG
+      RCTBundleURLProvider.sharedSettings().jsBundleURL(forBundleRoot: "index")
+      #else
+      Bundle.main.url(forResource: "main", withExtension: "jsbundle")
+      #endif
+    }
+
+}
```

</TabItem>
</Tabs>

#### 在 rootViewController 中呈現 React Native 視圖

最後，我們可以呈現 React Native 視圖。為此，我們需要一個新的 View Controller 來承載可載入 JS 內容的視圖。我們已經有初始的 `ViewController`，可以讓它呈現 `ReactViewController`。根據您的應用程式，有幾種方式可以實現。在此範例中，我們假設您有一個按鈕能以模態方式呈現 React Native。

<Tabs groupId="ios-language" queryString defaultValue={constants.defaultAppleLanguage} values={constants.appleLanguages}>
<TabItem value="objc">

```diff title="ViewController.m"
#import "ViewController.h"
+#import "ReactViewController.h"

@interface ViewController ()

@end

- @implementation ViewController
+@implementation ViewController {
+  ReactViewController *reactViewController;
+}

 - (void)viewDidLoad {
   [super viewDidLoad];
   // Do any additional setup after loading the view.
   self.view.backgroundColor = UIColor.systemBackgroundColor;
+  UIButton *button = [UIButton new];
+  [button setTitle:@"Open React Native" forState:UIControlStateNormal];
+  [button setTitleColor:UIColor.systemBlueColor forState:UIControlStateNormal];
+  [button setTitleColor:UIColor.blueColor forState:UIControlStateHighlighted];
+  [button addTarget:self action:@selector(presentReactNative) forControlEvents:UIControlEventTouchUpInside];
+  [self.view addSubview:button];

+  button.translatesAutoresizingMaskIntoConstraints = NO;
+  [NSLayoutConstraint activateConstraints:@[
+    [button.leadingAnchor constraintEqualToAnchor:self.view.leadingAnchor],
+    [button.trailingAnchor constraintEqualToAnchor:self.view.trailingAnchor],
+    [button.centerYAnchor constraintEqualToAnchor:self.view.centerYAnchor],
+    [button.centerXAnchor constraintEqualToAnchor:self.view.centerXAnchor],
+  ]];
 }

+- (void)presentReactNative
+{
+  if (reactViewController == NULL) {
+    reactViewController = [ReactViewController new];
+  }
+  [self presentViewController:reactViewController animated:YES];
+}

@end
```

</TabItem>
<TabItem value="swift">

```diff title="ViewController.swift"
import UIKit

class ViewController: UIViewController {

+  var reactViewController: ReactViewController?

  override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view.
    self.view.backgroundColor = .systemBackground

+    let button = UIButton()
+    button.setTitle("Open React Native", for: .normal)
+    button.setTitleColor(.systemBlue, for: .normal)
+    button.setTitleColor(.blue, for: .highlighted)
+    button.addAction(UIAction { [weak self] _ in
+      guard let self else { return }
+      if reactViewController == nil {
+       reactViewController = ReactViewController()
+      }
+      present(reactViewController!, animated: true)
+    }, for: .touchUpInside)
+    self.view.addSubview(button)
+
+    button.translatesAutoresizingMaskIntoConstraints = false
+    NSLayoutConstraint.activate([
+      button.leadingAnchor.constraint(equalTo: self.view.leadingAnchor),
+      button.trailingAnchor.constraint(equalTo: self.view.trailingAnchor),
+      button.centerXAnchor.constraint(equalTo: self.view.centerXAnchor),
+      button.centerYAnchor.constraint(equalTo: self.view.centerYAnchor),
+    ])
  }
}
```

</TabItem>
</Tabs>

請確保停用沙盒腳本功能。在 Xcode 中點擊您的應用程式，然後進入建置設定。篩選「script」並將 `User Script Sandboxing` 設為 `NO`。此步驟是為了正確切換 React Native 附帶的 [Hermes 引擎](https://github.com/facebook/hermes/blob/main/README.md) 的 Debug 和 Release 版本。

![停用沙盒功能](/docs/assets/disable-sandboxing.png)

最後，請確保在 `Info.plist` 檔案中加入 `UIViewControllerBasedStatusBarAppearance` 鍵，並將其值設為 `NO`。

![停用 UIViewControllerBasedStatusBarAppearance](/docs/assets/disable-UIViewControllerBasedStatusBarAppearance.png)

## 6. 測試整合

您已完成將 React Native 整合到應用程式的基本步驟。現在我們將啟動 [Metro 打包工具](https://metrobundler.dev/)，將您的 TypeScript 應用程式代碼打包成一個 bundle。Metro 的 HTTP 伺服器會將 bundle 從開發環境的 `localhost` 分享到模擬器或裝置，這支援 [熱重載](https://reactnative.dev/blog/2016/03/24/introducing-hot-reloading)。

首先，您需要在專案根目錄創建一個 `metro.config.js` 檔案，內容如下：

```js
const {getDefaultConfig} = require('@react-native/metro-config');
module.exports = getDefaultConfig(__dirname);
```

您可以參考社群模板中的 [metro.config.js 檔案](https://github.com/react-native-community/template/blob/0.78-stable/template/metro.config.js)。

接著，您需要在專案根目錄創建一個 `.watchmanconfig` 檔案。該檔案必須包含一個空的 json 物件：

```sh
echo {} > .watchmanconfig
```

設定檔就位後，即可執行打包工具。在專案的根目錄執行以下命令：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm start
```

</TabItem>
<TabItem value="yarn">

```shell
yarn start
```

</TabItem>
</Tabs>

現在像平常一樣建置並執行您的 iOS 應用程式。

當您進入應用程式中由 React 驅動的 Activity 時，它應從開發伺服器載入 JavaScript 代碼並顯示：

<center><img src="/docs/assets/EmbeddedAppIOS078.gif" width="300" /></center>

### 在 Xcode 中創建發布版本

您也可以使用 Xcode 創建發布版本！唯一的額外步驟是添加一個在建置應用程式時執行的腳本，將 JS 和圖片打包到 iOS 應用程式中。

1. 在 Xcode 中選擇您的應用程式
2. 點擊 `Build Phases`
3. 點擊左上角的 `+` 並選擇 `New Run Script Phase`
4. 點擊 `Run Script` 行，將腳本重新命名為 `Bundle React Native code and images`
5. 在文字框中貼上以下腳本

```sh title="Build React Native code and image"
set -e

WITH_ENVIRONMENT="$REACT_NATIVE_PATH/scripts/xcode/with-environment.sh"
REACT_NATIVE_XCODE="$REACT_NATIVE_PATH/scripts/react-native-xcode.sh"

/bin/sh -c "$WITH_ENVIRONMENT $REACT_NATIVE_XCODE"
```

6. Drag and drop the script before the one called `[CP] Embed Pods Frameworks`.

Now, if you build your app for Release, it will work as expected.

## 7. Passing initial props to the React Native view

In some case, you'd like to pass some information from the Native app to JavaScript. For example, you might want to pass the user id of the currently logged user to React Native, together with a token that can be used to retrieve information from a database.

This is possible by using the `initialProperties` parameter of the `view(withModuleName:initialProperty)` overload of the `RCTReactNativeFactory` class. The following steps shows you how to do it.

### Update the App.tsx file to read the initial properties.

Open the `App.tsx` file and add the following code:

```diff title="App.tsx"
import {
  Colors,
  DebugInstructions,
  Header,
  ReloadInstructions,
} from 'react-native/Libraries/NewAppScreen';

-function App(): React.JSX.Element {
+function App(props): React.JSX.Element {
  const isDarkMode = useColorScheme() === 'dark';

  const backgroundStyle = {
    backgroundColor: isDarkMode ? Colors.darker : Colors.lighter,
  };

  return (
    <SafeAreaView style={backgroundStyle}>
      <StatusBar
        barStyle={isDarkMode ? 'light-content' : 'dark-content'}
        backgroundColor={backgroundStyle.backgroundColor}
      />
      <ScrollView
        contentInsetAdjustmentBehavior="automatic"
        style={backgroundStyle}>
        <Header />
-       <View
-         style={{
-           backgroundColor: isDarkMode
-             ? Colors.black
-             : Colors.white,
-           padding: 24,
-         }}>
-         <Text style={styles.title}>Step One</Text>
-         <Text>
-           Edit <Text style={styles.bold}>App.tsx</Text> to
-           change this screen and see your edits.
-         </Text>
-         <Text style={styles.title}>See your changes</Text>
-         <ReloadInstructions />
-         <Text style={styles.title}>Debug</Text>
-         <DebugInstructions />
+         <Text style={styles.title}>UserID: {props.userID}</Text>
+         <Text style={styles.title}>Token: {props.token}</Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  title: {
    fontSize: 24,
    fontWeight: '600',
+   marginLeft: 20,
  },
  bold: {
    fontWeight: '700',
  },
});

export default App;
```

These changes will tell React Native that your App component is now accepting some properties. The `RCTreactNativeFactory` will take care of passing them to the component when it's rendered.

### Update the Native code to pass the initial properties to JavaScript.

<Tabs groupId="ios-language" queryString defaultValue={constants.defaultAppleLanguage} values={constants.appleLanguages}>
<TabItem value="objc">

Modify the `ReactViewController.mm` to pass the initial properties to JavaScript.

```diff title="ReactViewController.mm"
 - (void)viewDidLoad {
   [super viewDidLoad];
   // Do any additional setup after loading the view.

   _factoryDelegate = [ReactNativeFactoryDelegate new];
   _factoryDelegate.dependencyProvider = [RCTAppDependencyProvider new];
   _factory = [[RCTReactNativeFactory alloc] initWithDelegate:_factoryDelegate];
-  self.view = [_factory.rootViewFactory viewWithModuleName:@"HelloWorld"];
+  self.view = [_factory.rootViewFactory viewWithModuleName:@"HelloWorld" initialProperties:@{
+    @"userID": @"12345678",
+    @"token": @"secretToken"
+  }];
}
```

</TabItem>
<TabItem value="swift">

Modify the `ReactViewController.swift` to pass the initial properties to the React Native view.

```diff title="ReactViewController.swift"
  override func viewDidLoad() {
    super.viewDidLoad()
    reactNativeFactoryDelegate = ReactNativeDelegate()
    reactNativeFactoryDelegate!.dependencyProvider = RCTAppDependencyProvider()
    reactNativeFactory = RCTReactNativeFactory(delegate: reactNativeFactoryDelegate!)
-   view = reactNativeFactory!.rootViewFactory.view(withModuleName: "HelloWorld")
+   view = reactNativeFactory!.rootViewFactory.view(withModuleName: "HelloWorld" initialProperties: [
+     "userID": "12345678",
+     "token": "secretToken"
+])

  }
}
```

</TabItem>
</Tabs>

3. Run your app once again. You should see the following screen after you present the `ReactViewController`:

<center>
  <img src="/docs/assets/brownfield-with-initial-props.png" width="30%" height="30%"/>
</center>

## Now what?

At this point you can continue developing your app as usual. Refer to our [debugging](debugging) and [deployment](running-on-device) docs to learn more about working with React Native.