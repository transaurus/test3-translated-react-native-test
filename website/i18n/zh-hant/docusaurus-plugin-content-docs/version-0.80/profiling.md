---
id: profiling
title: Profiling
---

效能分析是評估應用程式性能、資源使用情況和行為的過程，目的是找出潛在的瓶頸或效率低下的問題。使用效能分析工具能確保應用程式在不同裝置和條件下運作順暢。

對於iOS，Instruments是一個極具價值的工具；而在Android上，你應該學習使用[Android Studio Profiler](profiling.md#profiling-android-ui-performance-with-system-tracing)。

但首先，[**確保開發者模式已關閉！**](performance.md#running-in-development-mode-devtrue) 你應該在應用程式日誌中看到 `__DEV__ === false, development-level warning are OFF, performance optimizations are ON`。

## 使用系統追蹤分析Android UI效能

Android支援超過1萬種不同的手機，並普遍化以支援軟體渲染：框架架構和需要適應多種硬體目標的現實，意味著相對於iOS，你能免費獲得的優化較少。但有時，仍有改進的空間——而且很多時候問題並非來自原生程式碼！

解決卡頓問題的第一步是回答一個基本問題：在每個16毫秒的幀中，時間都花在哪裡了？為此，我們將使用[Android Studio內建的系統追蹤分析工具](https://developer.android.com/studio/profile)。

### 1. 收集追蹤資料

首先，透過USB將出現卡頓問題的裝置連接到電腦。在Android Studio中打開專案的`android`資料夾，在右上角選擇你的裝置，並[以可分析模式運行專案](https://developer.android.com/studio/profile#build-and-run)。

當應用程式以可分析模式構建並在裝置上運行時，將應用程式導航到你想要分析的動畫或互動之前，然後在Android Studio Profiler面板中啟動["Capture System Activities"任務](https://developer.android.com/studio/profile#start-profiling)。

開始收集追蹤資料後，執行你關注的動畫或互動。然後點擊「停止錄製」。現在你可以[直接在Android Studio中檢查追蹤資料](https://developer.android.com/studio/profile/jank-detection)。或者，你也可以在「Past Recordings」面板中選擇它，點擊「Export recording」，並在[Perfetto](https://perfetto.dev/)等工具中打開。

### 2. 閱讀追蹤資料

在Android Studio或Perfetto中打開追蹤資料後，你應該會看到類似以下的內容：

![範例](/docs/assets/SystraceExample.png)

:::note[提示]
使用WASD鍵來平移和縮放。
:::

具體的UI可能有所不同，但無論你使用哪種工具，以下指示都適用。

:::info[啟用VSync高亮顯示]
勾選畫面右上角的這個核取方塊以高亮顯示16毫秒的幀邊界：

![啟用VSync高亮顯示](/docs/assets/SystraceHighlightVSync.png)

你應該會看到如上截圖中的斑馬紋。如果沒有，嘗試在其他裝置上進行分析：已知三星裝置在顯示vsync時可能會有問題，而Nexus系列通常較為可靠。
:::

### 3. 找到你的進程

滾動直到看到你的套件名稱（部分）。在這個例子中，我正在分析`com.facebook.adsmanager`，由於核心中執行緒名稱的限制，它顯示為`book.adsmanager`。

在左側，你會看到一組執行緒，對應右側時間軸上的行。我們關注的幾個執行緒包括：UI執行緒（顯示你的套件名稱或UI Thread）、`mqt_js`和`mqt_native_modules`。如果你在Android 5+上運行，我們還需要關注Render Thread。

- **UI 執行緒。** 這是標準 Android 測量/佈局/繪製發生的地方。右側的執行緒名稱會是你的套件名稱（在我的例子中是 book.adsmanager）或 UI Thread。你在這個執行緒上看到的事件應該會與 `Choreographer`、`traversals` 和 `DispatchUI` 相關，看起來像這樣：

  ![UI 執行緒範例](/docs/assets/SystraceUIThreadExample.png)

- **JS 執行緒。** 這是 JavaScript 執行的地方。執行緒名稱會是 `mqt_js` 或 `<...>`，取決於你設備上的核心是否合作。如果沒有名稱，可以透過尋找 `JSCall`、`Bridge.executeJSCall` 等來識別它：

  ![JS 執行緒範例](/docs/assets/SystraceJSThreadExample.png)

- **原生模組執行緒。** 這是原生模組呼叫（例如 `UIManager`）執行的地方。執行緒名稱會是 `mqt_native_modules` 或 `<...>`。在後者的情況下，可以透過尋找 `NativeCall`、`callJavaModuleMethod` 和 `onBatchComplete` 等來識別它：

  ![原生模組執行緒範例](/docs/assets/SystraceNativeModulesThreadExample.png)

- **額外：渲染執行緒。** 如果你使用的是 Android L（5.0）及以上版本，你的應用中還會有一個渲染執行緒。這個執行緒生成用於繪製 UI 的實際 OpenGL 指令。執行緒名稱會是 `RenderThread` 或 `<...>`。在後者的情況下，可以透過尋找 `DrawFrame` 和 `queueBuffer` 等來識別它：

  ![渲染執行緒範例](/docs/assets/SystraceRenderThreadExample.png)

## 識別問題根源

流暢的動畫應該看起來像下面這樣：

![流暢的動畫](/docs/assets/SystraceWellBehaved.png)

每個顏色的變化代表一幀——記住，為了顯示一幀，我們所有的 UI 工作需要在 16 毫秒的週期內完成。注意，沒有任何執行緒的工作接近幀邊界。這樣渲染的應用是以 60 FPS 運行的。

然而，如果你注意到卡頓，可能會看到像這樣的情況：

![來自 JS 的卡頓動畫](/docs/assets/SystraceBadJS.png)

注意，JS 執行緒幾乎一直在執行，並且跨越幀邊界！這個應用沒有以 60 FPS 渲染。在這種情況下，**問題出在 JS 上**。

你也可能會看到這樣的情況：

![來自 UI 的卡頓動畫](/docs/assets/SystraceBadUI.png)

在這種情況下，UI 和渲染執行緒的工作跨越了幀邊界。我們試圖在每一幀渲染的 UI 需要完成太多工作。在這種情況下，**問題出於正在渲染的原生視圖**。

此時，你將獲得一些非常有用的信息來指導你的下一步行動。

## 解決 JavaScript 問題

如果你確定了一個 JS 問題，請在你執行的具體 JS 中尋找線索。在上面的情境中，我們看到 `RCTEventEmitter` 每幀被呼叫多次。這是從上面追蹤中放大的 JS 執行緒：

![過多的 JS](/docs/assets/SystraceBadJS2.png)

這看起來不太對。為什麼它被呼叫得如此頻繁？它們真的是不同的事件嗎？這些問題的答案可能會取決於你的產品代碼。很多時候，你會想要查看 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)。

## 解決原生 UI 問題

如果你確定了一個原生 UI 問題，通常有兩種情況：

1. 你試圖在每一幀繪製的 UI 涉及 GPU 上的太多工作，或者
2. 你在動畫/互動期間構建了新的 UI（例如在滾動期間加載新內容）。

### GPU 負載過高

在第一種情境中，您會看到追蹤記錄顯示 UI 執行緒和/或渲染執行緒呈現如下狀態：

![GPU 過載](/docs/assets/SystraceBadUI.png)

注意跨越影格邊界的 `DrawFrame` 耗費大量時間。這表示 GPU 正在等待前一影格的指令緩衝區清空。

緩解方法包括：

- 對複雜靜態內容的動畫/變形（例如 `Navigator` 的滑動/透明度動畫）使用 `renderToHardwareTextureAndroid`
- 確認未啟用預設關閉的 `needsOffscreenAlphaCompositing`，此功能在多數情況下會大幅增加 GPU 每影格負載

### 在 UI 執行緒建立新視圖

第二種情境的追蹤記錄會呈現如下特徵：

![建立視圖](/docs/assets/SystraceBadCreateUI.png)

注意 JS 執行緒先進行運算，接著原生模組執行緒處理任務，最後 UI 執行緒執行高成本的遍歷操作

除非能延後建立新 UI 至互動結束後，或簡化新建 UI 的複雜度，否則難以快速緩解。React Native 團隊正在開發基礎架構解決方案，將允許在非主執行緒建立與配置新 UI，確保互動流暢度

### 找出原生 CPU 熱點

若問題源自原生端，可使用 [CPU 熱點分析器](https://developer.android.com/studio/profile/record-java-kotlin-methods)獲取詳細資訊。開啟 Android Studio Profiler 面板並選擇「Find CPU Hotspots (Java/Kotlin Method Recording)」

:::info[選擇 Java/Kotlin 記錄]

請確認選擇「Find CPU Hotspots **(Java/Kotlin Recording)**」而非「Find CPU Hotspots (Callstack Sample)」。兩者圖標相似但功能不同
:::

執行互動後點擊「Stop recording」。記錄過程會消耗大量資源，請保持互動簡短。您可在 Android Studio 檢視結果，或匯出至 [Firefox Profiler](https://profiler.firefox.com/) 等線上工具分析

與系統追蹤不同，CPU 熱點分析速度較慢，無法提供精確測量數據。但能顯示原生方法呼叫狀況，以及各影格時間消耗的相對比例分佈