---
id: profiling
title: Profiling
---

效能分析是透過監測應用程式的執行效能、資源使用狀況與行為模式，來識別潛在瓶頸或效率問題的過程。善用效能分析工具能確保應用程式在不同裝置與環境下流暢運作。

在iOS平台，Instruments是不可或缺的工具；而在Android平台，建議掌握[Android Studio Profiler](profiling.md#profiling-android-ui-performance-with-system-tracing)的使用技巧。

開始前請務必[關閉開發者模式！](performance.md#running-in-development-mode-devtrue) 您應在應用程式日誌中看到`__DEV__ === false, 開發階段的警告訊息已關閉，效能優化已啟用`的提示。

## 使用系統追蹤分析Android UI效能

Android需支援上萬種裝置並採用通用軟體渲染架構，這種框架設計與硬體兼容性需求意味著相較iOS平台，開發者需自行處理更多效能優化工作——但許多時候問題根源並非原生程式碼！

分析卡頓問題的首要步驟，是確認每個16毫秒幀周期內的時間消耗分佈。我們將使用[Android Studio內建的System Tracing分析工具](https://developer.android.com/studio/profile)。

### 1. 收集追蹤資料

首先透過USB連接出現卡頓問題的裝置。在Android Studio中開啟專案的`android`資料夾，於右上角面板選擇裝置後，[以可分析模式執行專案](https://developer.android.com/studio/profile#build-and-run)。

當應用程式以可分析模式在裝置上運行時，先導航至欲分析的互動場景前，接著點擊Android Studio分析器面板中的["Capture System Activities"任務](https://developer.android.com/studio/profile#start-profiling)。

開始記錄後，執行目標動畫或互動操作，完成後點擊「停止錄製」。您可[直接在Android Studio檢視追蹤結果](https://developer.android.com/studio/profile/jank-detection)，或於「Past Recordings」面板選擇記錄檔後點擊「Export recording」，透過[Perfetto](https://perfetto.dev/)等工具開啟分析。

### 2. 解讀追蹤資料

在Android Studio或Perfetto中開啟追蹤檔案後，您將看到類似以下畫面：

![範例](/docs/assets/SystraceExample.png)

:::note[操作提示]
使用WASD鍵進行平移與縮放。
:::

不同工具的介面可能略有差異，但下列分析原則皆適用。

:::info[啟用垂直同步標記]
勾選畫面右上角的核取方塊以標示16毫秒幀邊界：

![啟用垂直同步標記](/docs/assets/SystraceHighlightVSync.png)

如截圖所示應出現斑馬條紋標記。若未顯示，建議更換分析裝置：三星裝置可能無法正常顯示垂直同步標記，Nexus系列通常較為可靠。
:::

### 3. 定位應用程式進程

滾動時間軸直至看見應用程式套件名稱（可能被截斷）。本例分析的`com.facebook.adsmanager`因系統限制顯示為`book.adsmanager`。

左側線程列表對應右側時間軸軌道，需重點關注以下線程：UI線程（顯示套件名稱或UI Thread）、`mqt_js`與`mqt_native_modules`。若運行於Android 5+系統，還需注意Render Thread。

- **UI 執行緒。** 這是標準 Android 測量/佈局/繪製發生的地方。右側的執行緒名稱會是你的套件名稱（在我的例子中是 book.adsmanager）或 UI Thread。你在這個執行緒上看到的事件應該類似以下內容，並與 `Choreographer`、`traversals` 和 `DispatchUI` 相關：

  ![UI 執行緒範例](/docs/assets/SystraceUIThreadExample.png)

- **JS 執行緒。** 這是 JavaScript 執行的位置。執行緒名稱會是 `mqt_js` 或 `<...>`，取決於你裝置上的核心是否配合。如果沒有名稱，可以透過尋找 `JSCall`、`Bridge.executeJSCall` 等內容來識別它：

  ![JS 執行緒範例](/docs/assets/SystraceJSThreadExample.png)

- **原生模組執行緒。** 這是原生模組呼叫（例如 `UIManager`）執行的位置。執行緒名稱會是 `mqt_native_modules` 或 `<...>`。在後者的情況下，可以透過尋找 `NativeCall`、`callJavaModuleMethod` 和 `onBatchComplete` 等內容來識別它：

  ![原生模組執行緒範例](/docs/assets/SystraceNativeModulesThreadExample.png)

- **額外：渲染執行緒。** 如果你使用的是 Android L（5.0）及以上版本，你的應用程式中還會有一個渲染執行緒。這個執行緒生成用於繪製 UI 的實際 OpenGL 指令。執行緒名稱會是 `RenderThread` 或 `<...>`。在後者的情況下，可以透過尋找 `DrawFrame` 和 `queueBuffer` 等內容來識別它：

  ![渲染執行緒範例](/docs/assets/SystraceRenderThreadExample.png)

## 識別問題根源

流暢的動畫應該看起來像以下內容：

![流暢的動畫](/docs/assets/SystraceWellBehaved.png)

每個顏色的變化代表一個影格——記住，為了顯示一個影格，我們所有的 UI 工作需要在 16 毫秒的週期內完成。注意沒有任何執行緒的工作接近影格邊界。這樣渲染的應用程式是以 60 FPS 運行的。

如果你注意到卡頓，可能會看到類似以下的內容：

![來自 JS 的卡頓動畫](/docs/assets/SystraceBadJS.png)

注意 JS 執行緒幾乎一直在執行，並且跨越影格邊界！這個應用程式沒有以 60 FPS 渲染。在這種情況下，**問題出在 JS**。

你可能還會看到類似以下的內容：

![來自 UI 的卡頓動畫](/docs/assets/SystraceBadUI.png)

在這種情況下，UI 和渲染執行緒的工作跨越了影格邊界。我們試圖在每個影格上渲染的 UI 需要完成太多工作。在這種情況下，**問題出於正在渲染的原生視圖**。

此時，你將獲得一些非常有用的資訊來指導你的下一步。

## 解決 JavaScript 問題

如果你識別出一個 JS 問題，請在你執行的具體 JS 中尋找線索。在上面的情境中，我們看到 `RCTEventEmitter` 在每個影格中被多次呼叫。以下是從上述追蹤中放大的 JS 執行緒：

![過多的 JS](/docs/assets/SystraceBadJS2.png)

這看起來不太對。為什麼它被呼叫得如此頻繁？它們實際上是不同的事件嗎？這些問題的答案可能取決於你的產品代碼。很多時候，你會想查看 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)。

## 解決原生 UI 問題

如果你識別出一個原生 UI 問題，通常有兩種情境：

1. 你試圖在每個影格中繪製的 UI 在 GPU 上涉及太多工作，或者
2. 你在動畫/互動期間構建新的 UI（例如在滾動時載入新內容）。

### GPU 負載過高

在第一種情境中，你會看到追蹤記錄中的 UI 執行緒和/或渲染執行緒呈現如下狀態：

![GPU 過載](/docs/assets/SystraceBadUI.png)

注意 `DrawFrame` 中跨越多個影格邊界的長時間執行。這表示 GPU 正在等待排空前一影格的指令緩衝區。

緩解方法包括：

- 對複雜且靜態但需動畫/變形處理的內容（如 `Navigator` 的滑動/透明度動畫）使用 `renderToHardwareTextureAndroid`
- 確認未啟用預設關閉的 `needsOffscreenAlphaCompositing`，此選項在多數情況下會大幅增加 GPU 每影格負載

### 在 UI 執行緒建立新視圖

第二種情境的追蹤記錄會類似這樣：

![建立視圖](/docs/assets/SystraceBadCreateUI.png)

可觀察到 JS 執行緒先運算一段時間，接著原生模組執行緒處理工作，最後 UI 執行緒進行昂貴的遍歷操作

除非能延後建立新 UI 至互動結束後，或簡化建立的 UI 結構，否則無快速解決方案。React Native 團隊正在開發基礎架構層級的解決方案，將允許在非主執行緒建立與配置新 UI，保持互動流暢度

### 找出原生 CPU 熱點

若問題似乎出在原生端，可使用 [CPU 熱點分析器](https://developer.android.com/studio/profile/record-java-kotlin-methods)獲取詳細資訊。開啟 Android Studio Profiler 面板並選擇「Find CPU Hotspots (Java/Kotlin Method Recording)」

:::info[選擇 Java/Kotlin 記錄]

務必選擇「Find CPU Hotspots **(Java/Kotlin Recording)**」而非「Find CPU Hotspots (Callstack Sample)」。兩者圖標相似但功能不同
:::

執行互動後點擊「Stop recording」。記錄過程耗費資源，請保持互動簡短。結果追蹤可在 Android Studio 檢視，或匯出至 [Firefox Profiler](https://profiler.firefox.com/) 等線上工具分析

與系統追蹤不同，CPU 熱點分析速度較慢，無法提供精確測量數據，但能顯示原生方法呼叫情況及各影格時間佔比分佈