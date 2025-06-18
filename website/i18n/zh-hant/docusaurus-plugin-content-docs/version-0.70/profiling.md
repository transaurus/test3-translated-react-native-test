---
id: profiling
title: Profiling
---

使用內建的效能分析工具來獲取 JavaScript 執行緒與主執行緒工作的詳細並列資訊。透過從 Debug 選單中選擇「Perf Monitor」來存取該功能。

對於 iOS 來說，Instruments 是不可或缺的工具；而在 Android 上，你應該學習使用 [`systrace`](profiling.md#profiling-android-ui-performance-with-systrace)。

但首先，[**請確保開發者模式已關閉！**](performance.md#running-in-development-mode-devtrue) 你應該在應用程式日誌中看到 `__DEV__ === false, development-level warning are OFF, performance optimizations are ON`。

另一種分析 JavaScript 效能的方式是在除錯時使用 Chrome 的效能分析工具。雖然程式碼是在 Chrome 中執行，結果不會完全準確，但仍能大致指出可能的效能瓶頸所在。在 Chrome 的「Performance」分頁下執行分析工具，火焰圖會出現在「User Timing」下方。若要查看更詳細的表格數據，點擊下方的「Bottom Up」分頁，然後從左上角選單選擇「DedicatedWorker Thread」。

## 使用 `systrace` 分析 Android UI 效能

Android 支援上萬種不同的手機型號，並普遍採用軟體渲染：這樣的框架架構與對多種硬體目標的通用性，意味著相較於 iOS，你能獲得的免費優化較少。但有時仍有改進空間——而且很多時候問題並非出在原生程式碼！

偵測卡頓的第一步，是回答一個基本問題：在每個 16 毫秒的影格中，時間都花在哪裡？為此，我們將使用一個標準的 Android 效能分析工具 `systrace`。

`systrace` 是標準的 Android 基於標記的效能分析工具（安裝 Android platform-tools 套件時會一併安裝）。被分析的程式碼區塊會被開始/結束標記包圍，並以彩色圖表形式視覺化呈現。Android SDK 和 React Native 框架都提供了可視覺化的標準標記。

### 1. 收集追蹤記錄

首先，透過 USB 將出現卡頓問題的裝置連接到電腦，並讓它進入你想要分析的導航/動畫操作前的狀態。執行以下 `systrace` 指令：

```shell
$ <path_to_android_sdk>/platform-tools/systrace/systrace.py --time=10 -o trace.html sched gfx view -a <your_package_name>
```

快速說明此指令的各部分：

- `time` 是追蹤記錄收集的時間長度（秒）
- `sched`、`gfx` 和 `view` 是我們關注的 Android SDK 標記集合：`sched` 提供手機各核心的執行資訊，`gfx` 提供圖形相關資訊（如影格邊界），`view` 則提供測量、佈局和繪製階段的資訊
- `-a <your_package_name>` 啟用應用程式專屬標記，特別是 React Native 框架內建的標記。`your_package_name` 可在你應用的 `AndroidManifest.xml` 中找到，格式類似 `com.example.app`

開始收集追蹤記錄後，執行你關注的動畫或互動操作。追蹤結束時，systrace 會提供一個可在瀏覽器中開啟的追蹤記錄連結。

### 2. 解讀追蹤記錄

在瀏覽器（建議使用 Chrome）中開啟追蹤記錄後，你應該會看到類似以下的內容：

![範例](/docs/assets/SystraceExample.png)

:::note[提示]
使用 WASD 鍵來平移和縮放。
:::

如果你的追蹤 .html 檔案無法正確開啟，請檢查瀏覽器控制台是否有以下錯誤：

![ObjectObserveError](/docs/assets/ObjectObserveError.png)

由於 `Object.observe` 在較新的瀏覽器中已被棄用，你可能需要透過 Google Chrome Tracing 工具來開啟檔案。操作步驟如下：

- 在 Chrome 中開啟分頁 `chrome://tracing`
- 選擇「載入」
- 選擇上一步指令生成的 html 檔案

:::info[啟用垂直同步高亮顯示]
勾選畫面右上角的這個核取方塊，以高亮顯示16毫秒的幀邊界：

![啟用垂直同步高亮顯示](/docs/assets/SystraceHighlightVSync.png)

你應該會看到如上截圖中的斑馬條紋。如果沒有，請嘗試在其他裝置上進行分析：已知三星裝置在顯示垂直同步時會有問題，而Nexus系列通常較為可靠。
:::

### 3. 找到你的進程

滾動直到看到你的套件名稱（部分）。在這個例子中，我正在分析`com.facebook.adsmanager`，由於核心中的執行緒名稱限制，它顯示為`book.adsmanager`。

在左側，你會看到一組執行緒，對應右側的時間軸行。我們關注的幾個執行緒包括：UI執行緒（顯示你的套件名稱或UI Thread）、`mqt_js`和`mqt_native_modules`。如果你在Android 5+上運行，還需要關注Render Thread。

- **UI執行緒。** 這是標準Android測量/佈局/繪製發生的地方。右側的執行緒名稱會是你的套件名稱（在我的例子中是book.adsmanager）或UI Thread。你在這個執行緒上看到的事件應該與`Choreographer`、`traversals`和`DispatchUI`有關：

  ![UI執行緒範例](/docs/assets/SystraceUIThreadExample.png)

- **JS執行緒。** 這是執行JavaScript的地方。執行緒名稱會是`mqt_js`或`<...>`，取決於你的裝置核心是否合作。如果沒有名稱，可以尋找`JSCall`、`Bridge.executeJSCall`等來識別它：

  ![JS執行緒範例](/docs/assets/SystraceJSThreadExample.png)

- **原生模組執行緒。** 這是執行原生模組呼叫（例如`UIManager`）的地方。執行緒名稱會是`mqt_native_modules`或`<...>`。在後者的情況下，可以尋找`NativeCall`、`callJavaModuleMethod`和`onBatchComplete`來識別它：

  ![原生模組執行緒範例](/docs/assets/SystraceNativeModulesThreadExample.png)

- **額外：Render執行緒。** 如果你使用Android L（5.0）及以上版本，你的應用中還會有一個Render執行緒。這個執行緒生成用於繪製UI的實際OpenGL命令。執行緒名稱會是`RenderThread`或`<...>`。在後者的情況下，可以尋找`DrawFrame`和`queueBuffer`來識別它：

  ![Render執行緒範例](/docs/assets/SystraceRenderThreadExample.png)

## 識別問題所在

流暢的動畫應該看起來像這樣：

![流暢的動畫](/docs/assets/SystraceWellBehaved.png)

每個顏色的變化代表一幀——記住，為了顯示一幀，我們所有的UI工作需要在16毫秒的週期內完成。注意沒有任何執行緒的工作接近幀邊界。這樣渲染的應用是以60 FPS運行的。

如果你注意到卡頓，可能會看到這樣的情況：

![JS導致的卡頓動畫](/docs/assets/SystraceBadJS.png)

注意JS執行緒幾乎一直在執行，並且跨越了幀邊界！這個應用沒有以60 FPS渲染。在這種情況下，**問題出在JS**。

你也可能會看到這樣的情況：

![UI導致的卡頓動畫](/docs/assets/SystraceBadUI.png)

在這種情況下，UI和Render執行緒的工作跨越了幀邊界。我們試圖在每一幀渲染的UI需要完成太多工作。在這種情況下，**問題出於正在渲染的原生視圖**。

此時，你已經獲得了一些非常有用的信息來指導下一步操作。

## 解決 JavaScript 問題

若您確認問題出在 JavaScript 端，請從執行的具體 JS 程式碼中尋找線索。在上述情境中，我們觀察到每幀多次呼叫 `RCTEventEmitter`。以下是從前述追蹤記錄中放大檢視的 JS 執行緒：

![過多的 JS 執行](/docs/assets/SystraceBadJS2.png)

這顯然不合理。為何它被如此頻繁地呼叫？這些真的是不同的事件嗎？答案通常取決於您的產品程式碼。多數情況下，您需要檢視 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 的實作。

## 解決原生 UI 問題

若問題出在原生的 UI 端，通常有兩種情境：

1. 每幀繪製的 UI 在 GPU 上負荷過重，或
2. 您在動畫/互動過程中動態建立新 UI（例如在滾動時載入新內容）。

### GPU 負載過高

第一種情境下，您會看到追蹤記錄中 UI 執行緒和/或渲染執行緒呈現如下狀態：

![GPU 超載](/docs/assets/SystraceBadUI.png)

注意 `DrawFrame` 耗費大量時間且跨越幀邊界。這段時間是 GPU 在處理前一幀指令緩衝的等待時間。

緩解方法包括：

- 對複雜但靜態的動畫/變形內容（如 `Navigator` 的滑動/透明度動畫）使用 `renderToHardwareTextureAndroid`
- 確認未啟用預設關閉的 `needsOffscreenAlphaCompositing`，此功能在多數情況下會大幅增加 GPU 每幀負載

若仍無法解決，可透過 [Tracer for OpenGL ES](http://www.androiddocs.com/tools/help/gltracer.html) 深入分析 GPU 實際運作情況。

### 在 UI 執行緒建立新視圖

第二種情境的追蹤記錄會呈現如下模式：

![建立視圖的效能瓶頸](/docs/assets/SystraceBadCreateUI.png)

可見 JS 執行緒先進行運算，接著原生模組執行緒處理工作，最後 UI 執行緒執行昂貴的遍歷操作。

除非能延遲建立新 UI 至互動結束後，或簡化新建 UI 的複雜度，否則目前無快速解決方案。React Native 團隊正在開發基礎架構層級的解決方案，將允許在非主執行緒建立與配置新 UI，從而保持互動流暢性。