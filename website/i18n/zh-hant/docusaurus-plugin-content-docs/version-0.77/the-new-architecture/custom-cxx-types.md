import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

# 進階：自訂 C++ 型別

:::note
本指南假設您已熟悉[**純 C++ Turbo 原生模組**](pure-cxx-modules.md)指南。本文將在此基礎上進一步延伸。
:::

C++ Turbo 原生模組支援為大多數 `std::` 標準型別提供[橋接功能](https://github.com/facebook/react-native/tree/main/packages/react-native/ReactCommon/react/bridging)。您可以在模組中直接使用這些型別，無需額外編寫程式碼。

若需在應用程式或函式庫中新增自訂型別支援，您必須提供對應的 `bridging` 標頭檔案。

## 新增自訂型別：Int64

由於 JavaScript 不支援大於 2^53 的數字，C++ Turbo 原生模組目前尚未支援 `int64_t` 數值型別。我們可在 JS 中使用 `string` 型別來表示大於 2^53 的數字，並在 C++ 中自動轉換為 `int64_t`。

### 1. 建立橋接標頭檔案

支援新自訂型別的第一步，是定義負責將型別從 JS 表示法轉換為 C++ 表示法（以及反向轉換）的橋接標頭。

1. 在 `shared` 資料夾中新增名為 `Int64.h` 的檔案
2. 將以下程式碼加入該檔案：

```cpp title="Int64.h"
#pragma once

#include <react/bridging/Bridging.h>

namespace facebook::react {

template <>
struct Bridging<int64_t> {
  // Converts from the JS representation to the C++ representation
  static int64_t fromJs(jsi::Runtime &rt, const jsi::String &value) {
    try {
      size_t pos;
      auto str = value.utf8(rt);
      auto num = std::stoll(str, &pos);
      if (pos != str.size()) {
        throw std::invalid_argument("Invalid number"); // don't support alphanumeric strings
      }
      return num;
    } catch (const std::logic_error &e) {
      throw jsi::JSError(rt, e.what());
    }
  }

  // Converts from the C++ representation to the JS representation
  static jsi::String toJs(jsi::Runtime &rt, int64_t value) {
    return bridging::toJs(rt, std::to_string(value));
  }
};

}
```

自訂橋接標頭的關鍵組件包括：

- 為自訂型別明確特化的 `Bridging` 結構體（本例中模板指定 `int64_t` 型別）
- 將 JS 表示法轉換為 C++ 表示法的 `fromJs` 函式
- 將 C++ 表示法轉換為 JS 表示法的 `toJs` 函式

:::note
在 iOS 平台，請記得將 `Int64.h` 檔案加入 Xcode 專案。
:::

### 2. 修改 JS 規格

現在我們可以修改 JS 規格，新增使用此型別的方法。與往常一樣，我們可以使用 Flow 或 TypeScript 編寫規格。

1. 開啟 `specs/NativeSampleTurbomodule`
2. 按以下方式修改規格：

<Tabs groupId="custom-int64" queryString defaultValue={constants.defaultJavaScriptSpecLanguages} values={constants.javaScriptSpecLanguages}>
<TabItem value="typescript">

```diff title="NativeSampleModule.ts"
import {TurboModule, TurboModuleRegistry} from 'react-native';

export interface Spec extends TurboModule {
  readonly reverseString: (input: string) => string;
+  readonly cubicRoot: (input: string) => number;
}

export default TurboModuleRegistry.getEnforcing<Spec>(
  'NativeSampleModule',
);
```

</TabItem>
<TabItem value="flow">

```diff title="NativeSampleModule.js"
// @flow
import type {TurboModule} from 'react-native';
import { TurboModuleRegistry } from "react-native";

export interface Spec extends TurboModule {
  +reverseString: (input: string) => string;
+  +cubicRoot: (input: string) => number;
}

export default (TurboModuleRegistry.getEnforcing<Spec>(
  "NativeSampleModule"
): Spec);
```

</TabItem>
</Tabs>

此檔案中我們定義了需要在 C++ 中實作的函式。

### 3. 實作原生程式碼

現在我們需要實作在 JS 規格中宣告的函式。

1. 開啟 `specs/NativeSampleModule.h` 檔案並進行以下修改：

```diff title="NativeSampleModule.h"
#pragma once

#include <AppSpecsJSI.h>
#include <memory>
#include <string>

+ #include "Int64.h"

namespace facebook::react {

class NativeSampleModule : public NativeSampleModuleCxxSpec<NativeSampleModule> {
public:
  NativeSampleModule(std::shared_ptr<CallInvoker> jsInvoker);

  std::string reverseString(jsi::Runtime& rt, std::string input);
+ int32_t cubicRoot(jsi::Runtime& rt, int64_t input);
};

} // namespace facebook::react

```

2. 開啟 `specs/NativeSampleModule.cpp` 檔案並實作新函式：

```diff title="NativeSampleModule.cpp"
#include "NativeSampleModule.h"
+ #include <cmath>

namespace facebook::react {

NativeSampleModule::NativeSampleModule(std::shared_ptr<CallInvoker> jsInvoker)
    : NativeSampleModuleCxxSpec(std::move(jsInvoker)) {}

std::string NativeSampleModule::reverseString(jsi::Runtime& rt, std::string input) {
  return std::string(input.rbegin(), input.rend());
}

+int32_t NativeSampleModule::cubicRoot(jsi::Runtime& rt, int64_t input) {
+    return std::cbrt(input);
+}

} // namespace facebook::react
```

此實作導入 `<cmath>` C++ 函式庫來執行數學運算，並使用 `<cmath>` 模組中的 `cbrt` 原語來實作 `cubicRoot` 函式。

### 4. 在應用程式中測試程式碼

現在我們可以在應用程式中測試程式碼。

首先需更新 `App.tsx` 檔案以使用 TurboModule 的新方法，接著可為 Android 和 iOS 平台建置應用程式。

1. 開啟 `App.tsx` 程式碼並進行以下修改：

```diff title="App.tsx"
// ...
+ const [cubicSource, setCubicSource] = React.useState('')
+ const [cubicRoot, setCubicRoot] = React.useState(0)
  return (
    <SafeAreaView style={styles.container}>
      <View>
        <Text style={styles.title}>
          Welcome to C++ Turbo Native Module Example
        </Text>
        <Text>Write down here the text you want to revert</Text>
        <TextInput
          style={styles.textInput}
          placeholder="Write your text here"
          onChangeText={setValue}
          value={value}
        />
        <Button title="Reverse" onPress={onPress} />
        <Text>Reversed text: {reversedValue}</Text>
+        <Text>For which number do you want to compute the Cubic Root?</Text>
+        <TextInput
+          style={styles.textInput}
+          placeholder="Write your text here"
+          onChangeText={setCubicSource}
+          value={cubicSource}
+        />
+        <Button title="Get Cubic Root" onPress={() => setCubicRoot(SampleTurboModule.cubicRoot(cubicSource))} />
+        <Text>The cubic root is: {cubicRoot}</Text>
      </View>
    </SafeAreaView>
  );
}
//...
```

2. 測試 Android 應用程式：在專案根目錄執行 `yarn android`
3. 測試 iOS 應用程式：在專案根目錄執行 `yarn ios`

## 新增結構化自訂型別：Address

上述方法可通用於任何類型。對於結構化類型，React Native 提供了一些輔助函數，能更簡便地實現 JS 與 C++ 之間的橋接。

假設我們需要橋接一個具有以下屬性的自定義 `Address` 類型：

```ts
interface Address {
  street: string;
  num: number;
  isInUS: boolean;
}
```

### 1. 在規格中定義類型

首先，我們在 JS 規格中定義新的自定義類型，讓 Codegen 能自動生成所有支援代碼。這樣就不需手動編寫這些代碼。

1. 打開 `specs/NativeSampleModule` 文件並加入以下修改。

<Tabs groupId="custom-int64" queryString defaultValue={constants.defaultJavaScriptSpecLanguages} values={constants.javaScriptSpecLanguages}>
<TabItem value="typescript">

```diff title="NativeSampleModule (Add Address type and validateAddress function)"
import {TurboModule, TurboModuleRegistry} from 'react-native';

+export type Address = {
+  street: string,
+  num: number,
+  isInUS: boolean,
+};

export interface Spec extends TurboModule {
  readonly reverseString: (input: string) => string;
+ readonly validateAddress: (input: Address) => boolean;
}

export default TurboModuleRegistry.getEnforcing<Spec>(
  'NativeSampleModule',
);
```

</TabItem>
<TabItem value="flow">

```diff title="NativeSampleModule (Add Address type and validateAddress function)"

// @flow
import type {TurboModule} from 'react-native';
import { TurboModuleRegistry } from "react-native";

+export type Address = {
+  street: string,
+  num: number,
+  isInUS: boolean,
+};


export interface Spec extends TurboModule {
  +reverseString: (input: string) => string;
+ +validateAddress: (input: Address) => boolean;
}

export default (TurboModuleRegistry.getEnforcing<Spec>(
  "NativeSampleModule"
): Spec);
```

</TabItem>
</Tabs>

這段代碼定義了新的 `Address` 類型，並為 Turbo Native Module 定義了一個新的 `validateAddress` 函數。注意 `validateFunction` 需要一個 `Address` 物件作為參數。

也可以定義返回自定義類型的函數。

### 2. 定義橋接代碼

根據規格中定義的 `Address` 類型，Codegen 會生成兩個輔助類型：`NativeSampleModuleAddress` 和 `NativeSampleModuleAddressBridging`。

第一個類型是 `Address` 的定義。第二個類型包含了所有橋接自定義類型所需的基礎設施。我們唯一需要額外做的是定義擴展 `NativeSampleModuleAddressBridging` 類型的 `Bridging` 結構。

1. 打開 `shared/NativeSampleModule.h` 文件
2. 在文件中加入以下代碼：

```diff title="NativeSampleModule.h (Bridging the Address type)"
#include "Int64.h"
#include <memory>
#include <string>

namespace facebook::react {
+  using Address = NativeSampleModuleAddress<std::string, int32_t, bool>;

+  template <>
+  struct Bridging<Address>
+      : NativeSampleModuleAddressBridging<Address> {};
  // ...
}
```

這段代碼為泛型類型 `NativeSampleModuleAddress` 定義了一個 `Address` 類型別名。**泛型參數的順序很重要**：第一個模板參數對應結構體的第一個數據類型，第二個對應第二個，以此類推。

接著，代碼通過擴展 Codegen 生成的 `NativeSampleModuleAddressBridging`，為新的 `Address` 類型添加了 `Bridging` 特化。

:::note
生成這些類型時遵循以下命名慣例：

- 名稱的第一部分始終是模組的類型。本例中為 `NativeSampleModule`。
- 名稱的第二部分始終是規格中定義的 JS 類型名稱。本例中為 `Address`。
  :::

### 3. 實現原生代碼

現在，我們需要在 C++ 中實現 `validateAddress` 函數。首先，我們要在 `.h` 文件中加入函數聲明，然後在 `.cpp` 文件中實現它。

1. 打開 `shared/NativeSampleModule.h` 文件並加入函數定義

```diff title="NativeSampleModule.h (validateAddress function prototype)"
  std::string reverseString(jsi::Runtime& rt, std::string input);

+  bool validateAddress(jsi::Runtime &rt, jsi::Object input);
};

} // namespace facebook::react
```

2. 打開 `shared/NativeSampleModule.cpp` 文件並加入函數實現

```c++ title="NativeSampleModule.cpp (validateAddress implementation)"
bool NativeSampleModule::validateAddress(jsi::Runtime &rt, jsi::Object input) {
  std::string street = input.getProperty(rt, "street").asString(rt).utf8(rt);
  int32_t number = input.getProperty(rt, "num").asNumber();

  return !street.empty() && number > 0;
}
```

在實現中，代表 `Address` 的物件是一個 `jsi::Object`。要從這個物件中提取值，我們需要使用 `JSI` 提供的訪問器：

- `getProperty()` 通過名稱從物件中檢索屬性。
- `asString()` 將屬性轉換為 `jsi::String`。
- `utf8()` 將 `jsi::String` 轉換為 `std::string`。
- `asNumber()` 將屬性轉換為 `double`。

手動解析物件後，我們就可以實現所需的邏輯。

:::note
如果想了解更多關於 `JSI` 及其工作原理，可以觀看這個 [精彩演講](https://youtu.be/oLmGInjKU2U?feature=shared)（來自 App.JS 2024）
:::

### 4. 在應用中測試代碼

要在應用程式中測試這段程式碼，我們需要修改 `App.tsx` 檔案。

1. 開啟 `App.tsx` 檔案，移除 `App()` 函式的內容。
2. 將 `App()` 函式的主體替換為以下程式碼：

```ts title="App.tsx (App function body replacement)"
const [street, setStreet] = React.useState('');
const [num, setNum] = React.useState('');
const [isValidAddress, setIsValidAddress] = React.useState<
  boolean | null
>(null);

const onPress = () => {
  let houseNum = parseInt(num, 10);
  if (isNaN(houseNum)) {
    houseNum = -1;
  }
  const address = {
    street,
    num: houseNum,
    isInUS: false,
  };
  const result = SampleTurboModule.validateAddress(address);
  setIsValidAddress(result);
};

return (
  <SafeAreaView style={styles.container}>
    <View>
      <Text style={styles.title}>
        Welcome to C Turbo Native Module Example
      </Text>
      <Text>Address:</Text>
      <TextInput
        style={styles.textInput}
        placeholder="Write your address here"
        onChangeText={setStreet}
        value={street}
      />
      <Text>Number:</Text>
      <TextInput
        style={styles.textInput}
        placeholder="Write your address here"
        onChangeText={setNum}
        value={num}
      />
      <Button title="Validate" onPress={onPress} />
      {isValidAddress != null && (
        <Text>
          Your address is {isValidAddress ? 'valid' : 'not valid'}
        </Text>
      )}
    </View>
  </SafeAreaView>
);
```

恭喜！🎉

你已成功將第一個類型從 JS 橋接到 C++。