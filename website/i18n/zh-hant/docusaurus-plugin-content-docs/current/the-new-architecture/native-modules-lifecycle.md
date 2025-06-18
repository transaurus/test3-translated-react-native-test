import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

# Native Modules 生命週期

在 React Native 中，Native Modules 是單例模式。Native Module 基礎設施會在首次存取時惰性建立一個 Native Module，並在應用程式需要時保持其存在。這是一種效能優化，可避免在應用程式啟動時急切建立 Native Modules 的開銷，並確保更快的啟動時間。

在純 React Native 應用程式中，Native Modules 只會建立一次且永遠不會被銷毀。然而，在更複雜的應用程式中，可能會出現需要銷毀並重新建立 Native Modules 的使用情境。例如，想像一個混合了部分原生視圖與 React Native 介面的棕地應用程式，如[與現有應用程式整合指南](/docs/integration-with-existing-apps)所述。在這種情況下，當使用者導航離開 React Native 介面時銷毀 React Native 實例，並在使用者返回該介面時重新建立它，可能是合理的做法。

當這種情況發生時，無狀態的 Native Modules 不會造成任何問題。然而，對於有狀態的 Native Modules，可能需要正確地使其失效，以確保狀態被重置且資源被釋放。

在本指南中，您將探索如何正確初始化和使 Native Module 失效。本指南假設您已熟悉如何撰寫 Native Module 並能輕鬆撰寫原生程式碼。如果您不熟悉 Native Modules，請先閱讀 [Native Modules 指南](/docs/next/turbo-native-modules-introduction)。

## Android

在 Android 上，所有 Native Modules 都已實作 [TurboModule](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/turbomodule/core/interfaces/TurboModule.kt) 介面，該介面定義了兩個方法：`initialize()` 和 `invalidate()`。

`initialize()` 方法會在 Native Module 建立時由 Native Module 基礎設施呼叫。這是放置所有需要存取 ReactApplicationContext 的初始化程式碼的最佳位置。以下是一些核心 Native Modules 實作 `initialize()` 方法的範例：[BlobModule](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/modules/blob/BlobModule.java#L155-L157)、[NetworkingModule](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/modules/network/NetworkingModule.java#L193-L197)。

`invalidate()` 方法會在 Native Module 銷毀時由 Native Module 基礎設施呼叫。這是放置所有清理程式碼、重置 Native Module 狀態以及釋放不再需要的資源（如記憶體和檔案）的最佳位置。以下是一些核心 Native Modules 實作 `invalidate()` 方法的範例：[DeviceInfoModule](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/modules/deviceinfo/DeviceInfoModule.kt#L72-L76)、[NetworkModule](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/modules/network/NetworkingModule.java#L200-L212)。

## iOS

在 iOS 上，Native Modules 遵循 [`RCTTurboModule`](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/ReactCommon/react/nativemodule/core/platform/ios/ReactCommon/RCTTurboModule.h#L196-L200) 協定。然而，此協定並未公開 Android 的 `TurboModule` 類別所公開的 `initialize` 和 `invalidate` 方法。

相反地，在 iOS 上，有兩個額外的協定：[`RCTInitializing`](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/React/Base/RCTInitializing.h) 和 [`RCTInvalidating`](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/React/Base/RCTInvalidating.h)。這些協定分別用於定義 `initialize` 和 `invalidate` 方法。

如果你的模組需要執行一些初始化代碼，那麼你可以遵循 `RCTInitializing` 協定並實作 `initialize` 方法。要做到這一點，你需要：

1. 修改 `NativeModule.h` 文件，添加以下行：

```diff title="NativeModule.h"
+ #import <React/RCTInitializing.h>

//...

- @interface NativeModule : NSObject <NativeModuleSpec>
+ @interface NativeModule : NSObject <NativeModuleSpec, RCTInitializing>
//...
@end
```

2. 在 `NativeModule.mm` 文件中實作 `initialize` 方法：

```diff title="NativeModule.mm"
// ...

@implementation NativeModule

+- (void)initialize {
+ // add the initialization code here
+}

@end
```

以下是一些核心 Native Modules 實作 `initialize` 方法的範例：[RCTBlobManager](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/Libraries/Blob/RCTBlobManager.mm#L58-L68)、[RCTTiming](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/React/CoreModules/RCTTiming.mm#L121-L124)。

如果你的模組需要執行一些清理代碼，那麼你可以遵循 `RCTInvalidating` 協定並實作 `invalidate` 方法。要做到這一點，你需要：

1. 修改 `NativeModule.h` 文件，添加以下行：

```diff title="NativeModule.h"
+ #import <React/RCTInvalidating.h>

//...

- @interface NativeModule : NSObject <NativeModuleSpec>
+ @interface NativeModule : NSObject <NativeModuleSpec, RCTInvalidating>

//...

@end
```

2. 在 `NativeModule.mm` 文件中實作 `invalidate` 方法：

```diff title="NativeModule.mm"

// ...

@implementation NativeModule

+- (void)invalidate {
+ // add the cleanup code here
+}

@end
```

以下是一些核心 Native Modules 實作 `invalidate` 方法的範例：[RCTAppearance](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/React/CoreModules/RCTAppearance.mm#L151-L155)、[RCTDeviceInfo](https://github.com/facebook/react-native/blob/0617accecdcb11159ba15c34885f294bc206aa89/packages/react-native/React/CoreModules/RCTDeviceInfo.mm#L127-L133)。