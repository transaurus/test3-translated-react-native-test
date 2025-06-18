---
id: turbo-native-modules-ios
title: 'Turbo Native Modules: iOS'
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

現在是時候撰寫一些 iOS 平台程式碼，以確保 `localStorage` 在應用程式關閉後仍能保留資料。

## 準備您的 Xcode 專案

我們需要使用 Xcode 準備您的 iOS 專案。完成以下 **6 個步驟**後，您將獲得實現生成 `NativeLocalStorageSpec` 介面的 `RCTNativeLocalStorage`。

1. 開啟由 CocoPods 生成的 Xcode Workspace：

```bash
cd ios
open TurboModuleExample.xcworkspace
```

<img class="half-size" alt="Open Xcode Workspace" src="/docs/assets/turbo-native-modules/xcode/1.webp" />

2. 右鍵點選應用程式並選擇 <code>New Group</code>，將新群組命名為 `NativeLocalStorage`。

<img class="half-size" alt="Right click on app and select New Group" src="/docs/assets/turbo-native-modules/xcode/2.webp" />

3. 在 `NativeLocalStorage` 群組中，建立 <code>New</code>→<code>File from Template</code>。

<img class="half-size" alt="Create a new file using the Cocoa Touch Class template" src="/docs/assets/turbo-native-modules/xcode/3.webp" />

4. 使用 <code>Cocoa Touch Class</code> 模板。

<img class="half-size" alt="Use the Cocoa Touch Class template" src="/docs/assets/turbo-native-modules/xcode/4.webp"  />

5. 將 Cocoa Touch Class 命名為 <code>RCTNativeLocalStorage</code>，語言選擇 <code>Objective-C</code>。

<img class="half-size" alt="Create an Objective-C RCTNativeLocalStorage class" src="/docs/assets/turbo-native-modules/xcode/5.webp" />

6. 將 <code>RCTNativeLocalStorage.m</code> 重新命名為 <code>RCTNativeLocalStorage.mm</code>，使其成為 Objective-C++ 檔案。

<img class="half-size" alt="Convert to and Objective-C++ file" src="/docs/assets/turbo-native-modules/xcode/6.webp" />

## 使用 NSUserDefaults 實現 localStorage

首先更新 `RCTNativeLocalStorage.h`：

```objc title="NativeLocalStorage/RCTNativeLocalStorage.h"
//  RCTNativeLocalStorage.h
//  TurboModuleExample

#import <Foundation/Foundation.h>
// highlight-add-next-line
#import <NativeLocalStorageSpec/NativeLocalStorageSpec.h>

NS_ASSUME_NONNULL_BEGIN

// highlight-remove-next-line
@interface RCTNativeLocalStorage : NSObject
// highlight-add-next-line
@interface RCTNativeLocalStorage : NSObject <NativeLocalStorageSpec>

@end
```

接著更新我們的實作，使用帶有自訂 [suite name](https://developer.apple.com/documentation/foundation/nsuserdefaults/1409957-initwithsuitename) 的 `NSUserDefaults`。

```objc title="NativeLocalStorage/RCTNativeLocalStorage.mm"
//  RCTNativeLocalStorage.m
//  TurboModuleExample

#import "RCTNativeLocalStorage.h"

static NSString *const RCTNativeLocalStorageKey = @"local-storage";

@interface RCTNativeLocalStorage()
@property (strong, nonatomic) NSUserDefaults *localStorage;
@end

@implementation RCTNativeLocalStorage

RCT_EXPORT_MODULE(NativeLocalStorage)

- (id) init {
  if (self = [super init]) {
    _localStorage = [[NSUserDefaults alloc] initWithSuiteName:RCTNativeLocalStorageKey];
  }
  return self;
}

- (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:(const facebook::react::ObjCTurboModule::InitParams &)params {
  return std::make_shared<facebook::react::NativeLocalStorageSpecJSI>(params);
}

- (NSString * _Nullable)getItem:(NSString *)key {
  return [self.localStorage stringForKey:key];
}

- (void)setItem:(NSString *)value
          key:(NSString *)key {
  [self.localStorage setObject:value forKey:key];
}

- (void)removeItem:(NSString *)key {
  [self.localStorage removeObjectForKey:key];
}

- (void)clear {
  NSDictionary *keys = [self.localStorage dictionaryRepresentation];
  for (NSString *key in keys) {
    [self removeItem:key];
  }
}

@end
```

需要注意的重要事項：

- `RCT_EXPORT_MODULE` 使用我們在 JavaScript 環境中存取模組的識別碼 `NativeLocalStorage` 來匯出並註冊模組。詳情請參閱這些[文件](./legacy/native-modules-ios#module-name)。
- 您可以使用 Xcode 跳轉至 Codegen 生成的 `@protocol NativeLocalStorageSpec`，也可以讓 Xcode 為您生成存根(stubs)。

## 在模擬器上建置並執行您的程式碼

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">
```bash
npm run ios
```
</TabItem>
<TabItem value="yarn">
```bash
yarn run ios
```
</TabItem>
</Tabs>

<video width="30%" height="30%" playsinline="true" autoplay="true" muted="true" loop="true">
    <source src="/docs/assets/turbo-native-modules/turbo-native-modules-ios.webm" type="video/webm" />
    <source src="/docs/assets/turbo-native-modules/turbo-native-modules-ios.mp4" type="video/mp4" />
</video>