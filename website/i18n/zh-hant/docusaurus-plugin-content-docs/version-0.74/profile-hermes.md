---
id: profile-hermes
title: Profiling with Hermes
---

您可以使用 [Hermes](https://github.com/facebook/hermes) 來視覺化 React Native 應用程式中 JavaScript 的效能表現。Hermes 是一個輕量級的 JavaScript 引擎，專為運行 React Native 而優化（您可以[在此閱讀更多關於與 React Native 搭配使用的資訊](hermes)）。Hermes 有助於提升應用程式效能，並提供方法來分析其運行的 JavaScript 效能。

在本節中，您將學習如何對運行在 Hermes 上的 React Native 應用程式進行效能分析，以及如何使用 [Chrome DevTools 的 Performance 分頁](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference) 來視覺化分析結果。

:::caution
開始之前，請確保您已[在應用程式中啟用 Hermes](hermes)！
:::

請按照以下指示開始進行效能分析：

1. [記錄 Hermes 取樣分析檔](profile-hermes.md#record-a-hermes-sampling-profile)
2. [從 CLI 執行指令](profile-hermes.md#execute-command-from-cli)
3. [在 Chrome DevTools 中開啟下載的分析檔](profile-hermes.md#open-the-downloaded-profile-on-chrome-devtools)

## 記錄 Hermes 取樣分析檔

要從開發者選單記錄取樣分析器：

1. 導航至正在運行的 Metro 伺服器終端機。
2. 按下 `d` 開啟**開發者選單**。
3. 選擇**啟用取樣分析器**。
4. 在應用程式中執行您的 JavaScript（例如按鈕操作等）。
5. 再次按下 `d` 開啟**開發者選單**。
6. 選擇**停用取樣分析器**以停止記錄並儲存取樣分析器。

系統會顯示一個提示，說明取樣分析器的儲存位置，通常位於 `/data/user/0/com.appName/cache/*.cpuprofile`。

<img src="/docs/assets/HermesProfileSaved.png" height="465" width="250" alt="Toast Notification of Profile saving" />

## 從 CLI 執行指令

您可以使用 [React Native CLI](https://github.com/react-native-community/cli) 將 Hermes 追蹤分析檔轉換為 Chrome 追蹤分析檔，然後使用以下指令將其拉取到本地機器：

```sh
npx react-native profile-hermes [destinationDir]
```

### 啟用原始碼映射

:::info
您可以在[除錯發行版本](debugging-release-builds.md)頁面閱讀關於原始碼映射的資訊。
:::

### 常見錯誤

#### `adb: no devices/emulators found` 或 `adb: device offline`

- **原因** CLI 無法存取您用來運行應用程式的裝置或模擬器（透過 adb）。
- **解決方法** 確保您的 Android 裝置/模擬器已連接並正在運行。該指令僅在能夠存取 adb 時有效。

#### `There is no file in the cache/ directory`

- **原因** CLI 在應用程式的 **cache/** 目錄中找不到任何 **.cpuprofile** 檔案。您可能忘記從裝置記錄分析檔。
- **解決方法** 按照[指示](profile-hermes.md#record-a-hermes-sampling-profile)從裝置啟用/停用分析器。

#### `Error: your_profile_name.cpuprofile is an empty file`

- **原因** 分析檔為空，可能是因為 Hermes 未正確運行。
- **解決方法** 確保您的應用程式運行在最新版本的 Hermes 上。

## 在 Chrome DevTools 中開啟下載的分析檔

要在 Chrome DevTools 中開啟分析檔：

1. 開啟 Chrome DevTools。
2. 選擇 **Performance** 分頁。
3. 右鍵點擊並選擇 **Load profile...**

<img src="/docs/assets/openChromeProfile.png" alt="Loading a performance profile on Chrome DevTools" />

## Hermes 分析檔轉換器是如何運作的？

The Hermes Sample Profile is of the `JSON object format`, while the format that Google's DevTools supports is `JSON Array Format`. (More information about the formats can be found on the [Trace Event Format Document](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/preview))

```ts
export interface HermesCPUProfile {
  traceEvents: SharedEventProperties[];
  samples: HermesSample[];
  stackFrames: {[key in string]: HermesStackFrame};
}
```

The Hermes profile has most of its information encoded into the `samples` and `stackFrames` properties. Each sample is a snapshot of the function call stack at that particular timestamp as each sample has a `sf` property which corresponds to a function call.

```ts
export interface HermesSample {
  cpu: string;
  name: string;
  ts: string;
  pid: number;
  tid: string;
  weight: string;
  /**
   * Will refer to an element in the stackFrames object of the Hermes Profile
   */
  sf: number;
  stackFrameData?: HermesStackFrame;
}
```

The information about a function call can be found in `stackFrames` which contains key-object pairs, where the key is the `sf` number and the corresponding object gives us all the relevant information about the function including the `sf` number of its parent function. This parent-child relationship can be traced upwards to find the information of all the functions running at a particular timestamp.

```ts
export interface HermesStackFrame {
  line: string;
  column: string;
  funcLine: string;
  funcColumn: string;
  name: string;
  category: string;
  /**
   * A parent function may or may not exist
   */
  parent?: number;
}
```

At this point, you should define a few more terms, namely:

1. Nodes: The objects corresponding to `sf` numbers in `stackFrames`
2. Active Nodes: The nodes which are currently running at a particular timestamp. A node is classified as running if its `sf` number is in the function call stack. This call stack can be obtained from the `sf` number of the sample and tracing upwards till parent `sf`s are available

The `samples` and the `stackFrames` in tandem can then be used to generate all the start and end events at the corresponding timestamps, wherein:

1. Start Nodes/Events: Nodes absent in the previous sample's function call stack but present in the current sample's.
2. End Nodes/Events: Nodes present in the previous sample's function call stack but absent in the current sample's.

<img src="/docs/assets/CallStackDemo.jpg" height="800" width="600" alt="CallStack Terms Explained" />

You can now construct a `flamechart` of function calls as you have all the function information including its start and end timestamps.

The `hermes-profile-transformer` can convert any profile generated using Hermes into a format that can be directly displayed in Chrome DevTools. More information about this can be found on [ `@react-native-community/hermes-profile-transformer` ](https://github.com/react-native-community/hermes-profile-transformer)