---
id: react-18-and-react-native
title: React 18 & React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';
import NewArchitectureWarning from './\_markdown-new-architecture-warning.mdx';

<NewArchitectureWarning/>

本頁面說明如何透過 React Native 的新架構來使用 React 18。

> **簡而言之：** 第一個相容於 React 18 的 React Native 版本是 **0.69.0**。若要使用 React 18 的新功能，包括自動批次處理、`startTransition` 和 `useDeferredValue`，您必須將 React Native 應用程式遷移至新架構。

## React 18 與 React Native 新架構

React 18 引入了[多項新功能](https://reactjs.org/blog/2022/03/29/react-v18.html)，包括：

- 自動批次處理
- 新的嚴格模式行為
- 新的 Hook（`useId`、`useSyncExternalStore`）

同時也包含新的並行功能：

- `startTransition`
- `useTransition`
- `useDeferredValue`
- 完整的 Suspense 支援

React 18 的並行功能是建立在新的並行渲染引擎之上。並行渲染是一種新的幕後機制，讓 React 能夠同時準備多個版本的 UI。

基於舊架構的 React Native 舊版本**無法**支援並行渲染或並行功能。這是因為舊架構依賴於變更原生樹狀結構，這不允許 React 同時準備多個版本的樹狀結構。

幸運的是，新架構從底層開始設計時就考慮到了並行渲染，並且完全相容於 React 18。這意味著，若要將 React Native 應用程式升級至 React 18，您的應用程式需要遷移至 React Native 的新架構，包括 Fabric 原生元件和 Turbo 原生模組。

## 預設啟用 React 18

從 React Native 0.69 開始，當您啟用新架構時，React 18 會**預設啟用**。

這意味著您可以在遷移後立即使用 React 18 的新功能。由於新的並行功能是透過使用 `startTransition` 或 `Suspense` 等功能來選擇性啟用的，我們預期 React 18 能夠在遷移至新架構或建立啟用新架構的新應用程式時，以最少的變更即可開箱即用。

然而，如果您遇到任何問題，我們提供了選擇退出 React 18 新根節點的選項。選擇退出意味著您的應用程式將以 React 17 模式運行，且無法使用 React 18 的任何功能。

### 在 Android 上選擇退出 React 18

在 Android 上，您可以覆寫 `MainActivity` 檔案中的 `ActivityDelegate` 的 `isConcurrentRootEnabled`，以啟用或停用並行 React。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>

<TabItem value="java">

```diff
public class MainActivity extends ReactActivity {

  public static class MainActivityDelegate extends ReactActivityDelegate {
    public MainActivityDelegate(ReactActivity activity, String mainComponentName) {
      super(activity, mainComponentName);
    }

    @Override
    protected ReactRootView createRootView() {
      ReactRootView reactRootView = new ReactRootView(getContext());
      // If you opted-in for the New Architecture, we enable the Fabric Renderer.
      reactRootView.setIsFabric(BuildConfig.IS_NEW_ARCHITECTURE_ENABLED);
      return reactRootView;
    }

+   @Override
+   protected boolean isConcurrentRootEnabled() {
+     // If you opted-in for the New Architecture, we enable Concurrent Root (i.e. React 18).
+     // More on this on https://reactjs.org/blog/2022/03/29/react-v18.html
+     return BuildConfig.IS_NEW_ARCHITECTURE_ENABLED;
+   }
  }
}
```

</TabItem>

<TabItem value="kotlin">

```diff
class MainActivity : ReactActivity() {

    open class MainActivityDelegate(activity: ReactActivity?, mainComponentName: String?) : ReactActivityDelegate(activity, mainComponentName) {
        override fun createRootView(): ReactRootView = ReactRootView(context).apply {
            // If you opted-in for the New Architecture, we enable the Fabric Renderer.
            setIsFabric(BuildConfig.IS_NEW_ARCHITECTURE_ENABLED)
        }

+       // If you opted-in for the New Architecture, we enable Concurrent Root (i.e. React 18).
+       // More on this on https://reactjs.org/blog/2022/03/29/react-v18.html
+       override fun isConcurrentRootEnabled() = BuildConfig.IS_NEW_ARCHITECTURE_ENABLED
    }
}
```

</TabItem>
</Tabs>

### 在 iOS 上選擇退出 React 18

在 iOS 上，您可以在 `AppDelegate.mm` 檔案中存取 `concurrentRootEnabled` 方法。您應將返回值更改為 `false`（或 `NO`）以停用該功能。

```objc
/// This method controls whether the `concurrentRoot`feature of React18 is turned on or off.
///
/// @see: https://reactjs.org/blog/2022/03/29/react-v18.html
/// @note: This requires to be rendering on Fabric (i.e. on the New Architecture).
/// @return: `true` if the `concurrentRoot` feature is enabled. Otherwise, it returns `false`.
- (BOOL)concurrentRootEnabled
{
  // Switch this bool to turn on and off the concurrent root
  return true;
}
```

### 尚未遷移至新架構的 React Native 0.69 使用者

注意：使用 React Native 0.69 但仍處於舊架構的使用者，即使 `package.json` 檔案中安裝了 React 18，仍會使用 React 17 模式。

覆寫 `isConcurrentRootEnabled` 方法對您的應用程式不會產生任何影響。