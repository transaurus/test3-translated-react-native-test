---
title: Right-to-Left Layout Support For React Native Apps
author: Mengjue (Mandy) Wang
authorTitle: Software Engineer Intern at Facebook
authorURL: 'https://github.com/MengjueW'
authorImageURL: 'https://avatars0.githubusercontent.com/u/13987140?v=3&s=128'
tags: [engineering]
---

在將應用程式發布至應用商店後，國際化是擴大受眾範圍的下一步。全球有超過20個國家和無數人口使用從右至左（RTL）的語言。因此，讓您的應用程式支援RTL對這些使用者來說是必要的。

我們很高興地宣布，React Native已進行改進以支援RTL佈局。這項功能現已在[react-native](https://github.com/facebook/react-native)的主分支中提供，並將在下一個RC版本[`v0.33.0-rc`](https://github.com/facebook/react-native/releases)中推出。

這涉及更改[css-layout](https://github.com/facebook/css-layout)（RN使用的核心佈局引擎）、RN核心實現，以及特定的開源JS元件以支援RTL。

為了在生產環境中測試RTL支援，最新版本的**Facebook廣告管理員**應用（首個跨平台100% RN應用）現已提供阿拉伯語和希伯來語的RTL佈局，適用於[iOS](https://itunes.apple.com/app/id964397083)和[Android](https://play.google.com/store/apps/details?id=com.facebook.adsmanager)。以下是這些RTL語言中的顯示效果：

<>
<img src="/blog/assets/rtl-ama-ios-arabic.png" width={280} style={{ margin: 10 }} />
<img src="/blog/assets/rtl-ama-android-hebrew.png" width={280} style={{ margin: 10 }} />
</>

## RN中RTL支援的概述變更

[css-layout](https://github.com/facebook/css-layout)已經有`start`和`end`的佈局概念。在從左至右（LTR）佈局中，`start`表示`left`，`end`表示`right`。但在RTL中，`start`表示`right`，`end`表示`left`。這意味著我們可以讓RN依賴`start`和`end`的計算來得出正確的佈局，包括`position`、`padding`和`margin`。

此外，[css-layout](https://github.com/facebook/css-layout)已經讓每個元件的方向繼承自其父元件。這意味著，我們只需將根元件的方向設置為RTL，整個應用程式就會翻轉。

下圖從高層次描述了這些變更：

![](/blog/assets/rtl-rn-core-updates.png)

這些包括：

- [css-layout對絕對定位的RTL支援](https://github.com/facebook/css-layout/commit/46c842c71a1232c3c78c4215275d104a389a9a0f)
- 在RN核心實現中將`left`和`right`映射到`start`和`end`以用於陰影節點
- 並公開一個[橋接實用模組](https://github.com/facebook/react-native/blob/f0fb228ec76ed49e6ed6d786d888e8113b8959a2/Libraries/Utilities/I18nManager.js)來幫助控制RTL佈局

通過此更新，當您允許應用程式使用RTL佈局時：

- 每個元件的佈局將水平翻轉
- 如果您使用支援RTL的開源元件，某些手勢和動畫將自動具有RTL佈局
- 可能需要最少額外努力來讓您的應用程式完全支援RTL

## 讓應用程式支援RTL

1. 要支援 RTL，首先需將 RTL 語言套件加入你的應用程式。

   - 參閱 [iOS](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW1) 和 [Android](https://developer.android.com/training/basics/supporting-devices/languages.html) 的通用指南。

2. 在原生代碼起始處呼叫 `allowRTL()` 函式以啟用 RTL 佈局。此工具函式可確保僅在應用程式準備就緒時套用 RTL 佈局。範例如下：

   iOS:

   ```objc
   // 在 AppDelegate.m 中
     [[RCTI18nUtil sharedInstance] allowRTL:YES];
   ```

   Android:

   ```java
   // 在 MainActivity.java 中
     I18nUtil sharedI18nUtilInstance = I18nUtil.getInstance();
     sharedI18nUtilInstance.allowRTL(context, true);
   ```

3. Android 需在 `AndroidManifest.xml` 檔案的 [`<application>`](https://developer.android.com/guide/topics/manifest/application-element.html) 元素中加入 `android:supportsRtl="true"`。

重新編譯應用程式並將裝置語言切換為 RTL 語言（如阿拉伯語或希伯來語）後，應用程式佈局應會自動切換為 RTL 模式。

## 撰寫 RTL 相容元件

大多數元件本身已支援 RTL，例如：

- 左至右佈局

<img src="/blog/assets/rtl-demo-listitem-ltr.png" width="300" />

- 右至左佈局

<img src="/blog/assets/rtl-demo-listitem-rtl.png" width="300" />

但需注意某些特殊情況，此時需使用 [`I18nManager`](https://github.com/facebook/react-native/blob/f0fb228ec76ed49e6ed6d786d888e8113b8959a2/Libraries/Utilities/I18nManager.js)。[`I18nManager`](https://github.com/facebook/react-native/blob/f0fb228ec76ed49e6ed6d786d888e8113b8959a2/Libraries/Utilities/I18nManager.js) 中的常數 `isRTL` 可判斷當前佈局方向，據此進行必要調整。

#### 具方向性意義的圖示

若元件包含圖示或圖片，由於 RN 不會自動翻轉原始圖像，這些元素在 LTR 和 RTL 佈局中會以相同方向顯示。因此需根據佈局手動翻轉：

- 左至右佈局

<img src="/blog/assets/rtl-demo-icon-ltr.png" width="300" />

- 右至左佈局

<img src="/blog/assets/rtl-demo-icon-rtl.png" width="300" />

以下是兩種根據方向翻轉圖示的方法：

- 對圖片元件添加 `transform` 樣式：

  ```jsx
  <Image
    source={...}
    style={{transform: [{scaleX: I18nManager.isRTL ? -1 : 1}]}}
  />
  ```

- 或根據方向切換圖片來源：

  ```jsx
  let imageSource = require('./back.png');
  if (I18nManager.isRTL) {
    imageSource = require('./forward.png');
  }
  return <Image source={imageSource} />;
  ```

#### 手勢與動畫

在 Android 和 iOS 開發中，切換至 RTL 佈局時，手勢與動畫方向會與 LTR 佈局相反。目前 RN 核心代碼層級尚未支援手勢與動畫的自動轉換，需在元件層級實作。好消息是部分元件（如 [`SwipeableRow`](https://github.com/facebook/react-native/blob/38a6eec0db85a5204e85a9a92b4dee2db9641671/Libraries/Experimental/SwipeableRow/SwipeableRow.js) 和 [`NavigationExperimental`](https://github.com/facebook/react-native/tree/master/Libraries/NavigationExperimental)）已原生支援 RTL，但其他包含手勢操作的元件仍需手動實作 RTL 相容性。

說明手勢RTL支援的一個好例子是[`SwipeableRow`](https://github.com/facebook/react-native/blob/38a6eec0db85a5204e85a9a92b4dee2db9641671/Libraries/Experimental/SwipeableRow/SwipeableRow.js)。

<p align="center">
  <img src="/blog/assets/rtl-demo-swipe-ltr.png" width={280} style={{margin: 10}} />
  <img src="/blog/assets/rtl-demo-swipe-rtl.png" width={280} style={{margin: 10}} />
</p>

##### 手勢範例

```js
// SwipeableRow.js
_isSwipingExcessivelyRightFromClosedPosition(gestureState: Object): boolean {
  // ...
  const gestureStateDx = IS_RTL ? -gestureState.dx : gestureState.dx;
  return (
    this._isSwipingRightFromClosed(gestureState) &&
    gestureStateDx > RIGHT_SWIPE_THRESHOLD
  );
},
```

##### 動畫範例

```js
// SwipeableRow.js
_animateBounceBack(duration: number): void {
  // ...
  const swipeBounceBackDistance = IS_RTL ?
    -RIGHT_SWIPE_BOUNCE_BACK_DISTANCE :
    RIGHT_SWIPE_BOUNCE_BACK_DISTANCE;
  this._animateTo(
    -swipeBounceBackDistance,
    duration,
    this._animateToClosedPositionDuringBounce,
  );
},
```

## 維護您的RTL相容應用程式

即使在初始發布RTL相容應用程式後，您很可能仍需迭代新功能。為了提高開發效率，[`I18nManager`](https://github.com/facebook/react-native/blob/f0fb228ec76ed49e6ed6d786d888e8113b8959a2/Libraries/Utilities/I18nManager.js)提供了`forceRTL()`函數，可在不更改測試設備語言的情況下更快地測試RTL。您可能希望在應用程式中為此提供一個簡單的切換開關。以下是RNTester中RTL範例的一個例子：

<p align="center">
  <img src="/blog/assets/rtl-demo-forcertl.png" width="300" />
</p>

```js
<RNTesterBlock title={'Quickly Test RTL Layout'}>
  <View style={styles.flexDirectionRow}>
    <Text style={styles.switchRowTextView}>forceRTL</Text>
    <View style={styles.switchRowSwitchView}>
      <Switch
        onValueChange={this._onDirectionChange}
        style={styles.rightAlignStyle}
        value={this.state.isRTL}
      />
    </View>
  </View>
</RNTesterBlock>;

_onDirectionChange = () => {
  I18nManager.forceRTL(!this.state.isRTL);
  this.setState({isRTL: !this.state.isRTL});
  Alert.alert(
    'Reload this page',
    'Please reload this page to change the UI direction! ' +
      'All examples in this app will be affected. ' +
      'Check them out to see what they look like in RTL layout.',
  );
};
```

在開發新功能時，您可以輕鬆切換此按鈕並重新載入應用程式以查看RTL佈局。這樣的好處是您無需更改語言設置來進行測試，但某些文字對齊方式不會改變，如下一節所述。因此，在發布前始終使用RTL語言測試您的應用程式是一個好習慣。

## 限制與未來計劃

RTL支援應涵蓋您應用程式中的大多數用戶體驗；然而，目前存在一些限制：

- 文字對齊行為在Android和iOS上有所不同
  - 在iOS上，預設的文字對齊方式取決於活動的語言包，它們始終位於一側。在Android上，預設的文字對齊方式取決於文字內容的語言，即英文將左對齊，阿拉伯文將右對齊。
  - 理論上，這應該在平台上保持一致，但有些用戶在使用應用程式時可能更喜歡其中一種行為。可能需要更多的用戶體驗研究來找出文字對齊的最佳實踐。

* 沒有「真正的」左/右

  如前所述，我們將JS端的`left`/`right`樣式映射到`start`/`end`，所有在RTL佈局中代碼中的`left`在螢幕上變為「右」，而代碼中的`right`在螢幕上變為「左」。這很方便，因為您無需大幅更改產品代碼，但這意味著無法在代碼中指定「真正的左」或「真正的右」。未來可能需要允許組件控制其方向，而不受語言影響。

* 使手勢和動畫的RTL支援對開發者更友好

  目前，要使手勢和動畫RTL相容仍需要一些編程工作。未來，找到一種方法使手勢和動畫的RTL支援對開發者更友好將是理想的。

## 試試看！

查看`RNTester`中的[`RTLExample`](https://github.com/facebook/react-native/blob/master/packages/rn-tester/js/examples/RTL/RTLExample.js)以了解更多關於RTL支援的資訊，並告訴我們它的效果如何！

最後，感謝您的閱讀！我們希望React Native的RTL支援能幫助您為國際用戶擴展您的應用程式！