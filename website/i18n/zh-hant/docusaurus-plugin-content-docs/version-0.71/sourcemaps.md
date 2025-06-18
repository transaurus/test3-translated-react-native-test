---
id: sourcemaps
title: Source Maps
---

原始碼對應表(Source maps)能將轉換後的檔案映射回原始來源檔案，其主要用途是輔助除錯及協助調查正式版建置中的問題。

若無原始碼對應表，當正式版建置發生錯誤時，您將看到類似以下的堆疊追蹤：

```text
TypeError: Cannot read property 'data' of undefined
  at anonymous(app:///index.android.bundle:1:4277021)
  at call(native)
  at p(app:///index.android.bundle:1:227785)
```

若已生成原始碼對應表，堆疊追蹤將包含原始來源檔案的路徑、檔名與行號：

```text
TypeError: Cannot read property 'data' of undefined
  at anonymous(src/modules/notifications/Permission.js:15:requestNotificationPermission)
  at call(native)
  at p(node_modules/regenerator-runtime/runtime.js:64:Generator)
```

這能讓您根據更精確的堆疊追蹤來處理正式版問題。

請遵循以下指示開始使用原始碼對應表。

## 在 Android 上啟用原始碼對應表

### Hermes 引擎

:::info
原始碼對應表預設會在 Release 模式中建置。但若設定了 `hermesFlagsRelease` 參數，則需手動啟用原始碼對應表功能。
:::

若有此需求，請確認您應用程式的 `android/app/build.gradle` 檔案中包含以下設定：

```groovy
project.ext.react = [
    enableHermes: true,
    hermesFlagsRelease: ["-O", "-output-source-map"], // plus whichever flag was required to set this away from default
]
```

若設定正確，您可在 Metro 建置輸出中看到原始碼對應表的輸出位置。

```text
Writing bundle output to:, android/app/build/generated/assets/react/release/index.android.bundle
Writing sourcemap output to:, android/app/build/intermediates/sourcemaps/react/release/index.android.bundle.packager.map
```

開發版建置不會生成 bundle 檔案且本身已包含符號資訊，但若開發版建置需打包 bundle，可參照前述方式使用 `hermesFlagsDebug` 來啟用原始碼對應表。

## 在 iOS 上啟用原始碼對應表

原始碼對應表預設為停用狀態。需定義 `SOURCEMAP_FILE` 環境變數來啟用。

操作方式為：在 Xcode 中前往建置階段 - "Bundle React Native code and images"。

在檔案頂部其他 export 指令附近，加入 `SOURCEMAP_FILE` 的設定值並指定您偏好的路徑與名稱，如下所示：

```
export SOURCEMAP_FILE="$(pwd)/../main.jsbundle.map";

export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```

若設定正確，您可在 Metro 建置輸出中看到原始碼對應表的輸出位置。

```text
Writing bundle output to:, Build/Intermediates.noindex/ArchiveIntermediates/application/BuildProductsPath/Release-iphoneos/main.jsbundle
Writing sourcemap output to:, Build/Intermediates.noindex/ArchiveIntermediates/application/BuildProductsPath/Release-iphoneos/main.jsbundle.map
```

## 手動符號化

:::info
您可於[符號化](symbolication.md)頁面閱讀關於手動解析堆疊追蹤的詳細說明。
:::