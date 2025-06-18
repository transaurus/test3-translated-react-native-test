---
id: metro
title: Metro
---

React Native 使用 [Metro](https://metrobundler.dev/) 來建置您的 JavaScript 程式碼與資源。

## 設定 Metro

Metro 的設定選項可透過專案中的 `metro.config.js` 檔案進行自訂。此檔案可匯出以下兩種形式：

- **物件（推薦）**：將與 Metro 的內部預設設定合併。
- [**函式**](#advanced-using-a-config-function)：接收 Metro 的內部預設設定後，應回傳最終的設定物件。

:::tip
請參閱 Metro 官網的 [**設定 Metro**](https://metrobundler.dev/docs/configuration) 文件，了解所有可用的設定選項。
:::

在 React Native 中，您的 Metro 設定應繼承 [`@react-native/metro-config`](https://www.npmjs.com/package/@react-native/metro-config) 或 [`@expo/metro-config`](https://www.npmjs.com/package/@expo/metro-config)。這些套件包含建置與執行 React Native 應用程式所需的必要預設值。

以下是 React Native 範本專案中的預設 `metro.config.js` 檔案：

<!-- prettier-ignore -->

```js
const {getDefaultConfig, mergeConfig} = require('@react-native/metro-config');

/**
 * Metro configuration
 * https://metrobundler.dev/docs/configuration
 *
 * @type {import('metro-config').MetroConfig}
 */
const config = {};

module.exports = mergeConfig(getDefaultConfig(__dirname), config);
```

您可以在 `config` 物件中自訂所需的 Metro 選項。

### 進階：使用設定函式

匯出設定函式代表您選擇自行管理最終設定——**Metro 將不會套用任何內部預設值**。當需要從 Metro 讀取基礎預設設定物件或動態設定選項時，此模式會很有用。

:::info
**自 `@react-native/metro-config` 0.72.1 版本起**，不再需要使用設定函式來存取完整的預設設定。請參閱下方的**提示**段落。
:::

<!-- prettier-ignore -->

```js
const {getDefaultConfig, mergeConfig} = require('@react-native/metro-config');

module.exports = function (baseConfig) {
  const defaultConfig = mergeConfig(baseConfig, getDefaultConfig(__dirname));
  const {resolver: {assetExts, sourceExts}} = defaultConfig;

  return mergeConfig(
    defaultConfig,
    {
      resolver: {
        assetExts: assetExts.filter(ext => ext !== 'svg'),
        sourceExts: [...sourceExts, 'svg'],
      },
    },
  );
};
```

:::tip
使用設定函式適用於進階使用情境。相較於上述方法，更簡單的方式（例如自訂 `sourceExts`）是從 `@react-native/metro-config` 讀取這些預設值。

**替代方案**

<!-- prettier-ignore -->
```js
const defaultConfig = getDefaultConfig(__dirname);

const config = {
  resolver: {
    sourceExts: [...defaultConfig.resolver.sourceExts, 'svg'],
  },
};

module.exports = mergeConfig(defaultConfig, config);
```

**但！** 我們建議在覆寫這些設定值時採用複製並編輯的方式——將真實來源保留在您的設定檔中。

✅ **推薦做法**

<!-- prettier-ignore -->
```js
const config = {
  resolver: {
    sourceExts: ['js', 'ts', 'tsx', 'svg'],
  },
};
```

:::

## 深入了解 Metro

- [Metro 官網](https://metrobundler.dev/)
- [影片：App.js 2023 研討會的「Metro 與 React Native 開發者體驗」演講](https://www.youtube.com/watch?v=c9D4pg0y9cI)