# 使用 Codegen

本指南將教您如何：

- 配置 **Codegen**。
- 為每個平台手動調用它。

同時也描述了生成的程式碼。

## 先決條件

即使手動調用 **Codegen**，您始終需要一個 React Native 應用程式才能正確生成程式碼。

**Codegen** 過程與應用程式的構建緊密耦合，腳本位於 `react-native` NPM 套件中。

為了本指南的緣故，請使用 React Native CLI 創建一個專案，如下所示：

```bash
npx @react-native-community/cli@latest init SampleApp --version 0.76.0
```

**Codegen** 用於為您的自訂模組或元件生成粘合程式碼。有關如何創建它們的更多詳細信息，請參閱 Turbo Native Modules 和 Fabric Native Components 的指南。

<!-- TODO: add links -->

## 配置 **Codegen**

可以通過修改 `package.json` 文件來配置 **Codegen**。**Codegen** 由一個名為 `codegenConfig` 的自定義字段控制。

```json title="package.json"
  "codegenConfig": {
    "name": "<SpecName>",
    "type": "<types>",
    "jsSrcsDir": "<source_dir>",
    "android": {
      "javaPackageName": "<java.package.name>"
    },
    "ios": {
      "modulesConformingToProtocol": {
        "RCTImageURLLoader": [
          "<iOS-class-conforming-to-RCTImageURLLoader>",
          // example from react-native-camera-roll: https://github.com/react-native-cameraroll/react-native-cameraroll/blob/8a6d1b4279c76e5682a4b443e7a4e111e774ec0a/package.json#L118-L127
          // "RNCPHAssetLoader",
        ],
        "RCTURLRequestHandler": [
          "<iOS-class-conforming-to-RCTURLRequestHandler>",
          // example from react-native-camera-roll: https://github.com/react-native-cameraroll/react-native-cameraroll/blob/8a6d1b4279c76e5682a4b443e7a4e111e774ec0a/package.json#L118-L127
          // "RNCPHAssetUploader",
        ],
        "RCTImageDataDecoder": [
          "<iOS-class-conforming-to-RCTImageDataDecoder>",
          // we don't have a good example for this, but it works in the same way. Pass the name of the class that implements the RCTImageDataDecoder. It must be a Native Module.
        ]
      },
      "componentProvider": {
        "<componentName>": "<iOS-class-implementing-the-component>"
      },
      "modulesProvider": {
        "<moduleName>": "<iOS-class-implementing-the-RCTModuleProvider-protocol>"
      }
    }
  },
```

您可以將此片段添加到您的應用程式中，並自定義各個字段：

- `name:` 此名稱將用於建立包含規格的文件。按照慣例，應使用後綴 `Spec`，但這並非強制要求。
- `type`: 需生成的代碼類型。允許的值為 `modules`、`components`、`all`。
  - `modules:` 若僅需為 Turbo Native Modules 生成代碼，請使用此值。
  - `components:` 若僅需為 Native Fabric Components 生成代碼，請使用此值。
  - `all`: 若同時包含組件和模組，請使用此值。
- `jsSrcsDir`: 此為所有規格文件所在的根目錄。
- `android.javaPackageName`: 此為 Android 專用設定，用於讓 **Codegen** 在自定義套件中生成文件。
- `ios`: `ios` 字段是一個對象，可供應用開發者和庫維護者用於提供一些進階功能。以下所有字段均為**可選**。
  - `ios.modulesConformingToProtocol`: React Native 提供了一些協議，原生模組可通過實現這些協議來定制某些行為。這些字段允許你定義符合這些協議的模組列表。這些模組將在應用啟動時注入到 React Native 運行時中。
    - `ios.modulesConformingToProtocol.RCTImageURLLoader`: 實現 [`RCTImageURLLoader` 協議](https://github.com/facebook/react-native/blob/00d5caee9921b6c10be8f7d5b3903c6afe8dbefa/packages/react-native/Libraries/Image/RCTImageURLLoader.h#L26-L81)的 iOS 原生模組列表。你需要傳遞實現 `RCTImageURLLoader` 的 iOS 類名。這些必須是原生模組。
    - `ios.modulesConformingToProtocol.RCTURLRequestHandler`: 實現 [`RCTURLRequestHandler` 協議](https://github.com/facebook/react-native/blob/00d5caee9921b6c10be8f7d5b3903c6afe8dbefa/packages/react-native/React/Base/RCTURLRequestHandler.h#L11-L52)的 iOS 原生模組列表。你需要傳遞實現 `RCTURLRequestHandler` 的 iOS 類名。這些必須是原生模組。
    - `ios.modulesConformingToProtocol.RCTImageDataDecoder`: 實現 [`RCTImageDataDecoder` 協議](https://github.com/facebook/react-native/blob/00d5caee9921b6c10be8f7d5b3903c6afe8dbefa/packages/react-native/Libraries/Image/RCTImageDataDecoder.h#L15-L53)的 iOS 原生模組列表。你需要傳遞實現 `RCTImageDataDecoder` 的 iOS 類名。這些必須是原生模組。
  - `ios.componentProvider`: 此字段是一個映射，用於生成自定義 JS React 組件與實現它的原生類之間的關聯。映射的鍵是組件的 JS 名稱（例如 `TextInput`），值是實現該組件的 iOS 類（例如 `RCTTextInput`）。
  - `ios.modulesProvider`: 此字段是一個映射，用於生成自定義 JS Native Module 與提供它的原生類之間的關聯。映射的鍵是模組的 JS 名稱（例如 `NativeLocalStorage`），值是實現 [`RCTModuleProvider` 協議](https://github.com/facebook/react-native/blob/0.79-stable/packages/react-native/ReactCommon/react/nativemodule/core/platform/ios/ReactCommon/RCTTurboModule.h#L179-L190)的 iOS 類。對於 Objective-C 模組，實現 [`RCTTurboModule` 協議](https://github.com/facebook/react-native/blob/0.79-stable/packages/react-native/ReactCommon/react/nativemodule/core/platform/ios/ReactCommon/RCTTurboModule.h#L192-L200)的類也同時實現了 `RCTModuleProvider` 協議。更多信息，請參閱 [跨平台原生模組 (C++) 指南](/docs/next/the-new-architecture/pure-cxx-modules)。

當 **Codegen** 運行時，它會在應用的所有依賴中搜索符合特定約定的 JS 文件，並生成所需的代碼：

- Turbo Native Modules 要求規格文件以 `Native` 為前綴。例如，`NativeLocalStorage.ts` 是一個有效的規格文件名。
- Native Fabric Components 要求規格文件以 `NativeComponent` 為後綴。例如，`WebViewNativeComponent.ts` 是一個有效的規格文件名。

## 執行 **Codegen**

本指南的其餘部分假設您已在專案中設置好 Native Turbo Module、Native Fabric Component 或兩者。我們同時假設您已在 `package.json` 指定的 `jsSrcsDir` 目錄中準備好有效的規格檔案。

### Android

Android 的 **Codegen** 已整合至 React Native Gradle Plugin (RNGP)。RNGP 包含一個可執行的任務，該任務會讀取 `package.json` 中定義的配置並執行 **Codegen**。要執行此 gradle 任務，請先導航至專案的 `android` 資料夾，然後執行：

```bash
./gradlew generateCodegenArtifactsFromSchema
```

此任務會在應用程式的所有導入專案（應用程式本身及其連結的所有 node modules）上呼叫 `generateCodegenArtifactsFromSchema` 指令。生成的程式碼會存放在對應的 `node_modules/<dependency>` 資料夾中。例如，若您有一個名為 `my-fabric-component` 的 Fabric Native Component Node 模組，生成的程式碼會位於 `SampleApp/node_modules/my-fabric-component/android/build/generated/source/codegen` 路徑。而應用程式本身的程式碼則會生成在 `android/app/build/generated/source/codegen` 資料夾。

#### 生成的程式碼

執行上述 gradle 指令後，您可以在 `SampleApp/android/app/build` 資料夾中找到 Codegen 生成的程式碼。其結構如下：

```
build
└── generated
    └── source
        └── codegen
            ├── java
            │   └── com
            │       ├── facebook
            │       │   └── react
            │       │       └── viewmanagers
            │       │           ├── <nativeComponent>ManagerDelegate.java
            │       │           └── <nativeComponent>ManagerInterface.java
            │       └── sampleapp
            │           └── NativeLocalStorageSpec.java
            ├── jni
            │   ├── <codegenConfig.name>-generated.cpp
            │   ├── <codegenConfig.name>.h
            │   ├── CMakeLists.txt
            │   └── react
            │       └── renderer
            │           └── components
            │               └── <codegenConfig.name>
            │                   ├── <codegenConfig.name>JSI-generated.cpp
            │                   ├── <codegenConfig.name>.h
            │                   ├── ComponentDescriptors.cpp
            │                   ├── ComponentDescriptors.h
            │                   ├── EventEmitters.cpp
            │                   ├── EventEmitters.h
            │                   ├── Props.cpp
            │                   ├── Props.h
            │                   ├── ShadowNodes.cpp
            │                   ├── ShadowNodes.h
            │                   ├── States.cpp
            │                   └── States.h
            └── schema.json
```

生成的程式碼分為兩個資料夾：

- `java` 包含平台特定的程式碼
- `jni` 包含讓 JavaScript 與 Java 正確互動所需的 C++ 程式碼

在 `java` 資料夾中，您可以在 `com/facebook/viewmanagers` 子資料夾找到 Fabric Native Component 的生成程式碼。

- `<nativeComponent>ManagerDelegate.java` 包含 `ViewManager` 可呼叫的自訂 Native Component 方法
- `<nativeComponent>ManagerInterface.java` 包含 `ViewManager` 的介面

而在 `codegenConfig.android.javaPackageName` 設定的名稱對應資料夾中，您可以找到 Turbo Native Module 需實作的抽象類別以執行其任務。

最後，在 `jni` 資料夾中存放了所有連接 JavaScript 與 Android 的樣板程式碼。

- `<codegenConfig.name>.h` 包含自訂 C++ Turbo Native Modules 的介面
- `<codegenConfig.name>-generated.cpp` 包含自訂 C++ Turbo Native Modules 的黏合程式碼
- `react/renderer/components/<codegenConfig.name>`：此資料夾包含自訂元件所需的所有黏合程式碼

此結構是使用 `codegenConfig.type` 欄位值為 `all` 時生成的。若使用 `modules` 值，將不會看到 `react/renderer/components/` 資料夾；若使用 `components` 值，則不會看到其他檔案。

### iOS

iOS 的 **Codegen** 依賴建置過程中呼叫的 Node 腳本。這些腳本位於 `SampleApp/node_modules/react-native/scripts/` 資料夾中。

主要腳本為 `generate-codegen-artifacts.js`。要呼叫此腳本，您可以從應用程式的根目錄執行以下指令：

```bash
node node_modules/react-native/scripts/generate-codegen-artifacts.js

Usage: generate-codegen-artifacts.js -p [path to app] -t [target platform] -o [output path]

Options:
      --help            Show help                                      [boolean]
      --version         Show version number                            [boolean]
  -p, --path            Path to the React Native project root.        [required]
  -t, --targetPlatform  Target platform. Supported values: "android", "ios",
                        "all".                                        [required]
  -o, --outputPath      Path where generated artifacts will be output to.
```

其中：

- `--path` 是應用程式根目錄的路徑
- `--outputPath` 是 **Codegen** 寫入生成檔案的目標位置
- `--targetPlatform` 是您想生成程式碼的目標平台

#### 生成的程式碼

使用以下參數執行腳本：

```shell
node node_modules/react-native/scripts/generate-codegen-artifacts.js \
    --path . \
    --outputPath ios/ \
    --targetPlatform ios
```

將在 `ios/build` 資料夾中生成以下檔案：

```
build
└── generated
    └── ios
        ├── <codegenConfig.name>
        │   ├── <codegenConfig.name>-generated.mm
        │   └── <codegenConfig.name>.h
        ├── <codegenConfig.name>JSI-generated.cpp
        ├── <codegenConfig.name>JSI.h
        ├── FBReactNativeSpec
        │   ├── FBReactNativeSpec-generated.mm
        │   └── FBReactNativeSpec.h
        ├── FBReactNativeSpecJSI-generated.cpp
        ├── FBReactNativeSpecJSI.h
        ├── RCTModulesConformingToProtocolsProvider.h
        ├── RCTModulesConformingToProtocolsProvider.mm
        └── react
            └── renderer
                └── components
                    └── <codegenConfig.name>
                        ├── ComponentDescriptors.cpp
                        ├── ComponentDescriptors.h
                        ├── EventEmitters.cpp
                        ├── EventEmitters.h
                        ├── Props.cpp
                        ├── Props.h
                        ├── RCTComponentViewHelpers.h
                        ├── ShadowNodes.cpp
                        ├── ShadowNodes.h
                        ├── States.cpp
                        └── States.h
```

這些生成的文件中，有一部分會被 React Native 核心所使用。此外還有一組文件的名稱會與你在 package.json 的 `codegenConfig.name` 欄位中指定的名稱相同。

- `<codegenConfig.name>/<codegenConfig.name>.h`：此文件包含你的自訂 iOS Turbo Native Modules 的介面。
- `<codegenConfig.name>/<codegenConfig.name>-generated.mm`：此文件包含你的自訂 iOS Turbo Native Modules 的黏合代碼。
- `<codegenConfig.name>JSI.h`：此文件包含你的自訂 C++ Turbo Native Modules 的介面。
- `<codegenConfig.name>JSI-generated.h`：此文件包含你的自訂 C++ Turbo Native Modules 的黏合代碼。
- `react/renderer/components/<codegenConfig.name>`：此資料夾包含你的自訂元件所需的所有黏合代碼。

此結構是透過將 `codegenConfig.type` 欄位設為 `all` 所生成的。如果你使用 `modules` 值，則不會看到 `react/renderer/components/` 資料夾。如果你使用 `components` 值，則不會看到其他文件。