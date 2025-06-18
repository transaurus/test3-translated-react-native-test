---
id: speeding-ci-builds
title: Speeding Up CI Builds
---

您或您的公司可能已建立持續整合(CI)環境來測試React Native應用程式。

快速的CI服務之所以重要，主要有兩個原因：

- CI機器運行的時間越長，成本就越高
- CI任務執行時間越長，開發循環就越長

因此，盡量減少CI環境構建React Native的時間非常重要。

## 為iOS禁用Flipper

[Flipper](https://github.com/facebook/flipper)是React Native預設附帶的除錯工具，用於幫助開發者除錯和分析React Native應用程式。然而在CI環境中並不需要Flipper：您或同事不太可能需要除錯CI環境中構建的應用程式。

對於iOS應用程式，每次構建React Native框架時都會構建Flipper，這可能需要一些時間，而這些時間是可以節省的。

從React Native 0.71開始，我們在模板的Podfile中引入了一個新標誌：[`NO_FLIPPER`標誌](https://github.com/facebook/react-native/blob/main/packages/react-native/template/ios/Podfile#L20)。

預設情況下，`NO_FLIPPER`標誌未設置，因此Flipper會預設包含在您的應用程式中。

您可以在安裝iOS pods時指定`NO_FLIPPER=1`，指示React Native不安裝Flipper。通常，命令如下所示：

```shell
# from the root folder of the react native project
NO_FLIPPER=1 bundle exec pod install --project-directory=ios
```

在CI環境中添加此命令可以跳過Flipper依賴項的安裝，從而節省時間和金錢。

### 處理傳遞依賴項

您的應用程式可能使用了一些依賴於Flipper pods的函式庫。如果是這種情況，僅使用`NO_FLIPPER`標誌禁用Flipper可能不夠：您的應用程式可能會因此無法構建。

處理這種情況的正確方法是為react native添加自定義配置，指示應用程式正確安裝傳遞依賴項。具體做法如下：

1. 如果尚未創建，請新建一個名為`react-native.config.js`的文件
2. 當標誌開啟時，明確從`dependencies`中排除傳遞依賴項

例如，`react-native-flipper`函式庫是一個依賴於Flipper的附加函式庫。如果您的應用程式使用了它，則需要將其從依賴項中排除。您的`react-native.config.js`文件應如下所示：

```js title="react-native.config.js"
module.exports = {
  // other fields
  dependencies: {
    ...(process.env.NO_FLIPPER
      ? {'react-native-flipper': {platforms: {ios: null}}}
      : {}),
  },
};
```