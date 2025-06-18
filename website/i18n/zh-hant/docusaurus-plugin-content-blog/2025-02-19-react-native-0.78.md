---
title: 'React Native 0.78 - React 19 and more'
authors: [vonovak, shubham, fabriziocucci, cipolleschi]
tags: [engineering]
date: 2025-02-19
---

# React Native 0.78 - React 19 與更多新功能

我們很高興今天發布 React Native 0.78！

此版本在 React Native 中整合了 React 19，並包含其他重要功能，例如原生支援 Android 向量圖形繪製物件，以及針對 iOS 更好的棕地(brownfield)整合方案。

### 重點更新

- [React 19](/blog/2025/02/19/react-native-0.78#react-19)
- [邁向更小更快的版本發布](/blog/2025/02/19/react-native-0.78#towards-smaller-and-faster-releases)
- [Metro 中 JavaScript 日誌的選擇加入功能](/blog/2025/02/19/react-native-0.78#opt-in-for-javascript-logs-in-metro)
- [新增支援 Android XML 繪製物件](/blog/2025/02/19/react-native-0.78#added-support-for-android-xml-drawables)
- [iOS 上的 ReactNativeFactory](/blog/2025/02/19/react-native-0.78#reactnativefactory-on-ios)

<!--truncate-->

## 重點功能

### React 19

React 19 現已支援 React Native！

React 19 需要更新您的應用程式，因為我們從 React 18 引入了一些變更。例如移除了 `propTypes` 等 API，您需要調整應用程式以相容新版本。

請遵循我們的逐步[升級指南](https://react.dev/blog/2024/04/25/react-19-upgrade-guide)將應用程式遷移至 React 19。

完成遷移後，您將能使用 React 的所有新功能，包括（但不限於）：

- **[Actions](https://react.dev/blog/2024/12/05/react-19#actions):** 這些是使用非同步轉換的函數。非同步轉換會自動為您管理數據提交：處理等待狀態、樂觀更新、錯誤處理等。
- **[useActionState](https://react.dev/reference/react/useActionState):** 基於 Actions 構建的實用鉤子。它接收函數並返回可調用的包裝 Action，調用時會返回該 Action 的最新結果及其 `pending` 狀態。
- **[useOptimistic](https://react.dev/reference/react/useOptimistic):** 新鉤子，簡化在非同步請求進行時樂觀顯示最終狀態的流程。若請求出錯，React 會自動回退至先前值。
- **[`use`](https://react.dev/reference/react/use):** 新 API，允許在渲染期間存取資源。現在您可以用 `use` 讀取 promise 或 context，React 將暫停(Suspend)直到它們解析完成。
- **[將 `ref` 作為 `props` 傳遞](https://react.dev/blog/2024/12/05/react-19#ref-as-a-prop):** 現在可像常規 prop 一樣傳遞 `ref`。函數組件不再需要 `forwardRef`，您可立即遷移現有組件。
- 以及其他多項功能

完整新功能清單請參閱 [React 19 發布部落格](https://react.dev/blog/2024/12/05/react-19)。

#### React 編譯器

React 編譯器是透過自動應用記憶化(memoization)來優化 React 應用的建置時工具。雖然開發者可以手動使用 `useMemo`、`useCallback` 和 `React.memo` 等 API 來避免應用程式中未變更部分的不必要重新計算，但也可能遺漏或誤用這些優化。React 編譯器透過其對 JavaScript 和 [React 規則](https://react.dev/reference/rules)的理解，自動記憶組件與鉤子中的值或值群組來解決此問題。

本次發布簡化了在React Native應用中啟用React Compiler的流程。[在先前版本](https://react.dev/learn/react-compiler#using-react-compiler-with-react-17-or-18)中，您需要安裝兩個套件：編譯器及其運行時環境。安裝完成後，還需配置Babel插件以透過Metro啟用React Compiler。

現在，您只需安裝編譯器本身並配置Babel插件即可。具體啟用方式可參閱我們的逐步[指南](https://react.dev/learn/react-compiler#usage-with-babel)。

若要驗證編譯器是否正常運作，可開啟React Native開發者工具：在元件檢查器中，被記憶化的元件會標註`Memo ✨`標籤。

若想深入瞭解React Compiler，以下資源可供參考：

- [React Compiler](https://react.dev/learn/react-compiler) 文件
- [React Compiler深度解析](https://www.youtube.com/watch?v=uA_PVyZP7AI)

### 邁向更輕量、更快速的發布週期

我們正在調整發布流程，計劃在2025年更頻繁地推出穩定的React Native版本。

新版將減少重大變更的數量，使版本升級更為簡便。加速發布也意味著內部修復的錯誤能更快推送給開發者，讓您及早使用React Native的最新功能。

我們相信此新模式將使整個React Native生態受益——更少的破壞性變更意味著框架更加穩定可靠。

### 選擇性啟用Metro的JavaScript日誌功能

我們新增了選項來恢復透過Metro開發伺服器串流的JavaScript日誌功能（[0.77版已移除](/blog/2025/01/21/version-0.77#removal-of-consolelog-streaming-in-metro)）。此調整回應了社群反饋，同時評估了現有替代方案的成熟度。

請使用新參數`--client-logs`啟用此功能，也可透過npm script設定別名以簡化操作。

```sh
npx @react-native-community/cli start --client-logs
```

Metro的日誌串流功能未來仍會移除，目前預設保持關閉狀態。但我們將提供更長的遷移期讓開發者適應此變更。

此更新也將包含在即將發布的0.77.1小版本中。

### 新增Android XML繪圖資源支援

React Native 0.78引入新機制，可透過[XML資源](https://developer.android.com/guide/topics/resources/drawable-resource)載入Android平台的圖示與圖形元素。您現在能使用[向量繪圖](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources)實現無損縮放，或透過[形狀繪圖](https://developer.android.com/guide/topics/resources/drawable-resource#Shape)繪製基礎裝飾元素。這些功能皆整合於現有的`Image`元件，只需在`source`屬性中參照XML資源（參照[靜態資源](/docs/next/images#static-image-resources)的用法）。採用XML資源替代點陣圖還能有效縮減應用體積，並提升不同DPI螢幕的渲染品質。

```js
// via require
<Image
  source={require('./img/my_icon.xml')}
  style={{width: 40, height: 40}}
/>;

// or via import
import MyIcon from './img/my_icon.xml';
<Image source={MyIcon} style={{width: 40, height: 40}} />;
```

#### 效能與品質

[與其他圖片類型相同](/docs/next/images#off-thread-decoding)，Android的XML資源會在非主線程載入與解析，避免畫面卡頓。這意味著資源不會立即顯示，但載入過程也不會阻塞用戶操作。當需要同時渲染大量圖示時，非同步解碼尤其重要。內部應用實測顯示，採用Android向量繪圖可帶來顯著的效能提升。

使用向量可繪製資源（vector drawables）這類資源類型，是完美呈現不失真圖像的方式，且能縮小APK檔案體積——因為您無需為每種螢幕密度包含對應的圖片格式。此外，向量可繪製資源一旦載入後會從記憶體複製，因此若您多次渲染相同圖示，它們將能同時顯示。

#### 權衡取捨

需注意的是，可繪製XML資源並非完美，使用時存在以下限制：

- 必須在Android應用程式建構階段被引用。這些資源會透過[Android資產打包工具（AAPT）](https://developer.android.com/tools/aapt2)將原始XML轉換為二進制XML。Android不支援載入原始XML檔案，[此為已知限制](https://issuetracker.google.com/issues/62435069)。
- 無法透過Metro從網路載入。若變更XML資源的目錄或名稱，每次均需重新建構Android應用程式。
- 沒有預設尺寸。預設會以0x0大小顯示，您需明確指定寬高才能呈現。
- 執行階段無法完全自訂：僅能控制尺寸或整體色調，但無法調整資源內部元素的屬性（如線條寬度、邊框圓角或顏色）。這類客製化需準備不同變體的XML資源。

:::info
Android的向量可繪製資源並非`react-native-svg`等函式庫的完全替代方案。它們專為Android設計，無法在iOS上運作。若需跨平台使用相同SVG，仍需依賴`react-native-svg`。向量可繪製資源僅以犧牲客製化為代價換取效能優勢。
:::

### iOS上的ReactNativeFactory

React Native 0.78強化了iOS平台的整合度。

此版本新增名為`RCTReactNativeFactory`的類別，讓您無需透過AppDelegate即可建立React Native實例。例如，現在您能在ViewController中建立新版React Native，大幅簡化與Brownfield應用的整合流程。

假設您想在應用程式的視圖控制器中顯示React Native視圖。從React Native 0.78開始，按照[此指南](/docs/next/integration-with-existing-apps?language=apple#1-set-up-directory-structure)安裝所有依賴項後，只需加入以下代碼：

```diff

+import React
+import React_RCTAppDelegate

public class ViewController {

+  var reactNativeFactory: RCTReactNativeFactory?
+  var reactNativeDelegate: ReactNativeDelegate?

  public func viewdidLoad() {
    super.viewDidLoad()
    // …
+ reactNativeDelegate = ReactNativeDelegate()
+ reactNativeFactory = RCTReactNativeFactory(delegate: reactNativeDelegate!)
+ view = reactNativeFactory.rootViewFactory.view(withModuleName: "<your module name>")
  }

}

+class ReactNativeDelegate: RCTDefaultReactNativeFactoryDelegate {

+  override func sourceURL(for bridge: RCTBridge) -> URL? {
+    self.bundleURL()
+  }
+
+  override func bundleURL() -> URL? {
+    #if DEBUG
+    RCTBundleURLProvider.sharedSettings().jsBundleURL(forBundleRoot: "index")
+    #else
+    Bundle.main.url(forResource: "main", withExtension: "jsbundle")
+    #endif
+  }
+}

```

當您導航至該視圖控制器時，React Native將立即載入。

此代碼會建立`RCTReactNativeFactory`、指派委派實例，並要求其為React Native視圖建立`rootView`。

下方定義的委派會覆寫`sourceURL`與`bundleURL`屬性，告知React Native從何處載入JS套件至視圖中。

## 其他重大變更

### 通用項目

- React Native DevTools
  - 移除FuseboxClient CDP領域
- 代碼生成器（Codegen）
  - 分離元件陣列類型與指令陣列類型

### Android

- 可空性變更：將`RootView`遷移至Kotlin導致參數類型從可空變更為非可空。
- 以下類別已從公開改為內部或移除，無法再存取：
  - `com.facebook.react.bridge.GuardedResultAsyncTask`
  - `com.facebook.react.uimanager.ComponentNameResolver`
  - `com.facebook.react.uimanager.FabricViewStateManager`
  - `com.facebook.react.views.text.frescosupport.FrescoBasedReactTextInlineImageViewManager`

### iOS

- 將圖片載入事件的尺寸資訊從邏輯尺寸改為像素（僅影響舊架構）

## 致謝

React Native 0.78 版本包含了來自 87 位貢獻者的超過 509 次提交。感謝所有人的辛勤付出！

特別感謝以下作者在本版發布文件中協助記錄功能：

- [Dream11](https://github.com/dream-sports-labs) 團隊對 React Native 中 React 19 功能的全面測試
- [Nicola Corti](https://github.com/cortinico) 為「更快發布」功能所做的貢獻
- [Alex Hunt](https://github.com/huntie) 在 Metro 日誌選擇加入功能上的工作
- [Peter Abbondanzo](https://github.com/Abbondanzo) 對 Android XML Drawable 支援的開發
- [Oskar Kwaśniewski](https://github.com/okwasniewski) 在 ReactNativeFactory 上的貢獻

## 升級至 0.78 版本

請使用 [React Native Upgrade Helper](https://react-native-community.github.io/upgrade-helper/) 查看現有專案在 React Native 版本間的程式碼變更，並參閱升級文件。

若要建立新專案：

```
npx @react-native-community/cli@latest init MyProject --version latest
```

若您使用 Expo，[React Native 0.78 將在 Expo SDK 的 Canary 版本中獲得支援](https://expo.dev/changelog/react-native-78)。

:::info
0.78 版現為 React Native 的最新穩定版本，0.75.x 版本將不再獲得支援。更多資訊請參閱 [React Native 支援政策](https://github.com/reactwg/react-native-releases/blob/main/docs/support.md)。我們計劃在近期發布 0.75 版的最終終止支援更新。
:::