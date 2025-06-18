---
id: platform-specific-code
title: Platform-Specific Code
---

在開發跨平台應用程式時，您會希望盡可能重複使用程式碼。有時可能會出現需要針對不同平台實作不同程式碼的情況，例如您可能想為 Android 和 iOS 分別實作不同的視覺元件。

React Native 提供兩種方式來組織程式碼並依平台進行區分：

- 使用 [`Platform` 模組](platform-specific-code.md#platform-module)
- 使用 [平台特定副檔名](platform-specific-code.md#platform-specific-extensions)

某些元件可能具有僅在單一平台上有效的屬性。所有這類屬性都會標註 `@platform` 註解，並在網站上顯示小徽章。

## Platform 模組

React Native 提供了一個能檢測應用程式運行平台的模組。您可以使用此檢測邏輯來實作平台特定程式碼。當只有元件的小部分需要平台特定處理時，請使用此選項。

```tsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

`Platform.OS` 在 iOS 上運行時會是 `ios`，在 Android 上運行時會是 `android`。

還有一個 `Platform.select` 方法可用，該方法接受一個物件（其鍵可以是 `'ios' | 'android' | 'native' | 'default'` 之一），並返回最適合當前運行平台的值。也就是說，如果在手機上運行，將優先使用 `ios` 和 `android` 鍵。如果未指定這些鍵，則會使用 `native` 鍵，然後是 `default` 鍵。

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

這將使容器在所有平台上都具有 `flex: 1`，在 iOS 上具有紅色背景，在 Android 上具有綠色背景，在其他平台上則具有藍色背景。

由於它接受 `any` 值，您也可以用它來返回平台特定的元件，如下所示：

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

### 檢測 Android 版本 <div class="label android" title="此部分與 Android 平台相關">Android</div>

在 Android 上，`Platform` 模組還可用於檢測應用程式運行的 Android 平台版本：

```tsx
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

**注意**：`Version` 設定為 Android API 版本而非 Android 作業系統版本。如需對照表，請參閱 [Android 版本歷史](https://en.wikipedia.org/wiki/Android_version_history#Overview)。

### 檢測 iOS 版本 <div class="label ios" title="此部分與 iOS 平台相關">iOS</div>

在 iOS 上，`Version` 是 `-[UIDevice systemVersion]` 的結果，這是一個包含當前作業系統版本的字串。系統版本的範例如 "10.3"。例如，要檢測 iOS 的主要版本號：

```tsx
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

## 平台特定副檔名

當您的平台特定程式碼較為複雜時，應考慮將程式碼拆分到單獨的檔案中。React Native 會檢測具有 `.ios.` 或 `.android.` 副檔名的檔案，並在需要時從其他元件載入相應的平台檔案。

例如，假設您的專案中有以下檔案：

```shell
BigButton.ios.js
BigButton.android.js
```

接著您可以如下方式匯入元件：

```tsx
import BigButton from './BigButton';
```

React Native 會根據運行平台自動選擇正確的檔案。

## 原生特定副檔名（例如與 NodeJS 和 Web 共享程式碼）

當模組需要在 NodeJS/Web 和 React Native 之間共享但沒有 Android/iOS 差異時，您也可以使用 `.native.js` 副檔名。這對於在 React Native 和 ReactJS 之間共享通用程式碼的專案特別有用。

舉例來說，假設你的專案中有以下檔案：

```shell
Container.js # picked up by webpack, Rollup or any other Web bundler
Container.native.js # picked up by the React Native bundler for both Android and iOS (Metro)
```

你仍然可以不加 `.native` 副檔名來導入該檔案，如下所示：

```tsx
import Container from './Container';
```

**專業建議：** 設定你的 Web 打包工具忽略 `.native.js` 副檔名，以避免在生產環境打包時包含未使用的程式碼，從而減少最終打包檔案的大小。