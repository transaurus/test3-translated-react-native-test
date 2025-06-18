---
id: platform-specific-code
title: Platform Specific Code
---

在開發跨平台應用程式時，您會希望盡可能重複使用程式碼。但某些情況下可能需要針對不同平台實作差異化程式碼，例如為Android和iOS分別設計不同的視覺元件。

React Native提供兩種組織程式碼並按平台區分的方式：

- 使用[`Platform`模組](platform-specific-code.md#platform-module)
- 使用[平台特定副檔名](platform-specific-code.md#platform-specific-extensions)

某些元件可能包含僅在單一平台有效的屬性。這些屬性都標註有`@platform`標記，並在官網文件上顯示專屬標籤。

## Platform模組

React Native提供可檢測應用程式運行平台的模組。您可利用此檢測邏輯實作平台特定程式碼，適用於僅有少量元件需要平台差異化的情況。

```tsx
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

`Platform.OS`在iOS平台會返回`ios`，在Android平台則返回`android`。

另提供`Platform.select`方法，接收包含`'ios' | 'android' | 'native' | 'default'`鍵值的物件，會根據當前運行平台返回最匹配的值。手機設備會優先採用`ios`或`android`鍵值，若未指定則使用`native`鍵值，最後才使用`default`鍵值。

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

此範例將使容器在所有平台具有`flex: 1`樣式，iOS平台顯示紅色背景，Android平台顯示綠色背景，其他平台則顯示藍色背景。

由於該方法接受`any`型別值，您也可用它返回平台特定元件，如下所示：

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

### 檢測Android版本

在Android平台，`Platform`模組還可用於檢測應用程式運行的Android平台版本：

```tsx
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

**注意**：`Version`對應的是Android API版本號而非作業系統版本號。對照表請參考[Android版本歷史](https://en.wikipedia.org/wiki/Android_version_history#Overview)。

### 檢測iOS版本

在iOS平台，`Version`來自`-[UIDevice systemVersion]`方法，返回當前作業系統版本字串（例如"10.3"）。若要檢測主要版本號：

```tsx
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

## 平台特定副檔名

當平台特定程式碼較為複雜時，建議將程式碼拆分至獨立檔案。React Native會自動識別`.ios.`或`.android.`副檔名，並在元件引用時載入對應平台檔案。

例如專案中包含以下檔案：

```shell
BigButton.ios.js
BigButton.android.js
```

引用元件時只需如下導入：

```tsx
import BigButton from './BigButton';
```

React Native會根據運行平台自動選擇正確檔案。

## 原生特定副檔名（與NodeJS及Web共享程式碼）

當模組需在NodeJS/Web與React Native之間共享且無Android/iOS差異時，可使用`.native.js`副檔名。這對於需在React Native和ReactJS間共享程式碼的專案特別有用。

例如專案中包含以下檔案：

```shell
Container.js # picked up by webpack, Rollup or any other Web bundler
Container.native.js # picked up by the React Native bundler for both Android and iOS (Metro)
```

引用時仍可省略`.native`副檔名：

```tsx
import Container from './Container';
```

**專業建議：** 配置您的網頁打包工具忽略 `.native.js` 副檔名，以避免在生產環境打包中包含未使用的代碼，從而減少最終打包體積。