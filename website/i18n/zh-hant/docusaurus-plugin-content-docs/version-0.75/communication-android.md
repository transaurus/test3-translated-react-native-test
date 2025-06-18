---
id: communication-android
title: Communication between native and React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在[整合現有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](native-components-android)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當我們混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通橋樑。其他指南已提及部分實現方法，本文將統整現有技術方案。

## 簡介

React Native靈感源自React，因此資訊流的基本概念相似。React中的資料流是單向的——我們維護元件層級結構，每個元件僅依賴其父元件與自身內部狀態。透過屬性(properties)實現：資料以自上而下(top-down)的方式從父元件傳遞給子元件。若祖先元件需依賴後代元件的狀態，則應向下傳遞回調函數供後代更新祖先狀態。

此概念同樣適用於React Native。只要在框架內構建應用程式，我們就能透過屬性和回調驅動應用。但當混合使用React Native與原生元件時，就需要特定的跨語言機制來實現資訊傳遞。

## 屬性傳遞

屬性是最直接的跨元件通訊方式，因此我們需要實現雙向傳遞：

### 從原生端傳遞屬性至React Native

可透過在主活動(Main Activity)中自訂`ReactActivityDelegate`實現，覆寫其`getLaunchOptions`方法返回包含目標屬性的`Bundle`物件。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>

<TabItem value="java">

```java
public class MainActivity extends ReactActivity {
  @Override
  protected ReactActivityDelegate createReactActivityDelegate() {
    return new ReactActivityDelegate(this, getMainComponentName()) {
      @Override
      protected Bundle getLaunchOptions() {
        Bundle initialProperties = new Bundle();
        ArrayList<String> imageList = new ArrayList<String>(Arrays.asList(
                "https://dummyimage.com/600x400/ffffff/000000.png",
                "https://dummyimage.com/600x400/000000/ffffff.png"
        ));
        initialProperties.putStringArrayList("images", imageList);
        return initialProperties;
      }
    };
  }
}
```

</TabItem>

<TabItem value="kotlin">

```kotlin
class MainActivity : ReactActivity() {
    override fun createReactActivityDelegate(): ReactActivityDelegate {
        return object : ReactActivityDelegate(this, mainComponentName) {
            override fun getLaunchOptions(): Bundle {
                val imageList = arrayListOf("https://dummyimage.com/600x400/ffffff/000000.png", "https://dummyimage.com/600x400/000000/ffffff.png")
                val initialProperties = Bundle().apply { putStringArrayList("images", imageList) }
                return initialProperties
            }
        }
    }
}
```

</TabItem>
</Tabs>

```tsx
import React from 'react';
import {View, Image} from 'react-native';

export default class ImageBrowserApp extends React.Component {
  renderImage(imgURI) {
    return <Image source={{uri: imgURI}} />;
  }
  render() {
    return <View>{this.props.images.map(this.renderImage)}</View>;
  }
}
```

`ReactRootView`提供可讀寫屬性`appProperties`。設定後，React Native應用會以新屬性重新渲染，但僅在屬性值實際變更時觸發更新。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>

<TabItem value="java">

```java
Bundle updatedProps = mReactRootView.getAppProperties();
ArrayList<String> imageList = new ArrayList<String>(Arrays.asList(
        "https://dummyimage.com/600x400/ff0000/000000.png",
        "https://dummyimage.com/600x400/ffffff/ff0000.png"
));
updatedProps.putStringArrayList("images", imageList);

mReactRootView.setAppProperties(updatedProps);
```

</TabItem>

<TabItem value="kotlin">

```kotlin
var updatedProps: Bundle = reactRootView.getAppProperties()
var imageList = arrayListOf("https://dummyimage.com/600x400/ff0000/000000.png", "https://dummyimage.com/600x400/ffffff/ff0000.png")
```

</TabItem>

</Tabs>

屬性可隨時更新，但必須在主執行緒進行操作。讀取操作則可在任意執行緒執行。

目前無法局部更新屬性，建議自行封裝處理邏輯。

> **_注意：_** 當前頂層RN元件的JS函數`componentWillUpdateProps`在屬性更新後不會被呼叫，但可透過`componentDidMount`函數存取新屬性。

### 從React Native傳遞屬性至原生端

原生元件屬性的暴露方法詳見[此文](native-components-android#3-expose-view-property-setters-using-reactprop-or-reactpropgroup-annotation)。簡言之，需透過`@ReactProp`註解標記setter方法暴露屬性，即可在React Native中像普通RN元件般使用。

### 屬性機制的限制

跨語言屬性的主要缺陷在於不支援回調機制，無法實現自下而上(bottom-up)的資料綁定。例如當需要透過JS操作移除原生父視圖中的RN子視圖時，純屬性機制無法實現此逆向通訊。

雖然存在跨語言回調的變體方案([參見此處](native-modules-android#callbacks))，但這些回調並非萬能解方。核心問題在於它們不適合作為屬性傳遞，而是用於從JS觸發原生操作後，在JS端處理操作結果。

## 其他跨語言互動方式（事件與原生模組）

As stated in the previous chapter, using properties comes with some limitations. Sometimes properties are not enough to drive the logic of our app and we need a solution that gives more flexibility. This chapter covers other communication techniques available in React Native. They can be used for internal communication (between JS and native layers in RN) as well as for external communication (between RN and the 'pure native' part of your app).

React Native enables you to perform cross-language function calls. You can execute custom native code from JS and vice versa. Unfortunately, depending on the side we are working on, we achieve the same goal in different ways. For native - we use events mechanism to schedule an execution of a handler function in JS, while for React Native we directly call methods exported by native modules.

### Calling React Native functions from native (events)

Events are described in detail in [this article](native-components-android#events). Note that using events gives us no guarantees about execution time, as the event is handled on a separate thread.

Events are powerful, because they allow us to change React Native components without needing a reference to them. However, there are some pitfalls that you can fall into while using them:

- As events can be sent from anywhere, they can introduce spaghetti-style dependencies into your project.
- Events share namespace, which means that you may encounter some name collisions. Collisions will not be detected statically, which makes them hard to debug.
- If you use several instances of the same React Native component and you want to distinguish them from the perspective of your event, you'll likely need to introduce identifiers and pass them along with events (you can use the native view's `reactTag` as an identifier).

### Calling native functions from React Native (native modules)

Native modules are Java/Kotlin classes that are available in JS. Typically one instance of each module is created per JS bridge. They can export arbitrary functions and constants to React Native. They have been covered in detail in [this article](native-modules-android).

> **_Warning_**: All native modules share the same namespace. Watch out for name collisions when creating new ones.