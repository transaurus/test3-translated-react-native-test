import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 iOS 應用的關鍵在於：

1. 設定 React Native 依賴項與目錄結構
2. 確認應用中需使用的 React Native 元件
3. 透過 CocoaPods 添加這些元件作為依賴項
4. 使用 JavaScript 開發 React Native 元件
5. 在 iOS 應用中添加 `RCTRootView`，該視圖將作為 React Native 元件的容器
6. 啟動 React Native 伺服器並運行原生應用
7. 驗證 React Native 功能是否如預期運作

## 必要條件

請依照[環境設定指南](environment-setup)中的 React Native CLI 快速入門，配置開發環境以建構 iOS 版 React Native 應用

### 1. 設定目錄結構

為確保流程順暢，請為整合專案新建資料夾，並將現有 iOS 專案複製到 `/ios` 子資料夾中

### 2. 安裝 JavaScript 依賴項

進入專案根目錄，建立包含以下內容的 `package.json` 檔案：

```
{
  "name": "MyReactNativeApp",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "yarn react-native start"
  }
}
```

接著請確認已[安裝 yarn 套件管理工具](https://yarnpkg.com/lang/en/docs/install/)

安裝 `react` 與 `react-native` 套件。開啟終端機或命令提示字元，導航至含 `package.json` 的目錄後執行：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install react-native
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add react-native
```

</TabItem>
</Tabs>

此操作將輸出類似以下訊息（請向上滾動 yarn 輸出以查看）：

> 警告："react-native@0.52.2" 有未滿足的 peer 依賴項 "react@16.2.0"

這屬正常現象，表示我們還需安裝 React：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm install react@version_printed_above
```

</TabItem>
<TabItem value="yarn">

```shell
yarn add react@version_printed_above
```

</TabItem>
</Tabs>

安裝過程會建立新的 `/node_modules` 資料夾，此資料夾儲存專案建構所需的所有 JavaScript 依賴項

請將 `node_modules/` 加入 `.gitignore` 檔案

### 3. 安裝 CocoaPods

[CocoaPods](http://cocoapods.org) 是 iOS 與 macOS 開發的套件管理工具，我們用它將實際的 React Native 框架代碼本地化加入現有專案

建議透過 [Homebrew](http://brew.sh/) 安裝 CocoaPods

```shell
brew install cocoapods
```

> 技術上雖可不使用 CocoaPods，但這將導致需手動添加函式庫與連結器，使流程過度複雜化

## 將 React Native 添加至應用

假設[待整合應用](https://github.com/JoelMarcey/iOS-2048)為 [2048](https://en.wikipedia.org/wiki/2048_%28video_game%29) 遊戲。下圖為未整合 React Native 時的原生應用主選單樣貌

![整合前的畫面](/docs/assets/react-native-existing-app-integration-ios-before.png)

### Xcode 命令列工具

請安裝命令列工具。在 Xcode 選單中選擇「Preferences...」，進入 Locations 面板後，於 Command Line Tools 下拉選單中選擇最新版本進行安裝

![Xcode 命令列工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### 配置 CocoaPods 依賴項

在整合 React Native 至應用前，需先決定要整合哪些 React Native 框架部分。我們將使用 CocoaPods 來指定應用所依賴的「子規格」(subspecs)

支援的 `subspec` 清單可在 [`/node_modules/react-native/React.podspec`](https://github.com/facebook/react-native/blob/0.70-stable/React.podspec) 中找到。這些子規格通常以功能命名。例如，您通常會需要 `Core` 子規格，這將為您提供 `AppRegistry`、`StyleSheet`、`View` 和其他核心 React Native 函式庫。如果您想加入 React Native 的 `Text` 函式庫（例如用於 `<Text>` 元素），則需要 `RCTText` 子規格。如果您需要 `Image` 函式庫（例如用於 `<Image>` 元素），則需要 `RCTImage` 子規格。

您可以在 `Podfile` 檔案中指定應用程式將依賴哪些子規格。建立 `Podfile` 最簡單的方法是在專案的 `/ios` 子資料夾中執行 CocoaPods 的 `init` 指令：

```shell
pod init
```

`Podfile` 將包含一個樣板設定，您可以根據整合需求進行調整。

> `Podfile` 的版本會根據您的 `react-native` 版本而變化。請參考 https://react-native-community.github.io/upgrade-helper/ 以確認您應使用的特定 `Podfile` 版本。

最終，您的 `Podfile` 應該看起來類似這樣：

```
# The target name is most likely the name of your project.
target 'NumberTileGame' do

  # Your 'node_modules' directory is probably in the root of your project,
  # but if not, adjust the `:path` accordingly
  pod 'FBLazyVector', :path => "../node_modules/react-native/Libraries/FBLazyVector"
  pod 'FBReactNativeSpec', :path => "../node_modules/react-native/Libraries/FBReactNativeSpec"
  pod 'RCTRequired', :path => "../node_modules/react-native/Libraries/RCTRequired"
  pod 'RCTTypeSafety', :path => "../node_modules/react-native/Libraries/TypeSafety"
  pod 'React', :path => '../node_modules/react-native/'
  pod 'React-Core', :path => '../node_modules/react-native/'
  pod 'React-CoreModules', :path => '../node_modules/react-native/React/CoreModules'
  pod 'React-Core/DevSupport', :path => '../node_modules/react-native/'
  pod 'React-RCTActionSheet', :path => '../node_modules/react-native/Libraries/ActionSheetIOS'
  pod 'React-RCTAnimation', :path => '../node_modules/react-native/Libraries/NativeAnimation'
  pod 'React-RCTBlob', :path => '../node_modules/react-native/Libraries/Blob'
  pod 'React-RCTImage', :path => '../node_modules/react-native/Libraries/Image'
  pod 'React-RCTLinking', :path => '../node_modules/react-native/Libraries/LinkingIOS'
  pod 'React-RCTNetwork', :path => '../node_modules/react-native/Libraries/Network'
  pod 'React-RCTSettings', :path => '../node_modules/react-native/Libraries/Settings'
  pod 'React-RCTText', :path => '../node_modules/react-native/Libraries/Text'
  pod 'React-RCTVibration', :path => '../node_modules/react-native/Libraries/Vibration'
  pod 'React-Core/RCTWebSocket', :path => '../node_modules/react-native/'

  pod 'React-cxxreact', :path => '../node_modules/react-native/ReactCommon/cxxreact'
  pod 'React-jsi', :path => '../node_modules/react-native/ReactCommon/jsi'
  pod 'React-jsiexecutor', :path => '../node_modules/react-native/ReactCommon/jsiexecutor'
  pod 'React-jsinspector', :path => '../node_modules/react-native/ReactCommon/jsinspector'
  pod 'ReactCommon/callinvoker', :path => "../node_modules/react-native/ReactCommon"
  pod 'ReactCommon/turbomodule/core', :path => "../node_modules/react-native/ReactCommon"
  pod 'Yoga', :path => '../node_modules/react-native/ReactCommon/yoga'

  pod 'DoubleConversion', :podspec => '../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec'
  pod 'glog', :podspec => '../node_modules/react-native/third-party-podspecs/glog.podspec'
  pod 'Folly', :podspec => '../node_modules/react-native/third-party-podspecs/Folly.podspec'

end
```

建立 `Podfile` 後，您就可以安裝 React Native pod 了。

```shell
$ pod install
```

您應該會看到類似以下的輸出：

```
Analyzing dependencies
Fetching podspec for `React` from `../node_modules/react-native`
Downloading dependencies
Installing React (0.62.0)
Generating Pods project
Integrating client project
Sending stats
Pod installation complete! There are 3 dependencies from the Podfile and 1 total pod installed.
```

> 如果此步驟因提及 `xcrun` 的錯誤而失敗，請確認在 Xcode 的 **偏好設定 > 位置** 中已指定命令列工具。

### 程式碼整合

現在我們將實際修改原生 iOS 應用程式以整合 React Native。對於我們的 2048 範例應用程式，我們將在 React Native 中加入一個「高分」畫面。

#### React Native 元件

我們要編寫的第一段程式碼是新「高分」畫面的實際 React Native 程式碼，該畫面將整合到我們的應用程式中。

##### 1. 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且總是必需的。它可以是一個小檔案，`require` 其他屬於您的 React Native 元件或應用程式的檔案，也可以包含所需的所有程式碼。在我們的案例中，我們將把所有內容放在 `index.js` 中。

##### 2. 加入您的 React Native 程式碼

在您的 `index.js` 中，建立您的元件。在我們的範例中，我們將在一個帶有樣式的 `<View>` 中加入一個 `<Text>` 元件。

```jsx
import React from 'react';
import {AppRegistry, StyleSheet, Text, View} from 'react-native';

const RNHighScores = ({scores}) => {
  const contents = scores.map(score => (
    <Text key={score.name}>
      {score.name}:{score.value}
      {'\n'}
    </Text>
  ));
  return (
    <View style={styles.container}>
      <Text style={styles.highScoresTitle}>
        2048 High Scores!
      </Text>
      <Text style={styles.scores}>{contents}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#FFFFFF',
  },
  highScoresTitle: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  scores: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});

// Module name
AppRegistry.registerComponent('RNHighScores', () => RNHighScores);
```

> `RNHighScores` 是您的模組名稱，當您從 iOS 應用程式中加入一個視圖到 React Native 時將會使用它。

#### 魔法：`RCTRootView`

現在您的 React Native 元件已透過 `index.js` 建立，您需要將該元件加入到一個新的或現有的 `ViewController` 中。最簡單的方法是選擇性地建立一個通往您的元件的事件路徑，然後將該元件加入到現有的 `ViewController` 中。

我們將把 React Native 元件與 `ViewController` 中的一個新原生視圖綁定，該視圖將實際包含它，稱為 `RCTRootView`。

##### 1. 建立事件路徑

您可以在主遊戲選單中加入一個新連結，前往「高分」React Native 頁面。

![事件路徑](/docs/assets/react-native-add-react-native-integration-link.png)

##### 2. 事件處理器

We will now add an event handler from the menu link. A method will be added to the main `ViewController` of your application. This is where `RCTRootView` comes into play.

When you build a React Native application, you use the [Metro bundler][metro] to create an `index.bundle` that will be served by the React Native server. Inside `index.bundle` will be our `RNHighScore` module. So, we need to point our `RCTRootView` to the location of the `index.bundle` resource (via `NSURL`) and tie it to the module.

We will, for debugging purposes, log that the event handler was invoked. Then, we will create a string with the location of our React Native code that exists inside the `index.bundle`. Finally, we will create the main `RCTRootView`. Notice how we provide `RNHighScores` as the `moduleName` that we created [above](#the-react-native-component) when writing the code for our React Native component.

First `import` the `RCTRootView` header.

```objectivec
#import <React/RCTRootView.h>
```

> The `initialProperties` are here for illustration purposes so we have some data for our high score screen. In our React Native component, we will use `this.props` to get access to that data.

```objectivec
- (IBAction)highScoreButtonPressed:(id)sender {
    NSLog(@"High Score Button Pressed");
    NSURL *jsCodeLocation = [NSURL URLWithString:@"http://localhost:8081/index.bundle?platform=ios"];

    RCTRootView *rootView =
      [[RCTRootView alloc] initWithBundleURL: jsCodeLocation
                                  moduleName: @"RNHighScores"
                           initialProperties:
                             @{
                               @"scores" : @[
                                 @{
                                   @"name" : @"Alex",
                                   @"value": @"42"
                                  },
                                 @{
                                   @"name" : @"Joel",
                                   @"value": @"10"
                                 }
                               ]
                             }
                               launchOptions: nil];
    UIViewController *vc = [[UIViewController alloc] init];
    vc.view = rootView;
    [self presentViewController:vc animated:YES completion:nil];
}
```

> Note that `RCTRootView initWithBundleURL` starts up a new JSC VM. To save resources and simplify the communication between RN views in different parts of your native app, you can have multiple views powered by React Native that are associated with a single JS runtime. To do that, instead of using `[RCTRootView alloc] initWithBundleURL`, use [`RCTBridge initWithBundleURL`](https://github.com/facebook/react-native/blob/0.70-stable/React/Base/RCTBridge.h#L93) to create a bridge and then use `RCTRootView initWithBridge`.

> When moving your app to production, the `NSURL` can point to a pre-bundled file on disk via something like `[[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];`. You can use the `react-native-xcode.sh` script in `node_modules/react-native/scripts/` to generate that pre-bundled file.

##### 3. Wire Up

Wire up the new link in the main menu to the newly added event handler method.

![Event Path](/docs/assets/react-native-add-react-native-integration-wire-up.png)

> One of the easier ways to do this is to open the view in the storyboard and right click on the new link. Select something such as the `Touch Up Inside` event, drag that to the storyboard and then select the created method from the list provided.

### Test your integration

You have now done all the basic steps to integrate React Native with your current application. Now we will start the [Metro bundler][metro] to build the `index.bundle` package and the server running on `localhost` to serve it.

##### 1. Add App Transport Security exception

Apple has blocked implicit cleartext HTTP resource loading. So we need to add the following our project's `Info.plist` (or equivalent) file.

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>localhost</key>
        <dict>
            <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
            <true/>
        </dict>
    </dict>
</dict>
```

> App Transport Security is good for your users. Make sure to re-enable it prior to releasing your app for production.

##### 2. Run the packager

To run your app, you need to first start the development server. To do this, run the following command in the root directory of your React Native project:

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

##### 3. Run the app

If you are using Xcode or your favorite editor, build and run your native iOS application as normal. Alternatively, you can run the app from the command line using:

```
# From the root of your project
$ npx react-native run-ios
```

在我們的範例應用程式中，您會看到「高分榜」的連結，點擊後將呈現您的 React Native 元件。

這是_原生_應用程式的主畫面：

![主畫面](/docs/assets/react-native-add-react-native-integration-example-home-screen.png)

這是_React Native_的高分榜畫面：

![高分榜](/docs/assets/react-native-add-react-native-integration-example-high-scores.png)

> 若執行應用程式時遇到模組解析問題，請參閱 [GitHub 議題](https://github.com/facebook/react-native/issues/4968)以獲取資訊與解決方案。[此評論](https://github.com/facebook/react-native/issues/4968#issuecomment-220941717)可能是最新的解決方法。

### 接下來？

至此，您可以如常繼續開發應用程式。詳閱我們的[除錯指南](debugging)與[部署文件](running-on-device)以深入瞭解 React Native 開發。

[metro]: https://metrobundler.dev/