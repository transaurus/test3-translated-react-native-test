# Cross-Platform Native Modules (C++)

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

Writing a module in C++ is the best way to share platform-agnostic code between Android and iOS. With pure C++ modules, you can write your logic only once and reuse it right away from all the platform, without the need of writing platform specific code.

In this guide, we will go through the creation of a pure C++ Turbo Native Module:

1. Create the JS specs
2. Configure Codegen to generate the scaffolding
3. Implement the Native logic
4. Register the module in the Android and iOS application
5. Test your changes in JS

The rest of this guide assume that you have created your application running the command:

```shell
npx @react-native-community/cli@latest init SampleApp --version 0.76.0
```

## 1. Create the JS specs

Pure C++ Turbo Native Modules are Turbo Native Modules. They needs a specification file (also called spec file) so that Codegen can create the scaffolding code for us. The specification file is also what we use to access the Turbo Native Module in JS.

Specs files need to be written in a typed JS dialect. React Native currently supports Flow or TypeScript.

1. Inside the root folder of your app, create a new folder called `specs`.
2. Create a new file called `NativeSampleModule.ts` with the following code:

:::warning
All Native Turbo Module spec files must have the prefix `Native`, otherwise Codegen will ignore them.
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

## 2. Configure Codegen

The next step is to configure [Codegen](what-is-codegen.md) in your `package.json`. Update the file to include:

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

This configuration tells Codegen to look for specs files in the `specs` folder. It also instruct Codegen to only generate code for `modules` and to namespace the generated code as `AppSpecs`.

## 3. Write the Native Code

Writing a C++ Turbo Native Module allow you to share the code between Android an iOS. Therefore we will be writing the code once, and we will look into what changes we need to apply to the platforms so that the C++ code can be picked up.

1. Create a folder named `shared` at the same level of the `android` and `ios` folders.
2. Inside the `shared` folder, create a new file called `NativeSampleModule.h`.

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

3. Inside the `shared` folder, create a new file called `NativeSampleModule.cpp`.

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

Let's have a look at the two files we created:

- `NativeSampleModule.h` 檔案是純 C++ TurboModule 的標頭檔。`include` 語句確保我們包含了由 Codegen 生成的規範，這些規範包含了我們需要實現的介面和基礎類別。
- 該模組位於 `facebook::react` 命名空間中，以便存取該命名空間中的所有類型。
- `NativeSampleModule` 類別是實際的 Turbo Native Module 類別，它繼承了 `NativeSampleModuleCxxSpec` 類別，該類別包含了一些粘合程式碼和樣板程式碼，使這個類別能夠作為 Turbo Native Module 運作。
- 最後，我們有構造函數，它接受一個指向 `CallInvoker` 的指針，以便在需要時與 JS 進行通信，以及我們需要實現的函數原型。

`NativeSampleModule.cpp` 檔案是我們 Turbo Native Module 的實際實現，並實現了我們在規範中宣告的構造函數和方法。

## 4. 在平台中註冊模組

接下來的步驟將讓我們在平台中註冊模組。這一步驟將原生程式碼暴露給 JS，以便 React Native 應用程式最終可以從 JS 層調用原生方法。

這是我們唯一需要編寫一些平台特定程式碼的時候。

### Android

為了確保 Android 應用程式能夠有效地構建 C++ Turbo Native Module，我們需要：

1. 創建一個 `CMakeLists.txt` 來存取我們的 C++ 程式碼。
2. 修改 `build.gradle` 以指向新創建的 `CMakeLists.txt` 檔案。
3. 在我們的 Android 應用程式中創建一個 `OnLoad.cpp` 檔案來註冊新的 Turbo Native Module。

#### 1. 創建 `CMakeLists.txt` 檔案

Android 使用 CMake 來構建。CMake 需要存取我們在共享資料夾中定義的檔案，以便能夠構建它們。

1. 創建一個新資料夾 `SampleApp/android/app/src/main/jni`。`jni` 資料夾是 Android 的 C++ 部分所在的位置。
2. 創建一個 `CMakeLists.txt` 檔案並添加以下內容：

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

CMake 檔案執行了以下操作：

- 定義了 `appmodules` 庫，所有應用程式的 C++ 程式碼都將包含在其中。
- 載入基礎的 React Native 的 CMake 檔案。
- 使用 `target_sources` 指令添加我們需要構建的模組 C++ 原始碼。預設情況下，React Native 已經會將預設原始碼填充到 `appmodules` 庫中，這裡我們包含了我們自定義的部分。你可以看到我們需要從 `jni` 資料夾回溯到 `shared` 資料夾，那裡是我們的 C++ Turbo Module 所在的位置。
- 指定 CMake 可以找到模組標頭檔的位置。同樣在這種情況下，我們需要從 `jni` 資料夾回溯。

#### 2. 修改 `build.gradle` 以包含自定義 C++ 程式碼

Gradle 是協調 Android 構建的工具。我們需要告訴它可以在哪裡找到構建 Turbo Native Module 的 `CMake` 檔案。

1. 打開 `SampleApp/android/app/build.gradle` 檔案。
2. 在現有的 `android` 區塊中添加以下區塊：

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

這個區塊告訴 Gradle 檔案去哪裡尋找 CMake 檔案。路徑是相對於 `build.gradle` 檔案所在的資料夾，因此我們需要添加指向 `jni` 資料夾中 `CMakeLists.txt` 檔案的路徑。

#### 3. 註冊新的 Turbo Native Module

最後一步是在運行時註冊新的 C++ Turbo Native Module，以便當 JS 請求 C++ Turbo Native Module 時，應用程式知道去哪裡找到它並可以返回它。

1. 從資料夾 `SampleApp/android/app/src/main/jni` 中運行以下命令：

```sh
curl -O https://raw.githubusercontent.com/facebook/react-native/v0.76.0/packages/react-native/ReactAndroid/cmake-utils/default-app-setup/OnLoad.cpp
```

2. 接著，按照以下方式修改此檔案：

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

這些步驟會從 React Native 下載原始的 `OnLoad.cpp` 檔案，以便我們能安全地覆寫它，將 C++ Turbo Native Module 載入應用程式中。

下載檔案後，我們可以透過以下方式修改它：

- 引入指向我們模組的標頭檔
- 註冊 Turbo Native Module，讓當 JS 需要時，應用程式能回傳它。

現在，你可以從專案根目錄執行 `yarn android`，看到應用程式成功建置。

### iOS

為了確保 iOS 應用程式能有效建置 C++ Turbo Native Module，我們需要：

1. 安裝 pods 並執行 Codegen。
2. 將 `shared` 資料夾加入我們的 iOS 專案。
3. 在應用程式中註冊 C++ Turbo Native Module。

#### 1. 安裝 Pods 並執行 Codegen

我們需要執行的第一步是每次準備 iOS 應用程式時的常規步驟。CocoaPods 是我們用來設定和安裝 React Native 相依項的工具，在此過程中，它也會為我們執行 Codegen。

```bash
cd ios
bundle install
bundle exec pod install
```

#### 2. 將 shared 資料夾加入 iOS 專案

此步驟將 `shared` 資料夾加入專案，使其對 xcode 可見。

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

透過最後這個步驟，我們將告訴 iOS 應用程式去哪裡尋找純 C++ Turbo Native Module。

在 Xcode 中，開啟 `AppDelegate.mm` 檔案並如下修改：

```diff title="SampleApp/AppDelegate.mm"
#import <React/RCTBundleURLProvider.h>
+ #import <RCTAppDelegate+Protected.h>
+ #import "NativeSampleModule.h"

// ...
  return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
}

+- (std::shared_ptr<facebook::react::TurboModule>)getTurboModule:(const std::string &)name
+                                                      jsInvoker:(std::shared_ptr<facebook::react::CallInvoker>)jsInvoker
+{
+  if (name == "NativeSampleModule") {
+    return std::make_shared<facebook::react::NativeSampleModule>(jsInvoker);
+  }
+
+  return [super getTurboModule:name jsInvoker:jsInvoker];
+}

@end
```

這些變更做了幾件事：

1. 引入 `RCTAppDelegate+Protected` 標頭檔，讓 AppDelegate 知道它符合 `RCTTurboModuleManagerDelegate` 協議。
2. 引入純 C++ Native Turbo Module 介面 `NativeSampleModule.h`
3. 覆寫 C++ 模組的 `getTurboModule` 方法，讓當 JS 端要求名為 `NativeSampleModule` 的模組時，應用程式知道該回傳哪個模組。

如果你現在從 Xcode 建置應用程式，應該能成功建置。

## 5. 測試你的程式碼

現在是時候從 JS 存取我們的 C++ Turbo Native Module 了。為此，我們需要修改 `App.tsx` 檔案，引入 Turbo Native Module 並在程式碼中呼叫它。

1. 開啟 `App.tsx` 檔案。
2. 將範本的內容替換為以下程式碼：

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

此應用程式中值得注意的幾行是：

- `import SampleTurboModule from './specs/NativeSampleModule';`: this line imports the Turbo Native Module in the app,
- `const revString = SampleTurboModule.reverseString(value);` in the `onPress` callback: this is how you can use the Turbo Native Module in your app.

:::warning
For the sake of this example and to keep it as short as possible, we directly imported the spec file in our app.
The best practice in this case is to create a separate file to wrap the specs and use that file into your application.
This allow you to prepare the input for the specs and gives you more control over then in JS.
:::

Congratulation, you wrote your first C++ Turbo Native Module!

| <center>Android</center>                                                                             | <center>iOS</center>                                                                          |
| ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| <center><img src="/docs/assets/CxxGuideAndroidVideo.gif" alt="Android Video" height="600"/></center> | <center><img src="/docs/assets/CxxGuideIOSVideo.gif" alt="iOS video" height="600" /></center> |