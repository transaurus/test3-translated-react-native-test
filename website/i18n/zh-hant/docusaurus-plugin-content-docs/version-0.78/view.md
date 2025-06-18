---
id: view
title: View
---

作為構建使用者介面最基礎的元件，`View` 是一個支援 [flexbox](flexbox.md) 佈局、[樣式](style.md)、[觸控處理](handling-touches.md) 和 [無障礙功能](accessibility.md) 控制的容器。`View` 會直接對應到 React Native 運行平台的原生視圖，無論是 `UIView`、`<div>` 或 `android.view` 等。

`View` 設計用於嵌套在其他視圖中，可以包含 0 到多個任意類型的子元件。

此範例建立了一個 `View`，其中包裝了兩個帶有顏色的方塊和一個文字元件，並以行排列且具有內邊距。

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

> 雖然也支援行內樣式，但建議搭配 [`StyleSheet`](style.md) 使用 `View` 以獲得更好的清晰度和效能。

### 合成觸控事件

對於 `View` 的回應器屬性（例如 `onResponderMove`），傳遞給它們的合成觸控事件以 [PressEvent](pressevent) 的形式呈現。

---

# 參考資料

## 屬性

---

### `accessibilityActions`

無障礙操作允許輔助技術以程式化的方式呼叫元件的操作。`accessibilityActions` 屬性應包含一個操作物件列表，每個操作物件應包含名稱和標籤欄位。

詳見 [無障礙功能指南](accessibility.md#accessibility-actions) 以獲取更多資訊。

| Type  |
| ----- |
| array |

---

### `accessibilityElementsHidden` <div class="label ios">iOS</div>

表示此無障礙元素中包含的無障礙元素是否隱藏。預設值為 `false`。

詳見 [無障礙功能指南](accessibility.md#accessibilityelementshidden-ios) 以獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessibilityHint`

當無障礙標籤無法清楚表達操作結果時，無障礙提示可幫助使用者理解在無障礙元素上執行操作會發生的結果。

| Type   |
| ------ |
| string |

---

### `accessibilityLanguage` <div class="label ios">iOS</div>

指定螢幕閱讀器在使用者與元素互動時應使用的語言。應遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

詳見 [iOS `accessibilityLanguage` 文件](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage) 以獲取更多資訊。

| Type   |
| ------ |
| string |

---

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

表示當顏色反轉功能開啟時，此視圖是否應被反轉。設為 `true` 時，即使顏色反轉功能開啟，視圖也不會被反轉。

詳見 [無障礙功能指南](accessibility.md#accessibilityignoresinvertcolors) 以獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessibilityLabel`

覆寫螢幕閱讀器在使用者與元素互動時朗讀的文字。預設情況下，標籤是通過遍歷所有子元件並累積所有以空格分隔的 `Text` 節點來構建的。

| Type   |
| ------ |
| string |

---

### `accessibilityLiveRegion` <div class="label android">Android</div>

指示輔助技術服務是否應在該視圖變更時通知使用者。僅適用於 Android API >= 19。可能的值包括：

- `'none'` - 輔助技術服務不應宣告此視圖的變更。
- `'polite'` - 輔助技術服務應宣告此視圖的變更。
- `'assertive'` - 輔助技術服務應中斷正在進行的語音以立即宣告此視圖的變更。

詳見 [Android `View` 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:accessibilityLiveRegion) 以獲取參考。

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
- `'search'` - 當文字輸入元素也應被視為搜尋欄位時使用。
- `'image'` - 當元素應被視為圖片時使用。可與按鈕或連結等結合使用。
- `'keyboardkey'` - 當元素充當鍵盤按鍵時使用。
- `'text'` - 當元素應被視為靜態且不可變的文字時使用。
- `'adjustable'` - 當元素可被「調整」（例如滑桿）時使用。
- `'imagebutton'` - 當元素應被視為按鈕且同時是圖片時使用。
- `'header'` - 當元素充當內容區段的標題（例如導覽列的標題）時使用。
- `'summary'` - 當元素可用於提供應用程式啟動時當前條件的快速摘要時使用。
- `'alert'` - 當元素包含需向使用者展示的重要文字時使用。
- `'checkbox'` - 當元素代表可勾選、取消勾選或具有混合勾選狀態的核取方塊時使用。
- `'combobox'` - 當元素代表組合框，允許使用者從多個選項中選擇時使用。
- `'menu'` - 當組件是選單時使用。
- `'menubar'` - 當組件是多個選單的容器時使用。
- `'menuitem'` - 用於代表選單中的項目。
- `'progressbar'` - 用於代表指示任務進度的組件。
- `'radio'` - 用於代表單選按鈕。
- `'radiogroup'` - 用於代表一組單選按鈕。
- `'scrollbar'` - 用於代表捲軸。
- `'spinbutton'` - 用於代表開啟選項清單的按鈕。
- `'switch'` - 用於代表可開啟和關閉的開關。
- `'tab'` - 用於代表標籤頁。
- `'tablist'` - 用於代表標籤頁列表。
- `'timer'` - 用於代表計時器。
- `'toolbar'` - 用於代表工具列（動作按鈕或組件的容器）。
- `'grid'` - 與 ScrollView、VirtualizedList、FlatList 或 SectionList 一起使用，以代表網格。向 android GridView 添加網格內/外的宣告。

| Type   |
| ------ |
| string |

---

### `accessibilityState`

向輔助技術的使用者描述組件的當前狀態。

詳見 [無障礙指南](accessibility.md#accessibilitystate-ios-android) 以獲取更多資訊。

| Type                                                                                             |
| ------------------------------------------------------------------------------------------------ |
| object: `{disabled: bool, selected: bool, checked: bool or 'mixed', busy: bool, expanded: bool}` |

---

### `accessibilityValue`

代表組件的當前值。它可以是組件值的文字描述，或對於基於範圍的組件（如滑桿和進度條），它包含範圍資訊（最小值、當前值和最大值）。

詳見 [無障礙指南](accessibility.md#accessibilityvalue-ios-android) 以獲取更多資訊。

| Type                                                            |
| --------------------------------------------------------------- |
| object: `{min: number, max: number, now: number, text: string}` |

---

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。預設值為 `false`。

詳見[無障礙功能指南](accessibility.md#accessibilityviewismodal-ios)中的相關說明。

| Type |
| ---- |
| bool |

---

### `accessible`

當設為 `true` 時，表示該視圖是一個無障礙元素，可被螢幕閱讀器和硬體鍵盤等輔助技術發現。預設情況下，所有可觸控元素都具有無障礙屬性。

詳見[無障礙功能指南](accessibility.md#accessible)中的相關說明。

---

### `aria-busy`

表示元素正在被修改，輔助技術可能需要等待變更完成後再通知使用者更新。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-checked`

表示可勾選元素的狀態。此欄位可接受布林值或 "mixed" 字串來表示混合狀態的核取方塊。

| Type             | Default |
| ---------------- | ------- |
| boolean, 'mixed' | false   |

---

### `aria-disabled`

表示元素可感知但處於禁用狀態，因此不可編輯或操作。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-expanded`

表示可展開元素當前處於展開或折疊狀態。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-hidden`

表示此無障礙元素內包含的元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-label`

定義標記互動式元素的字串值。

| Type   |
| ------ |
| string |

---

### `aria-labelledby` <div class="label android">Android</div>

標識用於標記當前元素的關聯元素。`aria-labelledby` 的值應與關聯元素的 [`nativeID`](view.md#nativeid) 相匹配：

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

表示元素將被更新，並描述使用者代理、輔助技術和使用者可以預期的即時區域更新類型。

- **off** 無障礙服務不應宣佈此視圖的變更。
- **polite** 無障礙服務應宣佈此視圖的變更。
- **assertive** 無障礙服務應中斷當前語音以立即宣佈此視圖的變更。

| Type                                     | Default |
| ---------------------------------------- | ------- |
| enum(`'assertive'`, `'off'`, `'polite'`) | `'off'` |

---

### `aria-modal` <div class="label ios">iOS</div>

布林值，表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。優先級高於 [`accessibilityViewIsModal`](#accessibilityviewismodal-ios) 屬性。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-selected`

指示當前可選元素是否被選中。

| Type    |
| ------- |
| boolean |

### `aria-valuemax`

表示範圍型元件（如滑桿和進度條）的最大值。優先級高於 `accessibilityValue` 屬性中的 `max` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuemin`

表示範圍型元件（如滑桿和進度條）的最小值。優先級高於 `accessibilityValue` 屬性中的 `min` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuenow`

表示範圍型元件（如滑桿和進度條）的當前值。優先級高於 `accessibilityValue` 屬性中的 `now` 值。

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

此 `View` 是否應可透過非觸控輸入設備（例如硬體鍵盤）獲取焦點。

| Type    |
| ------- |
| boolean |

---

### `hitSlop`

定義觸控事件可從視圖外多遠處開始觸發。典型的介面指南建議觸控目標至少為 30-40 點/密度無關像素。

例如，若可觸控視圖高度為 20，可透過 `hitSlop={{top: 10, bottom: 10, left: 0, right: 0}}` 將可觸控高度延伸至 40。

> 觸控區域永遠不會超出父視圖邊界，且若觸控命中兩個重疊視圖，兄弟視圖的 Z-index 永遠優先。

| Type                                                                 |
| -------------------------------------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` |

---

### `id`

用於從原生類定位此視圖。優先級高於 `nativeID` 屬性。

> 這會禁用此視圖的「僅佈局視圖移除」優化！

| Type   |
| ------ |
| string |

---

### `importantForAccessibility` <div class="label android">Android</div>

控制視圖對無障礙功能的重要性，即是否觸發無障礙事件以及是否回報給查詢螢幕的無障礙服務。僅適用於 Android。

可能的值：

- `'auto'` - 由系統決定該視圖對無障礙功能的重要性 - 預設值（推薦）。
- `'yes'` - 該視圖對無障礙功能很重要。
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

此 `View` 是否需要離屏渲染並與 alpha 值合成，以保留 100% 正確的顏色和混合行為。預設值 (`false`) 會改為在繪製每個元素時，對使用的繪畫套用 alpha 值來繪製組件及其子元素，而非將整個組件離屏渲染後再與 alpha 值合成。當您設定透明度的 `View` 含有多個重疊元素（例如多個重疊的 `View`，或文字與背景）時，此預設行為可能會引起注意且不符合預期。

離屏渲染以保留正確的 alpha 行為非常耗費資源，且對非原生開發者難以調試，因此預設未啟用。若您確實需要為動畫啟用此屬性，請考慮與 renderToHardwareTextureAndroid 結合使用（前提是視圖**內容**是靜態的，即不需要每幀重新繪製）。若啟用該屬性，此 View 將被離屏渲染一次，儲存為硬體紋理，然後每幀與 alpha 值合成到螢幕上，無需在 GPU 上切換渲染目標。

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

當用戶執行無障礙操作時觸發。此函數的唯一參數是一個包含要執行操作名稱的事件對象。

詳見[無障礙功能指南](accessibility.md#accessibility-actions)獲取更多資訊。

| Type     |
| -------- |
| function |

---

### `onAccessibilityEscape` <div class="label ios">iOS</div>

當`accessible`設為`true`時，用戶執行返回手勢時系統將調用此函數。

| Type     |
| -------- |
| function |

---

### `onAccessibilityTap` <div class="label ios">iOS</div>

當`accessible`為true時，用戶執行無障礙點擊手勢時系統會嘗試調用此函數。

| Type     |
| -------- |
| function |

---

### `onLayout`

在組件掛載和佈局變化時觸發。

該事件在佈局計算完成後立即觸發，但新佈局可能尚未在屏幕上顯示，特別是在佈局動畫進行期間。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onMagicTap` <div class="label ios">iOS</div>

當`accessible`設為`true`時，用戶執行魔法點擊手勢時系統將調用此函數。

| Type     |
| -------- |
| function |

---

### `onMoveShouldSetResponder`

該視圖是否想"聲明"觸控響應？當視圖不是響應者時，每次觸控移動都會調用此函數。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onMoveShouldSetResponderCapture`

若父視圖想阻止子視圖在移動時成為響應者，應設置此處理函數並返回`true`。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onResponderGrant`

視圖現在開始響應觸控事件。此時應高亮顯示並向用戶展示正在發生的操作。

在Android上，從此回調返回true可阻止其他原生組件在此響應者終止前成為響應者。

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

已有其他響應者處於活動狀態且不願釋放給請求成為響應者的視圖。

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

響應者權限已從視圖剝離。可能在調用`onResponderTerminationRequest`後被其他視圖獲取，也可能被系統未經詢問直接剝離（例如iOS控制中心/通知中心的行為）。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminationRequest`

其他視圖想成為響應者並要求當前視圖釋放權限。返回`true`表示允許釋放。

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

- `'auto'`: 該視圖可成為觸摸事件的目標。
- `'none'`: 該視圖永遠不會成為觸摸事件的目標。
- `'box-none'`: 該視圖本身不會成為觸摸事件目標，但其子視圖可以。其行為類似CSS中的以下類別：

```css
.box-none {
  pointer-events: none;
}
.box-none * {
  pointer-events: auto;
}
```

- `'box-only'`: 該視圖可成為觸摸事件目標，但其子視圖不能。其行為類似CSS中的以下類別：

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

這是`RCTView`提供的保留性能屬性，適用於當存在大量子視圖且多數位於屏幕外時的滾動內容。要使此屬性生效，必須將其應用於包含許多超出其邊界的子視圖的視圖上。子視圖還必須設置`overflow: hidden`，包含視圖（或其某個父視圖）也應如此設置。

| Type |
| ---- |
| bool |

---

### `renderToHardwareTextureAndroid` <div class="label android">Android</div>

此`View`是否應將自身（及其所有子視圖）渲染為GPU上的單個硬件紋理。

在Android上，這對於僅修改透明度、旋轉、平移和/或縮放的動畫和交互非常有用：在這些情況下，視圖無需重繪且顯示列表無需重新執行。紋理可被重複使用並以不同參數重新合成。缺點是這可能消耗有限的顯存，因此應在交互/動畫結束後將此屬性設回false。

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

在iOS上，這對於不修改此組件尺寸或其子組件的動畫和交互非常有用；例如，當平移靜態視圖的位置時，柵格化允許渲染器重用靜態視圖的緩存位圖並在每幀快速合成。

柵格化會導致離屏繪製過程且位圖消耗內存。使用此屬性時需進行測試和測量。

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

此`View`是否應可通過非觸摸輸入設備（如硬件鍵盤）獲取焦點。
支持以下值：

- `0` - 視圖可獲取焦點
- `-1` - 視圖不可獲取焦點

| Type        |
| ----------- |
| enum(0, -1) |

---

### `testID`

用於在端到端測試中定位此視圖。

> 這會禁用對此視圖的「僅佈局視圖移除」優化！

| Type   |
| ------ |
| string |