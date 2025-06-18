---
id: platform-specific-code
title: Platform-Specific Code
---

在開發跨平台應用程式時，您會希望盡可能重複使用程式碼。但某些情況下可能需要針對不同平台實作差異化程式碼，例如您可能想為 Android 和 iOS 分別實作不同的視覺元件。

React Native 提供兩種方式來組織程式碼並依平台進行區分：

- 使用 [`Platform` 模組](platform-specific-code.md#platform-module)
- 使用 [平台特定副檔名](platform-specific-code.md#platform-specific-extensions)

某些元件可能含有僅在特定平台有效的屬性。這些屬性均標註有 `@platform` 標記，並在官方網站上顯示對應的平台標籤。

## Platform 模組

React Native 提供了一個能檢測應用程式運行平台的模組。您可運用此檢測邏輯來實作平台特定程式碼。當只有元件的小部分需要區分平台時，建議採用此方式。

```tsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

`Platform.OS` 在 iOS 平台會回傳 `ios`，在 Android 平台則回傳 `android`。

另提供 `Platform.select` 方法，該方法接收一個鍵值為 `'ios' | 'android' | 'native' | 'default'` 的物件，會根據當前運行平台返回最匹配的值。具體而言，在手機環境下會優先採用 `ios` 和 `android` 鍵值；若未指定，則依序使用 `native` 和 `default` 鍵值。

```tsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: 'red',
      },
      android: {
        backgroundColor: 'green',
      },
      default: {
        // other platforms, web for example
        backgroundColor: 'blue',
      },
    }),
  },
});
```

此範例會使容器在所有平台具有 `flex: 1` 樣式，在 iOS 顯示紅色背景、Android 顯示綠色背景，其他平台則顯示藍色背景。

由於該方法接受 `any` 型別值，您也可用它來返回平台特定元件，如下所示：

```tsx
const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid'),
})();

<Component />;
```

```tsx
const Component = Platform.select({
  native: () => require('ComponentForNative'),
  default: () => require('ComponentForWeb'),
})();

<Component />;
```

### 檢測 Android 版本

在 Android 平台，`Platform` 模組還可用於檢測應用程式運行的 Android 平台版本：

```tsx
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

**注意**：`Version` 對應的是 Android API 版本號，而非 Android 作業系統版本號。版本對照表請參閱 [Android 版本歷史](https://en.wikipedia.org/wiki/Android_version_history#Overview)。

### 檢測 iOS 版本

在 iOS 平台，`Version` 是透過 `-[UIDevice systemVersion]` 取得的字串，表示當前作業系統版本（例如 "10.3"）。以下示範如何檢測 iOS 的主要版本號：

```tsx
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

## 平台特定副檔名

當平台特定程式碼較為複雜時，建議將程式碼拆分至獨立檔案。React Native 會自動識別 `.ios.` 或 `.android.` 副檔名，並在元件引用時載入對應的平台檔案。

例如，假設專案中包含以下檔案：

```shell
BigButton.ios.js
BigButton.android.js
```

您可透過以下方式引入該元件：

```tsx
import BigButton from './BigButton';
```

React Native 會根據運行平台自動選擇正確的檔案。

## 原生特定副檔名（與 NodeJS 及 Web 共用程式碼）

當模組需在 NodeJS/Web 與 React Native 之間共用且無 Android/iOS 差異時，可使用 `.native.js` 副檔名。此方式特別適用於 React Native 與 ReactJS 共用程式碼的專案。

例如，假設專案中包含以下檔案：

```shell
Container.js # picked up by webpack, Rollup or any other Web bundler
Container.native.js # picked up by the React Native bundler for both Android and iOS (Metro)
```

您仍可省略 `.native` 副檔名來引入該模組，如下所示：

```tsx
import Container from './Container';
```

**專業建議：** 設定您的網頁打包工具忽略 `.native.js` 副檔名，以避免在生產環境打包中包含未使用的程式碼，從而減少最終打包體積。