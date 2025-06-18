---
id: native-modules-android
title: Android Native Modules
---

import NativeDeprecated from './the-new-architecture/\_markdown_native_deprecation.mdx'
import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

<NativeDeprecated />

歡迎使用 Android 原生模組。請先閱讀 [原生模組簡介](native-modules-intro) 了解原生模組的基本概念。

## 建立日曆原生模組

本指南將帶您建立一個名為 `CalendarModule` 的原生模組，該模組可讓您從 JavaScript 存取 Android 的日曆 API。最終您將能透過 JavaScript 呼叫 `CalendarModule.createCalendarEvent('晚餐派對', '我家');` 來觸發建立日曆事件的 Java/Kotlin 方法。

### 環境設定

首先，請在 Android Studio 中開啟您 React Native 應用程式內的 Android 專案。React Native 應用中的 Android 專案路徑如下：

<figure>
  <img src="/docs/assets/native-modules-android-open-project.png" width="500" alt="Image of opening up an Android project within a React Native app inside of Android Studio." />
  <figcaption>Image of where you can find your Android project</figcaption>
</figure>

建議使用 Android Studio 編寫原生程式碼。作為專為 Android 開發打造的 IDE，它能協助您快速解決程式語法等輕微問題。

同時建議啟用 [Gradle Daemon](https://docs.gradle.org/2.9/userguide/gradle_daemon.html) 以加速 Java/Kotlin 程式碼的迭代建構過程。

### 建立自訂原生模組檔案

第一步是在 `android/app/src/main/java/com/您的應用名稱/` 資料夾內建立 (`CalendarModule.java` 或 `CalendarModule.kt`) 檔案（Kotlin 與 Java 的路徑相同）。該檔案將包含您的原生模組類別。

<figure>
  <img src="/docs/assets/native-modules-android-add-class.png" width="700" alt="Image of adding a class called CalendarModule.java within the Android Studio." />
  <figcaption>Image of how to add the CalendarModuleClass</figcaption>
</figure>

接著加入以下內容：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
package com.your-apps-package-name; // replace your-apps-package-name with your app’s package name
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;
import java.util.Map;
import java.util.HashMap;

public class CalendarModule extends ReactContextBaseJavaModule {
   CalendarModule(ReactApplicationContext context) {
       super(context);
   }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
package com.your-apps-package-name; // replace your-apps-package-name with your app’s package name
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.bridge.ReactContext
import com.facebook.react.bridge.ReactContextBaseJavaModule
import com.facebook.react.bridge.ReactMethod

class CalendarModule(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {...}
```

</TabItem>
</Tabs>

如您所見，`CalendarModule` 類別繼承了 `ReactContextBaseJavaModule`。在 Android 中，Java/Kotlin 原生模組需擴展此類別並實作 JavaScript 所需功能。

> 技術上，Java/Kotlin 類別只需繼承 `BaseJavaModule` 或實作 `NativeModule` 介面即可被 React Native 視為原生模組。

> 但建議使用如上所示的 `ReactContextBaseJavaModule`，該類別提供對 `ReactApplicationContext` (RAC) 的存取權限，對於需要掛載活動生命週期方法的原生模組特別有用。未來也能更輕鬆實現型別安全的原生模組——React Native 會根據 JavaScript 規格生成繼承 `ReactContextBaseJavaModule` 的抽象基礎類別。

### 模組名稱

所有 Android 的 Java/Kotlin 原生模組都需實作 `getName()` 方法。該方法返回的字串代表模組名稱，JavaScript 可透過此名稱存取模組。例如下方程式碼片段中，`getName()` 返回 `"CalendarModule"`。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
// add to CalendarModule.java
@Override
public String getName() {
   return "CalendarModule";
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
// add to CalendarModule.kt
override fun getName() = "CalendarModule"
```

</TabItem>
</Tabs>

JavaScript 端可透過以下方式存取該原生模組：

```tsx
const {CalendarModule} = ReactNative.NativeModules;
```

### 向 JavaScript 導出原生方法

接著您需在原生模組中加入建立日曆事件的方法，該方法需標註 `@ReactMethod` 才能從 JavaScript 端呼叫。

為 `CalendarModule` 設定 `createCalendarEvent()` 方法，未來可透過 `CalendarModule.createCalendarEvent()` 在 JS 端呼叫。目前該方法先接收名稱與位置字串參數，參數型別選項將稍後說明。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@ReactMethod
public void createCalendarEvent(String name, String location) {
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactMethod fun createCalendarEvent(name: String, location: String) {}
```

</TabItem>
</Tabs>

在方法內加入除錯日誌，以確認從應用程式呼叫時確實觸發。以下是從 Android util 套件導入並使用 [Log](https://developer.android.com/reference/android/util/Log) 類別的範例：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
import android.util.Log;

@ReactMethod
public void createCalendarEvent(String name, String location) {
   Log.d("CalendarModule", "Create event called with name: " + name
   + " and location: " + location);
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
import android.util.Log

@ReactMethod
fun createCalendarEvent(name: String, location: String) {
    Log.d("CalendarModule", "Create event called with name: $name and location: $location")
}
```

</TabItem>
</Tabs>

完成原生模組的實作並在 JavaScript 中掛接後，您可以按照[這些步驟](https://developer.android.com/studio/debug/am-logcat.html)查看應用程式的日誌記錄。

### 同步方法

您可以將 `isBlockingSynchronousMethod = true` 傳遞給原生方法，將其標記為同步方法。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@ReactMethod(isBlockingSynchronousMethod = true)
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactMethod(isBlockingSynchronousMethod = true)
```

</TabItem>
</Tabs>

目前我們不建議這樣做，因為同步呼叫方法可能會嚴重影響效能，並為您的原生模組引入與線程相關的錯誤。此外，請注意，如果您選擇啟用 `isBlockingSynchronousMethod`，您的應用程式將無法再使用 Google Chrome 調試器。這是因為同步方法要求 JS VM 與應用程式共享記憶體。對於 Google Chrome 調試器，React Native 在 Google Chrome 的 JS VM 中運行，並通過 WebSockets 與移動設備進行異步通信。

### 註冊模組 (Android 專屬)

原生模組編寫完成後，需要向 React Native 註冊。為此，您需要將您的原生模組添加到 `ReactPackage` 中，並向 React Native 註冊該 `ReactPackage`。在初始化期間，React Native 會遍歷所有套件，並為每個 `ReactPackage` 註冊其中的每個原生模組。

React Native 調用 `ReactPackage` 上的 `createNativeModules()` 方法以獲取要註冊的原生模組列表。對於 Android，如果模組未在 createNativeModules 中實例化並返回，則該模組將無法從 JavaScript 中使用。

要將您的原生模組添加到 `ReactPackage`，首先在 `android/app/src/main/java/com/your-app-name/` 文件夾中創建一個新的 Java/Kotlin 類，名為 (`MyAppPackage.java` 或 `MyAppPackage.kt`)，並實現 `ReactPackage`：

然後添加以下內容：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
package com.your-app-name; // replace your-app-name with your app’s name
import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class MyAppPackage implements ReactPackage {

   @Override
   public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
       return Collections.emptyList();
   }

   @Override
   public List<NativeModule> createNativeModules(
           ReactApplicationContext reactContext) {
       List<NativeModule> modules = new ArrayList<>();

       modules.add(new CalendarModule(reactContext));

       return modules;
   }

}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
package com.your-app-name // replace your-app-name with your app’s name

import android.view.View
import com.facebook.react.ReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.uimanager.ReactShadowNode
import com.facebook.react.uimanager.ViewManager

class MyAppPackage : ReactPackage {

    override fun createViewManagers(
        reactContext: ReactApplicationContext
    ): MutableList<ViewManager<View, ReactShadowNode<*>>> = mutableListOf()

    override fun createNativeModules(
        reactContext: ReactApplicationContext
    ): MutableList<NativeModule> = listOf(CalendarModule(reactContext)).toMutableList()
}
```

</TabItem>
</Tabs>

此文件導入您創建的原生模組 `CalendarModule`。然後在 `createNativeModules()` 函數中實例化 `CalendarModule`，並將其作為要註冊的 `NativeModules` 列表返回。如果您後續添加更多原生模組，也可以實例化它們並將其添加到這裡返回的列表中。

> 值得注意的是，這種註冊原生模組的方式會在應用程式啟動時急切地初始化所有原生模組，這會增加應用程式的啟動時間。您可以改用 [TurboReactPackage](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/TurboReactPackage.java)。與 `createNativeModules` 返回實例化的原生模組對象列表不同，TurboReactPackage 實現了一個 `getModule(String name, ReactApplicationContext rac)` 方法，該方法在需要時創建原生模組對象。目前 TurboReactPackage 的實現稍微複雜一些。除了實現 `getModule()` 方法外，您還需要實現一個 `getReactModuleInfoProvider()` 方法，該方法返回套件可以實例化的所有原生模組的列表以及實例化它們的函數，示例[在此](https://github.com/facebook/react-native/blob/8ac467c51b94c82d81930b4802b2978c85539925/ReactAndroid/src/main/java/com/facebook/react/CoreModulesPackage.java#L86-L165)。再次強調，使用 TurboReactPackage 可以讓您的應用程式啟動更快，但目前編寫起來有點麻煩。因此，如果您選擇使用 TurboReactPackages，請謹慎行事。

要註冊 `CalendarModule` 套件，您必須將 `MyAppPackage` 添加到 ReactNativeHost 的 `getPackages()` 方法返回的套件列表中。打開您的 `MainApplication.java` 或 `MainApplication.kt` 文件，該文件可以在以下路徑中找到：`android/app/src/main/java/com/your-app-name/`。

找到 ReactNativeHost 的 `getPackages()` 方法，並將您的套件添加到 `getPackages()` 返回的套件列表中：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
protected List<ReactPackage> getPackages() {
    List<ReactPackage> packages = new PackageList(this).getPackages();
    // Packages that cannot be autolinked yet can be added manually here, for example:
    // packages.add(new MyReactNativePackage());
    packages.add(new MyAppPackage());
    return packages;
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun getPackages(): List<ReactPackage> =
    PackageList(this).packages.apply {
        // Packages that cannot be autolinked yet can be added manually here, for example:
        // add(MyReactNativePackage())
        add(MyAppPackage())
    }
```

</TabItem>
</Tabs>

您現在已成功為 Android 註冊了您的原生模組！

### 測試您所構建的內容

至此，您已為 Android 原生模組完成基礎架構搭建。請透過 JavaScript 存取該原生模組並呼叫其導出方法來進行測試。

在應用程式中找到適合呼叫原生模組 `createCalendarEvent()` 方法的位置。以下是一個可新增至應用程式的元件範例 `NewModuleButton`，您可以在該元件的 `onPress()` 函式中呼叫原生模組。

```tsx
import React from 'react';
import {NativeModules, Button} from 'react-native';

const NewModuleButton = () => {
  const onPress = () => {
    console.log('We will invoke the native module here!');
  };

  return (
    <Button
      title="Click to invoke your native module!"
      color="#841584"
      onPress={onPress}
    />
  );
};

export default NewModuleButton;
```

要從 JavaScript 存取原生模組，需先從 React Native 導入 `NativeModules`：

```tsx
import {NativeModules} from 'react-native';
```

接著即可從 `NativeModules` 取得 `CalendarModule` 原生模組。

```tsx
const {CalendarModule} = NativeModules;
```

現在您已取得 CalendarModule 原生模組，可呼叫原生方法 `createCalendarEvent()`。以下示範如何將其新增至 `NewModuleButton` 的 `onPress()` 方法：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent('testName', 'testLocation');
};
```

最後一步是重新建置 React Native 應用程式，使最新原生程式碼（包含您的新原生模組）生效。請在 React Native 應用程式所在目錄的命令列中執行：

<Tabs groupId="package-manager" queryString defaultValue={constants.defaultPackageManager} values={constants.packageManagers}>
<TabItem value="npm">

```shell
npm run android
```

</TabItem>
<TabItem value="yarn">

```shell
yarn android
```

</TabItem>
</Tabs>

### 迭代開發時的建置流程

當您遵循本指南迭代開發原生模組時，需透過原生建置才能使 JavaScript 端取得最新變更。這是因為您編寫的程式碼位於應用程式的原生部分。雖然 React Native 的 Metro 打包工具能監測 JavaScript 變更並即時重建，但不會對原生程式碼執行相同操作。因此若要測試最新的原生變更，必須使用上述指令重新建置。

### 重點回顧 ✨

現在您應能在應用程式中呼叫原生模組的 `createCalendarEvent()` 方法。在我們的範例中，透過點擊 `NewModuleButton` 觸發。您可檢視 `createCalendarEvent()` 方法中設定的日誌來確認。請參照[這些步驟](https://developer.android.com/studio/debug/am-logcat.html)查看應用程式的 ADB 日誌。搜尋您設定的 `Log.d` 訊息（範例中為「Create event called with name: testName and location: testLocation」），每次呼叫原生模組方法時都應能看到該日誌訊息。

<figure>
  <img src="/docs/assets/native-modules-android-logs.png" width="1000" alt="Image of logs." />
  <figcaption>Image of ADB logs in Android Studio</figcaption>
</figure>

至此您已完成 Android 原生模組的建立，並從 React Native 應用程式的 JavaScript 端呼叫其原生方法。您可繼續閱讀以了解更多進階主題，例如原生模組方法支援的參數類型，以及如何設定回調函式與 Promise。

## 日曆原生模組的進階應用

### 優化原生模組導出方式

透過從 `NativeModules` 提取來導入原生模組（如上所述）稍顯繁瑣。

為避免模組使用者每次存取時重複此操作，可為該模組建立 JavaScript 封裝層。建立名為 `CalendarModule.js` 的新 JavaScript 檔案，內容如下：

```tsx
/**
* This exposes the native CalendarModule module as a JS module. This has a
* function 'createCalendarEvent' which takes the following parameters:

* 1. String name: A string representing the name of the event
* 2. String location: A string representing the location of the event
*/
import {NativeModules} from 'react-native';
const {CalendarModule} = NativeModules;
export default CalendarModule;
```

此 JavaScript 檔案也是新增 JavaScript 端功能的理想位置。例如若您使用 TypeScript 等型別系統，可在此為原生模組添加型別註解。雖然 React Native 尚未支援 Native 到 JS 的型別安全，但所有 JS 程式碼都將具備型別安全性。此做法也能讓您未來更容易遷移至型別安全的原生模組。以下為 CalendarModule 添加型別安全的範例：

```tsx
/**
 * This exposes the native CalendarModule module as a JS module. This has a
 * function 'createCalendarEvent' which takes the following parameters:
 *
 * 1. String name: A string representing the name of the event
 * 2. String location: A string representing the location of the event
 */
import {NativeModules} from 'react-native';
const {CalendarModule} = NativeModules;
interface CalendarInterface {
  createCalendarEvent(name: string, location: string): void;
}
export default CalendarModule as CalendarInterface;
```

在其他 JavaScript 檔案中，您可透過以下方式存取原生模組並呼叫其方法：

```tsx
import CalendarModule from './CalendarModule';
CalendarModule.createCalendarEvent('foo', 'bar');
```

> 此範例假設導入 `CalendarModule` 的位置與 `CalendarModule.js` 處於相同目錄層級。請根據實際情況調整相對路徑。

### 參數類型

當在 JavaScript 中調用原生模組方法時，React Native 會將參數從 JS 物件轉換為對應的 Java/Kotlin 物件。例如，如果您的 Java 原生模組方法接受一個 double 類型，在 JS 中您需要用數字調用該方法。React Native 會自動處理轉換。以下是原生模組方法支持的參數類型及其對應的 JavaScript 等效類型列表。

| Java          | Kotlin        | JavaScript |
| ------------- | ------------- | ---------- |
| Boolean       | Boolean       | ?boolean   |
| boolean       |               | boolean    |
| Double        | Double        | ?number    |
| double        |               | number     |
| String        | String        | string     |
| Callback      | Callback      | Function   |
| Promise       | Promise       | Promise    |
| ReadableMap   | ReadableMap   | Object     |
| ReadableArray | ReadableArray | Array      |

> 以下類型目前受支援，但在 TurboModules 中將不再支援，請避免使用：
>
> - Integer Java/Kotlin -> ?number
> - Float Java/Kotlin -> ?number
> - int Java -> number
> - float Java -> number

對於未列出的參數類型，您需要自行處理轉換。例如，在 Android 中，`Date` 類型的轉換並未原生支援。您可以在原生方法中自行處理 `Date` 類型的轉換，如下所示：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
    String dateFormat = "yyyy-MM-dd";
    SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
    Calendar eStartDate = Calendar.getInstance();
    try {
        eStartDate.setTime(sdf.parse(startDate));
    }

```

</TabItem>
<TabItem value="kotlin">

```kotlin
    val dateFormat = "yyyy-MM-dd"
    val sdf = SimpleDateFormat(dateFormat, Locale.US)
    val eStartDate = Calendar.getInstance()
    try {
        sdf.parse(startDate)?.let {
            eStartDate.time = it
        }
    }
```

</TabItem>
</Tabs>

### 導出常數

原生模組可以通過實現原生方法 `getConstants()` 來導出常數，該方法在 JS 中可用。以下您將實現 `getConstants()` 並返回一個包含 `DEFAULT_EVENT_NAME` 常數的 Map，該常數可以在 JavaScript 中訪問：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
public Map<String, Object> getConstants() {
   final Map<String, Object> constants = new HashMap<>();
   constants.put("DEFAULT_EVENT_NAME", "New Event");
   return constants;
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun getConstants(): MutableMap<String, Any> =
    hashMapOf("DEFAULT_EVENT_NAME" to "New Event")
```

</TabItem>
</Tabs>

然後可以通過在 JS 中調用原生模組的 `getConstants` 來訪問該常數：

```tsx
const {DEFAULT_EVENT_NAME} = CalendarModule.getConstants();
console.log(DEFAULT_EVENT_NAME);
```

從技術上講，可以直接從原生模組物件訪問 `getConstants()` 導出的常數。這在 TurboModules 中將不再受支援，因此我們鼓勵社區改用上述方法，以避免未來不必要的遷移。

> 目前常數僅在初始化時導出，因此如果在運行時更改 `getConstants` 的值，不會影響 JavaScript 環境。這將在 TurboModules 中改變。在 TurboModules 中，`getConstants()` 將成為一個常規的原生模組方法，每次調用都會觸發原生端。

### 回調函數

原生模組還支持一種特殊的參數類型：回調函數。回調函數用於將數據從 Java/Kotlin 異步傳遞到 JavaScript。它們也可以用於從原生端異步執行 JavaScript。

要創建一個帶有回調函數的原生模組方法，首先導入 `Callback` 接口，然後在您的原生模組方法中添加一個類型為 `Callback` 的新參數。回調參數有幾個細節需要注意，這些細節將在 TurboModules 中得到改進。首先，您的函數參數中只能有兩個回調函數——一個 successCallback 和一個 failureCallback。此外，原生模組方法調用的最後一個參數（如果是函數）將被視為 successCallback，倒數第二個參數（如果是函數）將被視為 failureCallback。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
import com.facebook.react.bridge.Callback;

@ReactMethod
public void createCalendarEvent(String name, String location, Callback callBack) {
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
import com.facebook.react.bridge.Callback

@ReactMethod fun createCalendarEvent(name: String, location: String, callback: Callback) {}
```

</TabItem>
</Tabs>

您可以在 Java/Kotlin 方法中調用回調函數，並傳遞您希望傳遞給 JavaScript 的任何數據。請注意，您只能從原生代碼傳遞可序列化的數據到 JavaScript。如果需要傳回原生物件，可以使用 `WriteableMaps`；如果需要使用集合，可以使用 `WritableArrays`。還需要注意的是，回調函數不會在原函數完成後立即調用。以下示例將之前調用中創建的事件 ID 傳遞給回調函數。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
  @ReactMethod
   public void createCalendarEvent(String name, String location, Callback callBack) {
       Integer eventId = ...
       callBack.invoke(eventId);
   }
```

</TabItem>
<TabItem value="kotlin">

```kotlin
  @ReactMethod
  fun createCalendarEvent(name: String, location: String, callback: Callback) {
      val eventId = ...
      callback.invoke(eventId)
  }
```

</TabItem>
</Tabs>

然後可以在 JavaScript 中使用以下方式訪問該方法：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent(
    'Party',
    'My House',
    eventId => {
      console.log(`Created a new event with id ${eventId}`);
    },
  );
};
```

另一個需要注意的重要細節是，原生模組方法只能調用一個回調函數，且只能調用一次。這意味著您可以調用 successCallback 或 failureCallback，但不能同時調用兩者，且每個回調函數最多只能調用一次。不過，原生模組可以存儲回調函數並在稍後調用。

回調函數的錯誤處理有兩種方法。第一種是遵循 Node 的慣例，將傳遞給回調函數的第一個參數視為錯誤物件。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
  @ReactMethod
   public void createCalendarEvent(String name, String location, Callback callBack) {
       Integer eventId = ...
       callBack.invoke(null, eventId);
   }
```

</TabItem>
<TabItem value="kotlin">

```kotlin
  @ReactMethod
  fun createCalendarEvent(name: String, location: String, callback: Callback) {
      val eventId = ...
      callback.invoke(null, eventId)
  }
```

</TabItem>
</Tabs>

在 JavaScript 中，您可以檢查第一個參數來判斷是否有錯誤傳遞：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent(
    'testName',
    'testLocation',
    (error, eventId) => {
      if (error) {
        console.error(`Error found! ${error}`);
      }
      console.log(`event id ${eventId} returned`);
    },
  );
};
```

另一個選項是使用 onSuccess 和 onFailure 回調：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@ReactMethod
public void createCalendarEvent(String name, String location, Callback myFailureCallback, Callback mySuccessCallback) {
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactMethod
  fun createCalendarEvent(
      name: String,
      location: String,
      myFailureCallback: Callback,
      mySuccessCallback: Callback
  ) {}
```

</TabItem>
</Tabs>

然後在 JavaScript 中可以為錯誤和成功響應分別添加回調：

```tsx
const onPress = () => {
  CalendarModule.createCalendarEvent(
    'testName',
    'testLocation',
    error => {
      console.error(`Error found! ${error}`);
    },
    eventId => {
      console.log(`event id ${eventId} returned`);
    },
  );
};
```

### Promise

原生模組也可以實現 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，這能簡化您的 JavaScript 程式碼，特別是當使用 ES2016 的 [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 語法時。當原生模組 Java/Kotlin 方法的最後一個參數是 Promise 時，其對應的 JS 方法將返回一個 JS Promise 物件。

將上述程式碼重構為使用 promise 而非回調的範例如下：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
import com.facebook.react.bridge.Promise;

@ReactMethod
public void createCalendarEvent(String name, String location, Promise promise) {
    try {
        Integer eventId = ...
        promise.resolve(eventId);
    } catch(Exception e) {
        promise.reject("Create Event Error", e);
    }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
import com.facebook.react.bridge.Promise

@ReactMethod
fun createCalendarEvent(name: String, location: String, promise: Promise) {
    try {
        val eventId = ...
        promise.resolve(eventId)
    } catch (e: Throwable) {
        promise.reject("Create Event Error", e)
    }
}
```

</TabItem>
</Tabs>

> 與回調類似，原生模組方法可以拒絕（reject）或解決（resolve）一個 promise（但不能同時進行），且最多只能執行一次。這意味著您可以呼叫成功回調或失敗回調，但不能同時呼叫兩者，且每個回調最多只能被呼叫一次。不過，原生模組可以儲存回調並稍後呼叫。

此方法的 JavaScript 對應部分會返回一個 Promise。這意味著您可以在 async 函數中使用 `await` 關鍵字來呼叫它並等待其結果：

```tsx
const onSubmit = async () => {
  try {
    const eventId = await CalendarModule.createCalendarEvent(
      'Party',
      'My House',
    );
    console.log(`Created a new event with id ${eventId}`);
  } catch (e) {
    console.error(e);
  }
};
```

reject 方法接受以下參數的不同組合：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
String code, String message, WritableMap userInfo, Throwable throwable
```

</TabItem>
<TabItem value="kotlin">

```kotlin
code: String, message: String, userInfo: WritableMap, throwable: Throwable
```

</TabItem>
</Tabs>

更多細節，您可以在[這裡](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/src/main/java/com/facebook/react/bridge/Promise.java)找到 `Promise.java` 介面。如果未提供 `userInfo`，React Native 會將其設為 null。對於其餘參數，React Native 會使用預設值。`message` 參數提供了錯誤呼叫堆疊頂部顯示的錯誤 `message`。以下是從 Java/Kotlin 中的 reject 呼叫在 JavaScript 中顯示的錯誤訊息範例。

Java/Kotlin reject 呼叫：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
promise.reject("Create Event error", "Error parsing date", e);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
promise.reject("Create Event error", "Error parsing date", e)
```

</TabItem>
</Tabs>

當 promise 被拒絕時，React Native 應用中的錯誤訊息：

<figure>
  <img src="/docs/assets/native-modules-android-errorscreen.png" width="200" alt="Image of error message in React Native app." />
  <figcaption>Image of error message</figcaption>
</figure>

### 發送事件到 JavaScript

原生模組可以在不被直接呼叫的情況下向 JavaScript 發出事件信號。例如，您可能希望向 JavaScript 發出信號，提醒原生 Android 日曆應用中的日曆事件即將發生。最簡單的方法是使用 `RCTDeviceEventEmitter`，可以從 `ReactContext` 中取得，如下面的程式碼片段所示。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
...
import com.facebook.react.modules.core.DeviceEventManagerModule;
import com.facebook.react.bridge.WritableMap;
import com.facebook.react.bridge.Arguments;
...
private void sendEvent(ReactContext reactContext,
                      String eventName,
                      @Nullable WritableMap params) {
 reactContext
     .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
     .emit(eventName, params);
}

private int listenerCount = 0;

@ReactMethod
public void addListener(String eventName) {
  if (listenerCount == 0) {
    // Set up any upstream listeners or background tasks as necessary
  }

  listenerCount += 1;
}

@ReactMethod
public void removeListeners(Integer count) {
  listenerCount -= count;
  if (listenerCount == 0) {
    // Remove upstream listeners, stop unnecessary background tasks
  }
}
...
WritableMap params = Arguments.createMap();
params.putString("eventProperty", "someValue");
...
sendEvent(reactContext, "EventReminder", params);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
...
import com.facebook.react.bridge.WritableMap
import com.facebook.react.bridge.Arguments
import com.facebook.react.modules.core.DeviceEventManagerModule
...

private fun sendEvent(reactContext: ReactContext, eventName: String, params: WritableMap?) {
    reactContext
      .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter::class.java)
      .emit(eventName, params)
}

private var listenerCount = 0

@ReactMethod
fun addListener(eventName: String) {
  if (listenerCount == 0) {
    // Set up any upstream listeners or background tasks as necessary
  }

  listenerCount += 1
}

@ReactMethod
fun removeListeners(count: Int) {
  listenerCount -= count
  if (listenerCount == 0) {
    // Remove upstream listeners, stop unnecessary background tasks
  }
}
...
val params = Arguments.createMap().apply {
    putString("eventProperty", "someValue")
}
...
sendEvent(reactContext, "EventReminder", params)
```

</TabItem>
</Tabs>

然後，JavaScript 模組可以通過在 [NativeEventEmitter](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/EventEmitter/NativeEventEmitter.js) 類別上 `addListener` 來註冊接收事件。

```tsx
import {NativeEventEmitter, NativeModules} from 'react-native';
...
useEffect(() => {
    const eventEmitter = new NativeEventEmitter(NativeModules.ToastExample);
    let eventListener = eventEmitter.addListener('EventReminder', event => {
      console.log(event.eventProperty) // "someValue"
    });

    // Removes the listener once unmounted
    return () => {
      eventListener.remove();
    };
  }, []);
```

### 從 startActivityForResult 取得活動結果

如果您想從通過 `startActivityForResult` 啟動的活動中取得結果，您需要監聽 `onActivityResult`。為此，您必須擴展 `BaseActivityEventListener` 或實現 `ActivityEventListener`。前者更受推薦，因為它對 API 變更更具彈性。然後，您需要在模組的構造函數中註冊監聽器，如下所示：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
reactContext.addActivityEventListener(mActivityResultListener);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
reactContext.addActivityEventListener(mActivityResultListener);
```

</TabItem>
</Tabs>

現在，您可以通過實現以下方法來監聽 `onActivityResult`：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
public void onActivityResult(
 final Activity activity,
 final int requestCode,
 final int resultCode,
 final Intent intent) {
 // Your logic here
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun onActivityResult(
    activity: Activity?,
    requestCode: Int,
    resultCode: Int,
    intent: Intent?
) {
    // Your logic here
}
```

</TabItem>
</Tabs>

讓我們實現一個基本的圖片選擇器來演示這一點。圖片選擇器將向 JavaScript 公開 `pickImage` 方法，該方法在呼叫時會返回圖片的路径。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```kotlin
public class ImagePickerModule extends ReactContextBaseJavaModule {

  private static final int IMAGE_PICKER_REQUEST = 1;
  private static final String E_ACTIVITY_DOES_NOT_EXIST = "E_ACTIVITY_DOES_NOT_EXIST";
  private static final String E_PICKER_CANCELLED = "E_PICKER_CANCELLED";
  private static final String E_FAILED_TO_SHOW_PICKER = "E_FAILED_TO_SHOW_PICKER";
  private static final String E_NO_IMAGE_DATA_FOUND = "E_NO_IMAGE_DATA_FOUND";

  private Promise mPickerPromise;

  private final ActivityEventListener mActivityEventListener = new BaseActivityEventListener() {

    @Override
    public void onActivityResult(Activity activity, int requestCode, int resultCode, Intent intent) {
      if (requestCode == IMAGE_PICKER_REQUEST) {
        if (mPickerPromise != null) {
          if (resultCode == Activity.RESULT_CANCELED) {
            mPickerPromise.reject(E_PICKER_CANCELLED, "Image picker was cancelled");
          } else if (resultCode == Activity.RESULT_OK) {
            Uri uri = intent.getData();

            if (uri == null) {
              mPickerPromise.reject(E_NO_IMAGE_DATA_FOUND, "No image data found");
            } else {
              mPickerPromise.resolve(uri.toString());
            }
          }

          mPickerPromise = null;
        }
      }
    }
  };

  ImagePickerModule(ReactApplicationContext reactContext) {
    super(reactContext);

    // Add the listener for `onActivityResult`
    reactContext.addActivityEventListener(mActivityEventListener);
  }

  @Override
  public String getName() {
    return "ImagePickerModule";
  }

  @ReactMethod
  public void pickImage(final Promise promise) {
    Activity currentActivity = getCurrentActivity();

    if (currentActivity == null) {
      promise.reject(E_ACTIVITY_DOES_NOT_EXIST, "Activity doesn't exist");
      return;
    }

    // Store the promise to resolve/reject when picker returns data
    mPickerPromise = promise;

    try {
      final Intent galleryIntent = new Intent(Intent.ACTION_PICK);

      galleryIntent.setType("image/*");

      final Intent chooserIntent = Intent.createChooser(galleryIntent, "Pick an image");

      currentActivity.startActivityForResult(chooserIntent, IMAGE_PICKER_REQUEST);
    } catch (Exception e) {
      mPickerPromise.reject(E_FAILED_TO_SHOW_PICKER, e);
      mPickerPromise = null;
    }
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
class ImagePickerModule(reactContext: ReactApplicationContext) :
    ReactContextBaseJavaModule(reactContext) {

    private var pickerPromise: Promise? = null

    private val activityEventListener =
        object : BaseActivityEventListener() {
            override fun onActivityResult(
                activity: Activity?,
                requestCode: Int,
                resultCode: Int,
                intent: Intent?
            ) {
                if (requestCode == IMAGE_PICKER_REQUEST) {
                    pickerPromise?.let { promise ->
                        when (resultCode) {
                            Activity.RESULT_CANCELED ->
                                promise.reject(E_PICKER_CANCELLED, "Image picker was cancelled")
                            Activity.RESULT_OK -> {
                                val uri = intent?.data

                                uri?.let { promise.resolve(uri.toString())}
                                    ?: promise.reject(E_NO_IMAGE_DATA_FOUND, "No image data found")
                            }
                        }

                        pickerPromise = null
                    }
                }
            }
        }

    init {
        reactContext.addActivityEventListener(activityEventListener)
    }

    override fun getName() = "ImagePickerModule"

    @ReactMethod
    fun pickImage(promise: Promise) {
        val activity = currentActivity

        if (activity == null) {
            promise.reject(E_ACTIVITY_DOES_NOT_EXIST, "Activity doesn't exist")
            return
        }

        pickerPromise = promise

        try {
            val galleryIntent = Intent(Intent.ACTION_PICK).apply { type = "image\/*" }

            val chooserIntent = Intent.createChooser(galleryIntent, "Pick an image")

            activity.startActivityForResult(chooserIntent, IMAGE_PICKER_REQUEST)
        } catch (t: Throwable) {
            pickerPromise?.reject(E_FAILED_TO_SHOW_PICKER, t)
            pickerPromise = null
        }
    }

    companion object {
        const val IMAGE_PICKER_REQUEST = 1
        const val E_ACTIVITY_DOES_NOT_EXIST = "E_ACTIVITY_DOES_NOT_EXIST"
        const val E_PICKER_CANCELLED = "E_PICKER_CANCELLED"
        const val E_FAILED_TO_SHOW_PICKER = "E_FAILED_TO_SHOW_PICKER"
        const val E_NO_IMAGE_DATA_FOUND = "E_NO_IMAGE_DATA_FOUND"
    }
}
```

</TabItem>
</Tabs>

### 監聽生命週期事件

監聽活動的生命週期事件（如 `onResume`、`onPause` 等）與實作 `ActivityEventListener` 的方式非常相似。模組必須實作 `LifecycleEventListener`，然後在模組的建構函式中註冊監聽器，如下所示：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
reactContext.addLifecycleEventListener(this);
```

</TabItem>
<TabItem value="kotlin">

```kotlin
reactContext.addLifecycleEventListener(this)
```

</TabItem>
</Tabs>

現在，您可以通過實作以下方法來監聽活動的生命週期事件：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@Override
public void onHostResume() {
   // Activity `onResume`
}
@Override
public void onHostPause() {
   // Activity `onPause`
}
@Override
public void onHostDestroy() {
   // Activity `onDestroy`
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
override fun onHostResume() {
    // Activity `onResume`
}

override fun onHostPause() {
    // Activity `onPause`
}

override fun onHostDestroy() {
    // Activity `onDestroy`
}
```

</TabItem>
</Tabs>

### 執行緒處理

截至目前，在 Android 上，所有原生模組的異步方法都在單一執行緒上執行。原生模組不應對其被調用的執行緒做出任何假設，因為當前的分配方式可能在未來發生變化。如果需要進行阻塞調用，應將繁重的工作分派到內部管理的工作執行緒，並從那裡分發任何回調。