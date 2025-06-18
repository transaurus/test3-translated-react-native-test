---
id: view
title: View
---

作為構建使用者介面最基礎的元件，`View` 是一個支援 [flexbox](flexbox.md) 佈局、[樣式](style.md)、[觸控處理](handling-touches.md) 和 [無障礙功能](accessibility.md) 控制的容器。`View` 會直接對應到 React Native 運行平台的原生視圖，無論是 `UIView`、`<div>` 或 `android.view` 等。

`View` 設計用於嵌套在其他視圖中，可以包含 0 到多個任意類型的子元件。

此範例建立了一個 `View`，它包裹了兩個帶有顏色的方塊和一個文字元件，並以行排列且具有內邊距。

```SnackPlayer name=View%20Example
import React from 'react';
import {View, Text} from 'react-native';

const ViewBoxesWithColorAndText = () => {
  return (
    <View
      style={{
        flexDirection: 'row',
        height: 100,
        padding: 20,
      }}>
      <View style={{backgroundColor: 'blue', flex: 0.3}} />
      <View style={{backgroundColor: 'red', flex: 0.5}} />
      <Text>Hello World!</Text>
    </View>
  );
};

export default ViewBoxesWithColorAndText;
```

> 為了清晰性和效能，建議將 `View` 與 [`StyleSheet`](style.md) 搭配使用，但同時也支援行內樣式。

### 合成觸控事件

對於 `View` 的回應器屬性（例如 `onResponderMove`），傳遞給它們的合成觸控事件格式為 [PressEvent](pressevent)。

---

# 參考

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

此值表示此無障礙元素內包含的無障礙元素是否隱藏。預設值為 `false`。

詳見 [無障礙功能指南](accessibility.md#accessibilityelementshidden-ios) 以獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessibilityHint`

當無障礙標籤無法清楚表達操作結果時，無障礙提示可幫助使用者理解在無障礙元素上執行操作時會發生的結果。

| Type   |
| ------ |
| string |

---

### `accessibilityLanguage` <div class="label ios">iOS</div>

此值指示螢幕閱讀器在使用者與元素互動時應使用的語言。它應遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

詳見 [iOS `accessibilityLanguage` 文件](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage) 以獲取更多資訊。

| Type   |
| ------ |
| string |

---

### `accessibilityIgnoresInvertColors` <div class="label ios">iOS</div>

此值指示當顏色反轉功能開啟時，此視圖是否應被反轉。設為 `true` 時，即使顏色反轉功能開啟，視圖也不會被反轉。

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

向無障礙服務指示是否應在使用者介面變更時通知使用者。僅適用於 Android API >= 19。可能的值包括：

- `'none'` - 無障礙服務不應通知此視圖的變更。
- `'polite'` - 無障礙服務應通知此視圖的變更。
- `'assertive'` - 無障礙服務應中斷正在進行的語音，立即通知此視圖的變更。

詳見 [Android `View` 文件](https://developer.android.com/reference/android/view/View.html#attr_android:accessibilityLiveRegion)參考。

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
- `'search'` - 當文字輸入框元素也應被視為搜尋欄位時使用。
- `'image'` - 當元素應被視為圖片時使用。可與按鈕或連結結合使用。
- `'keyboardkey'` - 當元素充當鍵盤按鍵時使用。
- `'text'` - 當元素應被視為靜態不可變文字時使用。
- `'adjustable'` - 當元素可被「調整」時使用（例如滑動條）。
- `'imagebutton'` - 當元素應被視為按鈕且同時是圖片時使用。
- `'header'` - 當元素充當內容區段的標題時使用（例如導覽列標題）。
- `'summary'` - 當元素可用於在應用程式首次啟動時提供當前條件的快速摘要時使用。
- `'alert'` - 當元素包含需向使用者展示的重要文字時使用。
- `'checkbox'` - 當元素代表可勾選、取消勾選或混合勾選狀態的核取方塊時使用。
- `'combobox'` - 當元素代表可讓使用者從多個選項中選擇的下拉式方塊時使用。
- `'menu'` - 當組件是選單時使用。
- `'menubar'` - 當組件是多個選單的容器時使用。
- `'menuitem'` - 用於代表選單內的項目。
- `'progressbar'` - 用於代表指示任務進度的組件。
- `'radio'` - 用於代表單選按鈕。
- `'radiogroup'` - 用於代表一組單選按鈕。
- `'scrollbar'` - 用於代表捲軸。
- `'spinbutton'` - 用於代表開啟選項列表的按鈕。
- `'switch'` - 用於代表可開啟和關閉的開關。
- `'tab'` - 用於代表標籤頁。
- `'tablist'` - 用於代表標籤頁列表。
- `'timer'` - 用於代表計時器。
- `'toolbar'` - 用於代表工具列（動作按鈕或組件的容器）。
- `'grid'` - 與 ScrollView、VirtualizedList、FlatList 或 SectionList 一起使用，代表網格。向 Android GridView 添加網格內/外公告。

| Type   |
| ------ |
| string |

---

### `accessibilityState`

向輔助技術的使用者描述組件的當前狀態。

詳見 [無障礙指南](accessibility.md#accessibilitystate-ios-android)以獲取更多資訊。

| Type                                                                                             |
| ------------------------------------------------------------------------------------------------ |
| object: `{disabled: bool, selected: bool, checked: bool or 'mixed', busy: bool, expanded: bool}` |

---

### `accessibilityValue`

代表組件的當前值。可以是組件值的文字描述，或對於基於範圍的組件（如滑動條和進度條），包含範圍資訊（最小值、當前值和最大值）。

詳見 [無障礙指南](accessibility.md#accessibilityvalue-ios-android)以獲取更多資訊。

| Type                                                            |
| --------------------------------------------------------------- |
| object: `{min: number, max: number, now: number, text: string}` |

---

### `accessibilityViewIsModal` <div class="label ios">iOS</div>

表示 VoiceOver 是否應忽略接收器同層級視圖中的元素。預設值為 `false`。

詳見 [無障礙功能指南](accessibility.md#accessibilityviewismodal-ios) 以獲取更多資訊。

| Type |
| ---- |
| bool |

---

### `accessible`

當設為 `true` 時，表示該視圖是一個無障礙元素。預設情況下，所有可觸控元素都具有無障礙功能。

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

表示元素可感知但處於禁用狀態，因此不可編輯或操作。

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

表示此無障礙元素內包含的元素是否隱藏。

例如，在包含同層級視圖 `A` 和 `B` 的視窗中，將視圖 `B` 的 `aria-hidden` 設為 `true` 會導致 VoiceOver 忽略視圖 `B` 中的元素。

| Type    | Default |
| ------- | ------- |
| boolean | false   |

---

### `aria-label`

定義標記互動元素的字串值。

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

表示元素將被更新，並描述使用者代理、輔助技術和使用者可預期的即時區域更新類型。

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

表示可選元素當前是否被選中。

| Type    |
| ------- |
| boolean |

### `aria-valuemax`

代表範圍型元件（如滑桿和進度條）的最大值。優先級高於 `accessibilityValue` 屬性中的 `max` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuemin`

代表範圍型元件（如滑桿和進度條）的最小值。優先級高於 `accessibilityValue` 屬性中的 `min` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuenow`

代表範圍型元件（如滑桿和進度條）的當前值。優先級高於 `accessibilityValue` 屬性中的 `now` 值。

| Type   |
| ------ |
| number |

---

### `aria-valuetext`

代表元件的文字描述。優先級高於 `accessibilityValue` 屬性中的 `text` 值。

| Type   |
| ------ |
| string |

---

### `collapsable` <div class="label android">Android</div>

僅用於佈局子元件或不繪製任何內容的視圖，可能會作為優化被自動從原生層級中移除。將此屬性設為 `false` 可禁用此優化，確保該 `View` 存在於原生視圖層級中。

| Type |
| ---- |
| bool |

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

> 觸控區域絕不會超出父視圖邊界，且若觸控同時命中兩個重疊視圖，兄弟視圖的 Z-index 永遠具有優先權。

| Type                                                                 |
| -------------------------------------------------------------------- |
| object: `{top: number, left: number, bottom: number, right: number}` |

---

### `id`

用於從原生類別定位此視圖。優先級高於 `nativeID` 屬性。

> 這會禁用此視圖的「僅佈局視圖移除」優化！

| Type   |
| ------ |
| string |

---

### `importantForAccessibility` <div class="label android">Android</div>

控制視圖對無障礙功能的重要性，即是否觸發無障礙事件及是否回報給查詢螢幕的無障礙服務。僅適用於 Android。

可能的值：

- `'auto'` - 由系統決定視圖對無障礙功能是否重要 - 預設值（建議使用）。
- `'yes'` - 視圖對無障礙功能重要。
- `'no'` - 視圖對無障礙功能不重要。
- `'no-hide-descendants'` - 視圖對無障礙功能不重要，其所有子視圖亦不重要。

參閱 [Android `importantForAccessibility` 文件](https://developer.android.com/reference/android/R.attr.html#importantForAccessibility) 以獲取參考。

| Type                                             |
| ------------------------------------------------ |
| enum('auto', 'yes', 'no', 'no-hide-descendants') |

---

### `nativeID`

用於從原生類別定位此視圖。

> 這會停用此視圖的「僅佈局視圖移除」優化功能！

| Type   |
| ------ |
| string |

---

### `needsOffscreenAlphaCompositing`

決定此 `View` 是否需要離屏渲染並以 alpha 值合成，以保留 100% 正確的色彩與混合行為。預設值 (`false`) 會改為在繪製每個元素時直接套用 alpha 值到繪製顏料上，而非將整個元件離屏渲染後再以 alpha 值合成回來。當您設定透明度的 `View` 含有多個重疊元素時（例如多個重疊的 `View` 或文字與背景），此預設行為可能會產生明顯且非預期的效果。

為保留正確的 alpha 行為而進行離屏渲染的成本極高，且對非原生開發者難以除錯，因此預設不啟用此功能。若您確實需要為動畫啟用此屬性，且視圖內容是靜態的（即不需要每幀重繪），可考慮搭配使用 renderToHardwareTextureAndroid。若啟用該屬性，此 View 將僅離屏渲染一次並儲存為硬體紋理，之後每幀會直接以 alpha 值合成到畫面上，無需切換 GPU 的渲染目標。

| Type |
| ---- |
| bool |

---

### `nextFocusDown` <div class="label android">Android</div>

指定使用者向下導航時下一個獲得焦點的視圖。參閱 [Android 官方文件](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusDown)。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div>

指定使用者向前導航時下一個獲得焦點的視圖。參閱 [Android 官方文件](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusForward)。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div>

指定使用者向左導航時下一個獲得焦點的視圖。參閱 [Android 官方文件](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusLeft)。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div>

指定使用者向右導航時下一個獲得焦點的視圖。參閱 [Android 官方文件](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusRight)。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div>

指定使用者向上導航時下一個獲得焦點的視圖。參閱 [Android 官方文件](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusUp)。

| Type   |
| ------ |
| number |

---

### `onAccessibilityAction`

當使用者執行無障礙操作時觸發。此函數的唯一參數是一個包含待執行操作名稱的事件物件。

詳見 [無障礙功能指南](accessibility.md#accessibility-actions)。

| Type     |
| -------- |
| function |

---

### `onAccessibilityEscape` <div class="label ios">iOS</div>

當 `accessible` 為 `true` 時，系統會在使用者執行 escape 手勢時呼叫此函數。

| Type     |
| -------- |
| function |

---

### `onAccessibilityTap` <div class="label ios">iOS</div>

當 `accessible` 設為 true 時，系統將在使用者執行無障礙點擊手勢時嘗試調用此函數。

| Type     |
| -------- |
| function |

---

### `onLayout`

在掛載時和佈局變更時觸發。

此事件會在佈局計算完成後立即觸發，但新的佈局可能尚未反映在畫面上（尤其在佈局動畫進行期間）。

| Type                                                     |
| -------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)}) => void` |

---

### `onMagicTap` <div class="label ios">iOS</div>

當 `accessible` 設為 `true` 時，系統將在使用者執行 magic tap 手勢時調用此函數。

| Type     |
| -------- |
| function |

---

### `onMoveShouldSetResponder`

此視圖是否要「聲明」觸控響應權？當該視圖非當前響應者時，每次觸控移動都會觸發此回調。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onMoveShouldSetResponderCapture`

若父視圖要阻止子視圖在移動時成為響應者，應設置此處理函數並返回 `true`。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onResponderGrant`

該視圖現在開始響應觸控事件。此時應高亮顯示並向使用者展示反饋。

在 Android 上，從此回調返回 true 可阻止其他原生組件在此響應者終止前成為響應者。

| Type                                                              |
| ----------------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void ｜ boolean` |

---

### `onResponderMove`

使用者正在移動手指。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderReject`

已有其他響應者處於活躍狀態，且不願將響應權釋放給請求成為響應者的視圖。

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

響應權已從該視圖被剝奪。可能因 `onResponderTerminationRequest` 調用後被其他視圖取得，也可能被系統未經詢問直接剝奪（例如 iOS 控制中心/通知中心觸發時）。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onResponderTerminationRequest`

其他視圖希望成為響應者，並要求該視圖釋放響應權。返回 `true` 表示允許釋放。

| Type                                                   |
| ------------------------------------------------------ |
| `md ({nativeEvent: [PressEvent](pressevent)}) => void` |

---

### `onStartShouldSetResponder`

該視圖是否希望在觸控開始時成為響應者？

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `onStartShouldSetResponderCapture`

若父視圖要阻止子視圖在觸控開始時成為響應者，應設置此處理函數並返回 `true`。

| Type                                                      |
| --------------------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)}) => boolean` |

---

### `pointerEvents`

控制該視圖能否成為觸控事件的目標。

- `'auto'`：該視圖可成為觸控事件的目標。
- `'none'`：該視圖永遠不會成為觸控事件的目標。
- `'box-none'`：該視圖本身不會成為觸控事件的目標，但其子視圖可以。其行為類似於在 CSS 中為視圖添加以下類別：

```css
.box-none {
  pointer-events: none;
}
.box-none * {
  pointer-events: auto;
}
```

- `'box-only'`：該視圖可成為觸控事件的目標，但其子視圖不能。其行為類似於在 CSS 中為視圖添加以下類別：

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

這是 `RCTView` 提供的一個保留性能屬性，對於包含大量子視圖且多數位於畫面外的滾動內容非常有用。要使此屬性生效，必須將其應用於包含許多超出其邊界的子視圖的視圖上。子視圖必須設置 `overflow: hidden`，包含視圖（或其某個父視圖）也應如此設置。

| Type |
| ---- |
| bool |

---

### `renderToHardwareTextureAndroid` <div class="label android">Android</div>

此 `View` 是否應將自身（及其所有子視圖）渲染為 GPU 上的單一硬體紋理。

在 Android 上，這對於僅修改透明度、旋轉、平移和/或縮放的動畫和互動非常有用：在這些情況下，視圖無需重新繪製，顯示列表也無需重新執行。紋理可以重複使用並以不同參數重新合成。缺點是這可能會消耗有限的視訊記憶體，因此應在互動/動畫結束後將此屬性設回 `false`。

| Type |
| ---- |
| bool |

---

### `role`

`role` 向輔助技術的使用者傳達組件的用途。其優先級高於 [`accessibilityRole`](view#accessibilityrole) 屬性。

| Type                       |
| -------------------------- |
| [Role](accessibility#role) |

---

### `shouldRasterizeIOS` <div class="label ios">iOS</div>

此 `View` 是否應在合成前渲染為點陣圖。

在 iOS 上，這對於不修改此組件尺寸或其子組件的動畫和互動非常有用；例如，當平移靜態視圖的位置時，點陣化允許渲染器重用靜態視圖的快取點陣圖，並在每一幀快速合成。

點陣化會產生離屏繪製過程，且點陣圖會消耗記憶體。使用此屬性時需進行測試和效能評估。

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

此 `View` 是否應可透過非觸控輸入設備（例如硬體鍵盤）獲取焦點。
支援以下值：

- `0` - 視圖可獲取焦點
- `-1` - 視圖不可獲取焦點

| Type        |
| ----------- |
| enum(0, -1) |

---

### `testID`

用於在端到端測試中定位此視圖。

> 這會禁用此視圖的「僅佈局視圖移除」優化！

| Type   |
| ------ |
| string |