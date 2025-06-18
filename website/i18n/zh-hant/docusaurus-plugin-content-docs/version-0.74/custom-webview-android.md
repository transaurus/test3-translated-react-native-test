---
id: custom-webview-android
title: Custom WebView
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

雖然內建的 WebView 具備許多功能，但 React Native 仍無法涵蓋所有使用情境。不過您無需分叉 React Native 或複製現有 WebView 代碼，即可透過原生代碼擴展 WebView 功能。

:::info
React Native 的 WebView 組件已作為 [Lean Core 計劃](https://github.com/facebook/react-native/issues/23313)的一部分，遷移至 [`react-native-webview`](https://github.com/react-native-webview/react-native-webview) 套件。
這是目前 React Native 中使用 WebView 的推薦方式。請勿使用已棄用並從 React Native 移除的 [WebView](https://reactnative-archive-august-2023.netlify.app/docs/0.61/webview) 組件。
:::

進行此操作前，您應熟悉 [原生 UI 組件](native-components-android) 的概念，並預先了解 [WebView 的原生代碼實現](https://github.com/react-native-webview/react-native-webview/blob/master/android/src/main/java/com/reactnativecommunity/webview/RNCWebViewManagerImpl.kt)，這將作為實現新功能時的參考——儘管不需要深入理解所有細節。

## 原生代碼

:::info
本範例假設您已安裝 [`react-native-webview`](https://github.com/react-native-webview/react-native-webview)，若尚未安裝請先參閱其 [入門指南](https://github.com/react-native-webview/react-native-webview/blob/master/docs/Getting-Started.md)。
:::

首先需要創建 `RNCWebViewManager`、`RNCWebView` 和 `RNCWebViewClient` 的子類。在視圖管理器中需覆寫以下方法：

- `createRNCWebViewInstance`
- `getName`
- `addEventEmitters`

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
@ReactModule(name = CustomWebViewManager.REACT_CLASS)
public class CustomWebViewManager extends RNCWebViewManager {
  /* This name must match what we're referring to in JS */
  protected static final String REACT_CLASS = "RCTCustomWebView";

  protected static class CustomWebViewClient extends RNCWebViewClient { }

  protected static class CustomWebView extends RNCWebView {
    public CustomWebView(ThemedReactContext reactContext) {
      super(reactContext);
    }
  }

  @Override
  protected RNCWebView createRNCWebViewInstance(ThemedReactContext reactContext) {
    return new CustomWebView(reactContext);
  }

  @Override
  public String getName() {
    return REACT_CLASS;
  }

  @Override
  protected void addEventEmitters(ThemedReactContext reactContext, WebView view) {
    view.setWebViewClient(new CustomWebViewClient());
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
@ReactModule(name = CustomWebViewManager.REACT_CLASS)
class CustomWebViewManager : RNCWebViewManager() {
    protected class CustomWebViewClient : RNCWebViewClient()
    protected inner class CustomWebView(reactContext: ThemedReactContext?) :
        RNCWebView(reactContext)

    override fun createRNCWebViewInstance(reactContext: ThemedReactContext?): RNCWebView {
        return CustomWebView(reactContext)
    }

    override fun addEventEmitters(reactContext: ThemedReactContext, view: WebView) {
        view.webViewClient = CustomWebViewClient()
    }

    companion object {
        /* This name must match what we're referring to in JS */
        const val REACT_CLASS = "RCTCustomWebView"
    }
}
```

</TabItem>
</Tabs>

需按照標準流程 [註冊模組](native-modules-android.md#register-the-module)。

### 添加新屬性

添加新屬性時，需先在 `CustomWebView` 中定義，再於 `CustomWebViewManager` 中公開。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
public class CustomWebViewManager extends RNCWebViewManager {
  ...
  protected static class CustomWebView extends RNCWebView {
    public CustomWebView(ThemedReactContext reactContext) {
      super(reactContext);
    }

    protected @Nullable String mFinalUrl;

    public void setFinalUrl(String url) {
        mFinalUrl = url;
    }

    public String getFinalUrl() {
        return mFinalUrl;
    }
  }

  ...

  @ReactProp(name = "finalUrl")
  public void setFinalUrl(WebView view, String url) {
    ((CustomWebView) view).setFinalUrl(url);
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
class CustomWebViewManager : RNCWebViewManager() {
    protected inner class CustomWebView(
        reactContext: ThemedReactContext?,
        var finalUrl: String? = null
    ) : RNCWebView(reactContext)

    @ReactProp(name = "finalUrl")
    fun setFinalUrl(view: WebView, url: String?) {
        (view as CustomWebView).finalUrl = url
    }
}
```

</TabItem>
</Tabs>

### 添加新事件

對於事件處理，首先需要創建事件子類。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
// NavigationCompletedEvent.java
public class NavigationCompletedEvent extends Event<NavigationCompletedEvent> {
  private WritableMap mParams;

  public NavigationCompletedEvent(int viewTag, WritableMap params) {
    super(viewTag);
    this.mParams = params;
  }

  @Override
  public String getEventName() {
    return "navigationCompleted";
  }

  @Override
  public void dispatch(RCTEventEmitter rctEventEmitter) {
    init(getViewTag());
    rctEventEmitter.receiveEvent(getViewTag(), getEventName(), mParams);
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
// NavigationCompletedEvent.kt
class NavigationCompletedEvent(viewTag: Int, val params: WritableMap) :
    Event<NavigationCompletedEvent>(viewTag) {
    override fun getEventName(): String = "navigationCompleted"

    override fun dispatch(rctEventEmitter: RCTEventEmitter) {
        init(viewTag)
        rctEventEmitter.receiveEvent(viewTag, eventName, params)
    }
}
```

</TabItem>
</Tabs>

可在 WebView 客戶端中觸發事件。若事件基於現有處理程序，可直接掛載現有處理邏輯。

建議參考 React Native WebView 代碼庫中的 [RNCWebViewManagerImpl.kt](https://github.com/react-native-webview/react-native-webview/blob/master/android/src/main/java/com/reactnativecommunity/webview/RNCWebViewManagerImpl.kt)，了解可用處理程序及其實現方式。可透過擴展這些方法來提供額外功能。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
public class NavigationCompletedEvent extends Event<NavigationCompletedEvent> {
  private WritableMap mParams;

  public NavigationCompletedEvent(int viewTag, WritableMap params) {
    super(viewTag);
    this.mParams = params;
  }

  @Override
  public String getEventName() {
    return "navigationCompleted";
  }

  @Override
  public void dispatch(RCTEventEmitter rctEventEmitter) {
    init(getViewTag());
    rctEventEmitter.receiveEvent(getViewTag(), getEventName(), mParams);
  }
}

// CustomWebViewManager.java
protected static class CustomWebViewClient extends RNCWebViewClient {
  @Override
  public boolean shouldOverrideUrlLoading(WebView view, String url) {
    boolean shouldOverride = super.shouldOverrideUrlLoading(view, url);
    String finalUrl = ((CustomWebView) view).getFinalUrl();

    if (!shouldOverride && url != null && finalUrl != null && new String(url).equals(finalUrl)) {
      final WritableMap params = Arguments.createMap();
      dispatchEvent(view, new NavigationCompletedEvent(view.getId(), params));
    }

    return shouldOverride;
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
class NavigationCompletedEvent(viewTag: Int, val params: WritableMap) :
    Event<NavigationCompletedEvent>(viewTag) {

    override fun getEventName(): String = "navigationCompleted"

    override fun dispatch(rctEventEmitter: RCTEventEmitter) {
        init(viewTag)
        rctEventEmitter.receiveEvent(viewTag, eventName, params)
    }
}

// CustomWebViewManager.kt

protected class CustomWebViewClient : RNCWebViewClient() {
    override fun shouldOverrideUrlLoading(view: WebView, url: String?): Boolean {
        val shouldOverride: Boolean = super.shouldOverrideUrlLoading(view, url)
        val finalUrl: String? = (view as CustomWebView).finalUrl
        if (!shouldOverride && url != null && finalUrl != null && url == finalUrl) {
            val params: WritableMap = Arguments.createMap()
            dispatchEvent(view, NavigationCompletedEvent(view.getId(), params))
        }
        return shouldOverride
    }
}
```

</TabItem>
</Tabs>

最後需透過 `getExportedCustomDirectEventTypeConstants` 在 `CustomWebViewManager` 中公開事件。請注意當前默認實現返回 `null`，但未來可能變更。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="java">

```java
public class CustomWebViewManager extends RNCWebViewManager {
  ...

  @Override
  public @Nullable
  Map getExportedCustomDirectEventTypeConstants() {
    Map<String, Object> export = super.getExportedCustomDirectEventTypeConstants();
    if (export == null) {
      export = MapBuilder.newHashMap();
    }
    export.put("navigationCompleted", MapBuilder.of("registrationName", "onNavigationCompleted"));
    return export;
  }
}
```

</TabItem>
<TabItem value="kotlin">

```kotlin
class CustomWebViewManager : RNCWebViewManager() {
    override fun getExportedCustomDirectEventTypeConstants(): MutableMap<Any?, Any?>? {
        val superTypeConstants = super.getExportedCustomDirectEventTypeConstants()
        val export = superTypeConstants ?: MapBuilder.newHashMap<Any, Any?>()
        export["navigationCompleted"] = MapBuilder.of("registrationName", "onNavigationCompleted")
        return export
    }
}
```

</TabItem>
</Tabs>

## JavaScript 接口

使用自定義 WebView 時需創建對應類，該類必須：

- 導出 `WebView.propTypes` 中的所有屬性類型
- 返回的 `WebView` 組件需設置 `nativeConfig.component` 屬性指向您的原生組件（見下文）

與常規自定義組件相同，需使用 `requireNativeComponent` 獲取原生組件，但需傳入第三個參數 `WebView.extraNativeComponentConfig`。此參數包含僅原生代碼需要的屬性類型。

```jsx
import React, {Component, PropTypes} from 'react';
import {WebView, requireNativeComponent} from 'react-native';

export default class CustomWebView extends Component {
  static propTypes = WebView.propTypes;

  render() {
    return (
      <WebView
        {...this.props}
        nativeConfig={{component: RCTCustomWebView}}
      />
    );
  }
}

const RCTCustomWebView = requireNativeComponent(
  'RCTCustomWebView',
  CustomWebView,
  WebView.extraNativeComponentConfig,
);
```

若要為原生組件添加自定義屬性，可在 WebView 上使用 `nativeConfig.props` 進行配置。

對於事件處理，事件處理器必須始終設定為一個函數。這意味著直接從 `this.props` 使用事件處理器是不安全的，因為使用者可能沒有提供。標準的做法是在你的類別中創建一個事件處理器，然後在 `this.props` 中提供的事件處理器存在時調用它。

如果不確定如何從 JavaScript 端實現某些功能，可以參考 React Native WebView 原始碼中的 [WebView.android.js](https://github.com/react-native-webview/react-native-webview/blob/master/src/WebView.android.tsx)。

```jsx
export default class CustomWebView extends Component {
  static propTypes = {
    ...WebView.propTypes,
    finalUrl: PropTypes.string,
    onNavigationCompleted: PropTypes.func,
  };

  static defaultProps = {
    finalUrl: 'about:blank',
  };

  _onNavigationCompleted = event => {
    const {onNavigationCompleted} = this.props;
    onNavigationCompleted && onNavigationCompleted(event);
  };

  render() {
    return (
      <WebView
        {...this.props}
        nativeConfig={{
          component: RCTCustomWebView,
          props: {
            finalUrl: this.props.finalUrl,
            onNavigationCompleted: this._onNavigationCompleted,
          },
        }}
      />
    );
  }
}
```

與常規的原生組件類似，你必須在組件中提供所有的屬性類型，以便將它們轉發到原生組件。然而，如果你有一些僅在組件內部使用的屬性類型，可以將它們添加到前面提到的第三個參數的 `nativeOnly` 屬性中。對於事件處理器，你必須使用值 `true` 而不是常規的屬性類型。

例如，如果你想添加一個名為 `onScrollToBottom` 的內部事件處理器，你可以這樣使用：

```jsx
const RCTCustomWebView = requireNativeComponent(
  'RCTCustomWebView',
  CustomWebView,
  {
    ...WebView.extraNativeComponentConfig,
    nativeOnly: {
      ...WebView.extraNativeComponentConfig.nativeOnly,
      onScrollToBottom: true,
    },
  },
);
```