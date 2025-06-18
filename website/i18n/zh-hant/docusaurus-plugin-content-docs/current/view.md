---
id: view
title: View
---

作為構建使用者介面最基礎的元件，`View` 是一個支援 [flexbox](flexbox.md) 佈局、[樣式](style.md)、[觸控處理](handling-touches.md) 和 [無障礙功能](accessibility.md) 控制的容器。`View` 會直接對應到 React Native 運行平台的原生視圖，無論是 `UIView`、`<div>` 或 `android.view` 等。

`View` 設計用於嵌套在其他視圖中，可以包含 0 到多個任意類型的子元件。

此範例建立了一個 `View`，包含兩個帶顏色的方塊和一個文字元件，以水平排列並帶有內邊距。

```SnackPlayer name=View%20Example
import React from 'react';
import {View, Text} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const ViewBoxesWithColorAndText = () => {
  return (
    <SafeAreaProvider>
      <SafeAreaView style={{height: 100, flexDirection: 'row'}}>
        <View style={{backgroundColor: 'blue', flex: 0.2}} />
        <View style={{backgroundColor: 'red', flex: 0.4}} />
        <Text>Hello World!</Text>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

export default ViewBoxesWithColorAndText;
```

> 為提高程式碼清晰度和效能，建議搭配 [`StyleSheet`](style.md) 使用 `View`，當然也支援行內樣式。

### 合成觸控事件

對於 `View` 的回應器屬性（例如 `onResponderMove`），傳遞給它們的合成觸控事件格式為 [PressEvent](pressevent)。

---

# 參考文件

## 屬性

---

### `accessibilityActions`

無障礙操作允許輔助技術以程式化方式呼叫元件的操作。`accessibilityActions` 屬性應包含操作物件列表，每個操作物件需包含名稱和標籤欄位。

詳見 [無障礙功能指南](accessibility.md#accessibility-actions) 獲取更多資訊。

| Type  |
| ----- |
| array |

---

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

此值表示該無障礙元素內包含的子元素是否隱藏，預設值為 `false`。

詳見 [無障礙功能指南](accessibility.md#accessibilityelementshidden-ios) 獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessibilityHint`

當無障礙標籤無法明確表達操作結果時，無障礙提示可幫助使用者理解觸發該元素後會發生的行為。

| Type   |
| ------ |
| string |

---

### `accessibilityLanguage` <div class="label ios">iOS</div>

此值指定螢幕閱讀器與元素互動時應使用的語言，需符合 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

詳見 [iOS `accessibilityLanguage` 文件](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage) 獲取更多資訊。

| Type   |
| ------ |
| string |

---

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

此值決定當色彩反轉功能開啟時，該視圖是否應被反轉。設為 `true` 表示即使開啟色彩反轉，此視圖也不會被反轉。

詳見 [無障礙功能指南](accessibility.md#accessibilityignoresinvertcolors) 獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessibilityLabel`

覆寫螢幕閱讀器在使用者與元素互動時朗讀的文字。預設情況下，標籤會透過遍歷所有子元件並合併以空格分隔的 `Text` 節點來建構。

| Type   |
| ------ |
| string |

---

### `accessibilityLiveRegion` <div class="label android">Android</div>

指示輔助技術服務是否應在該視圖變更時通知使用者。僅適用於 Android API ≥ 19。可能的值包括：

- `'none'` - 輔助技術服務不應通知此視圖的變更。
- `'polite'` - 輔助技術服務應通知此視圖的變更。
- `'assertive'` - 輔助技術服務應中斷當前語音以立即通知此視圖的變更。

詳見 [Android `View` 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:accessibilityLiveRegion) 參考。

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
- `'text'` - 當元素應被視為無法變更的靜態文本時使用。
- `'adjustable'` - 當元素可以「調整」時使用（例如滑塊）。
- `'imagebutton'` - 當元素應被視為按鈕且同時是圖像時使用。
- `'header'` - 當元素充當內容區段的標題時使用（例如導航欄的標題）。
- `'summary'` - 當元素可用於在應用首次啟動時提供當前條件的快速摘要時使用。
- `'alert'` - 當元素包含需要向使用者展示的重要文本時使用。
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
- `'switch'` - 用於表示可以開啟和關閉的開關。
- `'tab'` - 用於表示標籤頁。
- `'tablist'` - 用於表示標籤頁列表。
- `'timer'` - 用於表示計時器。
- `'toolbar'` - 用於表示工具欄（操作按鈕或組件的容器）。
- `'grid'` - 與 ScrollView、VirtualizedList、FlatList 或 SectionList 一起使用，表示網格。向 android GridView 添加網格內/外的通知。

| Type   |
| ------ |
| string |

---

### `accessibilityState`

向輔助技術的使用者描述組件的當前狀態。

詳見 [無障礙指南](accessibility.md#accessibilitystate-ios-android) 以獲取更多信息。

| Type                                                                                             |
| ------------------------------------------------------------------------------------------------ |
| object: `{disabled: bool, selected: bool, checked: bool or 'mixed', busy: bool, expanded: bool}` |

---

### `accessibilityValue`

表示組件的當前值。它可以是組件值的文本描述，或者對於基於範圍的組件（如滑塊和進度條），它包含範圍信息（最小值、當前值和最大值）。

詳見 [無障礙指南](accessibility.md#accessibilityvalue-ios-android) 以獲取更多信息。

| Type                                                            |
| --------------------------------------------------------------- |
| object: `{min: number, max: number, now: number, text: string}` |

---

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。預設值為 `false`。

詳見[無障礙功能指南](accessibility.md#accessibilityviewismodal-ios)以獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessible`

當設為 `true` 時，表示該視圖是一個無障礙元素，可被螢幕閱讀器和硬體鍵盤等輔助技術發現。預設情況下，所有可觸控元素都具有無障礙功能。

詳見[無障礙功能指南](accessibility.md#accessible)以獲取更多資訊。

---

### `aria-busy`

表示元素正在被修改，輔助技術可能需要等待變更完成後再通知使用者更新。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-checked`

表示可勾選元素的狀態。此屬性可接受布林值或 "mixed" 字串來表示混合狀態的核取方塊。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

---

### `aria-disabled`

表示元素是可感知的但已被禁用，因此不可編輯或操作。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-expanded`

表示可展開元素當前是展開還是折疊狀態。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-hidden`

表示此無障礙元素中包含的元素是否被隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-label`

定義一個字串值，用於標記互動式元素。

| Type   |
| ------ |
| string |

---

### `aria-labelledby` <div class="label android">Android</div>

標識用於標記當前元素的相關元素。`aria-labelledby` 的值應與相關元素的 [`nativeID`](view.md#nativeid) 相匹配：

```tsx
<View>
  <Text nativeID="formLabel">Label for Input Field</Text>
  <TextInput aria-label="input" aria-labelledby="formLabel" />
</View>
```

| Type   |
| ------ |
| string |

---

### `aria-live` <div class="label android">Android</div>

表示元素將被更新，並描述使用者代理、輔助技術和使用者可從即時區域獲得的更新類型。

- **off** 無障礙服務不應宣佈此視圖的變更。
- **polite** 無障礙服務應宣佈此視圖的變更。
- **assertive** 無障礙服務應中斷正在進行的語音以立即宣佈此視圖的變更。

| Type                                     | Default |
| ---------------------------------------- | ------- |
| enum(`'assertive'`, `'off'`, `'polite'`) | `'off'` |

---

### `aria-modal` <div class="label ios">iOS</div>

布林值，表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。此屬性優先於 [`accessibilityViewIsModal`](#accessibilityviewismodal-ios) 屬性。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-selected`

表示當前可選元素是否處於選中狀態。

| Type    |
| ------- |
| boolean |

### `aria-valuemax`

表示範圍型元件（如滑塊和進度條）的最大值。優先級高於 `accessibilityValue` 屬性中的 `max` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuemin`

表示範圍型元件（如滑塊和進度條）的最小值。優先級高於 `accessibilityValue` 屬性中的 `min` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuenow`

表示範圍型元件（如滑塊和進度條）的當前值。優先級高於 `accessibilityValue` 屬性中的 `now` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuetext`

表示元件的文字描述。優先級高於 `accessibilityValue` 屬性中的 `text` 值。

| Type   |
| ------ |
| string |

---

### `collapsable`

僅用於佈局子元件或不繪製任何內容的視圖，可能會作為優化被自動從原生層級中移除。將此屬性設為 `false` 可禁用此優化，確保該 `View` 存在於原生視圖層級中。

| Type    | Default |
| ------- | ------- |
| boolean | true    |

---

### `collapsableChildren`

設為 false 可防止該視圖的直接子元件被從原生視圖層級中移除，效果類似於對每個子元件設置 `collapsable={false}`。

| Type    | Default |
| ------- | ------- |
| boolean | true    |

---

### `focusable` <div class="label android">Android</div>

此 `View` 是否應可透過非觸控輸入設備（如硬體鍵盤）獲取焦點。

| Type    |
| ------- |
| boolean |

---

### `hitSlop`

定義觸控事件可從視圖外多遠處開始觸發。典型的介面指南建議觸控目標至少為 30-40 點/密度無關像素。

例如，若可觸控視圖高度為 20，可透過 `hitSlop={{top: 10, bottom: 10, left: 0, right: 0}}` 將可觸控高度擴展至 40。

> 觸控區域絕不會超出父視圖邊界，且當觸控命中兩個重疊視圖時，兄弟視圖的 Z-index 始終優先。

| Type                                                                 |
| -------------------------------------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` |

---

### `id`

用於從原生類定位此視圖。優先級高於 `nativeID` 屬性。

> 這會禁用對此視圖的「僅佈局視圖移除」優化！

| Type   |
| ------ |
| string |

---

### `importantForAccessibility` <div class="label android">Android</div>

控制視圖對無障礙功能的重要性，即是否觸發無障礙事件以及是否被報告給查詢螢幕的無障礙服務。僅適用於 Android。

可能的值：

- `'auto'` - 由系統決定該視圖是否對無障礙功能重要 - 預設值（推薦）。
- `'yes'` - 該視圖對無障礙功能重要。
- `'no'` - 該視圖對無障礙功能不重要。
- `'no-hide-descendants'` - 該視圖及其所有子視圖對無障礙功能都不重要。

詳見 [Android `importantForAccessibility` 文檔](https://developer.android.com/reference/android/R.attr.html#importantForAccessibility) 參考。

| Type                                             |
| ------------------------------------------------ |
| enum('auto', 'yes', 'no', 'no-hide-descendants') |

---

### `nativeID`

用於從原生類別中定位此視圖。

> 這會停用此視圖的「僅佈局視圖移除」優化功能！

| Type   |
| ------ |
| string |

---

### `needsOffscreenAlphaCompositing`

此 `View` 是否需要離屏渲染並與 alpha 值合成，以保留 100% 正確的顏色和混合行為。預設值 (`false`) 會改為在繪製每個元素時，對使用的繪畫工具套用 alpha 值來繪製組件及其子元素，而非將整個組件離屏渲染後再與 alpha 值合成。當您設定透明度的 `View` 含有多個重疊元素（例如多個重疊的 `View` 或文字與背景）時，此預設行為可能會產生明顯且非預期的效果。

離屏渲染以保留正確的 alpha 行為非常耗費資源，且對非原生開發者難以調試，因此預設不啟用。若您確實需要為動畫啟用此屬性，請考慮與 renderToHardwareTextureAndroid 結合使用（前提是視圖**內容**是靜態的，即不需要每幀重繪）。若啟用該屬性，此 View 將被離屏渲染一次，儲存為硬體紋理，然後每幀與 alpha 值合成到畫面上，無需在 GPU 上切換渲染目標。

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

當用戶執行無障礙操作時調用此函數。該函數的唯一參數是一個包含要執行操作名稱的事件對象。

詳見[無障礙功能指南](accessibility.md#accessibility-actions)獲取更多資訊。

| Type     |
| -------- |
| function |

---

### `onAccessibilityEscape` <div class="label ios">iOS</div>

當`accessible`設為`true`時，系統會在用戶執行「返回」手勢時調用此函數。

| Type     |
| -------- |
| function |

---

### `onAccessibilityTap` <div class="label ios">iOS</div>

當`accessible`設為true時，系統會在用戶執行無障礙點擊手勢時嘗試調用此函數。

| Type     |
| -------- |
| function |

---

### `onLayout`

在組件掛載或佈局變更時觸發。

此事件會在佈局計算完成後立即觸發，但新的佈局可能尚未在屏幕上呈現（尤其在進行佈局動畫時）。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onMagicTap` <div class="label ios">iOS</div>

當`accessible`設為`true`時，系統會在用戶執行「魔法點擊」手勢時調用此函數。

| Type     |
| -------- |
| function |

---

### `onMoveShouldSetResponder`

當前視圖是否要「聲明」觸控響應權？當該視圖非響應者時，每次觸控移動都會觸發此判斷。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onMoveShouldSetResponderCapture`

若父視圖要阻止子視圖在移動時成為響應者，應設置此處理函數並返回`true`。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onResponderGrant`

當前視圖開始響應觸控事件。此時應進行視覺高亮以向用戶反饋操作狀態。

在Android平台，從此回調返回true可阻止其他原生組件在此響應週期結束前成為響應者。

| Type                                                              |
| ----------------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void ｜ boolean` |

---

### `onResponderMove`

用戶正在移動手指。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderReject`

已有其他響應者處於活躍狀態，且不願釋放權限給請求成為響應者的當前視圖。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderRelease`

在觸控結束時觸發。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminate`

響應權限已被從當前視圖剝奪。可能因`onResponderTerminationRequest`調用被其他視圖獲取，也可能被系統直接強制接管（如iOS控制中心/通知中心觸發時）。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminationRequest`

其他視圖請求當前視圖釋放響應權限。返回`true`表示允許釋放。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onStartShouldSetResponder`

此視圖是否希望在觸摸開始時成為響應者？

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onStartShouldSetResponderCapture`

若父級`View`希望阻止子級`View`在觸摸開始時成為響應者，應設置此處理函數並返回`true`。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `pointerEvents`

控制`View`是否能成為觸摸事件的目標。

- `'auto'`: 視圖可以成為觸摸事件的目標。
- `'none'`: 視圖永遠不能成為觸摸事件的目標。
- `'box-none'`: 視圖本身不能成為觸摸事件的目標，但其子視圖可以。其行為類似於CSS中的以下類別：

```css
.box-none {
  pointer-events: none;
}
.box-none * {
  pointer-events: auto;
}
```

- `'box-only'`: 視圖可以成為觸摸事件的目標，但其子視圖不能。其行為類似於CSS中的以下類別：

```css
.box-only {
  pointer-events: auto;
}
.box-only * {
  pointer-events: none;
}
```

| Type                                         |
| -------------------------------------------- |
| enum('box-none', 'none', 'box-only', 'auto') |

---

### `removeClippedSubviews`

這是`RCTView`暴露的保留性能屬性，對於包含大量子視圖（多數在屏幕外）的滾動內容非常有用。要使此屬性生效，必須將其應用於包含許多超出其邊界的子視圖的視圖上。子視圖必須設置`overflow: hidden`，包含視圖（或其父視圖之一）也應如此設置。

| Type |
| ---- |
| bool |

---

### `renderToHardwareTextureAndroid` <div class="label android">Android</div>

此`View`是否應將自身（及其所有子視圖）渲染為GPU上的單個硬件紋理。

在Android上，這對於僅修改透明度、旋轉、平移和/或縮放的動畫和交互非常有用：在這些情況下，視圖無需重繪，顯示列表也無需重新執行。紋理可以被重複使用並以不同參數重新合成。缺點是這可能會消耗有限的顯存，因此應在交互/動畫結束後將此屬性設回false。

| Type |
| ---- |
| bool |

---

### `role`

`role`向輔助技術用戶傳達組件的用途。其優先級高於[`accessibilityRole`](view#accessibilityrole)屬性。

| Type                       |
| -------------------------- |
| [Role](accessibility#role) |

---

### `shouldRasterizeIOS` <div class="label ios">iOS</div>

此`View`是否應在合成前渲染為位圖。

在iOS上，這對於不修改組件尺寸或其子視圖的動畫和交互非常有用；例如，當平移靜態視圖的位置時，柵格化允許渲染器重用靜態視圖的緩存位圖，並在每幀快速合成。

柵格化會導致離屏繪製過程，且位圖會消耗內存。使用此屬性時需進行測試和測量。

| Type |
| ---- |
| bool |

---

### `style`

| Type                           |
| ------------------------------ |
| [View Style](view-style-props) |

---

### `tabIndex` <div class="label android">Android</div>

此`View`是否應可通過非觸摸輸入設備（如硬件鍵盤）獲取焦點。支持以下值：

- `0` - View is focusable
- `-1` - View is not focusable

| Type        |
| ----------- |
| enum(0, -1) |

---

### `testID`

Used to locate this view in end-to-end tests.

> This disables the 'layout-only view removal' optimization for this view!

| Type   |
| ------ |
| string |