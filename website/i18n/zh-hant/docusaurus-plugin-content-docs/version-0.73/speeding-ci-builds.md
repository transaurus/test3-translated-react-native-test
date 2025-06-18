---
id: speeding-ci-builds
title: Speeding Up CI Builds
---

您或您的公司可能已設置了持續整合（CI）環境來測試您的 React Native 應用程式。

快速的 CI 服務之所以重要，主要有兩個原因：

- CI 機器運行的時間越長，成本就越高。
- CI 任務運行的時間越長，開發循環的時間也就越長。

因此，盡量減少 CI 環境構建 React Native 的時間是非常重要的。

## 為 iOS 停用 Flipper

[Flipper](https://github.com/facebook/flipper) 是 React Native 預設提供的除錯工具，用於幫助開發者除錯和分析他們的 React Native 應用程式。然而，在 CI 環境中並不需要 Flipper：您或您的同事不太可能需要除錯在 CI 環境中構建的應用程式。

對於 iOS 應用程式，每次構建 React Native 框架時都會構建 Flipper，這可能需要一些時間，而這些時間是可以節省的。

從 React Native 0.71 開始，我們在模板的 Podfile 中引入了一個新標誌：[`NO_FLIPPER` 標誌](https://github.com/facebook/react-native/blob/main/packages/react-native/template/ios/Podfile#L20)。

預設情況下，`NO_FLIPPER` 標誌並未設置，因此 Flipper 會預設包含在您的應用程式中。

您可以在安裝 iOS pods 時指定 `NO_FLIPPER=1`，以指示 React Native 不安裝 Flipper。通常，命令會如下所示：

```shell
# from the root folder of the react native project
NO_FLIPPER=1 bundle exec pod install --project-directory=ios
```

在您的 CI 環境中添加此命令，以跳過 Flipper 依賴項的安裝，從而節省時間和金錢。

### 處理傳遞依賴

您的應用程式可能使用了一些依賴於 Flipper pods 的函式庫。如果是這種情況，僅使用 `NO_FLIPPER` 標誌停用 Flipper 可能不夠：您的應用程式可能會因此無法構建。

處理這種情況的正確方法是為 React Native 添加一個自定義配置，指示應用程式正確安裝傳遞依賴。為此：

1. 如果尚未擁有，請創建一個名為 `react-native.config.js` 的新文件。
2. 當標誌開啟時，明確從 `dependency` 中排除傳遞依賴。

例如，`react-native-flipper` 函式庫是一個依賴於 Flipper 的附加函式庫。如果您的應用程式使用了它，您需要將其從依賴項中排除。您的 `react-native.config.js` 文件會如下所示：

```js title="react-native.config.js"
module.exports = {
  // other fields
  dependency: {
    ...(process.env.NO_FLIPPER
      ? {'react-native-flipper': {platforms: {ios: null}}}
      : {}),
  },
};
```