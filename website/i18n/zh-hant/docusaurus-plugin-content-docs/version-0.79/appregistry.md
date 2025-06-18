---
id: appregistry
title: AppRegistry
---

<div className="banner-native-code-required">
  <h3>Project with Native Code Required</h3>
  <p>If you are using the managed Expo workflow there is only ever one entry component registered with <code>AppRegistry</code> and it is handled automatically (or through <a href="https://docs.expo.dev/versions/latest/sdk/register-root-component/">registerRootComponent</a>). You do not need to use this API.</p>
</div>

`AppRegistry` 是所有 React Native 應用程式的 JavaScript 進入點。應用程式的根元件應透過 `AppRegistry.registerComponent` 註冊自身，之後原生系統便能載入應用程式的套件包，並在準備就緒時透過呼叫 `AppRegistry.runApplication` 實際執行應用程式。

```tsx
import {Text, AppRegistry} from 'react-native';

const App = () => (
  <View>
    <Text>App1</Text>
  </View>
);

AppRegistry.registerComponent('Appname', () => App);
```

若要「停止」應用程式（當視圖需要銷毀時），請使用傳入 `runApplication` 的標籤呼叫 `AppRegistry.unmountApplicationComponentAtRootTag`。這些方法應始終配對使用。

`AppRegistry` 應在 `require` 序列的早期階段引入，以確保 JavaScript 執行環境在其他模組被引入前已完成設定。

---

# 參考資料

## 方法

### `getAppKeys()`

```tsx
static getAppKeys(): string[];
```

回傳字串陣列。

---

### `getRegistry()`

```tsx
static getRegistry(): {sections: string[]; runnables: Runnable[]};
```

回傳 [Registry](appregistry#registry) 物件。

---

### `getRunnable()`

```tsx
static getRunnable(appKey: string): : Runnable | undefined;
```

回傳 [Runnable](appregistry#runnable) 物件。

**參數：**

| Name                                                        | Type   |
| ----------------------------------------------------------- | ------ |
| appKey <div className="label basic required">Required</div> | string |

---

### `getSectionKeys()`

```tsx
static getSectionKeys(): string[];
```

回傳字串陣列。

---

### `getSections()`

```tsx
static getSections(): Record<string, Runnable>;
```

回傳 [Runnables](appregistry#runnables) 物件。

---

### `registerCancellableHeadlessTask()`

```tsx
static registerCancellableHeadlessTask(
  taskKey: string,
  taskProvider: TaskProvider,
  taskCancelProvider: TaskCancelProvider,
);
```

註冊可取消的無介面任務。無介面任務是指不需使用者介面即可執行的程式碼片段。

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

註冊無介面任務。無介面任務是指不需使用者介面即可執行的程式碼片段。

此方法可用於在應用程式處於背景狀態時執行 JavaScript 任務，例如同步最新資料、處理推播通知或播放音樂。

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

載入 JavaScript 套件包並執行應用程式。

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

用於 `registerConfig` 方法的應用程序配置。

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

> **注意：** 每個配置都應設置 `component` 或 `run` 函數。

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

`TaskCanceller` 是一個不接受參數且無返回值的函數。

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