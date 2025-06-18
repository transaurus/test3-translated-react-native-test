---
id: communication-android
title: Communication between native and React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在[整合現有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](native-components-android)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當我們混合使用原生和React Native元件時，最終會需要在這兩個世界之間進行溝通。其他指南中已經提到了一些實現方法。本文總結了可用的技術。

## 簡介

React Native的靈感來自React，因此資訊流的基本概念相似。React中的資訊流是單向的。我們維護一個元件層次結構，其中每個元件僅依賴於其父元件和自身的內部狀態。我們通過屬性來實現這一點：資料以自上而下的方式從父元件傳遞給其子元件。如果祖先元件依賴於其後代元件的狀態，則應向下傳遞一個回調函數，供後代元件用來更新祖先元件。

同樣的概念適用於React Native。只要我們完全在框架內構建應用程式，就可以通過屬性和回調來驅動應用程式。但是，當我們混合使用React Native和原生元件時，需要一些特定的跨語言機制來在它們之間傳遞資訊。

## 屬性

屬性是跨元件溝通最直接的方式。因此，我們需要一種方法來將屬性從原生傳遞到React Native，以及從React Native傳遞到原生。

### 從原生傳遞屬性到React Native

您可以通過在主活動中提供自定義的`ReactActivityDelegate`實現來將屬性傳遞給React Native應用程式。此實現應覆寫`getLaunchOptions`以返回包含所需屬性的`Bundle`。

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

`ReactRootView`提供了一個可讀寫的屬性`appProperties`。設置`appProperties`後，React Native應用程式會使用新屬性重新渲染。僅當新更新的屬性與之前的屬性不同時，才會執行更新。

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

隨時更新屬性是可以的。但是，更新必須在主線程上執行。您可以在任何線程上使用getter。

目前無法僅更新部分屬性。我們建議您將其構建到自己的包裝器中。

> **_注意:_** 目前，頂層RN元件的JS函數`componentWillUpdateProps`在屬性更新後不會被調用。但是，您可以在`componentDidMount`函數中訪問新屬性。

### 從React Native傳遞屬性到原生

[本文](native-components-android#3-expose-view-property-setters-using-reactprop-or-reactpropgroup-annotation)詳細介紹了原生元件屬性的暴露問題。簡而言之，要在JavaScript中反映的屬性需要作為使用`@ReactProp`註解標記的setter方法暴露，然後在React Native中使用這些屬性，就像該元件是普通的React Native元件一樣。

### 屬性的限制

跨語言屬性的主要缺點是它們不支持回調函數，而回調函數可以讓我們處理自下而上的資料綁定。想像您有一個小的RN視圖，您希望由於JS操作而將其從原生父視圖中移除。使用屬性無法實現這一點，因為資訊需要自下而上傳遞。

儘管我們有一種跨語言回調的變體（[此處描述](native-modules-android#callbacks)），但這些回調並不總是我們需要的。主要問題是它們不適合作為屬性傳遞。相反，這種機制允許我們從JS觸發原生操作，並在JS中處理該操作的結果。

## 其他跨語言互動方式（事件和原生模組）

如前一章所述，使用屬性存在一些限制。有時屬性不足以驅動應用的邏輯，我們需要更靈活的解決方案。本章涵蓋 React Native 中其他可用的通訊技術，這些技術既可用於內部通訊（RN 中 JS 與原生層之間），也可用於外部通訊（RN 與應用中「純原生」部分之間）。

React Native 允許您執行跨語言函數調用。您可以從 JS 執行自訂原生代碼，反之亦然。遺憾的是，根據我們所處的端別，實現相同目標的方式會有所不同。對於原生端，我們使用事件機制來安排在 JS 中執行處理函數；而對於 React Native 端，我們直接調用原生模組導出的方法。

### 從原生端調用 React Native 函數（事件）

事件在[這篇文章](native-components-android#events)中有詳細說明。請注意，使用事件無法保證執行時間，因為事件是在單獨的線程上處理的。

事件功能強大，因為它們允許我們無需持有 React Native 元件的引用即可修改這些元件。然而，使用時可能會遇到一些陷阱：

- 由於事件可以從任何地方發送，可能會在專案中引入義大利麵條式的依賴關係。
- 事件共享命名空間，這意味著可能會遇到名稱衝突。這些衝突無法靜態檢測，因此難以調試。
- 如果使用多個相同 React Native 元件的實例，並希望從事件的角度區分它們，可能需要引入標識符並隨事件傳遞（可以使用原生視圖的 `reactTag` 作為標識符）。

### 從 React Native 調用原生函數（原生模組）

原生模組是可在 JS 中使用的 Java/Kotlin 類別。通常每個模組在 JS 橋接器中會創建一個實例。它們可以向 React Native 導出任意函數和常量，詳細說明請參閱[這篇文章](native-modules-android)。

> **_警告_**：所有原生模組共享相同的命名空間。創建新模組時請注意名稱衝突。