---
id: integration-with-android-fragment
title: Integration with an Android Fragment
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

[與現有應用整合](https://reactnative.dev/docs/integration-with-existing-apps)指南詳細說明了如何將全螢幕的React Native應用作為**Activity**整合到現有的Android應用中。

若要在現有應用中的**Fragments**內使用React Native元件，則需要進行一些額外的設定。

### 1. 將React Native加入你的應用

按照[與現有應用整合](https://reactnative.dev/docs/integration-with-existing-apps)指南的步驟操作，直到最後，以確保你能安全地在全螢幕Activity中運行你的React Native應用。

### 2. 為React Native Fragment添加FrameLayout

在這個例子中，我們將使用`FrameLayout`來將一個React Native Fragment添加到Activity中。這種方法足夠靈活，可以適應其他佈局，如Bottom Sheets或Tab Layouts中使用React Native。

首先在你的Activity佈局（例如`res/layouts`資料夾中的`main_activity.xml`）中添加一個帶有id、寬度和高度的`<FrameLayout>`。這是用來渲染你的React Native Fragment的佈局。

```xml
<FrameLayout
    android:id="@+id/react_native_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

### 3. 讓你的宿主Activity實現`DefaultHardwareBackBtnHandler`

由於你的宿主Activity不是`ReactActivity`，你需要實現`DefaultHardwareBackBtnHandler`接口來處理返回按鈕的按下事件。這是React Native處理返回按鈕按下事件所必需的。

進入你的宿主Activity並確保它實現了`DefaultHardwareBackBtnHandler`接口：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```diff
package <your-package-here>

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
+import com.facebook.react.modules.core.DefaultHardwareBackBtnHandler

+class MainActivity : AppCompatActivity() {
+class MainActivity : AppCompatActivity(), DefaultHardwareBackBtnHandler {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main_activity)

        findViewById<Button>(R.id.sample_button).setOnClickListener {
            // Handle button click
        }
    }

+   override fun invokeDefaultOnBackPressed() {
+       super.onBackPressed()
+   }
}
```

</TabItem>
<TabItem value="java">

```diff
package <your-package-here>;

import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
+import com.facebook.react.modules.core.DefaultHardwareBackBtnHandler;

-class MainActivity extends AppCompatActivity {
+class MainActivity extends AppCompatActivity implements DefaultHardwareBackBtnHandler {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main_activity);

        findViewById(R.id.button_appcompose).setOnClickListener(button -> {
            // Handle button click
        });
    }

+   @Override
+   public void invokeDefaultOnBackPressed() {
+       super.onBackPressed();
+   }
}
```

</TabItem>
</Tabs>

### 4. 將React Native Fragment添加到FrameLayout中

最後，我們可以更新Activity，將React Native Fragment添加到FrameLayout中。在這個具體的例子中，我們假設你的Activity有一個id為`sample_button`的按鈕，點擊後會將一個React Native Fragment渲染到FrameLayout中。

按如下方式更新你的Activity的`onCreate`方法：

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>
<TabItem value="kotlin">

```diff
package <your-package-here>

import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
+import com.facebook.react.ReactFragment
import com.facebook.react.modules.core.DefaultHardwareBackBtnHandler

public class MainActivity : AppCompatActivity(), DefaultHardwareBackBtnHandler {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main_activity)

        findViewById<Button>(R.id.sample_button).setOnClickListener {
+           val reactNativeFragment = ReactFragment.Builder()
+               .setComponentName("HelloWorld")
+               .setLaunchOptions(Bundle().apply { putString("message", "my value") })
+               .build()
+           supportFragmentManager
+               .beginTransaction()
+               .add(R.id.react_native_fragment, reactNativeFragment)
+               .commit()
        }
    }

   override fun invokeDefaultOnBackPressed() {
       super.onBackPressed()
   }
}
```

</TabItem>
<TabItem value="java">

```diff
package <your-package-here>;

import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
+import com.facebook.react.ReactFragment;
import com.facebook.react.modules.core.DefaultHardwareBackBtnHandler;

public class MainActivity extends AppCompatActivity implements DefaultHardwareBackBtnHandler {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main_activity);

        findViewById(R.id.button_appcompose).setOnClickListener(button -> {
+           Bundle launchOptions = new Bundle();
+           launchOptions.putString("message", "my value");
+
+           ReactFragment fragment = new ReactFragment.Builder()
+                   .setComponentName("HelloWorld")
+                   .setLaunchOptions(launchOptions)
+                   .build();
+           getSupportFragmentManager()
+                   .beginTransaction()
+                   .add(R.id.react_native_fragment, fragment)
+                   .commit();
        });
    }

    @Override
    public void invokeDefaultOnBackPressed() {
        super.onBackPressed();
    }
}
```

</TabItem>
</Tabs>

讓我們來看看上面的代碼。

`ReactFragment.Builder()`用於創建一個新的`ReactFragment`，然後我們使用`supportFragmentManager`將該Fragment添加到`FrameLayout`中。

在builder內部，你可以自定義Fragment的創建方式：

- `setComponentName`是你想要渲染的元件名稱。它是在`index.js`中`registerComponent`方法指定的相同字符串。
- `setLaunchOptions`是一個可選方法，用於傳遞初始props給你的元件。這是可選的，如果你不使用它，可以將其移除。

### 5. 測試你的整合

確保你運行`yarn start`來啟動bundler，然後在Android Studio中運行你的Android應用。應用應該會從開發伺服器加載JavaScript/TypeScript代碼，並在你的Activity中的React Native Fragment中顯示它。

你的應用應該看起來像這樣：

![截圖](/docs/assets/EmbeddedAppAndroidFragmentVideo.gif)