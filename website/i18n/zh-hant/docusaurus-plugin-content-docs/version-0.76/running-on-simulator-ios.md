---
id: running-on-simulator-ios
title: Running On Simulator
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 啟動模擬器

當您的 React Native 專案初始化完成後，可以在新建立的專案目錄中執行以下指令。

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios
```

</TabItem>
</Tabs>

若所有設定皆正確，您應該很快就能在 iOS 模擬器中看到新應用程式運行。

## 指定裝置

您可以使用 `--simulator` 參數指定模擬器運行的裝置，後接裝置名稱字串。預設值為 `"iPhone 14"`。若要在 iPhone SE（第 3 代）上運行應用程式，請執行以下指令：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios -- --simulator="iPhone SE (3rd generation)"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios --simulator "iPhone SE (3rd generation)"
```

</TabItem>
</Tabs>

裝置名稱對應 Xcode 中可用的裝置列表。您可以透過在終端機執行 `xcrun simctl list devices` 來查看可用裝置。

### 指定裝置版本

若您安裝了多個 iOS 版本，還需指定相應版本。例如，要在 iPhone 14 Pro（16.0 版）上運行應用程式，請執行以下指令：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios -- --simulator="iPhone 14 Pro (16.0)"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios --simulator "iPhone 14 Pro (16.0)"
```

</TabItem>
</Tabs>

## 指定 UDID

您可以指定從 `xcrun simctl list devices` 指令返回的裝置 UDID。例如，要使用 UDID `AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA` 運行應用程式，請執行以下指令：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run ios -- --udid="AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
```

</TabItem>
<TabItem value="yarn">

```shell
yarn ios --udid "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
```

</TabItem>
</Tabs>