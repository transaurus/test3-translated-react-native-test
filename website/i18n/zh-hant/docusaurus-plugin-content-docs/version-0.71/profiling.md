---
id: profiling
title: Profiling
---

使用內建的效能分析工具來獲取 JavaScript 執行緒與主執行緒工作的詳細並列資訊。可透過 Debug 選單中的 Perf Monitor 來存取此功能。

在 iOS 上，Instruments 是不可或缺的工具；而在 Android 上，你應該學習使用 [`systrace`](profiling.md#profiling-android-ui-performance-with-systrace)。

但首先，[**請確保開發者模式已關閉！**](performance.md#running-in-development-mode-devtrue) 你應該在應用程式日誌中看到 `__DEV__ === false, development-level warning are OFF, performance optimizations are ON`。

另一種分析 JavaScript 效能的方式是在除錯時使用 Chrome 的效能分析工具。由於程式碼是在 Chrome 中執行，這不會提供精確的結果，但能讓你大致了解可能的效能瓶頸所在。在 Chrome 的 `Performance` 分頁下執行分析工具。`User Timing` 下方會出現火焰圖。若要查看表格形式的詳細資料，點擊下方的 `Bottom Up` 分頁，然後從左上角選單中選擇 `DedicatedWorker Thread`。

## 使用 `systrace` 分析 Android UI 效能

Android 支援超過一萬種不同的手機，並普遍化以支援軟體渲染：框架架構與需要跨多種硬體目標普遍化的需求，意味著相對於 iOS，你能免費獲得的資源較少。但有時，仍有可以改進的地方——而且很多時候根本不是原生程式碼的問題！

除錯這種卡頓的第一步，是回答一個基本問題：在每個 16 毫秒的幀中，你的時間都花在哪裡。為此，我們將使用一個標準的 Android 分析工具，稱為 `systrace`。

`systrace` 是一個標準的基於標記的 Android 分析工具（安裝 Android platform-tools 套件時會一併安裝）。被分析的程式碼區塊會被開始/結束標記包圍，並以彩色圖表形式視覺化。Android SDK 和 React Native 框架都提供了可視覺化的標準標記。

### 1. 收集追蹤資料

首先，透過 USB 將一台表現出你想要調查的卡頓現象的裝置連接到電腦，並讓它進入你想要分析的導航/動畫之前的那一刻。如下執行 `systrace`：

```shell
$ <path_to_android_sdk>/platform-tools/systrace/systrace.py --time=10 -o trace.html sched gfx view -a <your_package_name>
```

快速解析此命令：

- `time` 是追蹤資料收集的時間長度，單位為秒
- `sched`、`gfx` 和 `view` 是我們關注的 Android SDK 標記（標記集合）：`sched` 提供關於手機每個核心上正在執行的資訊，`gfx` 提供圖形資訊如幀邊界，`view` 提供關於測量、佈局和繪製過程的資訊
- `-a <your_package_name>` 啟用應用程式特定的標記，特別是 React Native 框架內建的標記。`your_package_name` 可以在你應用的 `AndroidManifest.xml` 中找到，看起來像 `com.example.app`

一旦追蹤開始收集，執行你關注的動畫或互動。追蹤結束時，systrace 會提供一個追蹤連結，你可以在瀏覽器中開啟。

### 2. 閱讀追蹤資料

在瀏覽器（最好是 Chrome）中開啟追蹤資料後，你應該會看到類似以下的內容：

![範例](/docs/assets/SystraceExample.png)

:::note[提示]
使用 WASD 鍵來平移和縮放。
:::

如果你的追蹤 .html 檔案無法正確開啟，請檢查瀏覽器控制台是否有以下內容：

![ObjectObserveError](/docs/assets/ObjectObserveError.png)

由於 `Object.observe` 在最近的瀏覽器中已被棄用，你可能需要從 Google Chrome Tracing 工具中開啟檔案。你可以這樣做：

- 在 Chrome 中開啟分頁 `chrome://tracing`
- 選擇載入
- 選擇上一個命令生成的 html 檔案。

:::info[啟用垂直同步高亮顯示]
勾選畫面右上角的這個核取方塊，即可高亮標示16毫秒的幀邊界：

![啟用垂直同步高亮顯示](/docs/assets/SystraceHighlightVSync.png)

您應該會看到如上截圖中的斑馬條紋。如果沒有，請嘗試在其他裝置上進行分析：已知三星裝置在顯示垂直同步時可能會有問題，而Nexus系列通常較為可靠。
:::

### 3. 找到您的進程

滾動直到看見您的套件名稱（部分）。在此案例中，我正在分析`com.facebook.adsmanager`，由於核心執行緒名稱長度限制，它顯示為`book.adsmanager`。

左側會顯示一組執行緒，對應右側時間軸的行。我們關注以下幾個關鍵執行緒：UI執行緒（顯示您的套件名稱或「UI Thread」）、`mqt_js`和`mqt_native_modules`。若在Android 5+上運行，還需關注渲染執行緒(Render Thread)。

- **UI執行緒。** 這是Android標準的測量/佈局/繪製發生處。右側執行緒名稱會是您的套件名稱（本例為book.adsmanager）或「UI Thread」。此執行緒上的事件通常與`Choreographer`、`traversals`和`DispatchUI`相關，如下所示：

  ![UI執行緒範例](/docs/assets/SystraceUIThreadExample.png)

- **JS執行緒。** 這裡執行JavaScript程式碼。執行緒名稱可能是`mqt_js`或`<...>`（取決於裝置核心的相容性）。若無名稱，可透過尋找`JSCall`、`Bridge.executeJSCall`等標記來辨識：

  ![JS執行緒範例](/docs/assets/SystraceJSThreadExample.png)

- **原生模組執行緒。** 這裡處理原生模組呼叫（如`UIManager`）。執行緒名稱可能是`mqt_native_modules`或`<...>`。若無名稱，可透過`NativeCall`、`callJavaModuleMethod`和`onBatchComplete`等標記辨識：

  ![原生模組執行緒範例](/docs/assets/SystraceNativeModulesThreadExample.png)

- **額外提示：渲染執行緒。** 若使用Android L（5.0）及以上版本，您的應用還會有一個渲染執行緒。此執行緒產生實際用於繪製UI的OpenGL指令。執行緒名稱可能是`RenderThread`或`<...>`。若無名稱，可透過`DrawFrame`和`queueBuffer`等標記辨識：

  ![渲染執行緒範例](/docs/assets/SystraceRenderThreadExample.png)

## 定位問題根源

流暢的動畫應如下所示：

![流暢動畫](/docs/assets/SystraceWellBehaved.png)

每個顏色變化代表一幀——請記住，要顯示一幀，所有UI工作必須在16毫秒內完成。注意沒有任何執行緒的工作接近幀邊界。如此渲染的應用能以60 FPS運行。

若發現卡頓，可能會看到如下情況：

![JS導致的卡頓動畫](/docs/assets/SystraceBadJS.png)

注意JS執行緒幾乎全程執行，且跨越幀邊界！此應用未以60 FPS渲染。**問題出在JS程式碼**。

您也可能看到這種情況：

![UI導致的卡頓動畫](/docs/assets/SystraceBadUI.png)

此案例中，UI和渲染執行緒的工作跨越了幀邊界。每幀嘗試渲染的UI需要過多工作量。**問題出於正在渲染的原生視圖**。

此時，您已獲得關鍵資訊來指導後續優化步驟。

## 解決 JavaScript 問題

若確認問題出在 JavaScript 端，請從執行的具體 JS 程式碼中尋找線索。在上述情境中，我們觀察到每幀多次呼叫 `RCTEventEmitter`。以下是從前述追蹤記錄中放大檢視的 JS 執行緒情況：

![JS 負載過高](/docs/assets/SystraceBadJS2.png)

這顯然不正常。為何呼叫如此頻繁？這些是否為不同事件？答案通常取決於產品程式碼。多數情況下，您需要檢查 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 的實作。

## 解決原生 UI 問題

若問題出在原⽣ UI 端，通常有兩種情境：

1. 每幀繪製的 UI 在 GPU 上需要過多運算資源，或
2. 在動畫/互動過程中動態建立新 UI（例如滾動時載入新內容）。

### GPU 負載過高

第一種情境下，追蹤記錄中 UI 執行緒和/或渲染執行緒會呈現如下狀態：

![GPU 超載](/docs/assets/SystraceBadUI.png)

注意跨越幀邊界的 `DrawFrame` 長時間執行，這表示 GPU 正在處理前一幀的命令緩衝區。

緩解方法包括：

- 對複雜靜態內容（如 `Navigator` 滑動/透明度動畫）使用 `renderToHardwareTextureAndroid`
- 確認未啟用預設關閉的 `needsOffscreenAlphaCompositing`，此功能在多數情況下會大幅增加 GPU 每幀負載

若仍無法解決，可透過 [Tracer for OpenGL ES](http://www.androiddocs.com/tools/help/gltracer.html) 深入分析 GPU 實際運作情況。

### 在 UI 執行緒建立新視圖

第二種情境的追蹤記錄會呈現如下模式：

![建立視圖](/docs/assets/SystraceBadCreateUI.png)

可見 JS 執行緒先執行運算，接著原生模組執行緒處理任務，最後 UI 執行緒進行高成本的遍歷操作。

除非能延後建立新 UI 至互動結束後，或簡化 UI 結構，否則無快速解決方案。React Native 團隊正開發基礎架構層級解決方案，將允許在非主執行緒建立與配置新 UI，確保互動流暢度。