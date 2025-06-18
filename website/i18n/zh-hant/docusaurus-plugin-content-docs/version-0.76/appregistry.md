---
id: appregistry
title: AppRegistry
---

<div className="banner-native-code-required">
  <h3>Project with Native Code Required</h3>
  <p>If you are using the managed Expo workflow there is only ever one entry component registered with <code>AppRegistry</code> and it is handled automatically (or through <a href="https://docs.expo.dev/versions/latest/sdk/register-root-component/">registerRootComponent</a>). You do not need to use this API.</p>
</div>

`AppRegistry` 是所有 React Native 應用程式的 JavaScript 入口點。應用程式的根元件應透過 `AppRegistry.registerComponent` 註冊自身，之後原生系統可以載入應用程式的套件包，並在準備就緒時透過調用 `AppRegistry.runApplication` 實際運行應用程式。

```tsx
import {Text, AppRegistry} from 'react-native';

const App = () => (
  <View>
    <Text>App1</Text>
  </View>
);

AppRegistry.registerComponent('Appname', () => App);
```

若要「停止」應用程式（當視圖需要銷毀時），請調用 `AppRegistry.unmountApplicationComponentAtRootTag` 並傳入當初 `runApplication` 所使用的標籤。這兩者應始終配對使用。

`AppRegistry` 應在 `require` 序列的早期階段引入，以確保 JavaScript 執行環境在其他模組被引入前已完成設置。

---

# 參考資料

## 方法

### `getAppKeys()`

```tsx
static getAppKeys(): string[];
```

回傳一個字串陣列。

---

### `getRegistry()`

```tsx
static getRegistry(): {sections: string[]; runnables: Runnable[]};
```

回傳一個 [Registry](appregistry#registry) 物件。

---

### `getRunnable()`

```tsx
static getRunnable(appKey: string): : Runnable | undefined;
```

回傳一個 [Runnable](appregistry#runnable) 物件。

**參數：**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| appKey <div className="label basic required">Required</div> | string |

---

### `getSectionKeys()`

```tsx
static getSectionKeys(): string[];
```

回傳一個字串陣列。

---

### `getSections()`

```tsx
static getSections(): Record<string, Runnable>;
```

回傳一個 [Runnables](appregistry#runnables) 物件。

---

### `registerCancellableHeadlessTask()`

```tsx
static registerCancellableHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
  taskCancelProvider: TaskCancelProvider,
);
```

註冊一個可取消的無介面任務。無介面任務是指不需使用者介面即可執行的程式碼片段。

**參數：**

| Name                                                                                  | Type                                                 | Description                                                                                                                                                                                                                         |
| ------------------------------------------------------------------------------------- | ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| taskKey<br/><div className="label basic required two-lines">Required</div>            | string                                               | The native id for this task instance that was used when startHeadlessTask was called.                                                                                                                                               |
| taskProvider<br/><div className="label basic required two-lines">Required</div>       | [TaskProvider](appregistry#taskprovider)             | A promise returning function that takes some data passed from the native side as the only argument. When the promise is resolved or rejected the native side is notified of this event and it may decide to destroy the JS context. |
| taskCancelProvider<br/><div className="label basic required two-lines">Required</div> | [TaskCancelProvider](appregistry#taskcancelprovider) | a void returning function that takes no arguments; when a cancellation is requested, the function being executed by taskProvider should wrap up and return ASAP.                                                                    |

---

### `registerComponent()`

```tsx
static registerComponent(
  appKey: string,
  getComponentFunc: ComponentProvider,
  section?: boolean,
): string;
```

**參數：**

| Name                                                                   | Type              |
| ---------------------------------------------------------------------- | ----------------- |
| appKey <div className="label basic required">Required</div>            | string            |
| componentProvider <div className="label basic required">Required</div> | ComponentProvider |
| section                                                                | boolean           |

---

### `registerConfig()`

```tsx
static registerConfig(config: AppConfig[]);
```

**參數：**

| Name                                                        | Type                                 |
| ----------------------------------------------------------- | ------------------------------------ |
| config <div className="label basic required">Required</div> | [AppConfig](appregistry#appconfig)[] |

---

### `registerHeadlessTask()`

```tsx
static registerHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
);
```

註冊一個無介面任務。無介面任務是指不需使用者介面即可執行的程式碼片段。

此方法可讓應用程式在後台運行 JavaScript 任務，例如同步最新資料、處理推播通知或播放音樂。

**參數：**

| Name                                                                        | Type                                     | Description                                                                                                                                                                                                                         |
| --------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| taskKey <div className="label basic required two-lines">Required</div>      | string                                   | The native id for this task instance that was used when startHeadlessTask was called.                                                                                                                                               |
| taskProvider <div className="label basic required two-lines">Required</div> | [TaskProvider](appregistry#taskprovider) | A promise returning function that takes some data passed from the native side as the only argument. When the promise is resolved or rejected the native side is notified of this event and it may decide to destroy the JS context. |

---

### `registerRunnable()`

```tsx
static registerRunnable(appKey: string, func: Runnable): string;
```

**參數：**

| Name                                                        | Type     |
| ----------------------------------------------------------- | -------- |
| appKey <div className="label basic required">Required</div> | string   |
| run <div className="label basic required">Required</div>    | function |

---

### `registerSection()`

```tsx
static registerSection(
  appKey: string,
  component: ComponentProvider,
);
```

**參數：**

| Name                                                           | Type              |
| -------------------------------------------------------------- | ----------------- |
| appKey <div className="label basic required">Required</div>    | string            |
| component <div className="label basic required">Required</div> | ComponentProvider |

---

### `runApplication()`

```tsx
static runApplication(appKey: string, appParameters: any): void;
```

載入 JavaScript 套件包並運行應用程式。

**參數：**

| Name                                                               | Type   |
| ------------------------------------------------------------------ | ------ |
| appKey <div className="label basic required">Required</div>        | string |
| appParameters <div className="label basic required">Required</div> | any    |

---

### `setComponentProviderInstrumentationHook()`

```tsx
static setComponentProviderInstrumentationHook(
  hook: ComponentProviderInstrumentationHook,
);
```

**Parameters:**

| Name                                                      | Type     |
| --------------------------------------------------------- | -------- |
| hook <div className="label basic required">Required</div> | function |

A valid `hook` function accepts the following as arguments:

| Name                                                                         | Type               |
| ---------------------------------------------------------------------------- | ------------------ |
| component <div className="label basic required">Required</div>               | ComponentProvider  |
| scopedPerformanceLogger <div className="label basic required">Required</div> | IPerformanceLogger |

The function must also return a React Component.

---

### `setWrapperComponentProvider()`

```tsx
static setWrapperComponentProvider(
  provider: WrapperComponentProvider,
);
```

**Parameters:**

| Name                                                          | Type              |
| ------------------------------------------------------------- | ----------------- |
| provider <div className="label basic required">Required</div> | ComponentProvider |

---

### `startHeadlessTask()`

```tsx
static startHeadlessTask(
  taskId: number,
  taskKey: string,
  data: any,
);
```

Only called from native code. Starts a headless task.

**Parameters:**

| Name                                                         | Type   | Description                                                          |
| ------------------------------------------------------------ | ------ | -------------------------------------------------------------------- |
| taskId <div className="label basic required">Required</div>  | number | The native id for this task instance to keep track of its execution. |
| taskKey <div className="label basic required">Required</div> | string | The key for the task to start.                                       |
| data <div className="label basic required">Required</div>    | any    | The data to pass to the task.                                        |

---

### `unmountApplicationComponentAtRootTag()`

```tsx
static unmountApplicationComponentAtRootTag(rootTag: number);
```

Stops an application when a view should be destroyed.

**Parameters:**

| Name                                                         | Type   |
| ------------------------------------------------------------ | ------ |
| rootTag <div className="label basic required">Required</div> | number |

## Type Definitions

### AppConfig

Application configuration for the `registerConfig` method.

| Type   |
| ------ |
| object |

**Properties:**

| Name                                                        | Type              |
| ----------------------------------------------------------- | ----------------- |
| appKey <div className="label basic required">Required</div> | string            |
| component                                                   | ComponentProvider |
| run                                                         | function          |
| section                                                     | boolean           |

> **Note:** Every config is expected to set either `component` or `run` function.

### Registry

| Type   |
| ------ |
| object |

**Properties:**

| Name      | Type                                       |
| --------- | ------------------------------------------ |
| runnables | array of [Runnables](appregistry#runnable) |
| sections  | array of strings                           |

### Runnable

| Type   |
| ------ |
| object |

**Properties:**

| Name      | Type              |
| --------- | ----------------- |
| component | ComponentProvider |
| run       | function          |

### Runnables

An object with key of `appKey` and value of type of [`Runnable`](appregistry#runnable).

| Type   |
| ------ |
| object |

### Task

A `Task` is a function that accepts any data as argument and returns a Promise that resolves to `undefined`.

| Type     |
| -------- |
| function |

### TaskCanceller

A `TaskCanceller` is a function that accepts no argument and returns void.

| Type     |
| -------- |
| function |

### TaskCancelProvider

A valid `TaskCancelProvider` is a function that returns a [`TaskCanceller`](appregistry#taskcanceller).

| Type     |
| -------- |
| function |

### TaskProvider

A valid `TaskProvider` is a function that returns a [`Task`](appregistry#task).

| Type     |
| -------- |
| function |