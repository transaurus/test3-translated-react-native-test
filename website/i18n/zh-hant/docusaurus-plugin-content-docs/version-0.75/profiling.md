---
id: profiling
title: Profiling
---

Use the built-in profiler to get detailed information about work done in the JavaScript thread and main thread side-by-side. Access it by selecting Perf Monitor from the Debug menu.

For iOS, Instruments is an invaluable tool, and on Android you should learn to use [`systrace`](profiling.md#profiling-android-ui-performance-with-systrace).

But first, [**make sure that Development Mode is OFF!**](performance.md#running-in-development-mode-devtrue) You should see `__DEV__ === false, development-level warning are OFF, performance optimizations are ON` in your application logs.

Another way to profile JavaScript is to use the Chrome profiler while debugging. This won't give you accurate results as the code is running in Chrome but will give you a general idea of where bottlenecks might be. Run the profiler under Chrome's `Performance` tab. A flame graph will appear under `User Timing`. To view more details in tabular format, click at the `Bottom Up` tab below and then select `DedicatedWorker Thread` at the top left menu.

## Profiling Android UI Performance with `systrace`

Android supports 10k+ different phones and is generalized to support software rendering: the framework architecture and need to generalize across many hardware targets unfortunately means you get less for free relative to iOS. But sometimes, there are things you can improve -- and many times it's not native code's fault at all!

The first step for debugging this jank is to answer the fundamental question of where your time is being spent during each 16ms frame. For that, we'll be using a standard Android profiling tool called `systrace`.

`systrace` is a standard Android marker-based profiling tool (and is installed when you install the Android platform-tools package). Profiled code blocks are surrounded by start/end markers which are then visualized in a colorful chart format. Both the Android SDK and React Native framework provide standard markers that you can visualize.

### 1. Collecting a trace

First, connect a device that exhibits the stuttering you want to investigate to your computer via USB and get it to the point right before the navigation/animation you want to profile. Run `systrace` as follows:

```shell
$ <path_to_android_sdk>/platform-tools/systrace/systrace.py --time=10 -o trace.html sched gfx view -a <your_package_name>
```

A quick breakdown of this command:

- `time` is the length of time the trace will be collected in seconds
- `sched`, `gfx`, and `view` are the android SDK tags (collections of markers) we care about: `sched` gives you information about what's running on each core of your phone, `gfx` gives you graphics info such as frame boundaries, and `view` gives you information about measure, layout, and draw passes
- `-a <your_package_name>` enables app-specific markers, specifically the ones built into the React Native framework. `your_package_name` can be found in the `AndroidManifest.xml` of your app and looks like `com.example.app`

Once the trace starts collecting, perform the animation or interaction you care about. At the end of the trace, systrace will give you a link to the trace which you can open in your browser.

### 2. Reading the trace

After opening the trace in your browser (preferably Chrome), you should see something like this:

![Example](/docs/assets/SystraceExample.png)

:::note[Hint]
Use the WASD keys to strafe and zoom.
:::

If your trace .html file isn't opening correctly, check your browser console for the following:

![ObjectObserveError](/docs/assets/ObjectObserveError.png)

Since `Object.observe` was deprecated in recent browsers, you may have to open the file from the Google Chrome Tracing tool. You can do so by:

- Opening tab in chrome `chrome://tracing`
- Selecting load
- Selecting the html file generated from the previous command.

:::info[啟用垂直同步高亮顯示]
勾選畫面右上角的此選項以標示16毫秒的幀邊界：

![啟用垂直同步高亮顯示](/docs/assets/SystraceHighlightVSync.png)

您應會看到如上截圖中的斑馬條紋。若未顯示，請嘗試在其他裝置上分析：已知三星裝置可能無法正確顯示垂直同步信號，而Nexus系列通常較為可靠。
:::

### 3. 尋找您的進程

滾動直至看到您的套件名稱（部分）。此例中，我分析的`com.facebook.adsmanager`會顯示為`book.adsmanager`，這是因核心執行緒名稱長度限制所致。

左側會顯示一組執行緒，對應右側時間軸的行數。我們主要關注以下幾個執行緒：UI執行緒（顯示您的套件名稱或「UI Thread」）、`mqt_js`和`mqt_native_modules`。若在Android 5+上運行，還需注意Render Thread。

- **UI執行緒。**此處執行標準Android測量/佈局/繪製。右側執行緒名稱會是您的套件名稱（本例為book.adsmanager）或「UI Thread」。此執行緒上的事件應類似下圖，與`Choreographer`、`traversals`和`DispatchUI`相關：

  ![UI執行緒範例](/docs/assets/SystraceUIThreadExample.png)

- **JS執行緒。**此處執行JavaScript。執行緒名稱會是`mqt_js`或`<...>`（取決於裝置核心的相容性）。若無名稱，可透過尋找`JSCall`、`Bridge.executeJSCall`等標記來識別：

  ![JS執行緒範例](/docs/assets/SystraceJSThreadExample.png)

- **原生模組執行緒。**此處執行原生模組呼叫（如`UIManager`）。執行緒名稱會是`mqt_native_modules`或`<...>`。若無名稱，可透過尋找`NativeCall`、`callJavaModuleMethod`和`onBatchComplete`等標記來識別：

  ![原生模組執行緒範例](/docs/assets/SystraceNativeModulesThreadExample.png)

- **額外：Render執行緒。**若使用Android L（5.0）以上版本，您的應用還會有一個Render執行緒。此執行緒產生實際用於繪製UI的OpenGL指令。執行緒名稱會是「RenderThread」或`<...>`。若無名稱，可透過尋找`DrawFrame`和`queueBuffer`等標記來識別：

  ![Render執行緒範例](/docs/assets/SystraceRenderThreadExample.png)

## 識別問題根源

流暢的動畫應如下所示：

![流暢動畫](/docs/assets/SystraceWellBehaved.png)

每個顏色變化代表一幀——請記住，要顯示一幀，所有UI工作需在16毫秒內完成。注意沒有任何執行緒的工作接近幀邊界。如此渲染的應用能以60 FPS運行。

若發現卡頓，可能會看到如下情況：

![JS導致的卡頓動畫](/docs/assets/SystraceBadJS.png)

注意JS執行緒幾乎全程執行，且跨越幀邊界！此應用未以60 FPS渲染。**問題出在JS**。

您也可能看到以下情況：

![原生UI導致的卡頓動畫](/docs/assets/SystraceBadUI.png)

此例中，UI和Render執行緒的工作跨越了幀邊界。每幀要渲染的UI所需工作量過大。**問題出於渲染的原生視圖**。

至此，您已獲得關鍵資訊來決定後續步驟。

## Resolving JavaScript issues

If you identified a JS problem, look for clues in the specific JS that you're executing. In the scenario above, we see `RCTEventEmitter` being called multiple times per frame. Here's a zoom-in of the JS thread from the trace above:

![Too much JS](/docs/assets/SystraceBadJS2.png)

This doesn't seem right. Why is it being called so often? Are they actually different events? The answers to these questions will probably depend on your product code. And many times, you'll want to look into [shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate).

## Resolving native UI Issues

If you identified a native UI problem, there are usually two scenarios:

1. the UI you're trying to draw each frame involves too much work on the GPU, or
2. You're constructing new UI during the animation/interaction (e.g. loading in new content during a scroll).

### Too much GPU work

In the first scenario, you'll see a trace that has the UI thread and/or Render Thread looking like this:

![Overloaded GPU](/docs/assets/SystraceBadUI.png)

Notice the long amount of time spent in `DrawFrame` that crosses frame boundaries. This is time spent waiting for the GPU to drain its command buffer from the previous frame.

To mitigate this, you should:

- investigate using `renderToHardwareTextureAndroid` for complex, static content that is being animated/transformed (e.g. the `Navigator` slide/alpha animations)
- make sure that you are **not** using `needsOffscreenAlphaCompositing`, which is disabled by default, as it greatly increases the per-frame load on the GPU in most cases.

### Creating new views on the UI thread

In the second scenario, you'll see something more like this:

![Creating Views](/docs/assets/SystraceBadCreateUI.png)

Notice that first the JS thread thinks for a bit, then you see some work done on the native modules thread, followed by an expensive traversal on the UI thread.

There isn't a quick way to mitigate this unless you're able to postpone creating new UI until after the interaction, or you are able to simplify the UI you're creating. The react native team is working on an infrastructure level solution for this that will allow new UI to be created and configured off the main thread, allowing the interaction to continue smoothly.