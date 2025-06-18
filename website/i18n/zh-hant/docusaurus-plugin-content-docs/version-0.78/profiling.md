---
id: profiling
title: Profiling
---

效能分析是透過檢測應用程式的效能、資源使用情況和行為來識別潛在瓶頸或低效問題的過程。善用效能分析工具能確保您的應用在不同裝置和條件下流暢運作。

在iOS上，Instruments是不可或缺的工具；而在Android平台，您應該學習使用[Android Studio Profiler](profiling.md#profiling-android-ui-performance-with-system-tracing)。

但首先，[**請務必關閉開發者模式！**](performance.md#running-in-development-mode-devtrue) 您應在應用日誌中看到`__DEV__ === false, development-level warning are OFF, performance optimizations are ON`的提示。

## 使用系統追蹤分析Android UI效能

Android支援上萬種不同手機型號，其框架架構需通用於各種硬體規格，這意味著相較iOS，開發者需自行處理更多效能優化工作——但許多時候問題根源並非原生程式碼！

偵測卡頓的第一步是釐清每個16毫秒幀的時間消耗分佈。我們將使用[Android Studio內建的System Tracing分析工具](https://developer.android.com/studio/profile)。

### 1. 收集追蹤資料

首先透過USB連接出現卡頓問題的裝置。在Android Studio中開啟專案的`android`資料夾，於右上角面板選擇裝置，並[以可分析模式執行專案](https://developer.android.com/studio/profile#build-and-run)。

當應用以可分析模式在裝置上運行後，導航至欲分析的互動場景前，點擊Android Studio Profiler面板中的["Capture System Activities"按鈕](https://developer.android.com/studio/profile#start-profiling)開始記錄。

開始記錄後執行目標動畫或互動操作，完成後點擊"Stop recording"。您可[直接在Android Studio檢視追蹤資料](https://developer.android.com/studio/profile/jank-detection)，或於"Past Recordings"面板選擇記錄檔後點擊"Export recording"，用[Perfetto](https://perfetto.dev/)等工具開啟。

### 2. 解讀追蹤資料

在Android Studio或Perfetto中開啟追蹤檔後，您會看到類似以下畫面：

![範例](/docs/assets/SystraceExample.png)

:::note[提示]
使用WASD鍵進行平移和縮放。
:::

不同工具的介面可能略有差異，但下列說明均適用。

:::info[啟用垂直同步高亮顯示]
勾選畫面右上角的此選項以標記16毫秒幀邊界：

![啟用垂直同步高亮](/docs/assets/SystraceHighlightVSync.png)

您應看到如上圖的斑馬條紋。若未顯示，請嘗試在其他裝置上分析：三星裝置已知可能無法正常顯示垂直同步標記，而Nexus系列通常較可靠。
:::

### 3. 定位您的進程

滾動直至看見您的套件名稱（部分顯示）。本例中分析的`com.facebook.adsmanager`因系統執行緒名稱長度限制顯示為`book.adsmanager`。

左側面板顯示的執行緒列表對應右側時間軸。我們需關注以下執行緒：UI執行緒（顯示套件名稱或UI Thread）、`mqt_js`和`mqt_native_modules`。若在Android 5+裝置上運行，還需關注Render Thread。

- **UI 執行緒。** 這是標準 Android 測量/佈局/繪製發生的地方。右側的執行緒名稱會是你的套件名稱（在我的例子中是 book.adsmanager）或 UI Thread。你在這個執行緒上看到的事件應該類似以下內容，並與 `Choreographer`、`traversals` 和 `DispatchUI` 相關：

  ![UI 執行緒範例](/docs/assets/SystraceUIThreadExample.png)

- **JS 執行緒。** 這是 JavaScript 執行的地方。執行緒名稱會是 `mqt_js` 或 `<...>`，取決於你設備上的核心是否配合。如果沒有名稱，可以透過尋找 `JSCall`、`Bridge.executeJSCall` 等內容來識別：

  ![JS 執行緒範例](/docs/assets/SystraceJSThreadExample.png)

- **原生模組執行緒。** 這是原生模組呼叫（例如 `UIManager`）執行的地方。執行緒名稱會是 `mqt_native_modules` 或 `<...>`。在後者的情況下，可以透過尋找 `NativeCall`、`callJavaModuleMethod` 和 `onBatchComplete` 等內容來識別：

  ![原生模組執行緒範例](/docs/assets/SystraceNativeModulesThreadExample.png)

- **額外：渲染執行緒。** 如果你使用的是 Android L（5.0）及以上版本，你的應用中還會有一個渲染執行緒。這個執行緒生成用於繪製 UI 的實際 OpenGL 指令。執行緒名稱會是 `RenderThread` 或 `<...>`。在後者的情況下，可以透過尋找 `DrawFrame` 和 `queueBuffer` 等內容來識別：

  ![渲染執行緒範例](/docs/assets/SystraceRenderThreadExample.png)

## 識別問題根源

流暢的動畫應該看起來像以下這樣：

![流暢的動畫](/docs/assets/SystraceWellBehaved.png)

每個顏色的變化代表一個影格——記住，為了顯示一個影格，我們所有的 UI 工作需要在 16 毫秒的週期內完成。注意，沒有任何執行緒的工作接近影格邊界。這樣渲染的應用是以 60 FPS 運行的。

然而，如果你注意到卡頓，可能會看到類似以下的內容：

![來自 JS 的卡頓動畫](/docs/assets/SystraceBadJS.png)

注意，JS 執行緒幾乎一直在執行，並且跨越影格邊界！這個應用沒有以 60 FPS 渲染。在這種情況下，**問題出在 JS 上**。

你可能還會看到類似以下的內容：

![來自 UI 的卡頓動畫](/docs/assets/SystraceBadUI.png)

在這種情況下，UI 和渲染執行緒的工作跨越了影格邊界。我們試圖在每個影格上渲染的 UI 需要完成太多工作。在這種情況下，**問題出於正在渲染的原生視圖**。

此時，你將獲得一些非常有用的資訊來指導你的下一步。

## 解決 JavaScript 問題

如果你識別出一個 JS 問題，請在你執行的具體 JS 中尋找線索。在上面的情境中，我們看到 `RCTEventEmitter` 在每個影格中被多次呼叫。以下是從上述追蹤中放大 JS 執行緒的內容：

![過多的 JS](/docs/assets/SystraceBadJS2.png)

這看起來不太對。為什麼它被呼叫得如此頻繁？它們實際上是不同的事件嗎？這些問題的答案可能會取決於你的產品代碼。很多時候，你會想要查看 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)。

## 解決原生 UI 問題

如果你識別出一個原生 UI 問題，通常有兩種情境：

1. 你試圖在每個影格中繪製的 UI 在 GPU 上涉及太多工作，或者
2. 你在動畫/互動期間構建了新的 UI（例如在滾動期間載入新內容）。

### GPU 工作負載過高

在第一種情境中，您會看到追蹤記錄顯示 UI 執行緒和/或渲染執行緒呈現如下狀態：

![GPU 過載](/docs/assets/SystraceBadUI.png)

注意跨越影格邊界的 `DrawFrame` 耗費了大量時間。這表示 GPU 正在等待排空前一影格的指令緩衝區。

緩解方法包括：

- 對複雜且靜態但需動畫/變形處理的內容（如 `Navigator` 的滑動/透明度動畫）使用 `renderToHardwareTextureAndroid`
- 確認**未**啟用預設關閉的 `needsOffscreenAlphaCompositing`，此功能在多數情況下會大幅增加 GPU 每影格負載

### 在 UI 執行緒建立新視圖

第二種情境的追蹤記錄會類似這樣：

![建立視圖](/docs/assets/SystraceBadCreateUI.png)

可觀察到 JS 執行緒先運算一段時間，接著原生模組執行緒處理工作，最後 UI 執行緒進行昂貴的遍歷操作

除非能延後建立新 UI 至互動結束後，或簡化建立的 UI 結構，否則無快速解決方案。React Native 團隊正在開發基礎架構層級的解決方案，將允許在非主執行緒建立與配置新 UI，確保互動流暢性

### 找出原生 CPU 熱點

若問題疑似源自原生端，可使用 [CPU 熱點分析器](https://developer.android.com/studio/profile/record-java-kotlin-methods)獲取詳細資訊。開啟 Android Studio Profiler 面板並選擇「Find CPU Hotspots (Java/Kotlin Method Recording)」

:::info[選擇 Java/Kotlin 記錄]

務必選擇「Find CPU Hotspots **(Java/Kotlin Recording)**」而非「Find CPU Hotspots (Callstack Sample)」。兩者圖示相似但功能不同
:::

執行互動後點擊「Stop recording」。記錄過程耗費資源，請保持互動簡短。結果追蹤可在 Android Studio 檢視，或匯出至 [Firefox Profiler](https://profiler.firefox.com/) 等線上工具分析

與系統追蹤不同，CPU 熱點分析速度較慢且無法提供精確測量值，但能顯示原生方法呼叫狀況及各影格時間佔比分布