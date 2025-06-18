# iOS - 在原生模組中使用 Swift

Swift 是開發 iOS 原生應用程式的官方預設語言。

本指南將帶您探索如何使用 Swift 編寫原生模組。

:::note
React Native 的核心主要使用 C++ 編寫，而 Swift 與 C++ 之間的互通性並不理想，儘管 Apple 開發了[互通層](https://www.swift.org/documentation/cxx-interop/)。

因此，本指南中您將編寫的模組不會是純 Swift 實現，由於語言間的不相容性，您仍需編寫一些 Objective-C++ 黏合程式碼。本指南的目標是盡量減少所需的 Objective-C++ 程式碼量。如果您正將現有原生模組從舊架構遷移至新架構，此方法應能讓您重用大部分程式碼。
:::

本指南從 [原生模組](/docs/next/turbo-native-modules-introduction) 指南的 iOS 實現開始。  
在深入本指南前，請確保熟悉該指南內容，建議先實作該指南中的範例。

## 適配器模式

目標是使用 Swift 模組實現所有業務邏輯，並透過一層薄薄的 Objective-C++ 黏合層來連接應用程式與 Swift 實現。

您可以利用[適配器](https://en.wikipedia.org/wiki/Adapter_pattern)設計模式來連接 Swift 模組與 Objective-C++ 層。

Objective-C++ 物件由 React Native 創建，它持有 Swift 模組的參考並管理其生命週期。Objective-C++ 物件會將所有方法調用轉發至 Swift。

### 創建 Swift 模組

第一步是將實現從 Objective-C++ 層移至 Swift 層。

請依以下步驟操作：

1. 在 Xcode 專案中創建新檔案，命名為 `NativeLocalStorage.swift`
2. 按以下方式在 Swift 模組中添加實現：

```swift title="NativeLocalStorage.swift"
import Foundation

@objc public class NativeLocalStorage: NSObject {
  let userDefaults = UserDefaults(suiteName: "local-storage");


  @objc public func getItem(for key: String) -> String? {
    return userDefaults?.string(forKey: key)
  }

  @objc public func setItem(for key: String, value: String) {
    userDefaults?.set(value, forKey: key)
  }

  @objc public func removeItem(for key: String) {
    userDefaults?.removeObject(forKey: key)
  }

  @objc public  func clear() {
    userDefaults?.dictionaryRepresentation().keys.forEach { removeItem(for: $0) }
  }
}

```

請注意，您需要將所有需從 Objective-C 調用的方法宣告為 `public` 並加上 `@objc` 標註。  
同時記得讓您的類別繼承自 `NSObject`，否則將無法從 Objective-C 使用它。

### 更新 `RCTNativeLocalStorage` 檔案

接著，您需要更新 `RCTNativeLocalStorage` 的實現，使其能創建 Swift 模組並調用其方法。

1. 開啟 `RCTNativeLocalStorage.mm` 檔案
2. 按以下方式更新內容：

```diff title="RCTNativeLocalStorage.mm"
//  RCTNativeLocalStorage.m
//  TurboModuleExample

#import "RCTNativeLocalStorage.h"
+#import "SampleApp-Swift.h"

- static NSString *const RCTNativeLocalStorageKey = @"local-storage";

-@interface RCTNativeLocalStorage()
-@property (strong, nonatomic) NSUserDefaults *localStorage;
-@end

-@implementation RCTNativeLocalStorage
+@implementation RCTNativeLocalStorage {
+    NativeLocalStorage *storage;
+}

-RCT_EXPORT_MODULE(NativeLocalStorage)

 - (id) init {
   if (self = [super init]) {
-    _localStorage = [[NSUserDefaults alloc] initWithSuiteName:RCTNativeLocalStorageKey];
+    storage = [NativeLocalStorage new];
   }
   return self;
 }

 - (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:(const facebook::react::ObjCTurboModule::InitParams &)params {
   return std::make_shared<facebook::react::NativeLocalStorageSpecJSI>(params);
 }

 - (NSString * _Nullable)getItem:(NSString *)key {
-   return [self.localStorage stringForKey:key];
+   return [storage getItemFor:key];
 }

 - (void)setItem:(NSString *)value key:(NSString *)key {
-   [self.localStorage setObject:value forKey:key];
+   [storage setItemFor:key value:value];
 }

 - (void)removeItem:(NSString *)key {
-   [self.localStorage removeObjectForKey:key];
+   [storage removeItemFor:key];
 }

 - (void)clear {
-   NSDictionary *keys = [self.localStorage dictionaryRepresentation];
-   for (NSString *key in keys) {
-     [self removeItem:key];
-   }
+  [storage clear];
 }

++ (NSString *)moduleName
+{
+  return @"NativeLocalStorage";
+}

@end
```

程式碼並未大幅變動。與直接創建 `NSUserDefaults` 參考不同，現在改為使用 Swift 實現創建新的 `NativeLocalStorage`，每當原生模組函數被調用時，調用會被轉發至 Swift 實現的 `NativeLocalStorage`。

請記得導入 `"SampleApp-Swift.h"` 標頭檔。這是 Xcode 自動生成的標頭檔，包含 Swift 檔案的公開 API，並以 Objective-C 可使用的格式呈現。標頭中的 `SampleApp` 部分實際上是您的應用名稱，若您創建應用時使用了**不同於** `SampleApp` 的名稱，則需相應調整。

另請注意，由於原生模組現在透過 [`package.json` 註冊](/docs/next/turbo-native-modules-introduction?platforms=ios#register-the-native-module-in-your-app)，因此不再需要 `RCT_EXPORT_MODULE` 宏。

此方法雖然在介面上會產生些許程式碼重複，但能讓您以最小額外成本重用現有程式碼庫中的 Swift 程式碼。

### 實現橋接標頭

:::note
如果您是函式庫作者，正在開發將作為獨立函式庫分發的原生模組，則此步驟並非必要。
:::

將 Swift 程式碼與 Objective-C++ 對應部分連接的最後一個必要步驟是橋接標頭檔（bridging header）。

橋接標頭檔是一個可以導入所有需要讓 Swift 程式碼可見的 Objective-C 標頭檔的檔案。

您的程式碼庫中可能已經有橋接標頭檔，但如果沒有，可以按照以下步驟創建一個新的：

1. 在 Xcode 中創建一個新檔案，並命名為 `"SampleApp-Bridging-Header.h"`
2. 更新 `"SampleApp-Bridging-Header.h"` 的內容如下：

```diff title="SampleApp-Bridging-Header.h"
//
//  Use this file to import your target's public headers that you would like to expose to Swift.
//

+ #import <React-RCTAppDelegate/RCTDefaultReactNativeFactoryDelegate.h>
```

3. 在專案中連結橋接標頭檔：
   1. 在專案導覽器中選擇您的應用名稱（左側的 `SampleApp`）
   2. 點擊 `Build Settings`
   3. 篩選 `"Bridging Header"`
   4. 添加橋接標頭檔的相對路徑，例如範例中的 `SampleApp-Bridging-Header.h`

![橋接標頭檔](/docs/assets/BridgingHeader.png)

## 建置並運行您的應用

現在您可以按照[原生模組指南](/docs/turbo-native-modules-introduction#build-and-run-your-code-on-a-simulator)的最後一步操作，應該可以看到您的應用運行時使用了以 Swift 編寫的原生模組。