---
id: native-components-android
title: Android Native UI Components
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';
import NativeDeprecated from '@site/versioned_docs/version-0.76/the-new-architecture/\_markdown_native_deprecation.mdx'

<NativeDeprecated />

現有大量原生UI元件可供最新應用程式使用——其中部分屬於平台內建，其他則來自第三方函式庫，甚至可能是您過往專案中自行開發的元件。React Native已封裝了部分關鍵平台元件如`ScrollView`和`TextInput`，但並未涵蓋所有元件，特別是您過去為其他應用程式開發的自訂元件。幸運的是，我們可以將這些現有元件封裝起來，無縫整合至您的React Native應用中。

與原生模組指南類似，這同樣屬於進階指南，假設您已具備Android SDK開發基礎。本指南將示範如何建構原生UI元件，透過實作核心React Native函式庫中現有`ImageView`元件的部分功能來逐步說明。

:::info
您亦可透過單一指令設定包含原生元件的本地函式庫。詳情請參閱[本地函式庫設定指南](local-library-setup)。
:::

## ImageView範例

本範例將逐步說明實作需求，以允許在JavaScript中使用ImageView元件。

原生視圖透過繼承`ViewManager`或更常用的`SimpleViewManager`來建立與操作。此情境下使用`SimpleViewManager`較為便利，因其已實作常見屬性如背景色、透明度與Flexbox佈局。

這些子類別本質上是單例模式——橋接器僅會建立每個子類別的一個實例。它們將原生視圖傳送至`NativeViewHierarchyManager`，該管理器會反過來委派它們設定與更新視圖屬性。`ViewManagers`通常也作為視圖的委派，透過橋接器將事件回傳至JavaScript。

傳送視圖的步驟：

1. 建立ViewManager子類別
2. 實作`createViewInstance`方法
3. 使用`@ReactProp`（或`@ReactPropGroup`）註解公開視圖屬性設置器
4. 在應用程式套件的`createViewManagers`中註冊管理器
5. 實作JavaScript模組

### 1. 建立`ViewManager`子類別

本範例中，我們建立繼承`ReactImageView`類型之`SimpleViewManager`的視圖管理器類別`ReactImageManager`。`ReactImageView`是由管理器管理的物件類型，此即自訂原生視圖。`getName`返回的名稱用於從JavaScript引用原生視圖類型。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
class ReactImageManager(
    private val callerContext: ReactApplicationContext
) : SimpleViewManager<ReactImageView>() {

  override fun getName() = REACT_CLASS

  companion object {
    const val REACT_CLASS = "RCTImageView"
  }
}
```

</TabItem>
<TabItem value="java">

```java
public class ReactImageManager extends SimpleViewManager<ReactImageView> {

  public static final String REACT_CLASS = "RCTImageView";
  ReactApplicationContext mCallerContext;

  public ReactImageManager(ReactApplicationContext reactContext) {
    mCallerContext = reactContext;
  }

  @Override
  public String getName() {
    return REACT_CLASS;
  }
}
```

</TabItem>
</Tabs>

### 2. 實作`createViewInstance`方法

視圖在`createViewInstance`方法中建立，該視圖應以預設狀態初始化自身，所有屬性將透過後續呼叫`updateView`來設定。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
  override fun createViewInstance(context: ThemedReactContext) =
      ReactImageView(context, Fresco.newDraweeControllerBuilder(), null, callerContext)
```

</TabItem>
<TabItem value="java">

```java
  @Override
  public ReactImageView createViewInstance(ThemedReactContext context) {
    return new ReactImageView(context, Fresco.newDraweeControllerBuilder(), null, mCallerContext);
  }
```

</TabItem>
</Tabs>

### 3. 使用`@ReactProp`（或`@ReactPropGroup`）註解公開視圖屬性設置器

需在JavaScript中反映的屬性，必須透過以`@ReactProp`（或`@ReactPropGroup`）註解的設置器方法公開。設置器方法應將要更新的視圖（當前視圖類型）作為第一個參數，屬性值作為第二個參數。設置器應為公開方法且不返回值（即Java中返回類型應為`void`，Kotlin中為`Unit`）。傳送至JS的屬性類型會根據設置器值參數的類型自動判定。目前支援以下值類型（Java中）：`boolean`、`int`、`float`、`double`、`String`、`Boolean`、`Integer`、`ReadableArray`、`ReadableMap`。Kotlin中對應類型為`Boolean`、`Int`、`Float`、`Double`、`String`、`ReadableArray`、`ReadableMap`。

註解`@ReactProp`有一個必要參數`name`（類型為`String`）。分配給設置器方法的`@ReactProp`註解名稱，用於在JS端引用該屬性。

除了 `name` 之外，`@ReactProp` 註解還可以接受以下可選參數：`defaultBoolean`、`defaultInt`、`defaultFloat`。這些參數應為對應的類型（在 Java 中分別為 `boolean`、`int`、`float`，在 Kotlin 中為 `Boolean`、`Int`、`Float`），當 setter 方法所引用的屬性從組件中移除時，提供的值將傳遞給 setter 方法。請注意，「預設」值僅適用於基本類型，若 setter 的類型為複雜類型，當對應屬性被移除時，將提供 `null` 作為預設值。

使用 `@ReactPropGroup` 註解的方法在 setter 聲明要求上與 `@ReactProp` 不同，請參考 `@ReactPropGroup` 註解類別的文檔以獲取更多資訊。**重要提示！** 在 ReactJS 中更新屬性值將觸發 setter 方法的調用。請注意，更新組件的方式之一是移除先前設置的屬性。在這種情況下，setter 方法也會被調用以通知視圖管理器屬性已變更。此時將提供「預設」值（對於基本類型，可以使用 `@ReactProp` 註解的 `defaultBoolean`、`defaultFloat` 等參數指定「預設」值；對於複雜類型，setter 將被調用且值設為 `null`）。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
  @ReactProp(name = "src")
  fun setSrc(view: ReactImageView, sources: ReadableArray?) {
    view.setSource(sources)
  }

  @ReactProp(name = "borderRadius", defaultFloat = 0f)
  override fun setBorderRadius(view: ReactImageView, borderRadius: Float) {
    view.setBorderRadius(borderRadius)
  }

  @ReactProp(name = ViewProps.RESIZE_MODE)
  fun setResizeMode(view: ReactImageView, resizeMode: String?) {
    view.setScaleType(ImageResizeMode.toScaleType(resizeMode))
  }
```

</TabItem>
<TabItem value="java">

```java
  @ReactProp(name = "src")
  public void setSrc(ReactImageView view, @Nullable ReadableArray sources) {
    view.setSource(sources);
  }

  @ReactProp(name = "borderRadius", defaultFloat = 0f)
  public void setBorderRadius(ReactImageView view, float borderRadius) {
    view.setBorderRadius(borderRadius);
  }

  @ReactProp(name = ViewProps.RESIZE_MODE)
  public void setResizeMode(ReactImageView view, @Nullable String resizeMode) {
    view.setScaleType(ImageResizeMode.toScaleType(resizeMode));
  }
```

</TabItem>
</Tabs>

### 4. 註冊 `ViewManager`

最後一步是將 ViewManager 註冊到應用程序中，這與 [原生模組](native-modules-android.md) 的方式類似，通過應用程序包成員函數 `createViewManagers` 進行。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
  override fun createViewManagers(
      reactContext: ReactApplicationContext
  ) = listOf(ReactImageManager(reactContext))
```

</TabItem>
<TabItem value="java">

```java
  @Override
  public List<ViewManager> createViewManagers(
                            ReactApplicationContext reactContext) {
    return Arrays.<ViewManager>asList(
      new ReactImageManager(reactContext)
    );
  }
```

</TabItem>
</Tabs>

### 5. 實作 JavaScript 模組

最後一步是創建 JavaScript 模組，該模組定義了 Java/Kotlin 與 JavaScript 之間的接口層，供新視圖的使用者使用。建議在此模組中記錄組件接口（例如使用 TypeScript、Flow 或普通註釋）。

```tsx title="ImageView.tsx"
import {requireNativeComponent} from 'react-native';

/**
 * Composes `View`.
 *
 * - src: Array<{url: string}>
 * - borderRadius: number
 * - resizeMode: 'cover' | 'contain' | 'stretch'
 */
export default requireNativeComponent('RCTImageView');
```

`requireNativeComponent` 函數接受原生視圖的名稱。請注意，如果您的組件需要進行更複雜的操作（例如自定義事件處理），則應將原生組件包裝在另一個 React 組件中。這在下面的 `MyCustomView` 示例中有所說明。

## 事件

現在我們知道如何暴露可以從 JS 自由控制的原生視圖組件，但如何處理來自用戶的事件，例如縮放或平移？當原生事件發生時，原生代碼應向視圖的 JavaScript 表示發出事件，兩個視圖通過 `getId()` 方法返回的值進行連結。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
class MyCustomView(context: Context) : View(context) {
  ...
  fun onReceiveNativeEvent() {
    val event = Arguments.createMap().apply {
      putString("message", "MyMessage")
    }
    val reactContext = context as ReactContext
    reactContext
        .getJSModule(RCTEventEmitter::class.java)
        .receiveEvent(id, "topChange", event)
  }
}
```

</TabItem>
<TabItem value="java">

```java
class MyCustomView extends View {
   ...
   public void onReceiveNativeEvent() {
      WritableMap event = Arguments.createMap();
      event.putString("message", "MyMessage");
      ReactContext reactContext = (ReactContext)getContext();
      reactContext
          .getJSModule(RCTEventEmitter.class)
          .receiveEvent(getId(), "topChange", event);
    }
}
```

</TabItem>
</Tabs>

要將 `topChange` 事件名稱映射到 JavaScript 中的 `onChange` 回調屬性，請在 `ViewManager` 中覆寫 `getExportedCustomBubblingEventTypeConstants` 方法來註冊它：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin
class ReactImageManager : SimpleViewManager<MyCustomView>() {
  ...
  override fun getExportedCustomBubblingEventTypeConstants(): Map<String, Any> {
    return mapOf(
      "topChange" to mapOf(
        "phasedRegistrationNames" to mapOf(
          "bubbled" to "onChange"
        )
      )
    )
  }
}
```

</TabItem>
<TabItem value="java">

```java
public class ReactImageManager extends SimpleViewManager<MyCustomView> {
    ...
    public Map getExportedCustomBubblingEventTypeConstants() {
        return MapBuilder.builder().put(
            "topChange",
            MapBuilder.of(
                "phasedRegistrationNames",
                MapBuilder.of("bubbled", "onChange")
            )
        ).build();
    }
}
```

</TabItem>
</Tabs>

此回調會接收原始事件，我們通常在包裝組件中處理它以提供更簡單的 API：

```tsx {8-11,13-17} title="MyCustomView.tsx"
import {useCallback} from 'react';
import {requireNativeComponent} from 'react-native';

const RCTMyCustomView = requireNativeComponent('RCTMyCustomView');

export default function MyCustomView(props: {
  // ...
  /**
   * Callback that is called continuously when the user is dragging the map.
   */
  onChangeMessage: (message: string) => unknown;
}) {
  const onChange = useCallback(
    event => {
      props.onChangeMessage?.(event.nativeEvent.message);
    },
    [props.onChangeMessage],
  );

  return <RCTMyCustomView {...props} onChange={props.onChange} />;
}
```

## 與 Android Fragment 的整合示例

為了將現有的原生 UI 元素整合到您的 React Native 應用中，您可能需要使用 Android Fragments 來對原生組件進行更精細的控制，而不是從 `ViewManager` 返回一個 `View`。如果您希望通過 [生命週期方法](https://developer.android.com/guide/fragments/lifecycle)（如 `onViewCreated`、`onPause`、`onResume`）添加與視圖相關的自定義邏輯，則需要這樣做。以下步驟將展示如何實現：

### 1. 創建一個示例自定義視圖

首先，創建一個繼承 `FrameLayout` 的 `CustomView` 類（此視圖的內容可以是您希望渲染的任何視圖）

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin title="CustomView.kt"
// replace with your package
package com.mypackage

import android.content.Context
import android.graphics.Color
import android.widget.FrameLayout
import android.widget.TextView

class CustomView(context: Context) : FrameLayout(context) {
  init {
    // set padding and background color
    setPadding(16,16,16,16)
    setBackgroundColor(Color.parseColor("#5FD3F3"))

    // add default text view
    addView(TextView(context).apply {
      text = "Welcome to Android Fragments with React Native."
    })
  }
}
```

</TabItem>
<TabItem value="java">

```java title="CustomView.java"
// replace with your package
package com.mypackage;

import android.content.Context;
import android.graphics.Color;
import android.widget.FrameLayout;
import android.widget.ImageView;
import android.widget.TextView;

import androidx.annotation.NonNull;

public class CustomView extends FrameLayout {
  public CustomView(@NonNull Context context) {
    super(context);
    // set padding and background color
    this.setPadding(16,16,16,16);
    this.setBackgroundColor(Color.parseColor("#5FD3F3"));

    // add default text view
    TextView text = new TextView(context);
    text.setText("Welcome to Android Fragments with React Native.");
    this.addView(text);
  }
}
```

</TabItem>
</Tabs>

### 2. 創建一個 `Fragment`

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin title="MyFragment.kt"
// replace with your package
package com.mypackage

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment

// replace with your view's import
import com.mypackage.CustomView

class MyFragment : Fragment() {
  private lateinit var customView: CustomView

  override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View {
    super.onCreateView(inflater, container, savedInstanceState)
    customView = CustomView(requireNotNull(context))
    return customView // this CustomView could be any view that you want to render
  }

  override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    // do any logic that should happen in an `onCreate` method, e.g:
    // customView.onCreate(savedInstanceState);
  }

  override fun onPause() {
    super.onPause()
    // do any logic that should happen in an `onPause` method
    // e.g.: customView.onPause();
  }

  override fun onResume() {
    super.onResume()
    // do any logic that should happen in an `onResume` method
    // e.g.: customView.onResume();
  }

  override fun onDestroy() {
    super.onDestroy()
    // do any logic that should happen in an `onDestroy` method
    // e.g.: customView.onDestroy();
  }
}
```

</TabItem>
<TabItem value="java">

```java title="MyFragment.java"
// replace with your package
package com.mypackage;

import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import androidx.fragment.app.Fragment;

// replace with your view's import
import com.mypackage.CustomView;

public class MyFragment extends Fragment {
    CustomView customView;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup parent, Bundle savedInstanceState) {
        super.onCreateView(inflater, parent, savedInstanceState);
        customView = new CustomView(this.getContext());
        return customView; // this CustomView could be any view that you want to render
    }

    @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        // do any logic that should happen in an `onCreate` method, e.g:
        // customView.onCreate(savedInstanceState);
    }

    @Override
    public void onPause() {
        super.onPause();
        // do any logic that should happen in an `onPause` method
        // e.g.: customView.onPause();
    }

    @Override
    public void onResume() {
        super.onResume();
       // do any logic that should happen in an `onResume` method
       // e.g.: customView.onResume();
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        // do any logic that should happen in an `onDestroy` method
        // e.g.: customView.onDestroy();
    }
}
```

</TabItem>
</Tabs>

### 3. 建立 `ViewManager` 子類別

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin title="MyViewManager.kt"
// replace with your package
package com.mypackage

import android.view.Choreographer
import android.view.View
import android.view.ViewGroup
import android.widget.FrameLayout
import androidx.fragment.app.FragmentActivity
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.bridge.ReadableArray
import com.facebook.react.uimanager.ThemedReactContext
import com.facebook.react.uimanager.ViewGroupManager
import com.facebook.react.uimanager.annotations.ReactPropGroup

class MyViewManager(
    private val reactContext: ReactApplicationContext
) : ViewGroupManager<FrameLayout>() {
  private var propWidth: Int? = null
  private var propHeight: Int? = null

  override fun getName() = REACT_CLASS

  /**
   * Return a FrameLayout which will later hold the Fragment
   */
  override fun createViewInstance(reactContext: ThemedReactContext) =
      FrameLayout(reactContext)

  /**
   * Map the "create" command to an integer
   */
  override fun getCommandsMap() = mapOf("create" to COMMAND_CREATE)

  /**
   * Handle "create" command (called from JS) and call createFragment method
   */
  override fun receiveCommand(
      root: FrameLayout,
      commandId: String,
      args: ReadableArray?
  ) {
    super.receiveCommand(root, commandId, args)
    val reactNativeViewId = requireNotNull(args).getInt(0)

    when (commandId.toInt()) {
      COMMAND_CREATE -> createFragment(root, reactNativeViewId)
    }
  }

  @ReactPropGroup(names = ["width", "height"], customType = "Style")
  fun setStyle(view: FrameLayout, index: Int, value: Int) {
    if (index == 0) propWidth = value
    if (index == 1) propHeight = value
  }

  /**
   * Replace your React Native view with a custom fragment
   */
  fun createFragment(root: FrameLayout, reactNativeViewId: Int) {
    val parentView = root.findViewById<ViewGroup>(reactNativeViewId)
    setupLayout(parentView)

    val myFragment = MyFragment()
    val activity = reactContext.currentActivity as FragmentActivity
    activity.supportFragmentManager
        .beginTransaction()
        .replace(reactNativeViewId, myFragment, reactNativeViewId.toString())
        .commit()
  }

  fun setupLayout(view: View) {
    Choreographer.getInstance().postFrameCallback(object: Choreographer.FrameCallback {
      override fun doFrame(frameTimeNanos: Long) {
        manuallyLayoutChildren(view)
        view.viewTreeObserver.dispatchOnGlobalLayout()
        Choreographer.getInstance().postFrameCallback(this)
      }
    })
  }

  /**
   * Layout all children properly
   */
  private fun manuallyLayoutChildren(view: View) {
    // propWidth and propHeight coming from react-native props
    val width = requireNotNull(propWidth)
    val height = requireNotNull(propHeight)

    view.measure(
        View.MeasureSpec.makeMeasureSpec(width, View.MeasureSpec.EXACTLY),
        View.MeasureSpec.makeMeasureSpec(height, View.MeasureSpec.EXACTLY))

    view.layout(0, 0, width, height)
  }

  companion object {
    private const val REACT_CLASS = "MyViewManager"
    private const val COMMAND_CREATE = 1
  }
}
```

</TabItem>
<TabItem value="java">

```java title="MyViewManager.java"
// replace with your package
package com.mypackage;

import android.view.Choreographer;
import android.view.View;
import android.view.ViewGroup;
import android.widget.FrameLayout;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.FragmentActivity;

import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReadableArray;
import com.facebook.react.common.MapBuilder;
import com.facebook.react.uimanager.annotations.ReactProp;
import com.facebook.react.uimanager.annotations.ReactPropGroup;
import com.facebook.react.uimanager.ViewGroupManager;
import com.facebook.react.uimanager.ThemedReactContext;

import java.util.Map;

public class MyViewManager extends ViewGroupManager<FrameLayout> {

  public static final String REACT_CLASS = "MyViewManager";
  public final int COMMAND_CREATE = 1;
  private int propWidth;
  private int propHeight;

  ReactApplicationContext reactContext;

  public MyViewManager(ReactApplicationContext reactContext) {
    this.reactContext = reactContext;
  }

  @Override
  public String getName() {
    return REACT_CLASS;
  }

  /**
   * Return a FrameLayout which will later hold the Fragment
   */
  @Override
  public FrameLayout createViewInstance(ThemedReactContext reactContext) {
    return new FrameLayout(reactContext);
  }

  /**
   * Map the "create" command to an integer
   */
  @Nullable
  @Override
  public Map<String, Integer> getCommandsMap() {
    return MapBuilder.of("create", COMMAND_CREATE);
  }

  /**
   * Handle "create" command (called from JS) and call createFragment method
   */
  @Override
  public void receiveCommand(
    @NonNull FrameLayout root,
    String commandId,
    @Nullable ReadableArray args
  ) {
    super.receiveCommand(root, commandId, args);
    int reactNativeViewId = args.getInt(0);
    int commandIdInt = Integer.parseInt(commandId);

    switch (commandIdInt) {
      case COMMAND_CREATE:
        createFragment(root, reactNativeViewId);
        break;
      default: {}
    }
  }

  @ReactPropGroup(names = {"width", "height"}, customType = "Style")
  public void setStyle(FrameLayout view, int index, Integer value) {
    if (index == 0) {
      propWidth = value;
    }

    if (index == 1) {
      propHeight = value;
    }
  }

  /**
   * Replace your React Native view with a custom fragment
   */
  public void createFragment(FrameLayout root, int reactNativeViewId) {
    ViewGroup parentView = (ViewGroup) root.findViewById(reactNativeViewId);
    setupLayout(parentView);

    final MyFragment myFragment = new MyFragment();
    FragmentActivity activity = (FragmentActivity) reactContext.getCurrentActivity();
    activity.getSupportFragmentManager()
            .beginTransaction()
            .replace(reactNativeViewId, myFragment, String.valueOf(reactNativeViewId))
            .commit();
  }

  public void setupLayout(View view) {
    Choreographer.getInstance().postFrameCallback(new Choreographer.FrameCallback() {
      @Override
      public void doFrame(long frameTimeNanos) {
        manuallyLayoutChildren(view);
        view.getViewTreeObserver().dispatchOnGlobalLayout();
        Choreographer.getInstance().postFrameCallback(this);
      }
    });
  }

  /**
   * Layout all children properly
   */
  public void manuallyLayoutChildren(View view) {
      // propWidth and propHeight coming from react-native props
      int width = propWidth;
      int height = propHeight;

      view.measure(
              View.MeasureSpec.makeMeasureSpec(width, View.MeasureSpec.EXACTLY),
              View.MeasureSpec.makeMeasureSpec(height, View.MeasureSpec.EXACTLY));

      view.layout(0, 0, width, height);
  }
}
```

</TabItem>
</Tabs>

### 4. 註冊 `ViewManager`

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin title="MyPackage.kt"
// replace with your package
package com.mypackage

import com.facebook.react.ReactPackage
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.uimanager.ViewManager

class MyPackage : ReactPackage {
  ...
  override fun createViewManagers(
      reactContext: ReactApplicationContext
  ) = listOf(MyViewManager(reactContext))
}
```

</TabItem>
<TabItem value="java">

```java title="MyPackage.java"
// replace with your package
package com.mypackage;

import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.Arrays;
import java.util.List;

public class MyPackage implements ReactPackage {

   @Override
   public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
       return Arrays.<ViewManager>asList(
            new MyViewManager(reactContext)
       );
   }

}
```

</TabItem>
</Tabs>

### 5. 註冊 `Package`

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```kotlin title="MainApplication.kt"
override fun getPackages(): List<ReactPackage> =
    PackageList(this).packages.apply {
        // Packages that cannot be autolinked yet can be added manually here, for example:
        // add(MyReactNativePackage())
        add(MyAppPackage())
    }
```

</TabItem>
<TabItem value="java">

```java title="MainApplication.java"
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
</Tabs>

### 6. 實作 JavaScript 模組

I. 首先建立自訂的 View 管理器：

```tsx title="MyViewManager.tsx"
import {requireNativeComponent} from 'react-native';

export const MyViewManager =
  requireNativeComponent('MyViewManager');
```

II. 接著實作呼叫 `create` 方法的自訂 View：

```tsx title="MyView.tsx"
import React, {useEffect, useRef} from 'react';
import {
  PixelRatio,
  UIManager,
  findNodeHandle,
} from 'react-native';

import {MyViewManager} from './my-view-manager';

const createFragment = viewId =>
  UIManager.dispatchViewManagerCommand(
    viewId,
    // we are calling the 'create' command
    UIManager.MyViewManager.Commands.create.toString(),
    [viewId],
  );

export const MyView = () => {
  const ref = useRef(null);

  useEffect(() => {
    const viewId = findNodeHandle(ref.current);
    createFragment(viewId);
  }, []);

  return (
    <MyViewManager
      style={{
        // converts dpi to px, provide desired height
        height: PixelRatio.getPixelSizeForLayoutSize(200),
        // converts dpi to px, provide desired width
        width: PixelRatio.getPixelSizeForLayoutSize(200),
      }}
      ref={ref}
    />
  );
};
```

若想透過 `@ReactProp`（或 `@ReactPropGroup`）註解來公開屬性設定器，請參閱上方的 [ImageView 範例](#imageview-example)。