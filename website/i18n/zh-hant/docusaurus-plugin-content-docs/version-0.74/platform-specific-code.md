---
id: platform-specific-code
title: Platform-Specific Code
---

在開發跨平台應用程式時，你會希望盡可能重複使用程式碼。某些情況下可能需要針對不同平台實作差異化程式碼，例如為 Android 和 iOS 分別實作不同的視覺元件。

React Native 提供兩種組織程式碼並依平台區分的方式：

- 使用 [`Platform` 模組](platform-specific-code.md#platform-module)
- 使用 [平台特定副檔名](platform-specific-code.md#platform-specific-extensions)

某些元件可能含有僅在特定平台有效的屬性。所有這類屬性都標註有 `@platform` 標記，並在官方網站上顯示小徽章。

## Platform 模組

React Native 提供可檢測應用程式運行平台的模組。你可利用此檢測邏輯來實作平台特定程式碼。當只有元件的小部分需要平台差異化時，適合採用此方式。

```tsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

`Platform.OS` 在 iOS 平台會回傳 `ios`，在 Android 平台則回傳 `android`。

另提供 `Platform.select` 方法，接收一個鍵值可為 `'ios' | 'android' | 'native' | 'default'` 的物件，會根據當前運行平台返回最匹配的值。手機平台上會優先採用 `ios` 或 `android` 鍵值，若未指定則使用 `native` 鍵值，最後才使用 `default` 鍵值。

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

此範例會使容器在所有平台具有 `flex: 1` 樣式，iOS 平台顯示紅色背景，Android 平台顯示綠色背景，其他平台則顯示藍色背景。

由於該方法接受 `any` 型別值，也可用來返回平台特定元件，如下所示：

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

### 檢測 Android 版本 <div class="label android" title="此章節與 Android 平台相關">Android</div>

在 Android 平台，`Platform` 模組還可用於檢測應用程式運行的 Android 平台版本：

```tsx
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

**注意**：`Version` 對應的是 Android API 版本號，而非 Android 作業系統版本號。版本對照請參考 [Android 版本歷史](https://en.wikipedia.org/wiki/Android_version_history#Overview)。

### 檢測 iOS 版本 <div class="label ios" title="此章節與 iOS 平台相關">iOS</div>

在 iOS 平台，`Version` 是透過 `-[UIDevice systemVersion]` 取得的字串，表示當前作業系統版本（例如 "10.3"）。若要檢測 iOS 的主要版本號：

```tsx
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

## 平台特定副檔名

當平台特定程式碼較為複雜時，建議將程式碼拆分至獨立檔案。React Native 會自動識別 `.ios.` 或 `.android.` 副檔名，並在需要時載入對應的平台檔案。

例如，假設你的專案中有以下檔案：

```shell
BigButton.ios.js
BigButton.android.js
```

接著可透過以下方式引入元件：

```tsx
import BigButton from './BigButton';
```

React Native 會根據運行平台自動選擇正確的檔案。

## 原生特定副檔名（與 NodeJS 和 Web 共享程式碼）

當模組需在 NodeJS/Web 與 React Native 之間共享且無 Android/iOS 差異時，可使用 `.native.js` 副檔名。這對於需在 React Native 和 ReactJS 之間共享程式碼的專案特別有用。

For example, say you have the following files in your project:

```shell
Container.js # picked up by webpack, Rollup or any other Web bundler
Container.native.js # picked up by the React Native bundler for both Android and iOS (Metro)
```

You can still import it without the `.native` extension, as follows:

```tsx
import Container from './Container';
```

**Pro tip:** Configure your Web bundler to ignore `.native.js` extensions in order to avoid having unused code in your production bundle, thus reducing the final bundle size.