---
id: appregistry
title: AppRegistry
---

<div className="banner-native-code-required">
  <h3>Project with Native Code Required</h3>
  <p>If you are using the managed Expo workflow there is only ever one entry component registered with <code>AppRegistry</code> and it is handled automatically (or through <a href="https://docs.expo.dev/versions/latest/sdk/register-root-component/">registerRootComponent</a>). You do not need to use this API.</p>
</div>

`AppRegistry` 是所有 React Native 應用程式的 JavaScript 入口點。應用程式的根元件應透過 `AppRegistry.registerComponent` 註冊自身，之後原生系統便能載入應用程式的套件包，並在準備就緒時透過呼叫 `AppRegistry.runApplication` 實際執行應用程式。

```jsx
import {Text, AppRegistry} from 'react-native';

const App = props => (
  <View>
    <Text>App1</Text>
  </View>
);

AppRegistry.registerComponent('Appname', () => App);
```

若要「停止」應用程式（當視圖需要銷毀時），請使用傳入 `runApplication` 的標籤呼叫 `AppRegistry.unmountApplicationComponentAtRootTag`。這些方法應始終配對使用。

`AppRegistry` 應在 `require` 序列的早期階段引入，以確保 JavaScript 執行環境在其他模組被引入前已完成設定。

---

# 參考文獻

## 方法

### `cancelHeadlessTask()`

```jsx
static cancelHeadlessTask(taskId, taskKey)
```

僅由原生程式碼呼叫。取消無介面背景任務。

**參數：**

| Name                                                         | Type   | Description                                                                             |
| ------------------------------------------------------------ | ------ | --------------------------------------------------------------------------------------- |
| taskId <div className="label basic required">Required</div>  | number | The native id for this task instance that was used when `startHeadlessTask` was called. |
| taskKey <div className="label basic required">Required</div> | string | The key for the task that was used when `startHeadlessTask` was called.                 |

---

### `enableArchitectureIndicator()`

```jsx
static enableArchitectureIndicator(enabled)
```

**參數：**

| Name                                                         | Type    |
| ------------------------------------------------------------ | ------- |
| enabled <div className="label basic required">Required</div> | boolean |

---

### `getAppKeys()`

```jsx
static getAppKeys()
```

回傳字串陣列。

---

### `getRegistry()`

```jsx
static getRegistry()
```

回傳 [Registry](appregistry#registry) 物件。

---

### `getRunnable()`

```jsx
static getRunnable(appKey)
```

回傳 [Runnable](appregistry#runnable) 物件。

**參數：**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| appKey <div className="label basic required">Required</div> | string |

---

### `getSectionKeys()`

```jsx
static getSectionKeys()
```

回傳字串陣列。

---

### `getSections()`

```jsx
static getSections()
```

回傳 [Runnables](appregistry#runnables) 物件。

---

### `registerCancellableHeadlessTask()`

```jsx
static registerCancellableHeadlessTask(taskKey, taskProvider, taskCancelProvider)
```

註冊可取消的無介面背景任務。無介面任務是指不需使用者介面即可執行的程式碼片段。

**參數：**

| Name                                                                                  | Type                                                 | Description                                                                                                                                                                                                                         |
| ------------------------------------------------------------------------------------- | ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| taskKey<br/><div className="label basic required two-lines">Required</div>            | string                                               | The native id for this task instance that was used when startHeadlessTask was called.                                                                                                                                               |
| taskProvider<br/><div className="label basic required two-lines">Required</div>       | [TaskProvider](appregistry#taskprovider)             | A promise returning function that takes some data passed from the native side as the only argument. When the promise is resolved or rejected the native side is notified of this event and it may decide to destroy the JS context. |
| taskCancelProvider<br/><div className="label basic required two-lines">Required</div> | [TaskCancelProvider](appregistry#taskcancelprovider) | a void returning function that takes no arguments; when a cancellation is requested, the function being executed by taskProvider should wrap up and return ASAP.                                                                    |

---

### `registerComponent()`

```jsx
static registerComponent(appKey, componentProvider, section?)
```

**參數：**

| Name                                                                   | Type              |
| ---------------------------------------------------------------------- | ----------------- |
| appKey <div className="label basic required">Required</div>            | string            |
| componentProvider <div className="label basic required">Required</div> | ComponentProvider |
| section                                                                | boolean           |

---

### `registerConfig()`

```jsx
static registerConfig(config)
```

**參數：**

| Name                                                        | Type                               |
| ----------------------------------------------------------- | ---------------------------------- |
| config <div className="label basic required">Required</div> | [AppConfig](appregistry#appconfig) |

---

### `registerHeadlessTask()`

```jsx
static registerHeadlessTask(taskKey, taskProvider)
```

註冊無介面背景任務。無介面任務是指不需使用者介面即可執行的程式碼片段。

此方法可用於在應用程式處於背景狀態時執行 JavaScript 任務，例如同步最新資料、處理推播通知或播放音樂。

**參數：**

| Name                                                                        | Type                                     | Description                                                                                                                                                                                                                         |
| --------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| taskKey <div className="label basic required two-lines">Required</div>      | string                                   | The native id for this task instance that was used when startHeadlessTask was called.                                                                                                                                               |
| taskProvider <div className="label basic required two-lines">Required</div> | [TaskProvider](appregistry#taskprovider) | A promise returning function that takes some data passed from the native side as the only argument. When the promise is resolved or rejected the native side is notified of this event and it may decide to destroy the JS context. |

---

### `registerRunnable()`

```jsx
static registerRunnable(appKey, run)
```

**參數：**

| Name                                                        | Type     |
| ----------------------------------------------------------- | -------- |
| appKey <div className="label basic required">Required</div> | string   |
| run <div className="label basic required">Required</div>    | function |

---

### `registerSection()`

```jsx
static registerSection(appKey, component)
```

**參數：**

| Name                                                           | Type              |
| -------------------------------------------------------------- | ----------------- |
| appKey <div className="label basic required">Required</div>    | string            |
| component <div className="label basic required">Required</div> | ComponentProvider |

---

### `runApplication()`

```jsx
static runApplication(appKey, appParameters)
```

載入 JavaScript 套件並執行應用程式。

**參數：**

| Name                                                               | Type   |
| ------------------------------------------------------------------ | ------ |
| appKey <div className="label basic required">Required</div>        | string |
| appParameters <div className="label basic required">Required</div> | any    |

---

### `setComponentProviderInstrumentationHook()`

```jsx
static setComponentProviderInstrumentationHook(hook)
```

**參數：**

| Name                                                      | Type     |
| --------------------------------------------------------- | -------- |
| hook <div className="label basic required">Required</div> | function |

有效的 `hook` 函式需接受以下參數：

| Name                                                                         | Type               |
| ---------------------------------------------------------------------------- | ------------------ |
| component <div className="label basic required">Required</div>               | ComponentProvider  |
| scopedPerformanceLogger <div className="label basic required">Required</div> | IPerformanceLogger |

該函式必須回傳一個 React 元件。

---

### `setWrapperComponentProvider()`

```jsx
static setWrapperComponentProvider(provider)
```

**參數：**

| Name                                                          | Type              |
| ------------------------------------------------------------- | ----------------- |
| provider <div className="label basic required">Required</div> | ComponentProvider |

---

### `startHeadlessTask()`

```jsx
static startHeadlessTask(taskId, taskKey, data)
```

僅由原生代碼呼叫。啟動無介面任務。

**參數：**

| Name                                                         | Type   | Description                                                          |
| ------------------------------------------------------------ | ------ | -------------------------------------------------------------------- |
| taskId <div className="label basic required">Required</div>  | number | The native id for this task instance to keep track of its execution. |
| taskKey <div className="label basic required">Required</div> | string | The key for the task to start.                                       |
| data <div className="label basic required">Required</div>    | any    | The data to pass to the task.                                        |

---

### `unmountApplicationComponentAtRootTag()`

```jsx
static unmountApplicationComponentAtRootTag(rootTag)
```

當視圖需要銷毀時停止應用程式。

**參數：**

| Name                                                         | Type   |
| ------------------------------------------------------------ | ------ |
| rootTag <div className="label basic required">Required</div> | number |

## 類型定義

### AppConfig

`registerConfig` 方法的應用程式配置。

| Type   |
| ------ |
| object |

**屬性：**

| Name                                                        | Type              |
| ----------------------------------------------------------- | ----------------- |
| appKey <div className="label basic required">Required</div> | string            |
| component                                                   | ComponentProvider |
| run                                                         | function          |
| section                                                     | boolean           |

> **注意：** 每個配置都需設定 `component` 或 `run` 函式。

### Registry

| Type   |
| ------ |
| object |

**屬性：**

| Name      | Type                                       |
| --------- | ------------------------------------------ |
| runnables | array of [Runnables](appregistry#runnable) |
| sections  | array of strings                           |

### Runnable

| Type   |
| ------ |
| object |

**屬性：**

| Name      | Type              |
| --------- | ----------------- |
| component | ComponentProvider |
| run       | function          |

### Runnables

一個以 `appKey` 為鍵、[`Runnable`](appregistry#runnable) 類型為值的物件。

| Type   |
| ------ |
| object |

### Task

`Task` 是一個接受任意資料作為參數並回傳解析為 `undefined` 的 Promise 的函式。

| Type     |
| -------- |
| function |

### TaskCanceller

`TaskCanceller` 是一個不接受參數且不回傳值的函式。

| Type     |
| -------- |
| function |

### TaskCancelProvider

有效的 `TaskCancelProvider` 是一個回傳 [`TaskCanceller`](appregistry#taskcanceller) 的函式。

| Type     |
| -------- |
| function |

### TaskProvider

有效的 `TaskProvider` 是一個回傳 [`Task`](appregistry#task) 的函式。

| Type     |
| -------- |
| function |