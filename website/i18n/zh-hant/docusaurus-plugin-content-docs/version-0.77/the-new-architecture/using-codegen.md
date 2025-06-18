# 使用 Codegen

本指南將教您如何：

- 配置 **Codegen**。
- 為每個平台手動調用它。

同時也描述了生成的程式碼。

## 先決條件

您始終需要一個 React Native 應用程式才能正確生成程式碼，即使手動調用 **Codegen** 也是如此。

**Codegen** 過程與應用程式的構建緊密耦合，腳本位於 `react-native` NPM 套件中。

為了本指南的緣故，請使用 React Native CLI 創建一個專案，如下所示：

```bash
npx @react-native-community/cli@latest init SampleApp --version 0.76.0
```

**Codegen** 用於為您的自定義模組或組件生成粘合程式碼。有關如何創建它們的更多詳細信息，請參閱 Turbo Native Modules 和 Fabric Native Components 的指南。

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
    }
  },
```

您可以將此片段添加到您的應用程式中並自定義各個字段：

- `name:` 這將用於創建包含您的規格的文件的名稱。按照慣例，它應該有後綴 `Spec`，但這不是強制性的。
- `type`: 需要生成的程式碼類型。允許的值為 `modules`、`components`、`all`。
  - `modules:` 如果僅需要為 Turbo Native Modules 生成程式碼，請使用此值。
  - `components:` 如果僅需要為 Native Fabric Components 生成程式碼，請使用此值。
  - `all`: 如果同時擁有組件和模組，請使用此值。
- `jsSrcsDir`: 這是所有規格所在的根文件夾。
- `android.javaPackageName`: 這是一個 Android 特定的設置，用於讓 **Codegen** 在自定義包中生成文件。
- `ios`: `ios` 字段是一個對象，可供應用程式開發人員和庫維護人員使用，以提供一些高級功能。以下所有字段都是**可選的**。
    - `ios.modulesConformingToProtocol`: React Native 提供了一些協議，原生模組可以實現這些協議以自定義某些行為。這些字段允許您定義符合這些協議的模組列表。這些模組將在應用程式啟動時注入到 React Native 運行時中。
      - `ios.modulesConformingToProtocol.RCTImageURLLoader`: 實現 [`RCTImageURLLoader` 協議](https://github.com/facebook/react-native/blob/00d5caee9921b6c10be8f7d5b3903c6afe8dbefa/packages/react-native/Libraries/Image/RCTImageURLLoader.h#L26-L81) 的 iOS 原生模組列表。您需要傳遞實現 `RCTImageURLLoader` 的 iOS 類名。它們必須是 Native Modules。
      - `ios.modulesConformingToProtocol.RCTURLRequestHandler`: 實現 [`RCTURLRequestHandler` 協議](https://github.com/facebook/react-native/blob/00d5caee9921b6c10be8f7d5b3903c6afe8dbefa/packages/react-native/React/Base/RCTURLRequestHandler.h#L11-L52) 的 iOS 原生模組列表。您需要傳遞實現 `RCTURLRequestHandler` 的 iOS 類名。它們必須是 Native Modules。
      - `ios.modulesConformingToProtocol.RCTImageDataDecoder`: 實現 [`RCTImageDataDecoder` 協議](https://github.com/facebook/react-native/blob/00d5caee9921b6c10be8f7d5b3903c6afe8dbefa/packages/react-native/Libraries/Image/RCTImageDataDecoder.h#L15-L53) 的 iOS 原生模組列表。您需要傳遞實現 `RCTImageDataDecoder` 的 iOS 類名。它們必須是 Native Modules。
    - `ios.componentProvider`: 此字段是一個映射，用於生成自定義 JS React 組件與實現它的原生類之間的關聯。映射的鍵是組件的 JS 名稱（例如 `TextInput`），值是實現該組件的 iOS 類（例如 `RCTTextInput`）。

當 **Codegen** 執行時，它會搜尋應用程式的所有依賴項，尋找符合特定慣例的 JS 檔案，並生成所需的程式碼：

- Turbo Native Modules 要求規格檔案的前綴為 `Native`。例如，`NativeLocalStorage.ts` 是一個有效的規格檔案名稱。
- Native Fabric Components 要求規格檔案的後綴為 `NativeComponent`。例如，`WebViewNativeComponent.ts` 是一個有效的規格檔案名稱。

## 執行 **Codegen**

本指南的其餘部分假設您已在專案中設置了 Native Turbo Module、Native Fabric Component 或兩者。我們還假設您在 `package.json` 中指定的 `jsSrcsDir` 中有有效的規格檔案。

### Android

Android 的 **Codegen** 與 React Native Gradle Plugin (RNGP) 整合。RNGP 包含一個可執行的任務，該任務會讀取 `package.json` 檔案中定義的配置並執行 **Codegen**。要執行 gradle 任務，首先導航到專案的 `android` 資料夾，然後執行：

```bash
./gradlew generateCodegenArtifactsFromSchema
```

此任務在應用程式的所有導入專案（應用程式及其所有連結的 node modules）上調用 `generateCodegenArtifactsFromSchema` 命令。它會在相應的 `node_modules/<dependency>` 資料夾中生成程式碼。例如，如果您有一個名為 `my-fabric-component` 的 Fabric Native Component Node 模組，生成的程式碼位於 `SampleApp/node_modules/my-fabric-component/android/build/generated/source/codegen` 路徑。對於應用程式本身，程式碼則生成在 `android/app/build/generated/source/codegen` 資料夾中。

#### 生成的程式碼

執行上述 gradle 命令後，您將在 `SampleApp/android/app/build` 資料夾中找到 codegen 程式碼。結構如下：

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
- `jni` 包含讓 JS 和 Java 正確互動所需的 C++ 程式碼。

在 `java` 資料夾中，您可以在 `com/facebook/viewmanagers` 子資料夾中找到 Fabric Native Component 生成的程式碼。

- `<nativeComponent>ManagerDelegate.java` 包含 `ViewManager` 可以在自定義 Native Component 上調用的方法
- `<nativeComponent>ManagerInterface.java` 包含 `ViewManager` 的介面。

在 `codegenConfig.android.javaPackageName` 設定的名稱資料夾中，您可以找到 Turbo Native Module 必須實現的抽象類別以執行其任務。

最後，在 `jni` 資料夾中，包含了所有連接 JS 和 Android 的樣板程式碼。

- `<codegenConfig.name>.h` 包含自定義 C++ Turbo Native Modules 的介面。
- `<codegenConfig.name>-generated.cpp` 包含自定義 C++ Turbo Native Modules 的粘合程式碼。
- `react/renderer/components/<codegenConfig.name>`：此資料夾包含自定義元件所需的所有粘合程式碼。

此結構是使用 `codegenConfig.type` 欄位的值 `all` 生成的。如果使用值 `modules`，則不會看到 `react/renderer/components/` 資料夾。如果使用值 `components`，則不會看到其他檔案。

### iOS

iOS 的 **Codegen** 依賴於在構建過程中調用的一些 Node 腳本。這些腳本位於 `SampleApp/node_modules/react-native/scripts/` 資料夾中。

主要腳本是 `generate-codegen-artifacts.js` 腳本。要調用此腳本，您可以從應用程式的根資料夾執行以下命令：

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

- `--path` 參數指定應用程式的根目錄路徑。
- `--outputPath` 參數指定 **Codegen** 生成檔案的輸出位置。
- `--targetPlatform` 參數指定要生成程式碼的目標平台。

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

部分生成的檔案會被 React Native 核心使用。其餘檔案名稱則對應 package.json 中 `codegenConfig.name` 欄位指定的名稱。

- `<codegenConfig.name>/<codegenConfig.name>.h`：包含自訂 iOS Turbo Native Modules 的介面。
- `<codegenConfig.name>/<codegenConfig.name>-generated.mm`：包含自訂 iOS Turbo Native Modules 的黏合程式碼。
- `<codegenConfig.name>JSI.h`：包含自訂 C++ Turbo Native Modules 的介面。
- `<codegenConfig.name>JSI-generated.h`：包含自訂 C++ Turbo Native Modules 的黏合程式碼。
- `react/renderer/components/<codegenConfig.name>`：此資料夾包含自訂元件所需的所有黏合程式碼。

此結構是透過將 `codegenConfig.type` 欄位設為 `all` 所生成。若使用 `modules` 值，則不會出現 `react/renderer/components/` 資料夾；若使用 `components` 值，則不會生成其他檔案。