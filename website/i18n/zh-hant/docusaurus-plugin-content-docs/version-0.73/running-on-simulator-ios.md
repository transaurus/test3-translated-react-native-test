---
id: running-on-simulator-ios
title: Running On Simulator
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 啟動模擬器

當你初始化 React Native 專案後，可以在新建立的專案目錄中執行以下指令。

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

若所有設定皆正確完成，你應該很快就能在 iOS 模擬器中看到新應用程式運行。

## 指定裝置

你可以使用 `--simulator` 參數指定模擬器運行的裝置，後接裝置名稱字串。預設為 `"iPhone 14"`。若要在 iPhone SE（第三代）上運行應用程式，請執行以下指令：

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

裝置名稱對應 Xcode 中的可用裝置清單。可透過終端機執行 `xcrun simctl list devices` 來查閱可用裝置。

### 指定裝置版本

若已安裝多個 iOS 版本，還需指定對應版本。例如要在 iPhone 14 Pro（16.0 版）上運行應用程式，請執行：

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

可指定從 `xcrun simctl list devices` 指令取得的裝置 UDID。例如要使用 UDID `AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA` 運行應用程式，請執行：

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