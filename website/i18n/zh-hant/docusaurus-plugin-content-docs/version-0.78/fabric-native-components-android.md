---
id: fabric-native-components-android
title: 'Fabric Native Modules: Android'
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

現在是時候編寫一些 Android 平台代碼來渲染網頁視圖了。您需要遵循的步驟如下：

- 執行 Codegen
- 編寫 `ReactWebView` 的代碼
- 編寫 `ReactWebViewManager` 的代碼
- 編寫 `ReactWebViewPackage` 的代碼
- 在應用程式中註冊 `ReactWebViewPackage`

### 1. 透過 Gradle 執行 Codegen

執行一次以生成您的 IDE 可以使用的樣板代碼。

```bash title="Demo/"
cd android
./gradlew generateCodegenArtifactsFromSchema
```

Codegen 將生成您需要實現的 `ViewManager` 介面以及網頁視圖的 `ViewManager` 委託。

### 2. 編寫 `ReactWebView`

`ReactWebView` 是封裝 Android 原生視圖的組件，當 React Native 使用我們的自定義組件時會渲染該視圖。

在 `android/src/main/java/com/webview` 資料夾中創建一個 `ReactWebView.java` 或 `ReactWebView.kt` 文件，並填入以下代碼：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="Demo/android/src/main/java/com/webview/ReactWebView.java"
package com.webview;

import android.content.Context;
import android.util.AttributeSet;
import android.webkit.WebView;
import android.webkit.WebViewClient;

import com.facebook.react.bridge.Arguments;
import com.facebook.react.bridge.WritableMap;
import com.facebook.react.bridge.ReactContext;
import com.facebook.react.uimanager.UIManagerHelper;
import com.facebook.react.uimanager.events.Event;

public class ReactWebView extends WebView {
  public ReactWebView(Context context) {
    super(context);
    configureComponent();
  }

  public ReactWebView(Context context, AttributeSet attrs) {
    super(context, attrs);
    configureComponent();
  }

  public ReactWebView(Context context, AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
    configureComponent();
  }

  private void configureComponent() {
    this.setLayoutParams(new LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT));
    this.setWebViewClient(new WebViewClient() {
      @Override
      public void onPageFinished(WebView view, String url) {
        emitOnScriptLoaded(OnScriptLoadedEventResult.success);
      }
    });
  }

  public void emitOnScriptLoaded(OnScriptLoadedEventResult result) {
    ReactContext reactContext = (ReactContext) context;
    int surfaceId = UIManagerHelper.getSurfaceId(reactContext);
    EventDispatcher eventDispatcher = UIManagerHelper.getEventDispatcherForReactTag(reactContext, getId());
    WritableMap payload = Arguments.createMap();
    payload.putString("result", result.name());

    OnScriptLoadedEvent event = new OnScriptLoadedEvent(surfaceId, getId(), payload);
    if (eventDispatcher != null) {
      eventDispatcher.dispatchEvent(event);
    }
  }

  public enum OnScriptLoadedEventResult {
    success,
    error
  }

  private class OnScriptLoadedEvent extends Event<OnScriptLoadedEvent> {
    private final WritableMap payload;

    OnScriptLoadedEvent(int surfaceId, int viewId, WritableMap payload) {
      super(surfaceId, viewId);
      this.payload = payload;
    }

    @Override
    public String getEventName() {
      return "onScriptLoaded";
    }

    @Override
    public WritableMap getEventData() {
      return payload;
    }
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="Demo/android/src/main/java/com/webview/ReactWebView.kt"
package com.webview

import android.content.Context
import android.util.AttributeSet
import android.webkit.WebView
import android.webkit.WebViewClient
import com.facebook.react.bridge.Arguments
import com.facebook.react.bridge.WritableMap
import com.facebook.react.bridge.ReactContext
import com.facebook.react.uimanager.UIManagerHelper
import com.facebook.react.uimanager.events.Event

class ReactWebView: WebView {
  constructor(context: Context) : super(context) {
    configureComponent()
  }

  constructor(context: Context, attrs: AttributeSet?) : super(context, attrs) {
    configureComponent()
  }

  constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int) : super(context, attrs, defStyleAttr) {
    configureComponent()
  }

  private fun configureComponent() {
    this.layoutParams = LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT)
    this.webViewClient = object : WebViewClient() {
      override fun onPageFinished(view: WebView, url: String) {
        emitOnScriptLoaded(OnScriptLoadedEventResult.success)
      }
    }
  }

  fun emitOnScriptLoaded(result: OnScriptLoadedEventResult) {
    val reactContext = context as ReactContext
    val surfaceId = UIManagerHelper.getSurfaceId(reactContext)
    val eventDispatcher = UIManagerHelper.getEventDispatcherForReactTag(reactContext, id)
    val payload =
        Arguments.createMap().apply {
          putString("result", result.name)
        }
    val event = OnScriptLoadedEvent(surfaceId, id, payload)

    eventDispatcher?.dispatchEvent(event)
  }

  enum class OnScriptLoadedEventResult {
    success,
    error;
  }

  inner class OnScriptLoadedEvent(
      surfaceId: Int,
      viewId: Int,
      private val payload: WritableMap
  ) : Event<OnScriptLoadedEvent>(surfaceId, viewId) {
    override fun getEventName() = "onScriptLoaded"

    override fun getEventData() = payload
  }
}
```

</TabItem>
</Tabs>

`ReactWebView` 繼承自 Android 的 `WebView`，因此您可以輕鬆重用平台已定義的所有屬性。

該類定義了三個 Android 構造函數，但將其實際實現委託給私有的 `configureComponent` 函數。此函數負責初始化所有特定於組件的屬性：在此代碼中，您設置了 `WebView` 的佈局，並定義了用於自定義 `WebView` 行為的 `WebClient`。在這段代碼中，`ReactWebView` 通過實現 `WebClient` 的 `onPageFinished` 方法，在頁面加載完成時發出事件。

代碼隨後定義了一個輔助函數來實際發出事件。要發出事件，您需要：

- 獲取 `ReactContext` 的引用；
- 檢索您正在呈現的視圖的 `surfaceId`；
- 獲取與視圖關聯的 `eventDispatcher` 的引用；
- 使用 `WritableMap` 對象構建事件的負載；
- 創建需要發送到 JavaScript 的事件對象；
- 調用 `eventDispatcher.dispatchEvent` 來發送事件。

文件的最後部分包含發送事件所需的數據類型定義：

- `OnScriptLoadedEventResult`，包含 `OnScriptLoaded` 事件的可能結果。
- 實際的 `OnScriptLoadedEvent`，需要繼承自 React Native 的 `Event` 類。

### 3. 編寫 `WebViewManager`

`WebViewManager` 是連接 React Native 運行時與原生視圖的類。

當 React 從應用程式接收到渲染特定組件的指令時，React 使用已註冊的視圖管理器來創建視圖並傳遞所有必需的屬性。

以下是 `ReactWebViewManager` 的代碼。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="Demo/android/src/main/java/com/webview/ReactWebViewManager.java"
package com.webview;

import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.module.annotations.ReactModule;
import com.facebook.react.uimanager.SimpleViewManager;
import com.facebook.react.uimanager.ThemedReactContext;
import com.facebook.react.uimanager.ViewManagerDelegate;
import com.facebook.react.uimanager.annotations.ReactProp;
import com.facebook.react.viewmanagers.CustomWebViewManagerInterface;
import com.facebook.react.viewmanagers.CustomWebViewManagerDelegate;

import java.util.HashMap;
import java.util.Map;

@ReactModule(name = ReactWebViewManager.REACT_CLASS)
class ReactWebViewManager extends SimpleViewManager<ReactWebView> implements CustomWebViewManagerInterface<ReactWebView> {
  private final CustomWebViewManagerDelegate<ReactWebView, ReactWebViewManager> delegate =
          new CustomWebViewManagerDelegate<>(this);

  @Override
  public ViewManagerDelegate<ReactWebView> getDelegate() {
    return delegate;
  }

  @Override
  public String getName() {
    return REACT_CLASS;
  }

  @Override
  public ReactWebView createViewInstance(ThemedReactContext context) {
    return new ReactWebView(context);
  }

  @ReactProp(name = "sourceUrl")
  @Override
  public void setSourceURL(ReactWebView view, String sourceURL) {
    if (sourceURL == null) {
      view.emitOnScriptLoaded(ReactWebView.OnScriptLoadedEventResult.error);
      return;
    }
    view.loadUrl(sourceURL, new HashMap<>());
  }

  public static final String REACT_CLASS = "CustomWebView";

  @Override
  public Map<String, Object> getExportedCustomBubblingEventTypeConstants() {
    Map<String, Object> map = new HashMap<>();
    Map<String, Object> bubblingMap = new HashMap<>();
    bubblingMap.put("phasedRegistrationNames", new HashMap<String, String>() {{
      put("bubbled", "onScriptLoaded");
      put("captured", "onScriptLoadedCapture");
    }});
    map.put("onScriptLoaded", bubblingMap);
    return map;
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="Demo/android/src/main/java/com/webview/ReactWebViewManager.kt"
package com.webview

import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.module.annotations.ReactModule;
import com.facebook.react.uimanager.SimpleViewManager;
import com.facebook.react.uimanager.ThemedReactContext;
import com.facebook.react.uimanager.ViewManagerDelegate;
import com.facebook.react.uimanager.annotations.ReactProp;
import com.facebook.react.viewmanagers.CustomWebViewManagerInterface;
import com.facebook.react.viewmanagers.CustomWebViewManagerDelegate;

@ReactModule(name = ReactWebViewManager.REACT_CLASS)
class ReactWebViewManager(context: ReactApplicationContext) : SimpleViewManager<ReactWebView>(), CustomWebViewManagerInterface<ReactWebView> {
  private val delegate: CustomWebViewManagerDelegate<ReactWebView, ReactWebViewManager> =
    CustomWebViewManagerDelegate(this)

  override fun getDelegate(): ViewManagerDelegate<ReactWebView> = delegate

  override fun getName(): String = REACT_CLASS

  override fun createViewInstance(context: ThemedReactContext): ReactWebView = ReactWebView(context)

  @ReactProp(name = "sourceUrl")
  override fun setSourceURL(view: ReactWebView, sourceURL: String?) {
    if (sourceURL == null) {
      view.emitOnScriptLoaded(ReactWebView.OnScriptLoadedEventResult.error)
      return;
    }
    view.loadUrl(sourceURL, emptyMap())
  }

  companion object {
    const val REACT_CLASS = "CustomWebView"
  }

  override fun getExportedCustomBubblingEventTypeConstants(): Map<String, Any> =
      mapOf(
          "onScriptLoaded" to
              mapOf(
                  "phasedRegistrationNames" to
                      mapOf(
                          "bubbled" to "onScriptLoaded",
                          "captured" to "onScriptLoadedCapture"
                      )))
}
```

</TabItem>
</Tabs>

`ReactWebViewManager` 繼承自 React 的 `SimpleViewManager` 類，並實現了由 Codegen 生成的 `CustomWebViewManagerInterface`。

它持有由 Codegen 生成的另一個元素 `CustomWebViewManagerDelegate` 的引用。

然後它覆蓋了 `getName` 函數，該函數必須返回與 spec 中 `codegenNativeComponent` 函數調用所使用的名稱相同。

`createViewInstance` 函數負責實例化一個新的 `ReactWebView`。

接著，視圖管理器需要定義所有 React 組件屬性如何更新原生視圖。在此示例中，您需要決定如何處理 React 將在 `WebView` 上設置的 `sourceURL` 屬性。

最後，若元件能夠觸發事件，您需要覆寫 `getExportedCustomBubblingEventTypeConstants`（針對冒泡事件）或 `getExportedCustomDirectEventTypeConstants`（針對直接事件）來映射事件名稱。

### 4. 撰寫 `ReactWebViewPackage`

如同處理原生模組時的做法，原生元件同樣需要實作 `ReactPackage` 類別。此物件可用於在 React Native 運行時註冊元件。

以下是 `ReactWebViewPackage` 的程式碼：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java title="Demo/android/src/main/java/com/webview/ReactWebViewPackage.java"
package com.webview;

import com.facebook.react.BaseReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.module.model.ReactModuleInfo;
import com.facebook.react.module.model.ReactModuleInfoProvider;
import com.facebook.react.uimanager.ViewManager;

import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class ReactWebViewPackage extends BaseReactPackage {
  @Override
  public List<ViewManager<?, ?>> createViewManagers(ReactApplicationContext reactContext) {
    return Collections.singletonList(new ReactWebViewManager(reactContext));
  }

  @Override
  public NativeModule getModule(String s, ReactApplicationContext reactApplicationContext) {
    if (ReactWebViewManager.REACT_CLASS.equals(s)) {
      return new ReactWebViewManager(reactApplicationContext);
    }
    return null;
  }

  @Override
  public ReactModuleInfoProvider getReactModuleInfoProvider() {
    return new ReactModuleInfoProvider() {
      @Override
      public Map<String, ReactModuleInfo> getReactModuleInfos() {
        Map<String, ReactModuleInfo> map = new HashMap<>();
        map.put(ReactWebViewManager.REACT_CLASS, new ReactModuleInfo(
                ReactWebViewManager.REACT_CLASS, // name
                ReactWebViewManager.REACT_CLASS, // className
                false,                           // canOverrideExistingModule
                false,                           // needsEagerInit
                false,                           // isCxxModule
                true                             // isTurboModule
        ));
        return map;
      }
    };
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin title="Demo/android/src/main/java/com/webview/ReactWebViewPackage.kt"
package com.webview

import com.facebook.react.BaseReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.module.model.ReactModuleInfo
import com.facebook.react.module.model.ReactModuleInfoProvider
import com.facebook.react.uimanager.ViewManager

class ReactWebViewPackage : BaseReactPackage() {
  override fun createViewManagers(reactContext: ReactApplicationContext): List<ViewManager<*, *>> {
    return listOf(ReactWebViewManager(reactContext))
  }

  override fun getModule(s: String, reactApplicationContext: ReactApplicationContext): NativeModule? {
    when (s) {
      ReactWebViewManager.REACT_CLASS -> ReactWebViewManager(reactApplicationContext)
    }
    return null
  }

  override fun getReactModuleInfoProvider(): ReactModuleInfoProvider = ReactModuleInfoProvider {
    mapOf(ReactWebViewManager.REACT_CLASS to ReactModuleInfo(
      _name = ReactWebViewManager.REACT_CLASS,
      _className = ReactWebViewManager.REACT_CLASS,
      _canOverrideExistingModule = false,
      _needsEagerInit = false,
      isCxxModule = false,
      isTurboModule = true,
    )
    )
  }
}
```

</TabItem>
</Tabs>

`ReactWebViewPackage` 繼承自 `BaseReactPackage` 並實作所有必要方法以正確註冊我們的元件。

- `createViewManagers` 方法是工廠方法，用於建立管理自訂視圖的 `ViewManager`。
- `getModule` 方法會根據 React Native 需要渲染的視圖返回對應的 ViewManager。
- `getReactModuleInfoProvider` 提供在運行時註冊模組所需的所有資訊。

### 5. 在應用程式中註冊 `ReactWebViewPackage`

最後，您需要在應用程式中註冊 `ReactWebViewPackage`。具體做法是修改 `MainApplication` 檔案，將 `ReactWebViewPackage` 加入 `getPackages` 函數返回的套件清單中。

```kotlin title="Demo/app/src/main/java/com/demo/MainApplication.kt"
package com.demo

import android.app.Application
import com.facebook.react.PackageList
import com.facebook.react.ReactApplication
import com.facebook.react.ReactHost
import com.facebook.react.ReactNativeHost
import com.facebook.react.ReactPackage
import com.facebook.react.defaults.DefaultNewArchitectureEntryPoint.load
import com.facebook.react.defaults.DefaultReactHost.getDefaultReactHost
import com.facebook.react.defaults.DefaultReactNativeHost
import com.facebook.react.soloader.OpenSourceMergedSoMapping
import com.facebook.soloader.SoLoader
// highlight-next-line
import com.webview.ReactWebViewPackage

class MainApplication : Application(), ReactApplication {

  override val reactNativeHost: ReactNativeHost =
      object : DefaultReactNativeHost(this) {
        override fun getPackages(): List<ReactPackage> =
            PackageList(this).packages.apply {
              // highlight-next-line
              add(ReactWebViewPackage())
            }

        override fun getJSMainModuleName(): String = "index"

        override fun getUseDeveloperSupport(): Boolean = BuildConfig.DEBUG

        override val isNewArchEnabled: Boolean = BuildConfig.IS_NEW_ARCHITECTURE_ENABLED
        override val isHermesEnabled: Boolean = BuildConfig.IS_HERMES_ENABLED
      }

  override val reactHost: ReactHost
    get() = getDefaultReactHost(applicationContext, reactNativeHost)

  override fun onCreate() {
    super.onCreate()
    SoLoader.init(this, OpenSourceMergedSoMapping)
    if (BuildConfig.IS_NEW_ARCHITECTURE_ENABLED) {
      load()
    }
  }
}

```