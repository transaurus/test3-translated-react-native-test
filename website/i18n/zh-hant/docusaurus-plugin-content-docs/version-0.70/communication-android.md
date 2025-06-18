---
id: communication-android
title: Communication between native and React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在[整合現有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](native-components-android)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當我們混合使用原生和React Native元件時，最終會需要在這兩個世界之間進行溝通。其他指南中已經提到了一些實現方法。本文總結了可用的技術。

## 簡介

React Native的靈感來自React，因此資訊流的基本概念是相似的。React中的資訊流是單向的。我們維護一個元件層次結構，其中每個元件僅依賴其父元件和自身的內部狀態。我們通過屬性來實現這一點：數據以自上而下的方式從父元件傳遞給其子元件。如果祖先元件依賴其後代元件的狀態，則應向下傳遞一個回調函數，供後代元件用來更新祖先元件。

同樣的概念適用於React Native。只要我們完全在框架內構建應用程式，就可以通過屬性和回調來驅動我們的應用。但是，當我們混合使用React Native和原生元件時，需要一些特定的跨語言機制來在它們之間傳遞資訊。

## 屬性

屬性是跨元件通信最直接的方式。因此，我們需要一種方法來將屬性從原生傳遞到React Native，以及從React Native傳遞到原生。

### 從原生傳遞屬性到React Native

您可以通過在主活動中提供`ReactActivityDelegate`的自定義實現來將屬性傳遞給React Native應用。此實現應覆寫`getLaunchOptions`以返回包含所需屬性的`Bundle`。

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
                "http://foo.com/bar1.png",
                "http://foo.com/bar2.png"
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
                val imageList = arrayListOf("http://foo.com/bar1.png", "http://foo.com/bar2.png")
                val initialProperties = Bundle().apply { putStringArrayList("images", imageList) }
                return initialProperties
            }
        }
    }
}
```

</TabItem>
</Tabs>

```jsx
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

`ReactRootView`提供了一個可讀寫的屬性`appProperties`。設置`appProperties`後，React Native應用會使用新屬性重新渲染。只有在新的更新屬性與之前的屬性不同時才會執行更新。

<Tabs groupId="android-language" queryString defaultValue={constants.defaultAndroidLanguage} values={constants.androidLanguages}>

<TabItem value="java">

```java
Bundle updatedProps = mReactRootView.getAppProperties();
ArrayList<String> imageList = new ArrayList<String>(Arrays.asList(
        "http://foo.com/bar3.png",
        "http://foo.com/bar4.png"
));
updatedProps.putStringArrayList("images", imageList);

mReactRootView.setAppProperties(updatedProps);
```

</TabItem>

<TabItem value="kotlin">

```kotlin
var updatedProps: Bundle = reactRootView.getAppProperties()
var imageList = arrayListOf("http://foo.com/bar3.png", "http://foo.com/bar4.png")
```

</TabItem>

</Tabs>

隨時更新屬性是可以的。但是，更新必須在主線程上執行。您可以在任何線程上使用getter。

目前無法一次只更新部分屬性。我們建議您將其構建到自己的包裝器中。

> **_注意:_** 目前，頂層RN元件的JS函數`componentWillUpdateProps`在屬性更新後不會被調用。但是，您可以在`componentDidMount`函數中訪問新屬性。

### 從React Native傳遞屬性到原生

[這篇文章](native-components-android#3-expose-view-property-setters-using-reactprop-or-reactpropgroup-annotation)詳細介紹了原生元件屬性的暴露問題。簡而言之，要在JavaScript中反映的屬性需要作為使用`@ReactProp`註解標記的setter方法暴露，然後在React Native中使用它們，就像該元件是一個普通的React Native元件一樣。

### 屬性的限制

跨語言屬性的主要缺點是它們不支持回調，而回調可以讓我們處理自下而上的數據綁定。想像您有一個小的RN視圖，您希望由於JS操作而從原生父視圖中移除它。使用屬性無法實現這一點，因為資訊需要自下而上傳遞。

儘管我們有一種跨語言回調的變體（[在此描述](native-modules-android#callbacks)），但這些回調並不總是我們需要的。主要問題是它們不適合作為屬性傳遞。相反，這種機制允許我們從JS觸發原生操作，並在JS中處理該操作的結果。

## 其他跨語言交互方式（事件和原生模組）

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