---
id: button
title: Button
---

一個基礎按鈕元件，可在任何平台上呈現良好效果。支援最低限度的自訂選項。

若此按鈕不符合您的應用程式需求，您可以使用 [Pressable](pressable) 自行建構按鈕。如需靈感，請參考 [Button 元件的原始碼](https://github.com/facebook/react-native/blob/main/packages/react-native/Libraries/Components/Button.js)。

```tsx
<Button
  onPress={onPressLearnMore}
  title="Learn More"
  color="#841584"
  accessibilityLabel="Learn more about this purple button"
/>
```

## 範例

```SnackPlayer name=Button%20Example
import React from 'react';
import {StyleSheet, Button, View, Text, Alert} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const Separator = () => <View style={styles.separator} />;

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <View>
        <Text style={styles.title}>
          The title and onPress handler are required. It is recommended to set
          accessibilityLabel to help make your app usable by everyone.
        </Text>
        <Button
          title="Press me"
          onPress={() => Alert.alert('Simple Button pressed')}
        />
      </View>
      <Separator />
      <View>
        <Text style={styles.title}>
          Adjust the color in a way that looks standard on each platform. On
          iOS, the color prop controls the color of the text. On Android, the
          color adjusts the background color of the button.
        </Text>
        <Button
          title="Press me"
          color="#f194ff"
          onPress={() => Alert.alert('Button with adjusted color pressed')}
        />
      </View>
      <Separator />
      <View>
        <Text style={styles.title}>
          All interaction for the component are disabled.
        </Text>
        <Button
          title="Press me"
          disabled
          onPress={() => Alert.alert('Cannot press this one')}
        />
      </View>
      <Separator />
      <View>
        <Text style={styles.title}>
          This layout strategy lets the title define the width of the button.
        </Text>
        <View style={styles.fixToText}>
          <Button
            title="Left button"
            onPress={() => Alert.alert('Left button pressed')}
          />
          <Button
            title="Right button"
            onPress={() => Alert.alert('Right button pressed')}
          />
        </View>
      </View>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    marginHorizontal: 16,
  },
  title: {
    textAlign: 'center',
    marginVertical: 8,
  },
  fixToText: {
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
  separator: {
    marginVertical: 8,
    borderBottomColor: '#737373',
    borderBottomWidth: StyleSheet.hairlineWidth,
  },
});

export default App;
```

---

# 參考

## 屬性

### <div class="label required basic">必填</div>**`onPress`**

當使用者點擊按鈕時呼叫的處理函式。

| Type                                           |
| ---------------------------------------------- |
| `md ({nativeEvent: [PressEvent](pressevent)})` |

---

### <div class="label required basic">必填</div>**`title`**

顯示在按鈕內的文字。在 Android 上，給定的標題將轉換為大寫形式。

| Type   |
| ------ |
| string |

---

### `accessibilityLabel`

為視障輔助功能顯示的文字。

| Type   |
| ------ |
| string |

---

### `accessibilityLanguage` <div class="label ios">iOS</div>

指示螢幕閱讀器在使用者與元素互動時應使用的語言值。應遵循 [BCP 47 規範](https://www.rfc-editor.org/info/bcp47)。

詳見 [iOS `accessibilityLanguage` 文件](https://developer.apple.com/documentation/objectivec/nsobject/1615192-accessibilitylanguage) 以獲取更多資訊。

| Type   |
| ------ |
| string |

---

### `accessibilityActions`

輔助操作允許輔助技術以程式化方式呼叫元件的操作。`accessibilityActions` 屬性應包含操作物件列表，每個操作物件應包含名稱和標籤欄位。

詳見 [輔助功能指南](accessibility.md#accessibility-actions) 以獲取更多資訊。

| Type  | Required |
| ----- | -------- |
| array | No       |

---

### `onAccessibilityAction`

當使用者執行輔助操作時呼叫。此函式的唯一參數是包含要執行操作名稱的事件物件。

詳見 [輔助功能指南](accessibility.md#accessibility-actions) 以獲取更多資訊。

| Type     | Required |
| -------- | -------- |
| function | No       |

---

### `color`

文字顏色 (iOS)，或按鈕的背景顏色 (Android)。

```mdx-code-block
export function ColorDefaults() {
  return (
    <>
      <ins style={{ background: "#2196F3" }} className="color-box" />{" "}<code>'#2196F3'</code>
      {" "}<div className="label android">Android</div>
      <hr />
      <ins style={{ background: "#007AFF" }} className="color-box" />{" "}<code>'#007AFF'</code>
      {" "}<div className="label ios">iOS</div>
    </>
  );
}
```

| Type            | Default          |
| --------------- | ---------------- |
| [color](colors) | <ColorDefaults/> |

---

### `disabled`

若為 `true`，則停用此元件的所有互動。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `hasTVPreferredFocus` <div class="label tv">TV</div>

電視首選焦點。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `nextFocusDown` <div class="label android">Android</div><div class="label tv">TV</div>

指定當使用者向下導航時接收焦點的下一個視圖。詳見 [Android 文件](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusDown)。

| Type   |
| ------ |
| number |

---

### `nextFocusForward` <div class="label android">Android</div><div class="label tv">TV</div>

指定當用戶向前導航時接收焦點的下一個視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusForward)。

| Type   |
| ------ |
| number |

---

### `nextFocusLeft` <div class="label android">Android</div><div class="label tv">TV</div>

指定當用戶向左導航時接收焦點的下一個視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusLeft)。

| Type   |
| ------ |
| number |

---

### `nextFocusRight` <div class="label android">Android</div><div class="label tv">TV</div>

指定當用戶向右導航時接收焦點的下一個視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusRight)。

| Type   |
| ------ |
| number |

---

### `nextFocusUp` <div class="label android">Android</div><div class="label tv">TV</div>

指定當用戶向上導航時接收焦點的下一個視圖。詳見 [Android 文檔](https://developer.android.com/reference/android/view/View.html#attr_android:nextFocusUp)。

| Type   |
| ------ |
| number |

---

### `testID`

用於在端到端測試中定位此視圖。

| Type   |
| ------ |
| string |

---

### `touchSoundDisabled` <div class="label android">Android</div>

若為 `true`，觸摸時不會播放系統聲音。

| Type    | Default |
| ------- | ------- |
| boolean | `false` |