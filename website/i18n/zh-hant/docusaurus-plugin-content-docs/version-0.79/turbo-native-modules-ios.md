---
id: turbo-native-modules-ios
title: 'Turbo Native Modules: iOS'
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

現在是時候編寫一些 iOS 平台代碼，以確保 `localStorage` 在應用程式關閉後仍然保留。

## 準備你的 Xcode 專案

我們需要使用 Xcode 準備你的 iOS 專案。完成以下 **6 個步驟** 後，你將擁有實現生成 `NativeLocalStorageSpec` 接口的 `RCTNativeLocalStorage`。

1. 打開由 CocoPods 生成的 Xcode Workspace：

```bash
cd ios
open TurboModuleExample.xcworkspace
```

<img class="half-size" alt="Open Xcode Workspace" src="/docs/assets/turbo-native-modules/xcode/1.webp" />

2. 右鍵點擊應用程式並選擇 <code>New Group</code>，將新群組命名為 `NativeLocalStorage`。

<img class="half-size" alt="Right click on app and select New Group" src="/docs/assets/turbo-native-modules/xcode/2.webp" />

3. 在 `NativeLocalStorage` 群組中，創建 <code>New</code>→<code>File from Template</code>。

<img class="half-size" alt="Create a new file using the Cocoa Touch Class template" src="/docs/assets/turbo-native-modules/xcode/3.webp" />

4. 使用 <code>Cocoa Touch Class</code>。

<img class="half-size" alt="Use the Cocoa Touch Class template" src="/docs/assets/turbo-native-modules/xcode/4.webp"  />

5. 將 Cocoa Touch Class 命名為 <code>RCTNativeLocalStorage</code>，並選擇 <code>Objective-C</code> 語言。

<img class="half-size" alt="Create an Objective-C RCTNativeLocalStorage class" src="/docs/assets/turbo-native-modules/xcode/5.webp" />

6. 將 <code>RCTNativeLocalStorage.m</code> 重新命名為 <code>RCTNativeLocalStorage.mm</code>，使其成為 Objective-C++ 文件。

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

然後更新我們的實現，使用帶有自定義 [suite name](https://developer.apple.com/documentation/foundation/nsuserdefaults/1409957-initwithsuitename) 的 `NSUserDefaults`。

```objc title="NativeLocalStorage/RCTNativeLocalStorage.mm"
//  RCTNativeLocalStorage.m
//  TurboModuleExample

#import "RCTNativeLocalStorage.h"

static NSString *const RCTNativeLocalStorageKey = @"local-storage";

@interface RCTNativeLocalStorage()
@property (strong, nonatomic) NSUserDefaults *localStorage;
@end

@implementation RCTNativeLocalStorage

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

+ (NSString *)moduleName
{
  return @"NativeLocalStorage";
}

@end
```

需要注意的重要事項：

- 你可以使用 Xcode 跳轉到 Codegen 的 `@protocol NativeLocalStorageSpec`。你也可以使用 Xcode 為你生成存根。

## 在應用程式中註冊原生模組

最後一步是更新 `package.json`，告訴 React Native 關於原生模組的 JS 規格與原生代碼中這些規格的具體實現之間的連結。

修改 `package.json` 如下：

```json title="package.json"
     "start": "react-native start",
     "test": "jest"
   },
   "codegenConfig": {
     "name": "AppSpecs",
     "type": "modules",
     "jsSrcsDir": "specs",
     "android": {
       "javaPackageName": "com.sampleapp.specs"
     }
     // highlight-add-start
     "ios":
        "modulesProvider": {
          "NativeLocalStorage": "RCTNativeLocalStorage"
        }
     // highlight-add-end
   },

   "dependencies": {
```

此時，你需要重新安裝 pods，以確保 codegen 再次運行並生成新文件：

```bash
# from the ios folder
bundle exec pod install
open SampleApp.xcworkspace
```

如果你現在從 Xcode 構建應用程式，應該能夠成功構建。

## 在模擬器上構建並運行你的代碼

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