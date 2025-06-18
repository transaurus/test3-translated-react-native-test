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

## 必要條件

請依照[環境設定指南](environment-setup)中的 React Native CLI 快速入門，配置 iOS 版 React Native 應用的開發環境。

### 1. 設定目錄結構

為確保流程順暢，請為整合專案新建資料夾，並將現有 iOS 專案複製到 `/ios` 子資料夾中。

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

接著安裝 `react` 和 `react-native` 套件。開啟終端機，導航至包含 `package.json` 的目錄並執行：

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

這將輸出類似以下的訊息（請向上滾動 yarn 輸出以查看）：

> 警告："`react-native@0.52.2`" 有未滿足的 peer 依賴項 "`react@16.2.0`"。

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

Yarn 已建立新的 `/node_modules` 資料夾，用於存放專案建置所需的所有 JavaScript 依賴項。

請將 `node_modules/` 加入你的 `.gitignore` 檔案。

### 3. 安裝 CocoaPods

[CocoaPods](http://cocoapods.org) 是 iOS 和 macOS 開發的套件管理工具，我們用它將 React Native 框架程式碼本地化加入現有專案。

建議透過 [Homebrew](http://brew.sh/) 安裝 CocoaPods：

```shell
$ brew install cocoapods
```

> 技術上雖可不使用 CocoaPods，但這將導致需手動添加函式庫與連結器設定，使流程過度複雜化。

## 將 React Native 加入應用

假設[待整合應用](https://github.com/JoelMarcey/swift-2048)為 [2048](https://en.wikipedia.org/wiki/2048_%28video_game%29) 遊戲。下圖為未整合 React Native 時的原生應用主選單畫面。

![整合前的畫面](/docs/assets/react-native-existing-app-integration-ios-before.png)

### Xcode 指令列工具

請安裝指令列工具。在 Xcode 選單中選擇 **Settings... (或 Preferences...)**，進入 Locations 面板後，從 Command Line Tools 下拉選單中選擇最新版本進行安裝。

![Xcode 指令列工具](/docs/assets/GettingStartedXcodeCommandLineTools.png)

### 配置 CocoaPods 依賴項

在整合 React Native 前，需先決定要整合框架中的哪些部分。我們將使用 CocoaPods 來指定應用所依賴的這些「子規格」(subspecs)。

支援的 `subspec` 清單可在 [`/node_modules/react-native/React.podspec`](https://github.com/facebook/react-native/blob/main/packages/react-native/React.podspec) 中找到。這些子規格通常以功能命名。例如，您通常會需要 `Core` 子規格，它會提供 `AppRegistry`、`StyleSheet`、`View` 和其他 React Native 核心函式庫。如果您想加入 React Native 的 `Text` 函式庫（例如用於 `<Text>` 元素），則需要 `RCTText` 子規格。若需要 `Image` 函式庫（例如用於 `<Image>` 元素），則需 `RCTImage` 子規格。

您可以在 `Podfile` 檔案中指定應用程式依賴哪些子規格。建立 `Podfile` 最簡單的方式是在專案的 `/ios` 子資料夾中執行 CocoaPods 的 `init` 指令：

```shell
$ pod init
```

`Podfile` 會包含一個預設範本，您可根據整合需求進行調整。

> `Podfile` 的版本會根據您使用的 `react-native` 版本而異。請參考 https://react-native-community.github.io/upgrade-helper/ 以確認應使用的特定 `Podfile` 版本。

最終，您的 `Podfile` 應類似以下範例：
[Podfile 範本](https://github.com/facebook/react-native/blob/main/packages/react-native/template/ios/Podfile)

建立 `Podfile` 後，即可安裝 React Native pod。

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

> 若因提及 `xcrun` 的錯誤而失敗，請確認 Xcode 中 **設定...（或偏好設定...）> 位置** 已指定命令列工具。

> 若收到類似「_`swift-2048 [Debug]` 目標覆寫了 `Pods/Target Support Files/Pods-swift-2048/Pods-swift-2048.debug.xcconfig` 中定義的 `FRAMEWORK_SEARCH_PATHS` 建置設定。這可能導致 CocoaPods 安裝問題_」的警告，請確認 `建置設定` 中 `Debug` 和 `Release` 的 `Framework 搜尋路徑` 僅包含 `$(inherited)`。

### 程式碼整合

現在我們將實際修改原生 iOS 應用程式以整合 React Native。針對我們的 2048 範例應用程式，我們將新增一個 React Native 的「高分」畫面。

#### React Native 元件

我們要編寫的第一段程式碼是新「高分」畫面的實際 React Native 程式碼，它將被整合到我們的應用程式中。

##### 1. 建立 `index.js` 檔案

首先，在 React Native 專案的根目錄建立一個空的 `index.js` 檔案。

`index.js` 是 React Native 應用程式的起點，且必須存在。它可以是一個小檔案，用於 `require` 其他屬於 React Native 元件或應用程式的檔案，也可以包含所需的所有程式碼。在我們的案例中，我們會將所有內容放在 `index.js` 中。

##### 2. 加入您的 React Native 程式碼

在您的 `index.js` 中建立元件。在我們的範例中，我們將在一個帶樣式的 `<View>` 中加入一個 `<Text>` 元件。

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

> `RNHighScores` 是您的模組名稱，當您從 iOS 應用程式中新增視圖到 React Native 時會使用此名稱。

#### 魔法：`RCTRootView`

現在您的 React Native 元件已透過 `index.js` 建立，您需要將該元件新增到新的或現有的 `ViewController` 中。最簡單的方法是選擇性地建立一個通往元件的路徑，然後將該元件加入現有的 `ViewController`。

我們將把 React Native 元件與 `ViewController` 中的新原生視圖綁定，該視圖將實際包含稱為 `RCTRootView` 的元件。

##### 1. 建立事件路徑

您可以在主遊戲選單中添加一個新連結，導向「高分」React Native 頁面。

![事件路徑](/docs/assets/react-native-add-react-native-integration-link.png)

##### 2. 事件處理器

現在我們將從選單連結添加一個事件處理器。一個方法將被添加到應用程式的主 `ViewController` 中。這就是 `RCTRootView` 發揮作用的地方。

當您構建 React Native 應用程式時，您會使用 [Metro 打包工具][metro] 創建一個 `index.bundle`，該文件將由 React Native 伺服器提供。在 `index.bundle` 內部將是我們的 `RNHighScore` 模組。因此，我們需要將 `RCTRootView` 指向 `index.bundle` 資源的位置（通過 `NSURL`）並將其與模組綁定。

為了調試目的，我們將記錄事件處理器被調用的情況。然後，我們將創建一個字符串，其中包含存在於 `index.bundle` 中的 React Native 代碼的位置。最後，我們將創建主 `RCTRootView`。請注意，我們提供了 `RNHighScores` 作為 `moduleName`，這是我們在編寫 React Native 元件代碼時[上面](#the-react-native-component)創建的。

首先 `import` `React` 庫。

```jsx
import React
```

> `initialProperties` 在此處用於說明目的，以便我們有一些數據用於高分屏幕。在我們的 React Native 元件中，我們將使用 `this.props` 來訪問這些數據。

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

> 請注意，`RCTRootView bundleURL` 會啟動一個新的 JSC 虛擬機。為了節省資源並簡化原生應用中不同部分的 RN 視圖之間的通信，您可以擁有多個由 React Native 驅動的視圖，這些視圖與單個 JS 運行時關聯。要做到這一點，請使用 [`RCTBridge initWithBundleURL`](https://github.com/facebook/react-native/blob/main/packages/react-native/React/Base/RCTBridge.h#94) 創建一個橋接器，然後使用 `RCTRootView initWithBridge` 而不是 `RCTRootView bundleURL`。

> 當將應用程式移至生產環境時，`NSURL` 可以指向磁盤上的預打包文件，例如通過 `let mainBundle = NSBundle(URLForResource: "main" withExtension:"jsbundle")`。您可以使用 `node_modules/react-native/scripts/` 中的 `react-native-xcode.sh` 腳本來生成該預打包文件。

##### 3. 連接

將主選單中的新連結連接到新添加的事件處理器方法。

![事件路徑](/docs/assets/react-native-add-react-native-integration-wire-up.png)

> 其中一種較簡單的方法是打開故事板中的視圖並右鍵單擊新連結。選擇類似 `Touch Up Inside` 的事件，將其拖到故事板，然後從提供的列表中選擇創建的方法。

##### 3. 窗口引用

在您的 AppDelegate.swift 文件中添加一個窗口引用。最終，您的 AppDelegate 應該看起來類似於這樣：

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

### 測試您的集成

您現在已經完成了將 React Native 與當前應用程式集成的基本步驟。現在我們將啟動 [Metro 打包工具][metro] 來構建 `index.bundle` 包，並在 `localhost` 上運行伺服器來提供它。

##### 1. 添加應用傳輸安全例外

Apple 已阻止隱式的明文 HTTP 資源加載。因此，我們需要在項目的 `Info.plist`（或等效）文件中添加以下內容。

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

> 應用傳輸安全對您的用戶有好處。請確保在將應用程式發布到生產環境之前重新啟用它。

##### 2. 執行打包伺服器

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

以下是 _原生_ 應用程式的主畫面：

![主畫面](/docs/assets/react-native-add-react-native-integration-example-home-screen.png)

以下是 _React Native_ 的高分榜畫面：

![高分榜](/docs/assets/react-native-add-react-native-integration-example-high-scores.png)

> 如果在運行應用程式時遇到模組解析問題，請參閱 [此 GitHub 議題](https://github.com/facebook/react-native/issues/4968) 以獲取資訊和可能的解決方案。[此評論](https://github.com/facebook/react-native/issues/4968#issuecomment-220941717) 似乎是目前最新的解決方案。

### 接下來呢？

此時，您可以像平常一樣繼續開發您的應用程式。請參考我們的 [除錯](debugging) 和 [部署](running-on-device) 文件，以了解更多關於使用 React Native 的資訊。

[metro]: https://metrobundler.dev/