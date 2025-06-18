# 跨平台原生模組 (C++)

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

使用 C++ 編寫模組是實現 Android 與 iOS 平台無關程式碼共享的最佳方式。透過純 C++ 模組，您只需編寫一次邏輯，即可在所有平台上直接重用，無需編寫平台特定程式碼。

本指南將逐步說明如何建立純 C++ Turbo 原生模組：

1. 建立 JS 規格文件
2. 配置 Codegen 生成框架程式碼
3. 實作原生邏輯
4. 在 Android 和 iOS 應用中註冊模組
5. 在 JS 中測試變更

本指南後續內容假設您已透過以下指令建立應用程式：

```shell
npx @react-native-community/cli@latest init SampleApp --version 0.76.0
```

## 1. 建立 JS 規格文件

純 C++ Turbo 原生模組屬於 Turbo 原生模組，需要規格文件（spec file）讓 Codegen 能為我們生成框架程式碼。該規格文件也是我們在 JS 中存取 Turbo 原生模組的介面。

規格文件需使用具型別的 JS 方言撰寫。React Native 目前支援 Flow 或 TypeScript。

1. 在應用程式根目錄下建立名為 `specs` 的新資料夾。
2. 建立名為 `NativeSampleModule.ts` 的新檔案，內容如下：

:::warning
所有 Turbo 原生模組規格文件必須以 `Native` 為前綴，否則 Codegen 將忽略它們。
:::

<Tabs groupId="tnm-specs" queryString defaultValue={constants.defaultJavaScriptSpecLanguages} values={constants.javaScriptSpecLanguages}>
<TabItem value="flow">

```ts title="specs/NativeSampleModule.ts"
// @flow
import type {TurboModule} from 'react-native'
import { TurboModuleRegistry } from "react-native";

export interface Spec extends TurboModule {
  +reverseString: (input: string) => string;
}

export default (TurboModuleRegistry.getEnforcing<Spec>(
  "NativeSampleModule"
): Spec);
```

</TabItem>
<TabItem value="typescript">

```ts title="specs/NativeSampleModule.ts"
import {TurboModule, TurboModuleRegistry} from 'react-native';

export interface Spec extends TurboModule {
  readonly reverseString: (input: string) => string;
}

export default TurboModuleRegistry.getEnforcing<Spec>(
  'NativeSampleModule',
);
```

</TabItem>
</Tabs>

## 2. 配置 Codegen

下一步是在 `package.json` 中配置 [Codegen](what-is-codegen.md)。更新檔案內容以包含：

```json title="package.json"
     "start": "react-native start",
     "test": "jest"
   },
   // highlight-add-start
   "codegenConfig": {
     "name": "AppSpecs",
     "type": "modules",
     "jsSrcsDir": "specs",
     "android": {
       "javaPackageName": "com.sampleapp.specs"
     }
   },
   // highlight-add-end
   "dependencies": {
```

此配置指示 Codegen 在 `specs` 資料夾中尋找規格文件，並僅為 `modules` 生成程式碼，同時將生成的程式碼命名空間設為 `AppSpecs`。

## 3. 編寫原生程式碼

編寫 C++ Turbo 原生模組可讓您在 Android 和 iOS 之間共享程式碼。因此我們只需編寫一次程式碼，後續將說明如何調整各平台配置以引入 C++ 程式碼。

1. 在與 `android` 和 `ios` 資料夾同層級處建立名為 `shared` 的資料夾。
2. 在 `shared` 資料夾中建立名為 `NativeSampleModule.h` 的新檔案。

   ```cpp title="shared/NativeSampleModule.h"
   #pragma once

   #include <AppSpecsJSI.h>

   #include <memory>
   #include <string>

   namespace facebook::react {

   class NativeSampleModule : public NativeSampleModuleCxxSpec<NativeSampleModule> {
   public:
     NativeSampleModule(std::shared_ptr<CallInvoker> jsInvoker);

     std::string reverseString(jsi::Runtime& rt, std::string input);
   };

   } // namespace facebook::react

   ```

3. 在 `shared` 資料夾中建立名為 `NativeSampleModule.cpp` 的新檔案。

   ```cpp title="shared/NativeSampleModule.cpp"
   #include "NativeSampleModule.h"

   namespace facebook::react {

   NativeSampleModule::NativeSampleModule(std::shared_ptr<CallInvoker> jsInvoker)
       : NativeSampleModuleCxxSpec(std::move(jsInvoker)) {}

   std::string NativeSampleModule::reverseString(jsi::Runtime& rt, std::string input) {
     return std::string(input.rbegin(), input.rend());
   }

   } // namespace facebook::react
   ```

讓我們檢視這兩個新建的檔案：

- `NativeSampleModule.h` 檔案是純 C++ TurboModule 的標頭檔。`include` 語句確保我們引入由 Codegen 生成的規範，其中包含需要實作的介面與基礎類別。
- 該模組位於 `facebook::react` 命名空間中，以存取該命名空間內的所有類型。
- `NativeSampleModule` 類別是實際的 Turbo Native Module 類別，它繼承自 `NativeSampleModuleCxxSpec` 類別，後者包含一些黏合程式碼與樣板程式碼，使此類別能作為 Turbo Native Module 運作。
- 最後是建構函式，它接受指向 `CallInvoker` 的指標，以便在需要時與 JS 溝通，以及我們需要實作的函式原型。

`NativeSampleModule.cpp` 檔案是我們 Turbo Native Module 的實際實作，實作了在規範中宣告的建構函式與方法。

## 4. 在平台中註冊模組

接下來的步驟將讓我們在平台中註冊模組。這一步驟將原生程式碼暴露給 JS，使 React Native 應用程式最終能從 JS 層呼叫原生方法。

這是唯一需要撰寫一些平台特定程式碼的時候。

### Android

為了確保 Android 應用能有效建置 C++ Turbo Native Module，我們需要：

1. 建立 `CMakeLists.txt` 以存取我們的 C++ 程式碼。
2. 修改 `build.gradle` 以指向新建立的 `CMakeLists.txt` 檔案。
3. 在 Android 應用中建立 `OnLoad.cpp` 檔案以註冊新的 Turbo Native Module。

#### 1. 建立 `CMakeLists.txt` 檔案

Android 使用 CMake 進行建置。CMake 需要存取我們在共享資料夾中定義的檔案才能建置它們。

1. 建立新資料夾 `SampleApp/android/app/src/main/jni`。`jni` 資料夾是 Android 中 C++ 部分所在的位置。
2. 建立 `CMakeLists.txt` 檔案並加入以下內容：

```shell title="CMakeLists.txt"
cmake_minimum_required(VERSION 3.13)

# Define the library name here.
project(appmodules)

# This file includes all the necessary to let you build your React Native application
include(${REACT_ANDROID_DIR}/cmake-utils/ReactNative-application.cmake)

# Define where the additional source code lives. We need to crawl back the jni, main, src, app, android folders
target_sources(${CMAKE_PROJECT_NAME} PRIVATE ../../../../../shared/NativeSampleModule.cpp)

# Define where CMake can find the additional header files. We need to crawl back the jni, main, src, app, android folders
target_include_directories(${CMAKE_PROJECT_NAME} PUBLIC ../../../../../shared)
```

CMake 檔案執行以下操作：

- 定義 `appmodules` 函式庫，所有應用 C++ 程式碼將包含於此。
- 載入基礎 React Native 的 CMake 檔案。
- 透過 `target_sources` 指令加入我們需要建置的模組 C++ 原始碼。預設情況下，React Native 會將預設原始碼填入 `appmodules` 函式庫，這裡我們加入自訂的部分。可以看到我們需要從 `jni` 資料夾回溯到 `shared` 資料夾，那裡存放著我們的 C++ Turbo Module。
- 指定 CMake 可以找到模組標頭檔的位置。同樣在此情況下，我們需要從 `jni` 資料夾回溯。

#### 2. 修改 `build.gradle` 以包含自訂 C++ 程式碼

Gradle 是協調 Android 建置的工具。我們需要告訴它哪裡可以找到建置 Turbo Native Module 的 `CMake` 檔案。

1. 開啟 `SampleApp/android/app/build.gradle` 檔案。
2. 在現有的 `android` 區塊內加入以下區塊：

```diff title="android/app/build.gradle"
    buildTypes {
        debug {
            signingConfig signingConfigs.debug
        }
        release {
            // Caution! In production, you need to generate your own keystore file.
            // see https://reactnative.dev/docs/signed-apk-android.
            signingConfig signingConfigs.debug
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        }
    }

+   externalNativeBuild {
+       cmake {
+           path "src/main/jni/CMakeLists.txt"
+       }
+   }
}
```

此區塊告訴 Gradle 檔案去哪裡尋找 CMake 檔案。路徑相對於 `build.gradle` 檔案所在的資料夾，因此我們需要加入指向 `jni` 資料夾中 `CMakeLists.txt` 檔案的路徑。

#### 3. 註冊新的 Turbo Native Module

最後一步是在執行時期註冊新的 C++ Turbo Native Module，這樣當 JS 要求 C++ Turbo Native Module 時，應用程式知道去哪裡找到它並回傳。

1. 從資料夾 `SampleApp/android/app/src/main/jni` 執行以下命令：

```sh
curl -O https://raw.githubusercontent.com/facebook/react-native/v0.76.0/packages/react-native/ReactAndroid/cmake-utils/default-app-setup/OnLoad.cpp
```

2. 接著，按以下方式修改此檔案：

```diff title="android/app/src/main/jni/OnLoad.cpp"
#include <DefaultComponentsRegistry.h>
#include <DefaultTurboModuleManagerDelegate.h>
#include <autolinking.h>
#include <fbjni/fbjni.h>
#include <react/renderer/componentregistry/ComponentDescriptorProviderRegistry.h>
#include <rncore.h>

+ // Include the NativeSampleModule header
+ #include <NativeSampleModule.h>

//...

std::shared_ptr<TurboModule> cxxModuleProvider(
    const std::string& name,
    const std::shared_ptr<CallInvoker>& jsInvoker) {
  // Here you can provide your CXX Turbo Modules coming from
  // either your application or from external libraries. The approach to follow
  // is similar to the following (for a module called `NativeCxxModuleExample`):
  //
  // if (name == NativeCxxModuleExample::kModuleName) {
  //   return std::make_shared<NativeCxxModuleExample>(jsInvoker);
  // }

+  // This code register the module so that when the JS side asks for it, the app can return it
+  if (name == NativeSampleModule::kModuleName) {
+    return std::make_shared<NativeSampleModule>(jsInvoker);
+  }

  // And we fallback to the CXX module providers autolinked
  return autolinking_cxxModuleProvider(name, jsInvoker);
}

// leave the rest of the file
```

這些步驟會從 React Native 下載原始的 `OnLoad.cpp` 檔案，以便我們能安全地覆寫它，將 C++ Turbo Native Module 載入應用程式。

下載檔案後，我們可以透過以下方式修改：

- 引入指向我們模組的標頭檔
- 註冊 Turbo Native Module，讓當 JS 需要時，應用程式能回傳它。

現在，你可以在專案根目錄執行 `yarn android`，確認應用程式能成功建置。

### iOS

為了確保 iOS 應用程式能有效建置 C++ Turbo Native Module，我們需要：

1. 安裝 pods 並執行 Codegen。
2. 將 `shared` 資料夾加入我們的 iOS 專案。
3. 在應用程式中註冊 C++ Turbo Native Module。

#### 1. 安裝 Pods 並執行 Codegen

第一步是執行每次準備 iOS 應用程式時的例行步驟。CocoaPods 是我們用來設定和安裝 React Native 相依套件的工具，在此過程中，它也會為我們執行 Codegen。

```bash
cd ios
bundle install
bundle exec pod install
```

#### 2. 將 shared 資料夾加入 iOS 專案

此步驟將 `shared` 資料夾加入專案，讓 Xcode 能看見它。

1. 開啟 CocoPods 產生的 Xcode Workspace。

```bash
cd ios
open SampleApp.xcworkspace
```

2. 點擊左側的 `SampleApp` 專案，選擇 `Add files to "Sample App"...`。

![Add Files to Sample App...](/docs/assets/AddFilesToXcode1.png)

3. 選擇 `shared` 資料夾並點擊 `Add`。

![Add Files to Sample App...](/docs/assets/AddFilesToXcode2.png)

如果一切正確，左側的專案應如下所示：

![Xcode Project](/docs/assets/CxxTMGuideXcodeProject.png)

#### 3. 在應用程式中註冊 Cxx Turbo Native Module

要在應用程式中註冊純 Cxx Turbo Native Module，你需要：

1. 為 Native Module 建立 `ModuleProvider`
2. 設定 `package.json`，將 JS 模組名稱與 ModuleProvider 類別關聯。

ModuleProvider 是一個 Objective-C++ 類別，負責將純 C++ 模組與 iOS 應用程式的其他部分連接起來。

##### 3.1 建立 ModuleProvider

1. 在 Xcode 中選擇 `SampleApp` 專案，按下 <kbd>⌘</kbd> + <kbd>N</kbd> 建立新檔案。
2. 選擇 `Cocoa Touch Class` 模板
3. 輸入名稱 `SampleNativeModuleProvider`（保持其他欄位為 `Subclass of: NSObject` 和 `Language: Objective-C`）
4. 點擊 Next 產生檔案。
5. 將 `SampleNativeModuleProvider.m` 重新命名為 `SampleNativeModuleProvider.mm`。`.mm` 副檔名表示這是一個 Objective-C++ 檔案。
6. 在 `SampleNativeModuleProvider.h` 中實作以下內容：

```objc title="NativeSampleModuleProvider.h"

#import <Foundation/Foundation.h>
#import <ReactCommon/RCTTurboModule.h>

NS_ASSUME_NONNULL_BEGIN

@interface NativeSampleModuleProvider : NSObject <RCTModuleProvider>

@end

NS_ASSUME_NONNULL_END
```

這宣告了一個符合 `RCTModuleProvider` 協議的 `NativeSampleModuleProvider` 物件。

7. 在 `SampleNativeModuleProvider.mm` 中實作以下內容：

```objc title="NativeSampleModuleProvider.mm"

#import "NativeSampleModuleProvider.h"
#import <ReactCommon/CallInvoker.h>
#import <ReactCommon/TurboModule.h>
#import "NativeSampleModule.h"

@implementation NativeSampleModuleProvider

- (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:
    (const facebook::react::ObjCTurboModule::InitParams &)params
{
  return std::make_shared<facebook::react::NativeSampleModule>(params.jsInvoker);
}

@end
```

此程式碼透過在呼叫 `getTurboModule:` 方法時建立純 C++ 的 `NativeSampleModule`，來實作 `RCTModuleProvider` 協議。

##### 3.2 更新 package.json

最後一步是更新 `package.json`，告知 React Native 關於原生模組的 JS 規格與原生程式碼具體實現之間的關聯。

請依照以下方式修改 `package.json`：

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
     // highlight-add-start
     },
     "ios": {
        "modulesProvider": {
          "NativeSampleModule":  "NativeSampleModuleProvider"
        }
     }
     // highlight-add-end
   },

   "dependencies": {
```

此時需重新安裝 pods，確保 Codegen 再次執行以生成新檔案：

```bash
# from the ios folder
bundle exec pod install
open SampleApp.xcworkspace
```

若現在透過 Xcode 建置應用程式，應能成功完成建置。

## 5. 測試你的程式碼

現在是時候從 JS 端存取我們的 C++ Turbo 原生模組了。為此，我們需修改 `App.tsx` 檔案，導入 Turbo 原生模組並在程式碼中呼叫它。

1. 開啟 `App.tsx` 檔案。
2. 將範本內容替換為以下程式碼：

```tsx title="App.tsx"
import React from 'react';
import {
  Button,
  SafeAreaView,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';
import SampleTurboModule from './specs/NativeSampleModule';

function App(): React.JSX.Element {
  const [value, setValue] = React.useState('');
  const [reversedValue, setReversedValue] = React.useState('');

  const onPress = () => {
    const revString = SampleTurboModule.reverseString(value);
    setReversedValue(revString);
  };

  return (
    <SafeAreaView style={styles.container}>
      <View>
        <Text style={styles.title}>
          Welcome to C++ Turbo Native Module Example
        </Text>
        <Text>Write down here he text you want to revert</Text>
        <TextInput
          style={styles.textInput}
          placeholder="Write your text here"
          onChangeText={setValue}
          value={value}
        />
        <Button title="Reverse" onPress={onPress} />
        <Text>Reversed text: {reversedValue}</Text>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  title: {
    fontSize: 18,
    marginBottom: 20,
  },
  textInput: {
    borderColor: 'black',
    borderWidth: 1,
    borderRadius: 5,
    padding: 10,
    marginTop: 10,
  },
});

export default App;
```

此應用程式中值得注意的關鍵行包括：

- `import SampleTurboModule from './specs/NativeSampleModule';`：此行將 Turbo 原生模組導入應用程式，
- `onPress` 回調函數中的 `const revString = SampleTurboModule.reverseString(value);`：此為在應用程式中使用 Turbo 原生模組的方式。

:::warning
為簡化範例並保持簡潔，我們直接將規格檔案導入應用程式。
最佳實踐是建立獨立的包裝檔案處理規格，並在應用程式中使用該檔案。
這能讓你在 JS 端預處理規格的輸入參數並提供更強的控制力。
:::

恭喜，你已成功撰寫第一個 C++ Turbo 原生模組！

| <center>Android</center>                                                                             | <center>iOS</center>                                                                          |
| ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| <center><img src="/docs/assets/CxxGuideAndroidVideo.gif" alt="Android Video" height="600"/></center> | <center><img src="/docs/assets/CxxGuideIOSVideo.gif" alt="iOS video" height="600" /></center> |