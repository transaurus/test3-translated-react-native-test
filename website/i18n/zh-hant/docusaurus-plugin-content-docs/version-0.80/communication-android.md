---
id: communication-android
title: Communication between native and React Native
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在[整合既有應用程式指南](integration-with-existing-apps)和[原生UI元件指南](legacy/native-components-android)中，我們學習了如何將React Native嵌入原生元件，反之亦然。當混合使用原生與React Native元件時，最終會需要在這兩個世界之間建立溝通管道。其他指南中已提及部分實現方式，本文將統整現有技術方案。

## 簡介

React Native的設計理念承襲自React，因此資訊流的基本概念相似。React中的資料流是單向的——我們維護元件層級結構，每個元件僅依賴其父元件與自身內部狀態。這透過屬性(properties)實現：資料以自上而下的方式從父元件傳遞給子元件。若祖先元件需依賴後代元件的狀態，則應向下傳遞回調函數供後代元件更新祖先狀態。

此概念同樣適用於React Native。只要完全在框架內建構應用程式，我們就能透過屬性和回調驅動應用。但當混合使用React Native與原生元件時，就需要特定的跨語言機制來實現彼此間的資訊傳遞。

## 屬性傳遞

屬性是最直接的跨元件通訊方式，因此我們需要實現原生與React Native之間的雙向屬性傳遞。

### 從原生端傳遞屬性至React Native

可透過在主活動中自定義`ReactActivityDelegate`實現來向下傳遞屬性，該實現需覆寫`getLaunchOptions`方法以返回包含目標屬性的`Bundle`物件。

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

`ReactRootView`提供可讀寫屬性`appProperties`。設定此屬性後，React Native應用會以新屬性重新渲染，但僅在屬性值實際變更時才會觸發更新。

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

屬性可隨時更新，但更新操作必須在主線程執行，而讀取操作可在任意線程進行。

目前無法單獨更新部分屬性，建議自行建立封裝層實現此功能。

> **_注意：_** 目前頂層RN元件的JS函數`componentWillUpdateProps`在屬性更新後不會被觸發，但可透過`componentDidMount`函數存取新屬性。

### 從React Native傳遞屬性至原生端

[此文章](legacy/native-components-android#3-expose-view-property-setters-using-reactprop-or-reactpropgroup-annotation)詳細說明了原生元件屬性的暴露方式。簡言之，需透過`@ReactProp`註解標記setter方法來暴露屬性，之後在React Native中即可像普通RN元件般使用這些屬性。

### 屬性的限制

跨語言屬性的主要缺陷在於不支援回調機制，因而無法實現自下而上的資料綁定。例如當需要透過JS操作移除原生父視圖中的RN子視圖時，純屬性機制就無法滿足此需求。

雖然存在跨語言回調的變通方案([參見此處](legacy/native-modules-android#callbacks))，但這些回調並非設計用來作為屬性傳遞。其主要用途是從JS觸發原生操作，並在JS端處理操作結果。

## 其他跨語言互動方式（事件與原生模組）

如前一章所述，使用屬性存在一些限制。有時屬性不足以驅動應用程序的邏輯，我們需要一個提供更大靈活性的解決方案。本章涵蓋了 React Native 中可用的其他通訊技術。這些技術既可用於內部通訊（RN 中 JS 與原生層之間的交互），也可用於外部通訊（RN 與應用程序中「純原生」部分之間的交互）。

React Native 允許您執行跨語言函數調用。您可以從 JS 執行自定義原生代碼，反之亦然。遺憾的是，根據我們所處的環境，實現相同目標的方式有所不同。對於原生端，我們使用事件機制來安排在 JS 中執行處理函數；而對於 React Native，我們直接調用原生模塊導出的方法。

### 從原生端調用 React Native 函數（事件）

事件在[這篇文章](legacy/native-components-android#events)中有詳細描述。請注意，使用事件無法保證執行時間，因為事件是在單獨的線程中處理的。

事件功能強大，因為它們允許我們無需持有 React Native 組件的引用即可對其進行修改。然而，在使用時可能會遇到一些陷阱：

- 由於事件可以從任何地方發送，它們可能會在項目中引入混亂的依賴關係。
- 事件共享命名空間，這意味著可能會遇到名稱衝突。衝突無法靜態檢測，因此難以調試。
- 如果使用多個相同 React Native 組件的實例，並希望從事件的角度區分它們，可能需要引入標識符並與事件一起傳遞（可以使用原生視圖的 `reactTag` 作為標識符）。

### 從 React Native 調用原生函數（原生模塊）

原生模塊是可在 JS 中使用的 Java/Kotlin 類。通常每個 JS 橋接器會創建每個模塊的一個實例。它們可以向 React Native 導出任意函數和常量。[這篇文章](legacy/native-modules-android)對此有詳細說明。

> **_警告_**：所有原生模塊共享相同的命名空間。創建新模塊時請注意名稱衝突。