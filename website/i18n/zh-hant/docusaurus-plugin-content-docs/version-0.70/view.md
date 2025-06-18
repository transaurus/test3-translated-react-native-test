---
id: view
title: View
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

作為構建使用者介面的最基本元件，`View` 是一個支援 [flexbox](flexbox.md) 佈局、[樣式](style.md)、[觸控處理](handling-touches.md) 和 [無障礙功能](accessibility.md) 控制的容器。`View` 直接對應於 React Native 運行平台的原生視圖，無論是 `UIView`、`<div>` 還是 `android.view` 等。

`View` 設計用於嵌套在其他視圖中，可以包含 0 到多個任意類型的子元件。

此範例創建了一個 `View`，其中包含兩個帶有顏色的方塊和一個文字元件，並以帶有內邊距的行排列。

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=View%20Function%20Component%20Example
import React from "react";
import { View, Text } from "react-native";

const ViewBoxesWithColorAndText = () => {
  return (
    <View
      style={{
        flexDirection: "row",
        height: 100,
        padding: 20
      }}
    >
      <View style={{ backgroundColor: "blue", flex: 0.3 }} />
      <View style={{ backgroundColor: "red", flex: 0.5 }} />
      <Text>Hello World!</Text>
    </View>
  );
};

export default ViewBoxesWithColorAndText;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=View%20Class%20Component%20Example
import React, { Component } from "react";
import { View, Text } from "react-native";

class App extends Component {
  render() {
    return (
      <View
        style={{
          flexDirection: "row",
          height: 100,
          padding: 20
        }}
      >
        <View style={{ backgroundColor: "blue", flex: 0.3 }} />
        <View style={{ backgroundColor: "red", flex: 0.5 }} />
        <Text>Hello World!</Text>
      </View>
    );
  }
}

export default App;
```

</TabItem>
</Tabs>

> 為清晰和效能考量，`View` 建議與 [`StyleSheet`](style.md) 搭配使用，但也支援行內樣式。

### 合成觸控事件

對於 `View` 的回應屬性（如 `onResponderMove`），傳遞給它們的合成觸控事件以 [PressEvent](pressevent) 的形式呈現。

---

# 參考

## 屬性

---

### `accessibilityActions`

無障礙操作允許輔助技術以程式化的方式呼叫元件的操作。`accessibilityActions` 屬性應包含一個操作物件列表，每個操作物件應包含名稱和標籤欄位。

詳見 [無障礙功能指南](accessibility.md#accessibility-actions)。

| Type  |
| ----- |
| array |

---

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

表示此無障礙元素中包含的無障礙元素是否隱藏。預設值為 `false`。

詳見 [無障礙功能指南](accessibility.md#accessibilityelementshidden-ios)。

| Type |
| ---- |
| bool |

---

### `accessibilityHint`

當操作結果無法從無障礙標籤中明確理解時，無障礙提示可幫助使用者理解在無障礙元素上執行操作時會發生的結果。

| Type   |
| ------ |
| string |

---

### `accessibilityLanguage` <div class="label ios">iOS</div>

表示螢幕閱讀器在使用者與元素互動時應使用的語言。應遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

詳見 [iOS `accessibilityLanguage` 文檔](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage)。

| Type   |
| ------ |
| string |

---

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

表示當顏色反轉開啟時，此視圖是否應被反轉。值為 `true` 時，即使顏色反轉開啟，視圖也不會被反轉。

詳見 [無障礙功能指南](accessibility.md#accessibilityignoresinvertcolors)。

| Type |
| ---- |
| bool |

---

### `accessibilityLabel`

覆寫螢幕閱讀器在使用者與元素互動時讀取的文字。預設情況下，標籤是通過遍歷所有子元件並累積所有以空格分隔的 `Text` 節點來構建的。

| Type   |
| ------ |
| string |

---

### `accessibilityLiveRegion` <div class="label android">Android</div>

向無障礙服務指示當此視圖變更時是否應通知使用者。僅適用於 Android API >= 19。可能的值：

- `'none'` - 無障礙服務不應宣佈此視圖的變更。
- `'polite'` - 無障礙服務應宣佈此視圖的變更。
- `'assertive'` - 無障礙服務應中斷正在進行的語音以立即宣佈此視圖的變更。

參閱 [Android `View` 文檔](http://developer.android.com/reference/android/view/View.html#attr_android:accessibilityLiveRegion)以獲取參考。

| Type                                |
| ----------------------------------- |
| enum('none', 'polite', 'assertive') |

---

### `accessibilityRole`

`accessibilityRole` 向輔助技術的使用者傳達組件的用途。

`accessibilityRole` 可以是以下之一：

- `'none'` - 當元素沒有角色時使用。
- `'button'` - 當元素應被視為按鈕時使用。
- `'link'` - 當元素應被視為連結時使用。
- `'search'` - 當文本字段元素也應被視為搜索字段時使用。
- `'image'` - 當元素應被視為圖像時使用。可以與按鈕或連結等結合使用。
- `'keyboardkey'` - 當元素充當鍵盤按鍵時使用。
- `'text'` - 當元素應被視為無法更改的靜態文本時使用。
- `'adjustable'` - 當元素可以「調整」時使用（例如滑塊）。
- `'imagebutton'` - 當元素應被視為按鈕且同時是圖像時使用。
- `'header'` - 當元素充當內容部分的標題時使用（例如導航欄的標題）。
- `'summary'` - 當元素可用於在應用首次啟動時提供當前條件的快速摘要時使用。
- `'alert'` - 當元素包含需要向使用者呈現的重要文本時使用。
- `'checkbox'` - 當元素表示可以勾選、取消勾選或具有混合勾選狀態的複選框時使用。
- `'combobox'` - 當元素表示組合框，允許使用者在多個選項中選擇時使用。
- `'menu'` - 當組件是選項菜單時使用。
- `'menubar'` - 當組件是多個菜單的容器時使用。
- `'menuitem'` - 用於表示菜單中的項目。
- `'progressbar'` - 用於表示指示任務進度的組件。
- `'radio'` - 用於表示單選按鈕。
- `'radiogroup'` - 用於表示一組單選按鈕。
- `'scrollbar'` - 用於表示滾動條。
- `'spinbutton'` - 用於表示打開選項列表的按鈕。
- `'switch'` - 用於表示可以打開和關閉的開關。
- `'tab'` - 用於表示標籤頁。
- `'tablist'` - 用於表示標籤頁列表。
- `'timer'` - 用於表示計時器。
- `'toolbar'` - 用於表示工具欄（操作按鈕或組件的容器）。

| Type   |
| ------ |
| string |

---

### `accessibilityState`

向輔助技術的使用者描述組件的當前狀態。

參閱 [無障礙指南](accessibility.md#accessibilitystate-ios-android)以獲取更多信息。

| Type                                                                                             |
| ------------------------------------------------------------------------------------------------ |
| object: `{disabled: bool, selected: bool, checked: bool or 'mixed', busy: bool, expanded: bool}` |

---

### `accessibilityValue`

表示組件的當前值。它可以是組件值的文本描述，或者對於基於範圍的組件（如滑塊和進度條），它包含範圍信息（最小值、當前值和最大值）。

參閱 [無障礙指南](accessibility.md#accessibilityvalue-ios-android)以獲取更多信息。

| Type                                                            |
| --------------------------------------------------------------- |
| object: `{min: number, max: number, now: number, text: string}` |

---

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

表示VoiceOver是否應忽略接收器同級視圖中的元素。預設值為`false`。

詳見[無障礙功能指南](accessibility.md#accessibilityviewismodal-ios)以獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessible`

當設為`true`時，表示該視圖是一個無障礙元素。預設情況下，所有可觸控元素都具有無障礙特性。

---

### `collapsable` <div class="label android">Android</div>

僅用於佈局子元素或不繪製任何內容的視圖，可能會作為優化被自動從原生層級結構中移除。將此屬性設為`false`可禁用此優化，確保該`View`存在於原生視圖層級中。

| Type |
| ---- |
| bool |

---

### `focusable` <div class="label android">Android</div>

此`View`是否應可透過非觸控輸入設備（例如硬體鍵盤）獲取焦點。

| Type    |
| ------- |
| boolean |

---

### `hitSlop`

定義觸控事件可從視圖外多遠處開始觸發。典型的介面指南建議觸控目標至少為30-40點/密度無關像素。

例如，若一個可觸控視圖高度為20，透過設定`hitSlop={{top: 10, bottom: 10, left: 0, right: 0}}`可將其可觸控高度擴展至40。

> 觸控區域永遠不會超出父視圖邊界，且當觸控點擊兩個重疊視圖時，兄弟視圖的Z-index始終具有優先權。

| Type                                                                 |
| -------------------------------------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` |

---

### `importantForAccessibility` <div class="label android">Android</div>

控制視圖對無障礙功能的重要性，即是否觸發無障礙事件以及是否被查詢螢幕的無障礙服務報告。僅適用於Android。

可能的值：

- `'auto'` - 由系統決定視圖對無障礙功能是否重要 - 預設值（推薦）。
- `'yes'` - 視圖對無障礙功能重要。
- `'no'` - 視圖對無障礙功能不重要。
- `'no-hide-descendants'` - 視圖及其所有子視圖對無障礙功能都不重要。

詳見[Android `importantForAccessibility`文檔](http://developer.android.com/reference/android/R.attr.html#importantForAccessibility)以獲取參考。

| Type                                             |
| ------------------------------------------------ |
| enum('auto', 'yes', 'no', 'no-hide-descendants') |

---

### `nativeID`

用於從原生類別中定位此視圖。

> 這會禁用對此視圖的「僅佈局視圖移除」優化！

| Type   |
| ------ |
| string |

---

### `needsOffscreenAlphaCompositing`

此`View`是否需要離屏渲染並透過alpha合成以保留100%正確的色彩和混合行為。預設值(`false`)會改為在繪製每個元素時對使用的繪畫應用alpha值，而非將整個元件離屏渲染後再透過alpha值合成回來。在您設定透明度的`View`包含多個重疊元素（例如多個重疊的`View`，或文字和背景）的情況下，此預設行為可能會引起注意且不符合需求。

為保留正確的 alpha 混合行為而進行離屏渲染的成本極高，且對非原生開發者難以調試，因此預設未啟用此功能。若您確實需要為動畫啟用此屬性，可考慮將其與 renderToHardwareTextureAndroid 結合使用（前提是視圖內容是靜態的，即不需要每幀重繪）。啟用該屬性後，此視圖將被離屏渲染一次，保存為硬體紋理，隨後每幀以 alpha 值合成到螢幕上，無需在 GPU 上切換渲染目標。

| Type |
| ---- |
| bool |

---

### `nextFocusDown` <div class="label android">Android</div>

指定使用者向下導航時下一個獲得焦點的視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusDown)。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div>

指定使用者向前導航時下一個獲得焦點的視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusForward)。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div>

指定使用者向左導航時下一個獲得焦點的視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusLeft)。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div>

指定使用者向右導航時下一個獲得焦點的視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusRight)。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div>

指定使用者向上導航時下一個獲得焦點的視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusUp)。

| Type   |
| ------ |
| number |

---

### `onAccessibilityAction`

當使用者執行無障礙操作時觸發。此函數的唯一參數是一個包含要執行操作名稱的事件對象。

詳見 [無障礙指南](accessibility.md#accessibility-actions) 獲取更多資訊。

| Type     |
| -------- |
| function |

---

### `onAccessibilityEscape` <div class="label ios">iOS</div>

當 `accessible` 為 `true` 時，系統會在使用者執行 escape 手勢時調用此函數。

| Type     |
| -------- |
| function |

---

### `onAccessibilityTap` <div class="label ios">iOS</div>

當 `accessible` 為 true 時，系統會在使用者執行無障礙點擊手勢時嘗試調用此函數。

| Type     |
| -------- |
| function |

---

### `onLayout`

在掛載時和佈局變化時觸發。

此事件會在佈局計算完成後立即觸發，但新佈局可能尚未在事件接收時反映到螢幕上（尤其在佈局動畫進行期間）。

| Type                                                       |
| ---------------------------------------------------------- |
| `md ({ nativeEvent: [LayoutEvent](layoutevent) }) => void` |

---

### `onMagicTap` <div class="label ios">iOS</div>

當 `accessible` 設為 `true` 時，系統會在用戶執行魔法點擊手勢時調用此函數。

| Type     |
| -------- |
| function |

---

### `onMoveShouldSetResponder`

此視圖是否要「聲明」觸控響應權？當該視圖非當前響應者時，每次觸控移動都會觸發此回調。

| Type                                                        |
| ----------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => boolean` |

---

### `onMoveShouldSetResponderCapture`

若父視圖需阻止子視圖在移動時成為響應者，應設置此處理函數並返回 `true`。

| Type                                                        |
| ----------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => boolean` |

---

### `onResponderGrant`

視圖現已取得觸控事件響應權。此時應高亮顯示並向用戶反饋互動狀態。

在 Android 上，從此回調返回 true 可阻止其他原生組件在此響應者終止前取得響應權。

| Type                                                              |
| ----------------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void ｜ boolean` |

---

### `onResponderMove`

用戶正在移動手指。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => void` |

---

### `onResponderReject`

已有其他響應者處於活動狀態且不願釋放權限給請求成為響應者的視圖。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => void` |

---

### `onResponderRelease`

觸控結束時觸發。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => void` |

---

### `onResponderTerminate`

響應權已從視圖剝離。可能因 `onResponderTerminationRequest` 調用被其他視圖奪取，也可能被系統無徵兆回收（例如 iOS 控制中心/通知中心觸發時）。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => void` |

---

### `onResponderTerminationRequest`

當其他視圖請求成為響應者並要求當前視圖釋放權限時，返回 `true` 表示允許釋放。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => void` |

---

### `onStartShouldSetResponder`

觸控開始時，此視圖是否希望成為響應者？

| Type                                                        |
| ----------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => boolean` |

---

### `onStartShouldSetResponderCapture`

若父視圖需阻止子視圖在觸控開始時成為響應者，應設置此處理函數並返回 `true`。

| Type                                                        |
| ----------------------------------------------------------- |
| `md ({ nativeEvent: [PressEvent](pressevent) }) => boolean` |

---

### `pointerEvents`

控制視圖能否成為觸控事件的目標。

- `'auto'`: 視圖可成為觸控事件目標
- `'none'`: 視圖絕不會成為觸控事件目標
- `'box-none'`: 視圖本身不會成為觸控目標，但其子視圖可以。行為類似 CSS 設置如下類別：

```css
.box-none {
  pointer-events: none;
}
.box-none * {
  pointer-events: auto;
}
```

- `'box-only'`: 視圖可成為觸控目標但其子視圖不可。行為類似 CSS 設置如下類別：

```css
.box-only {
  pointer-events: auto;
}
.box-only * {
  pointer-events: none;
}
```

> 由於 `pointerEvents` 不會影響佈局/外觀，且我們已透過新增額外模式偏離規範，因此選擇不將 `pointerEvents` 包含在 `style` 中。在某些平台上，我們仍需將其實現為 `className`。使用 `style` 與否是平台的實作細節。

| Type                                         |
| -------------------------------------------- |
| enum('box-none', 'none', 'box-only', 'auto') |

---

### `removeClippedSubviews`

這是 `RCTView` 公開的保留效能屬性，對於包含許多子視圖（大多數位於畫面外）的滾動內容非常有用。要使此屬性生效，必須將其應用於包含許多超出其邊界的子視圖的視圖。子視圖還必須具有 `overflow: hidden`，包含視圖（或其父視圖之一）也應如此。

| Type |
| ---- |
| bool |

---

### `renderToHardwareTextureAndroid` <div class="label android">Android</div>

此 `View` 是否應將自身（及其所有子視圖）渲染為 GPU 上的單一硬體紋理。

在 Android 上，這對於僅修改透明度、旋轉、平移和/或縮放的動畫和互動非常有用：在這些情況下，視圖無需重新繪製，也無需重新執行顯示列表。紋理可以重複使用並以不同參數重新合成。缺點是這可能會消耗有限的視訊記憶體，因此應在互動/動畫結束時將此屬性設回 `false`。

| Type |
| ---- |
| bool |

---

### `shouldRasterizeIOS` <div class="label ios">iOS</div>

此 `View` 是否應在合成前渲染為點陣圖。

在 iOS 上，這對於不修改此元件尺寸或其子視圖的動畫和互動非常有用；例如，當平移靜態視圖的位置時，點陣化允許渲染器重用靜態視圖的快取點陣圖，並在每一幀快速合成。

點陣化會產生離屏繪製過程，且點陣圖會消耗記憶體。使用此屬性時請測試並衡量效能。

| Type |
| ---- |
| bool |

---

### `style`

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `testID`

用於在端到端測試中定位此視圖。

> 這會停用此視圖的「僅佈局視圖移除」優化！

| Type   |
| ------ |
| string |