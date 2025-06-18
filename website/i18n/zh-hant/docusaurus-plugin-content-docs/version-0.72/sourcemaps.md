---
id: sourcemaps
title: Source Maps
---

原始碼映射(source maps)能將轉換後的檔案對應回原始來源檔案，主要用途是協助除錯及調查正式版建置中的問題。

若無原始碼映射，當正式版建置發生錯誤時，您將看到如下堆疊追蹤：

```text
TypeError: Cannot read property 'data' of undefined
  at anonymous(app:///index.android.bundle:1:4277021)
  at call(native)
  at p(app:///index.android.bundle:1:227785)
```

若已生成原始碼映射，堆疊追蹤會顯示原始檔案的完整路徑、檔名與行號：

```text
TypeError: Cannot read property 'data' of undefined
  at anonymous(src/modules/notifications/Permission.js:15:requestNotificationPermission)
  at call(native)
  at p(node_modules/regenerator-runtime/runtime.js:64:Generator)
```

這讓您能透過可解讀的堆疊追蹤來處理正式版問題。

請遵循以下指示開始使用原始碼映射。

## 在 Android 啟用原始碼映射

### Hermes 引擎

:::info
原始碼映射在 Release 模式預設會自動建置，除非設定了 `hermesFlagsRelease` 參數。此情況下需手動啟用原始碼映射。
:::

請確認您應用程式的 `android/app/build.gradle` 檔案中有以下設定：

```groovy
project.ext.react = [
    enableHermes: true,
    hermesFlagsRelease: ["-O", "-output-source-map"], // plus whichever flag was required to set this away from default
]
```

設定正確時，您應能在 Metro 建置輸出中看到原始碼映射的輸出位置。

```text
Writing bundle output to:, android/app/build/generated/assets/react/release/index.android.bundle
Writing sourcemap output to:, android/app/build/intermediates/sourcemaps/react/release/index.android.bundle.packager.map
```

開發版建置不會產生 bundle 檔案（已包含符號表），但若開發版需打包時，可參照前述方式使用 `hermesFlagsDebug` 啟用原始碼映射。

## 在 iOS 啟用原始碼映射

原始碼映射預設為停用狀態。需定義 `SOURCEMAP_FILE` 環境變數來啟用。

請於 Xcode 中前往建置階段(Build Phase)的「Bundle React Native code and images」項目。

在檔案頂部其他環境變數旁，新增 `SOURCEMAP_FILE` 變數並指定輸出路徑與名稱，如下所示：

```
export SOURCEMAP_FILE="$(pwd)/../main.jsbundle.map";

export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

設定正確時，您應能在 Metro 建置輸出中看到原始碼映射的輸出位置。

```text
Writing bundle output to:, Build/Intermediates.noindex/ArchiveIntermediates/application/BuildProductsPath/Release-iphoneos/main.jsbundle
Writing sourcemap output to:, Build/Intermediates.noindex/ArchiveIntermediates/application/BuildProductsPath/Release-iphoneos/main.jsbundle.map
```

## 手動符號化

:::info
關於堆疊追蹤的手動符號化程序，請參閱[符號化說明頁面](symbolication.md)。
:::