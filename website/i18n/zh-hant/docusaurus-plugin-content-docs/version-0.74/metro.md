---
id: metro
title: Metro
---

React Native 使用 [Metro](https://metrobundler.dev/) 來構建您的 JavaScript 代碼和資源。

## 配置 Metro

Metro 的配置選項可以在您項目的 `metro.config.js` 文件中進行自定義。該文件可以導出以下兩種形式：

- **一個對象（推薦）**，該對象將合併到 Metro 的內部默認配置之上。
- [**一個函數**](#advanced-using-a-config-function)，該函數將被調用並傳入 Metro 的內部默認配置，並應返回最終的配置對象。

:::tip
請參閱 Metro 網站上的 [**配置 Metro**](https://metrobundler.dev/docs/configuration) 以獲取所有可用配置選項的文檔。
:::

在 React Native 中，您的 Metro 配置應擴展 [`@react-native/metro-config`](https://www.npmjs.com/package/@react-native/metro-config) 或 [`@expo/metro-config`](https://www.npmjs.com/package/@expo/metro-config)。這些包包含了構建和運行 React Native 應用程序所必需的默認配置。

以下是 React Native 模板項目中的默認 `metro.config.js` 文件：

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

您可以在 `config` 對象中自定義您希望修改的 Metro 選項。

### 進階：使用配置函數

導出一個配置函數意味著您需要自行管理最終配置 — **Metro 不會應用任何內部默認配置**。這種模式在需要從 Metro 讀取基礎默認配置對象或動態設置選項時非常有用。

:::info
**從 `@react-native/metro-config` 0.72.1 版本開始**，不再需要使用配置函數來獲取完整的默認配置。請參閱下方的 **提示** 部分。
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
使用配置函數適用於高級用例。比上述方法更簡單的方式（例如自定義 `sourceExts`）是從 `@react-native/metro-config` 中讀取這些默認值。

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

**但是！** 我們建議在覆蓋這些配置值時複製並編輯 — 將配置的來源放在您的配置文件中。

✅ **推薦**

<!-- prettier-ignore -->
```js
const config = {
  resolver: {
    sourceExts: ['js', 'ts', 'tsx', 'svg'],
  },
};
```

:::

## 了解更多關於 Metro 的資訊

- [Metro 網站](https://metrobundler.dev/)
- [視頻：在 App.js 2023 上的 "Metro & React Native DevX" 演講](https://www.youtube.com/watch?v=c9D4pg0y9cI)