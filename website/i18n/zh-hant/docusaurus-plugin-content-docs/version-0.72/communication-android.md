---
id: communication-android
title: Communication between native and React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在[整合既有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](native-components-android)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通管道。其他指南已提及部分實現方式，本文將統整現有技術方案。

## 簡介

React Native的設計靈感源自React，因此資訊流的基本概念相似。React採用單向數據流，我們維護元件的階層結構，每個元件僅依賴其父元件與自身內部狀態。這透過屬性(properties)實現：資料以自上而下的方式從父元件傳遞給子元件。若祖先元件需根據後代元件的狀態進行調整，則應向下傳遞回調函數(callback)供後代元件更新祖先狀態。

此概念同樣適用於React Native。只要完全在框架內建構應用程式，我們就能透過屬性和回調驅動應用。但當混合使用React Native與原生元件時，需要特定的跨語言機制來實現彼此間的資訊傳遞。

## 屬性傳遞

屬性是最直接的跨元件通訊方式。因此我們需要實現雙向屬性傳遞：從原生到React Native，以及從React Native到原生。

### 從原生端傳遞屬性至React Native

您可透過在主活動(main activity)中實作自訂的`ReactActivityDelegate`來傳遞屬性至React Native應用。此實作需覆寫`getLaunchOptions`方法，返回包含所需屬性的`Bundle`物件。

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

`ReactRootView`提供可讀寫屬性`appProperties`。設定此屬性後，React Native應用會以新屬性重新渲染。僅當新屬性與先前值不同時才會觸發更新。

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

隨時更新屬性皆可，但更新操作必須在主執行緒進行。讀取操作則可在任何執行緒執行。

目前無法僅更新部分屬性，建議自行建立封裝層處理此需求。

> **_注意：_** 目前頂層RN元件的JS函數`componentWillUpdateProps`在屬性更新後不會被呼叫。但您仍可透過`componentDidMount`函數存取新屬性。

### 從React Native傳遞屬性至原生端

[此文章](native-components-android#3-expose-view-property-setters-using-reactprop-or-reactpropgroup-annotation)詳細說明原生元件屬性的暴露方式。簡言之，需透過`@ReactProp`註解標記setter方法來暴露屬性至JavaScript端，之後即可像操作普通React Native元件般使用這些屬性。

### 屬性機制的限制

跨語言屬性的主要缺憾在於不支援回調機制，這使得我們無法實現自下而上的數據綁定。例如當您需要透過JS操作移除原生父視圖中的小型RN視圖時，純屬性機制無法達成此需求，因為資訊需逆向傳遞。

雖然存在跨語言回調的變體方案（[參見此處說明](native-modules-android#callbacks)），但這些回調未必符合所有需求。主要問題在於它們並非設計用來作為屬性傳遞，而是提供從JS觸發原生操作後，在JS端處理結果的機制。

## 其他跨語言互動方式（事件與原生模組）

如前一章所述，使用屬性存在一些限制。有時屬性不足以驅動應用程序的邏輯，我們需要一個提供更大靈活性的解決方案。本章涵蓋了 React Native 中可用的其他通信技術。它們既可用於內部通信（RN 中 JS 和原生層之間的通信），也可用於外部通信（RN 與應用程序「純原生」部分之間的通信）。

React Native 允許您執行跨語言函數調用。您可以從 JS 執行自定義原生代碼，反之亦然。遺憾的是，根據我們所處的端點，我們以不同的方式實現相同的目標。對於原生端，我們使用事件機制來安排在 JS 中執行處理函數，而對於 React Native 端，我們直接調用原生模塊導出的方法。

### 從原生端調用 React Native 函數（事件）

事件在[這篇文章](native-components-android#events)中有詳細描述。請注意，使用事件無法保證執行時間，因為事件是在單獨的線程上處理的。

事件非常強大，因為它們允許我們無需引用 React Native 組件即可對其進行修改。然而，在使用事件時可能會遇到一些陷阱：

- 由於事件可以從任何地方發送，它們可能會在項目中引入意大利麵條式的依賴關係。
- 事件共享命名空間，這意味著您可能會遇到名稱衝突。衝突不會被靜態檢測，因此難以調試。
- 如果您使用多個相同 React Native 組件的實例，並且希望從事件的角度區分它們，您可能需要引入標識符並與事件一起傳遞（可以使用原生視圖的 `reactTag` 作為標識符）。

### 從 React Native 調用原生函數（原生模塊）

原生模塊是可在 JS 中使用的 Java/Kotlin 類。通常每個 JS 橋會創建每個模塊的一個實例。它們可以向 React Native 導出任意函數和常量。[這篇文章](native-modules-android)對此有詳細介紹。

> **_警告_**：所有原生模塊共享相同的命名空間。在創建新模塊時請注意名稱衝突。