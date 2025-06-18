import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 iOS 應用的關鍵步驟包括：

1. 建立正確的目錄結構
2. 安裝必要的 NPM 依賴項
3. 在 Podfile 配置中添加 React Native
4. 為首個 React Native 畫面編寫 TypeScript 程式碼
5. 使用 `RCTRootView` 將 React Native 與 iOS 程式碼整合
6. 執行打包器測試整合結果，並查看應用運行情況

## 使用社群範本

遵循本指南時，建議參考 [React Native 社群範本](https://github.com/react-native-community/template/)。該範本包含一個**最小化 iOS 應用**，可幫助您理解如何將 React Native 整合至現有 iOS 應用。

## 必要條件

請先完成[開發環境設定指南](set-up-your-environment)並學習[無框架使用 React Native](getting-started-without-a-framework)，以配置 iOS 版 React Native 應用的開發環境。本指南同時假設您已掌握 iOS 開發基礎知識，例如建立 `UIViewController` 和編輯 `Podfile` 文件。

### 1. 建立目錄結構

為確保流程順暢，請為整合專案新建資料夾，並將**現有 iOS 專案**移動至 `/ios` 子目錄。

## 2. 安裝 NPM 依賴項

進入根目錄執行以下指令：

```shell
curl -O https://raw.githubusercontent.com/react-native-community/template/refs/heads/0.78-stable/template/package.json
```

此指令會將[社群範本的 package.json 文件](https://github.com/react-native-community/template/blob/0.78-stable/template/package.json)複製到您的專案中。

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

安裝過程會建立新的 `node_modules` 資料夾，此資料夾存放專案建構所需的所有 JavaScript 依賴項。

請將 `node_modules/` 加入您的 `.gitignore` 文件（可參考[社群預設範本](https://github.com/react-native-community/template/blob/0.78-stable/template/_gitignore)）。

### 3. 安裝開發工具

### Xcode 命令行工具

安裝命令行工具：在 Xcode 選單中選擇 **Settings... (或 Preferences...)**，進入 Locations 面板後，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令行工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### CocoaPods

[CocoaPods](https://cocoapods.org) 是 iOS 和 macOS 開發的套件管理工具，我們用它將 React Native 框架程式碼本地化加入您的專案。

建議透過 [Homebrew](https://brew.sh/) 安裝 CocoaPods：

```shell
brew install cocoapods
```

## 4. 將 React Native 加入應用

### 配置 CocoaPods

配置 CocoaPods 需要兩個文件：

- **Gemfile**：定義所需的 Ruby 依賴項
- **Podfile**：定義依賴項的正確安裝方式

建立 **Gemfile**：進入專案根目錄執行此指令

```sh
curl -O https://raw.githubusercontent.com/react-native-community/template/refs/heads/0.78-stable/template/Gemfile
```

此指令會從範本下載 Gemfile。

:::note
若您使用 Xcode 16 創建專案，需按以下方式更新 Gemfile：

```diff
-gem 'cocoapods', '>= 1.13', '!= 1.15.0', '!= 1.15.1'
+gem 'cocoapods', '1.16.2'
gem 'activesupport', '>= 6.1.7.5', '!= 7.1.0'
-gem 'xcodeproj', '< 1.26.0'
+gem 'xcodeproj', '1.27.0'
```

Xcode 16 生成專案的方式與舊版略有不同，需使用最新版 CocoaPods 和 Xcodeproj gem 才能正常運作。
:::

同樣地，針對 **Podfile**，請進入專案的 `ios` 資料夾並執行

```sh
curl -O https://raw.githubusercontent.com/react-native-community/template/refs/heads/0.78-stable/template/ios/Podfile
```

請以社群模板作為 [Gemfile](https://github.com/react-native-community/template/blob/0.78-stable/template/Gemfile) 和 [Podfile](https://github.com/react-native-community/template/blob/0.78-stable/template/ios/Podfile) 的參考基準。

:::note
請記得修改 [這行程式碼](https://github.com/react-native-community/template/blob/0.78-stable/template/ios/Podfile#L17)。
:::

現在我們需要執行幾個額外指令來安裝 Ruby gems 和 Pods。
請導航至 `ios` 資料夾並執行以下指令：

```sh
bundle install
bundle exec pod install
```

第一道指令會安裝 Ruby 相依套件，第二道指令則會將 React Native 程式碼實際整合至您的應用程式，讓 iOS 檔案能導入 React Native 標頭檔。

## 5. 編寫 TypeScript 程式碼

現在我們要實際修改原生 iOS 應用程式來整合 React Native。

我們將編寫的第一段程式碼是新畫面的 React Native 程式碼，該畫面將被整合至我們的應用程式中。

### 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且為必要檔案。它可以是一個小檔案，僅用來 `import` React Native 元件或應用程式的其他檔案，也可以包含所需的所有程式碼。

我們的 `index.js` 應如下所示（此為[社群模板檔案參考](https://github.com/react-native-community/template/blob/0.78-stable/template/index.js)）：

```js
import {AppRegistry} from 'react-native';
import App from './App';

AppRegistry.registerComponent('HelloWorld', () => App);
```

### 建立 `App.tsx` 檔案

讓我們建立一個 `App.tsx` 檔案。這是可包含 [JSX](<https://en.wikipedia.org/wiki/JSX_(JavaScript)>) 表達式的 [TypeScript](https://www.typescriptlang.org/) 檔案，內含我們將整合至 iOS 應用程式的根 React Native 元件（[連結](https://github.com/react-native-community/template/blob/0.78-stable/template/App.tsx)）：

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

此為[社群模板檔案參考](https://github.com/react-native-community/template/blob/0.78-stable/template/App.tsx)

## 5. 與 iOS 程式碼整合

現在我們需要添加一些原生程式碼來啟動 React Native 運行環境，並告知其渲染我們的 React 元件。

### 需求

React Native 初始化現已與 iOS 應用程式的任何特定部分解耦。

可使用名為 `RCTReactNativeFactory` 的類別來初始化 React Native，該類別會為您處理 React Native 的生命週期。

一旦該類別初始化完成，您既可以透過提供 `UIWindow` 物件來啟動 React Native 視圖，也可以要求工廠生成一個能在任何 `UIViewController` 中載入的 `UIView`。

在以下範例中，我們將創建一個能將 React Native 視圖作為其 `view` 載入的 ViewController。

#### 創建 ReactViewController

從模板創建新檔案（<kbd>⌘</kbd>+<kbd>N</kbd>），選擇 Cocoa Touch Class 模板。

確保在「Subclass of」欄位中選擇 `UIViewController`。

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

最後，我們可以呈現 React Native 視圖。為此，我們需要一個能承載 JS 內容視圖的新 View Controller。我們已有初始的 `ViewController`，可讓它呈現 `ReactViewController`。具體方式取決於您的應用程式架構，本範例假設您有一個能模態呈現 React Native 的按鈕。

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

請務必停用沙盒腳本功能。在 Xcode 中點選您的應用程式，進入建置設定，篩選「script」並將 `User Script Sandboxing` 設為 `NO`。此步驟對於 React Native 內建的 [Hermes 引擎](https://github.com/facebook/hermes/blob/main/README.md)在 Debug 與 Release 版本間切換至關重要。

![停用沙盒功能](/docs/assets/disable-sandboxing.png)

最後，請確認在 `Info.plist` 檔案中添加 `UIViewControllerBasedStatusBarAppearance` 鍵，並將其值設為 `NO`。

![停用 UIViewControllerBasedStatusBarAppearance](/docs/assets/disable-UIViewControllerBasedStatusBarAppearance.png)

## 6. 測試整合結果

您已完成 React Native 與應用程式整合的基本步驟。現在我們將啟動 [Metro 打包工具](https://metrobundler.dev/)，將 TypeScript 程式碼打包成 bundle。Metro 的 HTTP 伺服器會將 bundle 從開發環境的 `localhost` 共享至模擬器或實體裝置，這使得[熱重載](https://reactnative.dev/blog/2016/03/24/introducing-hot-reloading)成為可能。

首先，請在專案根目錄創建 `metro.config.js` 檔案，內容如下：

```js
const {getDefaultConfig} = require('@react-native/metro-config');
module.exports = getDefaultConfig(__dirname);
```

您可參考社群模板中的 [metro.config.js 檔案](https://github.com/react-native-community/template/blob/0.78-stable/template/metro.config.js)。

接著，在專案根目錄創建 `.watchmanconfig` 檔案，內容需為空 JSON 物件：

```sh
echo {} > .watchmanconfig
```

完成設定檔配置後，即可執行打包工具。在專案根目錄執行以下指令：

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

現在如常建置並執行您的 iOS 應用程式。

當應用程式內載入 React 驅動的 Activity 時，應會從開發伺服器取得 JavaScript 程式碼並顯示：

<center><img src="/docs/assets/EmbeddedAppIOS078.gif" width="300" /></center>

### 在 Xcode 中創建正式版建置

您也能使用 Xcode 創建正式版建置！唯一需增加的步驟是在建置時添加執行腳本，將 JS 與圖片打包進 iOS 應用程式。

1. 在 Xcode 中選擇您的應用程式
2. 點選 `Build Phases`
3. 點擊左上角 `+` 並選擇 `New Run Script Phase`
4. 點擊 `Run Script` 行，將腳本更名為 `Bundle React Native code and images`
5. 在文字框中貼入以下腳本

```sh title="Build React Native code and image"
set -e

WITH_ENVIRONMENT="$REACT_NATIVE_PATH/scripts/xcode/with-environment.sh"
REACT_NATIVE_XCODE="$REACT_NATIVE_PATH/scripts/react-native-xcode.sh"

/bin/sh -c "$WITH_ENVIRONMENT $REACT_NATIVE_XCODE"
```

6. 將該腳本拖放到名為 `[CP] Embed Pods Frameworks` 的腳本之前。

現在，如果您為 Release 版本建構應用程式，它將按預期運作。

## 7. 將初始屬性傳遞給 React Native 視圖

在某些情況下，您可能希望將一些資訊從原生應用程式傳遞給 JavaScript。例如，您可能希望將當前登入使用者的使用者 ID 以及可用於從資料庫檢索資訊的令牌傳遞給 React Native。

這可以通過使用 `RCTReactNativeFactory` 類的 `view(withModuleName:initialProperty)` 重載的 `initialProperties` 參數來實現。以下步驟將向您展示如何操作。

### 更新 App.tsx 文件以讀取初始屬性。

打開 `App.tsx` 文件並添加以下代碼：

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

這些更改將告訴 React Native，您的 App 組件現在接受一些屬性。`RCTreactNativeFactory` 將負責在渲染組件時將這些屬性傳遞給它。

### 更新原生代碼以將初始屬性傳遞給 JavaScript。

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

3. 再次運行您的應用程式。在呈現 `ReactViewController` 後，您應該會看到以下畫面：

<center>
  <img src="/docs/assets/brownfield-with-initial-props.png" width="30%" height="30%"/>
</center>

## 接下來呢？

此時，您可以像往常一樣繼續開發您的應用程式。請參考我們的[除錯](debugging)和[部署](running-on-device)文檔，以了解更多關於使用 React Native 的資訊。