import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 iOS 應用的關鍵在於：

1. 設定 React Native 依賴項與目錄結構。
2. 了解應用中將使用的 React Native 元件。
3. 使用 CocoaPods 添加這些元件作為依賴項。
4. 用 JavaScript 開發 React Native 元件。
5. 在 iOS 應用中添加 `RCTRootView`，該視圖將作為 React Native 元件的容器。
6. 啟動 React Native 伺服器並運行原生應用。
7. 驗證應用的 React Native 功能是否正常運作。

## 必要條件

依照[環境設定指南](environment-setup)中的 React Native CLI 快速入門，配置開發環境以建構 iOS 版 React Native 應用。

### 1. 設定目錄結構

為確保流程順暢，請為整合的 React Native 專案建立新資料夾，並將現有 iOS 專案複製到 `/ios` 子資料夾中。

### 2. 安裝 JavaScript 依賴項

進入專案的根目錄，建立包含以下內容的 `package.json` 檔案：

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

接著，請確認已[安裝 yarn 套件管理工具](https://yarnpkg.com/lang/en/docs/install/)。

安裝 `react` 和 `react-native` 套件。開啟終端機或命令提示字元，導航至包含 `package.json` 檔案的目錄並執行：

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

這將輸出類似以下的訊息（在 yarn 輸出中向上滾動即可看到）：

> 警告："react-native@0.52.2" 有未滿足的 peer 依賴 "react@16.2.0"。

這沒問題，表示我們還需要安裝 React：

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

安裝過程會建立新的 `/node_modules` 資料夾，該資料夾儲存建置專案所需的所有 JavaScript 依賴項。

將 `node_modules/` 加入你的 `.gitignore` 檔案。

### 3. 安裝 CocoaPods

[CocoaPods](http://cocoapods.org) 是 iOS 和 macOS 開發的套件管理工具，我們用它將實際的 React Native 框架程式碼本地化加入當前專案。

建議使用 [Homebrew](http://brew.sh/) 安裝 CocoaPods。

```shell
brew install cocoapods
```

> 技術上可以不使用 CocoaPods，但這需要手動添加函式庫和連結器設定，會使流程過於複雜。

## 將 React Native 加入你的應用

假設[待整合的應用](https://github.com/JoelMarcey/iOS-2048)是 [2048](https://en.wikipedia.org/wiki/2048_%28video_game%29) 遊戲。下圖是未整合 React Native 時原生應用的主選單畫面。

![整合 RN 前的畫面](/docs/assets/react-native-existing-app-integration-ios-before.png)

### Xcode 命令列工具

安裝命令列工具。在 Xcode 選單中選擇「Preferences...」，進入 Locations 面板，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令列工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### 配置 CocoaPods 依賴項

在將 React Native 整合至應用前，需決定要整合哪些 React Native 框架部分。我們將使用 CocoaPods 指定應用依賴的「子規格」(subspecs)。

支援的 `subspec` 清單可在 [`/node_modules/react-native/React.podspec`](https://github.com/facebook/react-native/blob/0.71-stable/React.podspec) 中找到。這些子規格通常以功能命名。例如，您通常會需要 `Core` 子規格，這將包含 `AppRegistry`、`StyleSheet`、`View` 和其他 React Native 核心函式庫。如果您想加入 React Native 的 `Text` 函式庫（例如用於 `<Text>` 元素），則需要 `RCTText` 子規格。如果您需要 `Image` 函式庫（例如用於 `<Image>` 元素），則需要 `RCTImage` 子規格。

您可以在 `Podfile` 檔案中指定應用程式將依賴哪些子規格。建立 `Podfile` 最簡單的方法是在專案的 `/ios` 子資料夾中執行 CocoaPods 的 `init` 指令：

```shell
pod init
```

`Podfile` 將包含一個樣板設定，您可以根據整合需求進行調整。

> `Podfile` 的版本會根據您使用的 `react-native` 版本而變化。請參考 https://react-native-community.github.io/upgrade-helper/ 以獲取您應使用的特定 `Podfile` 版本。

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

建立 `Podfile` 後，您就可以安裝 React Native 的 pod 了。

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

> 如果安裝失敗並出現提及 `xcrun` 的錯誤，請確認在 Xcode 的 **偏好設定 > 位置** 中已指定 Command Line Tools。

### 程式碼整合

現在我們將實際修改原生 iOS 應用程式以整合 React Native。對於我們的 2048 範例應用程式，我們將新增一個用 React Native 開發的「高分」畫面。

#### React Native 元件

我們要編寫的第一段程式碼是用於新「高分」畫面的實際 React Native 程式碼，該畫面將整合到我們的應用程式中。

##### 1. 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且始終是必需的。它可以是一個小檔案，用於 `require` 其他屬於您的 React Native 元件或應用程式的檔案，也可以包含所有需要的程式碼。在我們的案例中，我們將所有程式碼放在 `index.js` 中。

##### 2. 加入您的 React Native 程式碼

在您的 `index.js` 中，建立您的元件。在我們的範例中，我們將在一個樣式化的 `<View>` 中加入一個 `<Text>` 元件：

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

> `RNHighScores` 是您的模組名稱，當您從 iOS 應用程式中新增一個視圖到 React Native 時將會使用它。

#### 魔法：`RCTRootView`

現在您的 React Native 元件已透過 `index.js` 建立，您需要將該元件新增到一個新的或現有的 `ViewController` 中。最簡單的方法是選擇性地建立一個到您元件的路徑，然後將該元件新增到現有的 `ViewController` 中。

我們將把 React Native 元件與 `ViewController` 中的一個新原生視圖綁定，該視圖將實際包含它，稱為 `RCTRootView`。

##### 1. 建立事件路徑

您可以在主遊戲選單中新增一個連結，以進入「高分」React Native 頁面。

![事件路徑](/docs/assets/react-native-add-react-native-integration-link.png)

##### 2. 事件處理器

我們現在將為選單連結添加一個事件處理器。這個方法會被加入到應用程式的主 `ViewController` 中。這裡就是 `RCTRootView` 發揮作用的地方。

當你建構 React Native 應用程式時，會使用 [Metro 打包工具][metro] 來建立一個 `index.bundle`，這個檔案將由 React Native 伺服器提供。在 `index.bundle` 中會有我們的 `RNHighScore` 模組。因此，我們需要將 `RCTRootView` 指向 `index.bundle` 資源的位置（透過 `NSURL`）並將其與模組綁定。

為了除錯目的，我們會記錄事件處理器被調用的情況。接著，我們會建立一個字串，指向存在於 `index.bundle` 中的 React Native 程式碼位置。最後，我們會建立主要的 `RCTRootView`。請注意我們如何提供 `RNHighScores` 作為 `moduleName`，這是在[前面](#the-react-native-component)撰寫 React Native 元件程式碼時所建立的。

首先 `import` 引入 `RCTRootView` 的標頭檔。

```objectivec
#import <React/RCTRootView.h>
```

> 這裡的 `initialProperties` 是為了示範用途，讓我們的高分畫面有一些資料。在我們的 React Native 元件中，會使用 `this.props` 來存取這些資料。

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

> 注意 `RCTRootView initWithBundleURL` 會啟動一個新的 JSC 虛擬機器。為了節省資源並簡化原生應用中不同部分 RN 視圖之間的溝通，你可以讓多個由 React Native 驅動的視圖與單一 JS 運行時關聯。要做到這點，不要使用 `[RCTRootView alloc] initWithBundleURL`，而是使用 [`RCTBridge initWithBundleURL`](https://github.com/facebook/react-native/blob/0.71-stable/React/Base/RCTBridge.h#L93) 來建立橋接，然後使用 `RCTRootView initWithBridge`。

> 當將應用程式移至生產環境時，`NSURL` 可以指向磁碟上的預打包檔案，例如透過 `[[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];`。你可以使用 `node_modules/react-native/scripts/` 中的 `react-native-xcode.sh` 腳本來產生這個預打包檔案。

##### 3. 連接設定

將主選單中的新連結與剛添加的事件處理器方法連接起來。

![事件路徑](/docs/assets/react-native-add-react-native-integration-wire-up.png)

> 較簡單的做法之一是在 storyboard 中打開視圖，然後右鍵點擊新連結。選擇類似 `Touch Up Inside` 這樣的事件，將其拖曳到 storyboard，然後從提供的清單中選擇已建立的方法。

### 測試你的整合

你現在已經完成了將 React Native 與現有應用程式整合的所有基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來建構 `index.bundle` 套件，並啟動運行在 `localhost` 上的伺服器來提供它。

##### 1. 添加 App Transport Security 例外

Apple 已禁止隱式的明文 HTTP 資源載入。因此我們需要在專案的 `Info.plist`（或等效檔案）中添加以下內容。

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

> App Transport Security 對你的使用者有好處。請確保在將應用程式發布到生產環境前重新啟用它。

##### 2. 執行打包工具

要運行你的應用程式，首先需要啟動開發伺服器。為此，請在 React Native 專案的根目錄中執行以下命令：

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

如果你使用 Xcode 或喜愛的編輯器，像平常一樣建構並運行你的原生 iOS 應用程式。或者，你也可以使用以下命令從命令行運行應用程式：

```
# From the root of your project
$ npx react-native run-ios
```

在我們的範例應用程式中，您應該會看到「高分排行榜」的連結，點擊後將看到 React Native 元件的渲染畫面。

這是原生應用程式的主畫面：

![主畫面](/docs/assets/react-native-add-react-native-integration-example-home-screen.png)

這是 React Native 的高分排行榜畫面：

![高分排行榜](/docs/assets/react-native-add-react-native-integration-example-high-scores.png)

> 若執行應用程式時遇到模組解析問題，請參閱 [GitHub 議題](https://github.com/facebook/react-native/issues/4968) 以獲取相關資訊與可能的解決方案。[此評論](https://github.com/facebook/react-native/issues/4968#issuecomment-220941717) 似乎是目前最新的解決方法。

### 接下來呢？

至此，您可以繼續照常開發應用程式。請參考我們的[除錯指南](debugging)與[部署文件](running-on-device)以深入瞭解 React Native 的開發工作流程。

[metro]: https://metrobundler.dev/