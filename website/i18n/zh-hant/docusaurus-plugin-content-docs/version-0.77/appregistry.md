---
id: appregistry
title: AppRegistry
---

<div className="banner-native-code-required">
  <h3>Project with Native Code Required</h3>
  <p>If you are using the managed Expo workflow there is only ever one entry component registered with <code>AppRegistry</code> and it is handled automatically (or through <a href="https://docs.expo.dev/versions/latest/sdk/register-root-component/">registerRootComponent</a>). You do not need to use this API.</p>
</div>

`AppRegistry` is the JS entry point to running all React Native apps. App root components should register themselves with `AppRegistry.registerComponent`, then the native system can load the bundle for the app and then actually run the app when it's ready by invoking `AppRegistry.runApplication`.

```tsx
import {Text, AppRegistry} from 'react-native';

const App = () => (
  <View>
    <Text>App1</Text>
  </View>
);

AppRegistry.registerComponent('Appname', () => App);
```

To "stop" an application when a view should be destroyed, call `AppRegistry.unmountApplicationComponentAtRootTag` with the tag that was passed into `runApplication`. These should always be used as a pair.

`AppRegistry` should be required early in the `require` sequence to make sure the JS execution environment is setup before other modules are required.

---

# Reference

## Methods

### `getAppKeys()`

```tsx
static getAppKeys(): string[];
```

Returns an array of strings.

---

### `getRegistry()`

```tsx
static getRegistry(): {sections: string[]; runnables: Runnable[]};
```

Returns a [Registry](appregistry#registry) object.

---

### `getRunnable()`

```tsx
static getRunnable(appKey: string): : Runnable | undefined;
```

Returns a [Runnable](appregistry#runnable) object.

**Parameters:**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| appKey <div className="label basic required">Required</div> | string |

---

### `getSectionKeys()`

```tsx
static getSectionKeys(): string[];
```

Returns an array of strings.

---

### `getSections()`

```tsx
static getSections(): Record<string, Runnable>;
```

Returns a [Runnables](appregistry#runnables) object.

---

### `registerCancellableHeadlessTask()`

```tsx
static registerCancellableHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
  taskCancelProvider: TaskCancelProvider,
);
```

Register a headless task which can be cancelled. A headless task is a bit of code that runs without a UI.

**Parameters:**

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

**Parameters:**

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

**Parameters:**

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

Register a headless task. A headless task is a bit of code that runs without a UI.

This is a way to run tasks in JavaScript while your app is in the background. It can be used, for example, to sync fresh data, handle push notifications, or play music.

**Parameters:**

| Name                                                                        | Type                                     | Description                                                                                                                                                                                                                         |
| --------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| taskKey <div className="label basic required two-lines">Required</div>      | string                                   | The native id for this task instance that was used when startHeadlessTask was called.                                                                                                                                               |
| taskProvider <div className="label basic required two-lines">Required</div> | [TaskProvider](appregistry#taskprovider) | A promise returning function that takes some data passed from the native side as the only argument. When the promise is resolved or rejected the native side is notified of this event and it may decide to destroy the JS context. |

---

### `registerRunnable()`

```tsx
static registerRunnable(appKey: string, func: Runnable): string;
```

**Parameters:**

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

**Parameters:**

| Name                                                           | Type              |
| -------------------------------------------------------------- | ----------------- |
| appKey <div className="label basic required">Required</div>    | string            |
| component <div className="label basic required">Required</div> | ComponentProvider |

---

### `runApplication()`

```tsx
static runApplication(appKey: string, appParameters: any): void;
```

Loads the JavaScript bundle and runs the app.

**Parameters:**

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

**參數：**

| Name                                                      | Type     |
| --------------------------------------------------------- | -------- |
| hook <div className="label basic required">Required</div> | function |

有效的 `hook` 函數需接受以下參數：

| Name                                                                         | Type               |
| ---------------------------------------------------------------------------- | ------------------ |
| component <div className="label basic required">Required</div>               | ComponentProvider  |
| scopedPerformanceLogger <div className="label basic required">Required</div> | IPerformanceLogger |

該函數必須返回一個 React 組件。

---

### `setWrapperComponentProvider()`

```tsx
static setWrapperComponentProvider(
  provider: WrapperComponentProvider,
);
```

**參數：**

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

僅由原生代碼調用。啟動一個無界面任務。

**參數：**

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

當視圖需要銷毀時停止應用程序。

**參數：**

| Name                                                         | Type   |
| ------------------------------------------------------------ | ------ |
| rootTag <div className="label basic required">Required</div> | number |

## 類型定義

### AppConfig

`registerConfig` 方法的應用程序配置。

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

> **注意：** 每個配置都需設置 `component` 或 `run` 函數。

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

一個鍵為 `appKey`、值為 [`Runnable`](appregistry#runnable) 類型的對象。

| Type   |
| ------ |
| object |

### Task

`Task` 是一個接受任意數據作為參數並返回解析為 `undefined` 的 Promise 的函數。

| Type     |
| -------- |
| function |

### TaskCanceller

`TaskCanceller` 是一個不接受參數且返回 void 的函數。

| Type     |
| -------- |
| function |

### TaskCancelProvider

有效的 `TaskCancelProvider` 是一個返回 [`TaskCanceller`](appregistry#taskcanceller) 的函數。

| Type     |
| -------- |
| function |

### TaskProvider

有效的 `TaskProvider` 是一個返回 [`Task`](appregistry#task) 的函數。

| Type     |
| -------- |
| function |