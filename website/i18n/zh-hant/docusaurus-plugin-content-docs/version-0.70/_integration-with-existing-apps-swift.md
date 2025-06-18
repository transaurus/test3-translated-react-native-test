## 核心概念

將 React Native 元件整合至 iOS 應用的關鍵步驟如下：

1. 設定 React Native 相依套件與目錄結構
2. 確認應用中將使用的 React Native 元件
3. 透過 CocoaPods 添加這些元件作為相依套件  
4. 使用 JavaScript 開發 React Native 元件
5. 在 iOS 應用中添加 `RCTRootView`，該視圖將作為 React Native 元件的容器
6. 啟動 React Native 伺服器並運行原生應用
7. 驗證 React Native 功能是否正常運作

## 必要條件

請依照[環境設定指南](environment-setup)中的 React Native CLI 快速入門，配置 iOS 版 React Native 應用的開發環境。

### 1. 設定目錄結構

為確保流程順暢，請先為整合專案建立新資料夾，再將現有 iOS 專案複製到 `/ios` 子資料夾中。

### 2. 安裝 JavaScript 相依套件

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

接著請確認已[安裝 yarn 套件管理工具](https://yarnpkg.com/lang/en/docs/install/)。

安裝 `react` 與 `react-native` 套件。開啟終端機並導航至 `package.json` 所在目錄，執行：

```shell
$ yarn add react-native
```

此指令會輸出類似以下訊息（請向上滾動 yarn 輸出以查看）：

> 警告："react-native@0.52.2" 需要未滿足的 peer 相依套件 "react@16.2.0"。

此為正常現象，表示我們還需安裝 React：

```shell
$ yarn add react@version_printed_above
```

Yarn 會建立新的 `/node_modules` 資料夾，此資料夾存放專案建構所需的所有 JavaScript 相依套件。

請將 `node_modules/` 加入 `.gitignore` 檔案。

### 3. 安裝 CocoaPods

[CocoaPods](http://cocoapods.org) 是 iOS 與 macOS 開發的套件管理工具，我們用它將 React Native 框架程式碼本地化加入現有專案。

建議透過 [Homebrew](http://brew.sh/) 安裝 CocoaPods：

```shell
$ brew install cocoapods
```

> 技術上雖可不使用 CocoaPods，但需手動添加函式庫與連結器設定，將使流程過度複雜化。

## 將 React Native 加入應用

假設[待整合應用](https://github.com/JoelMarcey/swift-2048)為 [2048](https://en.wikipedia.org/wiki/2048_%28video_game%29) 遊戲。下圖為未整合 React Native 時的原生應用主選單畫面：

![整合前畫面](/docs/assets/react-native-existing-app-integration-ios-before.png)

### Xcode 指令列工具

請安裝指令列工具。於 Xcode 選單選擇「Preferences...」，進入 Locations 面板後，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 指令列工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### 配置 CocoaPods 相依套件

在整合 React Native 前，需先決定要整合框架中的哪些部分。我們將使用 CocoaPods 來指定應用所依賴的「子規格」(subspecs)。

支援的 `subspec` 清單可在 [`/node_modules/react-native/React.podspec`](https://github.com/facebook/react-native/blob/0.70-stable/React.podspec) 中找到。這些子規格通常以功能命名。例如，您通常會需要 `Core` 子規格，這將為您提供 `AppRegistry`、`StyleSheet`、`View` 和其他 React Native 核心函式庫。如果您想加入 React Native 的 `Text` 函式庫（例如用於 `<Text>` 元素），則需要 `RCTText` 子規格。如果您需要 `Image` 函式庫（例如用於 `<Image>` 元素），則需要 `RCTImage` 子規格。

您可以在 `Podfile` 檔案中指定應用程式將依賴哪些子規格。建立 `Podfile` 最簡單的方法是在專案的 `/ios` 子資料夾中執行 CocoaPods 的 `init` 指令：

```shell
$ pod init
```

`Podfile` 將包含一個樣板設定，您可以根據整合需求進行調整。

> `Podfile` 的版本會根據您的 `react-native` 版本而變化。請參考 https://react-native-community.github.io/upgrade-helper/ 以確定您應該使用的特定 `Podfile` 版本。

最終，您的 `Podfile` 應該看起來類似這樣：

```
source 'https://github.com/CocoaPods/Specs.git'

# Required for Swift apps
platform :ios, '8.0'
use_frameworks!

# The target name is most likely the name of your project.
target 'swift-2048' do

  # Your 'node_modules' directory is probably in the root of your project,
  # but if not, adjust the `:path` accordingly
  pod 'React', :path => '../node_modules/react-native', :subspecs => [
    'Core',
    'CxxBridge', # Include this for RN >= 0.47
    'DevSupport', # Include this to enable In-App Devmenu if RN >= 0.43
    'RCTText',
    'RCTNetwork',
    'RCTWebSocket', # needed for debugging
    # Add any other subspecs you want to use in your project
  ]
  # Explicitly include Yoga if you are using RN >= 0.42.0
  pod "Yoga", :path => "../node_modules/react-native/ReactCommon/yoga"

  # Third party deps podspec link
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

> 如果此步驟因提及 `xcrun` 的錯誤而失敗，請確保在 Xcode 的 **Preferences > Locations** 中已指定 Command Line Tools。

> 如果您收到類似「_The `swift-2048 [Debug]` target overrides the `FRAMEWORK_SEARCH_PATHS` build setting defined in `Pods/Target Support Files/Pods-swift-2048/Pods-swift-2048.debug.xcconfig`. This can lead to problems with the CocoaPods installation_」的警告，請確保 `Build Settings` 中的 `Framework Search Paths` 對於 `Debug` 和 `Release` 僅包含 `$(inherited)`。

### 程式碼整合

現在我們將實際修改原生 iOS 應用程式以整合 React Native。對於我們的 2048 範例應用程式，我們將在 React Native 中加入一個「高分」畫面。

#### React Native 元件

我們將編寫的第一段程式碼是用於新「高分」畫面的實際 React Native 程式碼，該畫面將整合到我們的應用程式中。

##### 1. 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且始終是必需的。它可以是一個小檔案，`require` 其他屬於您的 React Native 元件或應用程式的檔案，也可以包含所需的所有程式碼。在我們的案例中，我們將把所有內容都放在 `index.js` 中。

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

> `RNHighScores` 是您的模組名稱，當您從 iOS 應用程式中加入一個視圖到 React Native 時將使用此名稱。

#### 魔法：`RCTRootView`

現在您的 React Native 元件已透過 `index.js` 建立，您需要將該元件加入到一個新的或現有的 `ViewController` 中。最簡單的方法是選擇性地建立一個通往您的元件的事件路徑，然後將該元件加入到現有的 `ViewController` 中。

我們將把 React Native 元件與一個新的原生視圖綁定，這個視圖在 `ViewController` 中實際包含它，稱為 `RCTRootView`。

##### 1. 建立事件路徑

你可以在主遊戲選單中添加一個新連結，導向「高分」的 React Native 頁面。

![事件路徑](/docs/assets/react-native-add-react-native-integration-link.png)

##### 2. 事件處理器

我們現在將從選單連結添加一個事件處理器。一個方法將被添加到你的應用程式的主 `ViewController` 中。這就是 `RCTRootView` 發揮作用的地方。

當你構建一個 React Native 應用程式時，你會使用 [Metro 打包工具][metro] 來創建一個 `index.bundle`，它將由 React Native 伺服器提供。在 `index.bundle` 中將包含我們的 `RNHighScore` 模組。因此，我們需要將 `RCTRootView` 指向 `index.bundle` 資源的位置（通過 `NSURL`）並將其與模組綁定。

為了調試目的，我們將記錄事件處理器被調用的情況。然後，我們將創建一個字符串，其中包含存在於 `index.bundle` 中的 React Native 代碼的位置。最後，我們將創建主 `RCTRootView`。請注意，我們提供了 `RNHighScores` 作為 `moduleName`，這是我們在編寫 React Native 元件的代碼時[上面](#the-react-native-component)創建的。

首先 `import` `React` 庫。

```jsx
import React
```

> `initialProperties` 在這裡是為了說明目的，以便我們有一些數據用於我們的高分屏幕。在我們的 React Native 元件中，我們將使用 `this.props` 來訪問這些數據。

```swift
@IBAction func highScoreButtonTapped(sender : UIButton) {
  NSLog("Hello")
  let jsCodeLocation = URL(string: "http://localhost:8081/index.bundle?platform=ios")
  let mockData:NSDictionary = ["scores":
      [
          ["name":"Alex", "value":"42"],
          ["name":"Joel", "value":"10"]
      ]
  ]

  let rootView = RCTRootView(
      bundleURL: jsCodeLocation,
      moduleName: "RNHighScores",
      initialProperties: mockData as [NSObject : AnyObject],
      launchOptions: nil
  )
  let vc = UIViewController()
  vc.view = rootView
  self.present(vc, animated: true, completion: nil)
}
```

> 請注意，`RCTRootView bundleURL` 會啟動一個新的 JSC 虛擬機。為了節省資源並簡化你的原生應用中不同部分的 RN 視圖之間的通信，你可以有多個由 React Native 驅動的視圖，這些視圖與單個 JS 運行時相關聯。要做到這一點，不要使用 `RCTRootView bundleURL`，而是使用 [`RCTBridge initWithBundleURL`](https://github.com/facebook/react-native/blob/0.70-stable/React/Base/RCTBridge.h#L89) 來創建一個橋接器，然後使用 `RCTRootView initWithBridge`。

> 當將你的應用程式移動到生產環境時，`NSURL` 可以指向磁盤上的預打包文件，例如 `let mainBundle = NSBundle(URLForResource: "main" withExtension:"jsbundle")`。你可以使用 `node_modules/react-native/scripts/` 中的 `react-native-xcode.sh` 腳本來生成該預打包文件。

##### 3. 連接

將主選單中的新連結連接到新添加的事件處理器方法。

![事件路徑](/docs/assets/react-native-add-react-native-integration-wire-up.png)

> 其中一個更簡單的方法是打開故事板中的視圖，然後右鍵單擊新連結。選擇類似 `Touch Up Inside` 的事件，將其拖到故事板中，然後從提供的列表中選擇創建的方法。

### 測試你的整合

你現在已經完成了將 React Native 與當前應用程式整合的所有基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來構建 `index.bundle` 包，並在 `localhost` 上運行的伺服器來提供它。

##### 1. 添加應用程式傳輸安全例外

Apple 已經阻止了隱式的明文 HTTP 資源加載。因此，我們需要在項目的 `Info.plist`（或等效文件）中添加以下內容。

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

> 應用程式傳輸安全對你的用戶有好處。確保在發布應用程式之前重新啟用它。

##### 2. 運行打包工具

要運行你的應用程式，你需要首先啟動開發伺服器。為此，請在你的 React Native 項目的根目錄中運行以下命令：

```shell
$ npm start
```

##### 3. 執行應用程式

若您使用 Xcode 或其他慣用的編輯器，請如常建置並執行您的原生 iOS 應用程式。或者，您也可以透過命令列執行以下指令來啟動應用程式：

```
# From the root of your project
$ npx react-native run-ios
```

在我們的範例應用程式中，您應會看到「高分榜」的連結，點擊後即可看到 React Native 元件的渲染畫面。

以下是 _原生_ 應用程式的主畫面：

![主畫面](/docs/assets/react-native-add-react-native-integration-example-home-screen.png)

以下是 _React Native_ 的高分榜畫面：

![高分榜](/docs/assets/react-native-add-react-native-integration-example-high-scores.png)

> 若執行應用程式時遇到模組解析問題，請參閱 [此 GitHub 議題](https://github.com/facebook/react-native/issues/4968) 以獲取相關資訊與可能的解決方案。[此則留言](https://github.com/facebook/react-native/issues/4968#issuecomment-220941717) 似乎是目前最新的解決方案。

### 接下來呢？

至此，您可以繼續如常開發應用程式。請參考我們的 [除錯指南](debugging) 與 [部署文件](running-on-device) 以深入瞭解 React Native 的開發工作流程。

[metro]: https://metrobundler.dev/