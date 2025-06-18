---
id: profiling
title: Profiling
---

效能分析是透過檢測應用程式的效能、資源使用情況和行為來識別潛在瓶頸或低效問題的過程。善用分析工具能確保您的應用在不同裝置和條件下流暢運行。

在iOS平台上，Instruments是不可或缺的工具；而在Android上，您應該學習使用[`systrace`](profiling.md#profiling-android-ui-performance-with-systrace)。

但首先，[**請確保開發者模式已關閉！**](performance.md#running-in-development-mode-devtrue) 您應該在應用日誌中看到`__DEV__ === false, development-level warning are OFF, performance optimizations are ON`的提示。

## 使用`systrace`分析Android UI效能

Android支援上萬種不同手機且採用通用軟體渲染架構：這種框架架構和需要兼容多種硬體的特性，意味著相較iOS您需要更多自主優化。但有時問題可能根本不在原生代碼！

偵測卡頓的第一步是確認每個16毫秒幀的時間消耗分佈。我們將使用Android標準分析工具`systrace`。

`systrace`是標準的Android基於標記的分析工具（安裝Android platform-tools套件時會自動安裝）。被分析的代碼塊會被開始/結束標記包圍，並以彩色圖表可視化。Android SDK和React Native框架都提供可視化的標準標記。

### 1. 收集追蹤數據

首先透過USB連接出現卡頓的裝置，並導航至您想分析的動畫觸發前狀態。執行以下`systrace`命令：

```shell
$ <path_to_android_sdk>/platform-tools/systrace/systrace.py --time=10 -o trace.html sched gfx view -a <your_package_name>
```

快速說明此命令參數：

- `time`是追蹤收集時間（秒）
- `sched`、`gfx`和`view`是關鍵的Android SDK標記組：`sched`顯示手機各核心運行狀態，`gfx`提供圖形幀邊界資訊，`view`包含測量/佈局/繪製流程數據
- `-a <your_package_name>`啟用應用專用標記（特別是React Native框架內建標記）。套件名稱格式如`com.example.app`，可在您應用的`AndroidManifest.xml`中找到

追蹤開始後立即執行目標動畫操作。結束時systrace會生成瀏覽器可開啟的追蹤文件連結。

### 2. 解讀追蹤數據

用瀏覽器（建議Chrome）開啟追蹤文件後，您會看到類似畫面：

![範例](/docs/assets/SystraceExample.png)

:::note[提示]
使用WASD鍵進行平移和縮放。
:::

若追蹤.html文件無法正確開啟，請檢查瀏覽器控制台是否出現：

![ObjectObserve錯誤](/docs/assets/ObjectObserveError.png)

由於`Object.observe`已在新版瀏覽器中棄用，您可能需要透過Chrome Tracing工具開啟：

- 在Chrome地址欄輸入`chrome://tracing`
- 點擊"Load"按鈕
- 選擇先前命令生成的html文件

:::info[啟用垂直同步高亮顯示]
勾選畫面右上角的這個核取方塊以高亮顯示16毫秒的幀邊界：

![啟用垂直同步高亮顯示](/docs/assets/SystraceHighlightVSync.png)

您應該會看到如上截圖中的斑馬條紋。如果沒有，請嘗試在其他裝置上進行分析：已知三星裝置在顯示垂直同步時會有問題，而Nexus系列通常較為可靠。
:::

### 3. 找到您的進程

滾動直到看到您套件名稱的部分。在此案例中，我正在分析`com.facebook.adsmanager`，由於核心中執行緒名稱長度的限制，它顯示為`book.adsmanager`。

左側您會看到一組執行緒，對應右側時間軸上的行。我們關注以下幾個執行緒：UI執行緒（顯示您的套件名稱或UI Thread）、`mqt_js`和`mqt_native_modules`。如果您在Android 5+上執行，我們還需關注Render Thread。

- **UI執行緒。** 這是標準Android測量/佈局/繪製發生的地方。右側的執行緒名稱會是您的套件名稱（在我的案例中是book.adsmanager）或UI Thread。您在此執行緒上看到的事件應該類似以下內容，並與`Choreographer`、`traversals`和`DispatchUI`相關：

  ![UI執行緒範例](/docs/assets/SystraceUIThreadExample.png)

- **JS執行緒。** 這是JavaScript執行的地方。執行緒名稱會是`mqt_js`或`<...>`，取決於您裝置核心的配合程度。如果沒有名稱，可以透過尋找`JSCall`、`Bridge.executeJSCall`等來識別：

  ![JS執行緒範例](/docs/assets/SystraceJSThreadExample.png)

- **原生模組執行緒。** 這是原生模組呼叫（例如`UIManager`）執行的地方。執行緒名稱會是`mqt_native_modules`或`<...>`。在後者情況下，可以透過尋找`NativeCall`、`callJavaModuleMethod`和`onBatchComplete`來識別：

  ![原生模組執行緒範例](/docs/assets/SystraceNativeModulesThreadExample.png)

- **額外：Render執行緒。** 如果您使用Android L（5.0）及以上版本，您的應用中還會有一個Render執行緒。此執行緒生成用於繪製UI的實際OpenGL指令。執行緒名稱會是`RenderThread`或`<...>`。在後者情況下，可以透過尋找`DrawFrame`和`queueBuffer`來識別：

  ![Render執行緒範例](/docs/assets/SystraceRenderThreadExample.png)

## 識別問題根源

流暢的動畫應該如下所示：

![流暢的動畫](/docs/assets/SystraceWellBehaved.png)

每個顏色的變化代表一幀——記住，為了顯示一幀，我們所有的UI工作需要在16毫秒的週期內完成。注意沒有任何執行緒的工作接近幀邊界。這樣渲染的應用是以60 FPS運行的。

如果您注意到卡頓，可能會看到如下情況：

![JS導致的卡頓動畫](/docs/assets/SystraceBadJS.png)

注意JS執行緒幾乎一直在執行，且跨越幀邊界！此應用沒有以60 FPS渲染。在此案例中，**問題出在JS**。

您也可能看到如下情況：

![UI導致的卡頓動畫](/docs/assets/SystraceBadUI.png)

在此案例中，UI和Render執行緒的工作跨越了幀邊界。我們試圖在每幀渲染的UI需要完成太多工作。在此案例中，**問題出於正在渲染的原生視圖**。

此時，您將獲得一些非常有用的資訊來指導下一步操作。

## 解決 JavaScript 問題

若確認問題出在 JavaScript 端，請檢查執行的具體 JS 程式碼。在上方情境中，我們看到每幀多次呼叫 `RCTEventEmitter`。以下是從上述追蹤記錄中放大檢視的 JS 執行緒：

![過多 JS 執行](/docs/assets/SystraceBadJS2.png)

這顯然不正常。為何呼叫如此頻繁？這些是不同的事件嗎？答案通常取決於產品程式碼。多數情況下，您需要檢查 [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) 的實作。

## 解決原生 UI 問題

若問題出在原生 UI 端，通常有兩種情境：

1. 每幀繪製的 UI 在 GPU 上負載過重，或
2. 在動畫/互動過程中動態建立新 UI（例如滾動時載入新內容）。

### GPU 負載過高

第一種情境下，您會看到 UI 執行緒和/或渲染執行緒的追蹤記錄如下：

![GPU 超載](/docs/assets/SystraceBadUI.png)

注意 `DrawFrame` 耗時過長且跨越幀邊界，這表示 GPU 正在處理前一幀的指令緩衝區。

緩解方法包括：

- 對複雜靜態內容（如 `Navigator` 滑動/透明度動畫）使用 `renderToHardwareTextureAndroid`
- 確認未啟用預設關閉的 `needsOffscreenAlphaCompositing`，此選項在多數情況下會大幅增加 GPU 每幀負載。

### 在 UI 執行緒建立新視圖

第二種情境的追蹤記錄會類似這樣：

![建立視圖](/docs/assets/SystraceBadCreateUI.png)

可見 JS 執行緒先運算一段時間，接著原生模組執行緒處理工作，最後 UI 執行緒進行高成本的遍歷操作。

除非能延後建立新 UI 至互動結束後，或簡化 UI 結構，否則無快速解決方案。React Native 團隊正在開發基礎架構級解決方案，將允許在非主執行緒建立與配置新 UI，確保互動流暢度。