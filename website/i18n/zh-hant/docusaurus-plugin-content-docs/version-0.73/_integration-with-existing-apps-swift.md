import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 核心概念

將 React Native 元件整合至 iOS 應用的關鍵在於：

1. 設定 React Native 依賴項與目錄結構
2. 了解應用中將使用的 React Native 元件
3. 使用 CocoaPods 添加這些元件作為依賴項
4. 用 JavaScript 開發 React Native 元件
5. 在 iOS 應用中添加 `RCTRootView`，該視圖將作為 React Native 元件的容器
6. 啟動 React Native 伺服器並運行原生應用
7. 驗證應用的 React Native 功能是否正常運作

## 必要條件

請依照[環境設定指南](environment-setup)中的 React Native CLI 快速入門，配置開發環境以構建 iOS 版 React Native 應用。

### 1. 設定目錄結構

為確保流程順暢，請為整合專案新建資料夾，並將現有 iOS 專案複製到 `/ios` 子資料夾中。

### 2. 安裝 JavaScript 依賴項

進入專案根目錄，創建包含以下內容的 `package.json` 檔案：

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

接著安裝 `react` 和 `react-native` 套件。開啟終端機或命令提示字元，導航至含 `package.json` 的目錄並執行：

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

這將輸出類似以下的訊息（請向上滾動安裝命令輸出以查看）：

> 警告："`react-native@0.52.2`" 有未滿足的對等依賴 "`react@16.2.0`"。

這是正常的，表示我們還需要安裝 React：

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

安裝過程會創建新的 `/node_modules` 資料夾，此資料夾存放構建專案所需的所有 JavaScript 依賴項。

請將 `node_modules/` 加入你的 `.gitignore` 檔案。

### 3. 安裝 CocoaPods

[CocoaPods](https://cocoapods.org) 是 iOS 和 macOS 開發的套件管理工具，我們用它將實際的 React Native 框架代碼本地化添加到當前專案中。

建議使用 [Homebrew](https://brew.sh/) 安裝 CocoaPods：

```shell
$ brew install cocoapods
```

> 技術上可以不使用 CocoaPods，但這需要手動添加函式庫與連結器設定，會使流程過度複雜化。

## 將 React Native 添加至應用

假設[待整合應用](https://github.com/JoelMarcey/swift-2048)是 [2048](https://en.wikipedia.org/wiki/2048_%28video_game%29) 遊戲。下圖為未整合 React Native 時的原生應用主選單樣貌：

![整合前畫面](/docs/assets/react-native-existing-app-integration-ios-before.png)

### Xcode 命令行工具

請安裝命令行工具。在 Xcode 選單中選擇 **Settings... (或 Preferences...)**，進入 Locations 面板，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 命令行工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### 配置 CocoaPods 依賴項

在整合 React Native 至應用前，需決定要整合框架的哪些部分。我們將使用 CocoaPods 來指定應用所依賴的這些「子規格」(subspecs)。

支援的 `subspec` 清單可在 [`/node_modules/react-native/React.podspec`](https://github.com/facebook/react-native/blob/main/packages/react-native/React.podspec) 中找到。這些子規格通常以功能命名。例如，您通常會需要 `Core` 子規格，這將為您提供 `AppRegistry`、`StyleSheet`、`View` 和其他核心 React Native 函式庫。如果您想加入 React Native 的 `Text` 函式庫（例如用於 `<Text>` 元素），則需要 `RCTText` 子規格。如果您需要 `Image` 函式庫（例如用於 `<Image>` 元素），則需要 `RCTImage` 子規格。

您可以在 `Podfile` 檔案中指定應用程式將依賴哪些子規格。建立 `Podfile` 最簡單的方法是在專案的 `/ios` 子資料夾中執行 CocoaPods 的 `init` 指令：

```shell
$ pod init
```

`Podfile` 將包含一個樣板設定，您可以根據整合需求進行調整。

> `Podfile` 的版本會根據您使用的 `react-native` 版本而變化。請參考 https://react-native-community.github.io/upgrade-helper/ 以確認您應使用的特定 `Podfile` 版本。

最終，您的 `Podfile` 應類似以下內容：
[Podfile 範本](https://github.com/facebook/react-native/blob/main/packages/react-native/template/ios/Podfile)

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

> 如果因提及 `xcrun` 的錯誤而失敗，請確認在 Xcode 的 **設定...（或偏好設定...）> 位置** 中已指定命令列工具。

> 如果您收到類似「_`swift-2048 [Debug]` 目標覆寫了 `Pods/Target Support Files/Pods-swift-2048/Pods-swift-2048.debug.xcconfig` 中定義的 `FRAMEWORK_SEARCH_PATHS` 建置設定。這可能會導致 CocoaPods 安裝問題_」的警告，請確認 `建置設定` 中的 `Framework Search Paths` 在 `Debug` 和 `Release` 下僅包含 `$(inherited)`。

### 程式碼整合

現在我們將實際修改原生 iOS 應用程式以整合 React Native。對於我們的 2048 範例應用程式，我們將加入一個以 React Native 實作的「高分」畫面。

#### React Native 元件

我們要編寫的第一段程式碼是新「高分」畫面的實際 React Native 程式碼，該畫面將整合到我們的應用程式中。

##### 1. 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄中建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且始終是必需的。它可以是一個小檔案，用於 `require` 其他屬於您的 React Native 元件或應用程式的檔案，也可以包含所需的所有程式碼。在我們的案例中，我們將把所有內容放在 `index.js` 中。

##### 2. 加入您的 React Native 程式碼

在您的 `index.js` 中建立元件。在我們的範例中，我們將在一個帶樣式的 `<View>` 中加入一個 `<Text>` 元件

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

> `RNHighScores` 是您的模組名稱，當您從 iOS 應用程式中加入一個視圖到 React Native 時會使用到它。

#### 魔法：`RCTRootView`

現在您的 React Native 元件已透過 `index.js` 建立，您需要將該元件加入到新的或現有的 `ViewController` 中。最簡單的方法是選擇性地建立一個通往您元件的事件路徑，然後將該元件加入到現有的 `ViewController` 中。

我們將把 React Native 元件與 `ViewController` 中的新原生視圖綁定，該視圖將實際包含稱為 `RCTRootView` 的元件。

##### 1. 建立事件路徑

您可以在主遊戲選單上新增一個連結，導向「高分」React Native 頁面。

![事件路徑](/docs/assets/react-native-add-react-native-integration-link.png)

##### 2. 事件處理器

現在我們將從選單連結新增一個事件處理器。一個方法將被添加到應用程式的主 `ViewController` 中。這就是 `RCTRootView` 發揮作用的地方。

當您建構 React Native 應用程式時，您會使用 [Metro 打包工具][metro] 來建立一個 `index.bundle`，該檔案將由 React Native 伺服器提供。在 `index.bundle` 內部將是我們的 `RNHighScore` 模組。因此，我們需要將 `RCTRootView` 指向 `index.bundle` 資源的位置（透過 `NSURL`）並將其與模組綁定。

為了除錯目的，我們將記錄事件處理器被調用的情況。然後，我們將建立一個字串，包含存在於 `index.bundle` 中的 React Native 程式碼位置。最後，我們將建立主要的 `RCTRootView`。請注意，我們提供了 `RNHighScores` 作為 `moduleName`，這是我們在編寫 React Native 元件程式碼時[上方](#the-react-native-component)建立的。

首先 `import` `React` 函式庫。

```jsx
import React
```

> `initialProperties` 在此用於說明目的，以便我們的高分畫面有一些資料。在我們的 React Native 元件中，我們將使用 `this.props` 來存取這些資料。

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

> 請注意，`RCTRootView bundleURL` 會啟動一個新的 JSC 虛擬機器。為了節省資源並簡化原生應用程式中不同部分的 RN 視圖之間的通信，您可以擁有多個由 React Native 驅動的視圖，這些視圖與單一 JS 運行時相關聯。要做到這一點，請使用 [`RCTBridge initWithBundleURL`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridge.h#94) 來建立一個橋接器，然後使用 `RCTRootView initWithBridge` 而不是 `RCTRootView bundleURL`。

> 當將您的應用程式移至生產環境時，`NSURL` 可以指向磁碟上的預打包檔案，例如 `let mainBundle = NSBundle(URLForResource: "main" withExtension:"jsbundle")`。您可以使用 `node_modules/react-native/scripts/` 中的 `react-native-xcode.sh` 腳本來生成該預打包檔案。

##### 3. 連接

將主選單中的新連結連接到新添加的事件處理器方法。

![事件路徑](/docs/assets/react-native-add-react-native-integration-wire-up.png)

> 其中一種較簡單的方法是打開故事板中的視圖，然後右鍵點擊新連結。選擇類似 `Touch Up Inside` 的事件，將其拖曳到故事板，然後從提供的列表中選擇已建立的方法。

##### 3. 視窗參考

在您的 AppDelegate.swift 檔案中添加一個視窗參考。最終，您的 AppDelegate 應該看起來類似這樣：

```swift
import UIKit

@main
class AppDelegate: UIResponder, UIApplicationDelegate {

    // Add window reference
    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        return true
    }

    ....
}
```

### 測試您的整合

您現在已經完成了將 React Native 與當前應用程式整合的所有基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來建構 `index.bundle` 套件，並啟動運行在 `localhost` 上的伺服器來提供它。

##### 1. 添加應用程式傳輸安全性例外

Apple 已阻止隱式的明文 HTTP 資源加載。因此，我們需要將以下內容添加到專案的 `Info.plist`（或等效）檔案中。

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

> 應用程式傳輸安全性對您的用戶有好處。請確保在發布應用程式之前重新啟用它。

##### 2. 執行封裝伺服器

要運行您的應用程式，首先需要啟動開發伺服器。為此，請在 React Native 專案的根目錄中執行以下命令：

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

##### 3. 運行應用程式

如果您使用 Xcode 或偏好的編輯器，請像平常一樣建置並運行您的原生 iOS 應用程式。或者，您也可以從 React Native 專案的根目錄使用以下命令來運行應用程式：

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

在我們的範例應用程式中，您應該會看到「高分榜」的連結，點擊後將看到 React Native 元件的渲染結果。

這是 _原生_ 應用程式的主畫面：

![主畫面](/docs/assets/react-native-add-react-native-integration-example-home-screen.png)

這是 _React Native_ 的高分榜畫面：

![高分榜](/docs/assets/react-native-add-react-native-integration-example-high-scores.png)

> 如果在運行應用程式時遇到模組解析問題，請參閱 [此 GitHub 議題](https://github.com/facebook/react-native/issues/4968) 以獲取資訊和可能的解決方案。[此評論](https://github.com/facebook/react-native/issues/4968#issuecomment-220941717) 似乎是目前最新的解決方案。

### 接下來呢？

此時您可以繼續照常開發應用程式。請參考我們的 [除錯](debugging) 和 [部署](running-on-device) 文件，以了解更多關於使用 React Native 的資訊。

[metro]: https://facebook.github.io/metro/