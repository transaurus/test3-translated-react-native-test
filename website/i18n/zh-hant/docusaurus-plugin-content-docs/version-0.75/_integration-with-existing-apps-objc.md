import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 iOS 應用的關鍵在於：

1. 設定 React Native 依賴項與目錄結構
2. 確認應用中將使用的 React Native 元件
3. 透過 CocoaPods 添加這些元件作為依賴項
4. 使用 JavaScript 開發 React Native 元件
5. 在 iOS 應用中添加 `RCTRootView`，該視圖將作為 React Native 元件的容器
6. 啟動 React Native 伺服器並運行原生應用
7. 驗證 React Native 功能是否正常運作

## 環境準備

請先依照[環境設定指南](set-up-your-environment)與[無框架 React Native 入門](getting-started-without-a-framework)配置 iOS 開發環境。

### 1. 建立目錄結構

為確保流程順暢，請為整合專案新建資料夾，並將現有 iOS 專案複製至 `/ios` 子目錄。

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

請確認已安裝 [yarn 套件管理工具](https://yarnpkg.com/lang/en/docs/install/)。

安裝 `react` 與 `react-native` 套件。開啟終端機並導航至含 `package.json` 的目錄，執行：

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

此操作將輸出類似以下訊息（請向上滾動安裝命令輸出以查看）：

> 警告："`react-native@0.52.2`" 缺少必要依賴項 "`react@16.2.0`"。

此為正常現象，表示需同步安裝 React：

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

安裝過程會建立 `/node_modules` 資料夾，此目錄儲存專案所需的所有 JavaScript 依賴項。

請將 `node_modules/` 加入 `.gitignore` 檔案。

### 3. 安裝 CocoaPods

[CocoaPods](https://cocoapods.org) 是 iOS/macOS 開發的套件管理工具，我們用它將 React Native 框架代碼本地化至專案中。

建議透過 [Homebrew](https://brew.sh/) 安裝 CocoaPods：

```shell
brew install cocoapods
```

> 技術上可不使用 CocoaPods，但需手動處理函式庫與連結器設定，將大幅增加流程複雜度。

## 將 React Native 整合至應用

假設[待整合應用](https://github.com/austinzheng/iOS-2048)為 [2048](https://en.wikipedia.org/wiki/2048_%28video_game%29) 遊戲。下圖為未整合 React Native 時的原生應用主選單：

![整合前的畫面](/docs/assets/react-native-existing-app-integration-ios-before.png)

### 安裝 Xcode 命令行工具

於 Xcode 選單選擇 **Settings... (或 Preferences...)**，進入 Locations 面板，從 Command Line Tools 下拉選單安裝最新版本工具。

![Xcode 命令行工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### 配置 CocoaPods 依賴項

整合 React Native 前，需決定要引入框架的哪些部分。我們將透過 CocoaPods 指定應用依賴的「子規格」(subspecs)。

支援的 `subspec` 清單可在 [`/node_modules/react-native/React.podspec`](https://github.com/facebook/react-native/blob/main/packages/react-native/React.podspec) 中找到。這些子規格通常以功能命名。例如，您通常會需要 `Core` 子規格，它會包含 `AppRegistry`、`StyleSheet`、`View` 等 React Native 核心函式庫。若您想加入 React Native 的 `Text` 函式庫（例如用於 `<Text>` 元素），則需要 `RCTText` 子規格；若需要 `Image` 函式庫（例如用於 `<Image>` 元素），則需 `RCTImage` 子規格。

您可以在 `Podfile` 檔案中指定應用程式依賴的子規格。建立 `Podfile` 最簡單的方式是在專案的 `/ios` 子目錄中執行 CocoaPods 的 `init` 指令：

```shell
pod init
```

`Podfile` 會包含一個預設範本，您可根據整合需求進行調整。

> `Podfile` 的版本會根據您使用的 `react-native` 版本而異。請參考 https://react-native-community.github.io/upgrade-helper/ 以確認應使用的特定版本。

最終，您的 `Podfile` 應類似以下內容：

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

建立 `Podfile` 後，即可安裝 React Native 的 pod。

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

> 若安裝失敗並出現提及 `xcrun` 的錯誤，請確認 Xcode 中 **Settings... (或 Preferences...) > Locations** 下的 Command Line Tools 已設定正確。

### 程式碼整合

現在我們將實際修改原生 iOS 應用程式以整合 React Native。以 2048 範例應用程式為例，我們將加入一個以 React Native 開發的「高分榜」畫面。

#### React Native 元件

我們要編寫的第一段程式碼是新「高分榜」畫面的實際 React Native 程式碼，它將被整合到我們的應用程式中。

##### 1. 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且為必要檔案。它可以是一個小檔案，僅 `require` 其他屬於您 React Native 元件或應用程式的檔案，也可以包含所有需要的程式碼。在本例中，我們會將所有內容放在 `index.js` 中。

##### 2. 加入您的 React Native 程式碼

在 `index.js` 中建立您的元件。在此範例中，我們將在一個帶樣式的 `<View>` 中加入一個 `<Text>` 元件：

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

> `RNHighScores` 是您的模組名稱，當您從 iOS 應用程式中加入一個視圖到 React Native 時會使用此名稱。

#### 魔法所在：`RCTRootView`

現在您的 React Native 元件已透過 `index.js` 建立，您需要將該元件加入一個新的或現有的 `ViewController` 中。最簡單的方式是選擇性地建立一個通往您元件的事件路徑，然後將該元件加入現有的 `ViewController`。

我們將把 React Native 元件與 `ViewController` 中的一個新原生視圖綁定，該視圖將實際包含它，稱為 `RCTRootView`。

##### 1. 建立事件路徑

您可以在主遊戲選單中加入一個新連結，通往「高分榜」的 React Native 頁面。

![事件路徑](/docs/assets/react-native-add-react-native-integration-link.png)

##### 2. 事件處理器

我們現在將為選單連結添加一個事件處理器。這個方法會被加入到應用程式的主 `ViewController` 中。這裡就是 `RCTRootView` 發揮作用的地方。

當你建構一個 React Native 應用程式時，會使用 [Metro 打包工具][metro] 來建立一個 `index.bundle`，這個檔案將由 React Native 伺服器提供。在 `index.bundle` 內部會有我們的 `RNHighScore` 模組。因此，我們需要將 `RCTRootView` 指向 `index.bundle` 資源的位置（透過 `NSURL`）並將其與模組綁定。

為了除錯目的，我們會記錄事件處理器被調用的情況。接著，我們會建立一個字串，包含存在於 `index.bundle` 中的 React Native 程式碼位置。最後，我們會建立主要的 `RCTRootView`。請注意我們如何提供 `RNHighScores` 作為 `moduleName`，這是我們在編寫 React Native 元件程式碼時[上方](#the-react-native-component)所建立的。

首先 `import` 這個 `RCTRootView` 標頭檔。

```objectivec
#import <React/RCTRootView.h>
```

> 這裡的 `initialProperties` 是為了示範用途，讓我們的高分畫面有一些資料。在我們的 React Native 元件中，我們會使用 `this.props` 來存取這些資料。

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

> 請注意 `RCTRootView initWithBundleURL` 會啟動一個新的 JSC 虛擬機器。為了節省資源並簡化原生應用中不同部分 RN 視圖之間的溝通，你可以讓多個由 React Native 驅動的視圖與單一 JS 運行時關聯。要做到這一點，不要使用 `[RCTRootView alloc] initWithBundleURL`，而是使用 [`RCTBridge initWithBundleURL`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridge.h#L94) 來建立一個橋接器，然後使用 `RCTRootView initWithBridge`。

> 當將應用程式移至生產環境時，`NSURL` 可以指向磁碟上的預打包檔案，例如透過 `[[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];`。你可以使用 `node_modules/react-native/scripts/` 中的 `react-native-xcode.sh` 腳本來生成該預打包檔案。

##### 3. 連接設定

將主選單中的新連結與新添加的事件處理器方法連接起來。

![事件路徑](/docs/assets/react-native-add-react-native-integration-wire-up.png)

> 其中一個較簡單的方法是打開故事板中的視圖，然後右鍵點擊新連結。選擇類似 `Touch Up Inside` 這樣的事件，將其拖曳到故事板，然後從提供的列表中選擇所建立的方法。

### 測試你的整合

你現在已經完成了將 React Native 與當前應用程式整合的所有基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來建構 `index.bundle` 套件，並啟動運行在 `localhost` 上的伺服器來提供它。

##### 1. 添加應用程式傳輸安全性例外

Apple 已經阻止了隱式的明文 HTTP 資源加載。因此我們需要在專案的 `Info.plist`（或等效檔案）中添加以下內容。

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

> 應用程式傳輸安全性對你的用戶有好處。請確保在發布應用程式到生產環境之前重新啟用它。

##### 2. 執行打包工具

要運行你的應用程式，首先需要啟動開發伺服器。為此，請在你的 React Native 專案根目錄中執行以下命令：

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

##### 3. 執行應用程式

如果你使用 Xcode 或你喜歡的編輯器，像平常一樣建構並運行你的原生 iOS 應用程式。或者，你也可以使用以下命令從命令行運行應用程式：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios
```

</TabItem>
</Tabs>

在我們的範例應用程式中，您應該會看到「高分排行榜」的連結，點擊後將呈現您的 React Native 元件。

這是原生應用程式的主畫面：

![主畫面](/docs/assets/react-native-add-react-native-integration-example-home-screen.png)

這是 React Native 的高分排行榜畫面：

![高分排行榜](/docs/assets/react-native-add-react-native-integration-example-high-scores.png)

> 若執行應用程式時遇到模組解析問題，請參閱 [GitHub 議題](https://github.com/facebook/react-native/issues/4968) 以獲取相關資訊與解決方案。[此評論](https://github.com/facebook/react-native/issues/4968#issuecomment-220941717) 似乎是目前最新的解決方法。

### 接下來？

現在您可以繼續常規的應用程式開發。請參考我們的[除錯指南](debugging)和[部署文件](running-on-device)以了解更多關於 React Native 的開發工作。

[metro]: https://metrobundler.dev/