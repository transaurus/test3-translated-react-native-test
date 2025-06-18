---
id: handling-touches
title: Handling Touches
---

使用者主要透過觸控與行動應用程式互動，例如點擊按鈕、滾動列表或縮放地圖等組合手勢。React Native 提供處理各種常見手勢的元件，以及完整的[手勢回應系統](gesture-responder-system.md)來實現進階手勢識別，但開發者最常使用的基礎元件仍是基本按鈕(Button)。

## 顯示基礎按鈕

[按鈕元件](button.md)提供能在各平台完美渲染的基礎按鈕，最簡範例如下：

```tsx
<Button
  onPress={() => {
    console.log('You tapped the button!');
  }}
  title="Press Me"
/>
```

在iOS上會渲染藍色文字標籤，Android則顯示藍色圓角矩形與淺色文字。點擊按鈕會觸發"onPress"函數，此範例中會顯示警示彈窗。您可透過"color"屬性來自訂按鈕顏色。

![](/docs/assets/Button.png)

請透過下方範例實際操作按鈕元件，點擊右下角切換鍵選擇預覽平台後，點擊"Tap to Play"即可預覽應用程式。

```SnackPlayer name=Button%20Basics
import React from 'react';
import {Alert, Button, StyleSheet, View} from 'react-native';

const ButtonBasics = () => {
  const onPress = () => {
    Alert.alert('You tapped the button!');
  };

  return (
    <View style={styles.container}>
      <View style={styles.buttonContainer}>
        <Button onPress={onPress} title="Press Me" />
      </View>
      <View style={styles.buttonContainer}>
        <Button onPress={onPress} title="Press Me" color="#841584" />
      </View>
      <View style={styles.alternativeLayoutButtonContainer}>
        <Button onPress={onPress} title="This looks great!" />
        <Button onPress={onPress} title="OK!" color="#841584" />
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  buttonContainer: {
    margin: 20,
  },
  alternativeLayoutButtonContainer: {
    margin: 20,
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
});

export default ButtonBasics;
```

## 可觸控元件

若基礎按鈕不符合您的應用風格，可使用React Native提供的各種"Touchable"元件自訂按鈕。這些元件能捕捉點擊手勢並在識別時提供視覺回饋，但需自行設計樣式。

根據所需回饋類型選擇元件：

- 通常可將[**可觸控高亮**](touchablehighlight.md)用於任何需要網頁按鈕或連結的場景，按壓時會使視圖背景變暗
- 在Android平台可考慮使用[**原生觸控回饋**](touchablenativefeedback.md)來顯示墨水漣漪擴散效果
- [**可觸控透明度**](touchableopacity.md)透過降低按鈕透明度來顯示背景，提供按壓回饋
- 若需處理點擊手勢但不需要視覺回饋，請使用[**無回饋可觸控**](touchablewithoutfeedback.md)

某些情況下，您可能需要偵測使用者長按視圖的行為。所有"Touchable"元件都可透過`onLongPress`屬性來處理長按事件。

實際操作範例：

```SnackPlayer name=Touchables
import React from 'react';
import {
  Alert,
  Platform,
  StyleSheet,
  Text,
  TouchableHighlight,
  TouchableOpacity,
  TouchableNativeFeedback,
  TouchableWithoutFeedback,
  View,
} from 'react-native';

const Touchables = () => {
  const onPressButton = () => {
    Alert.alert('You tapped the button!');
  };

  const onLongPressButton = () => {
    Alert.alert('You long-pressed the button!');
  };

  return (
    <View style={styles.container}>
      <TouchableHighlight onPress={onPressButton} underlayColor="white">
        <View style={styles.button}>
          <Text style={styles.buttonText}>TouchableHighlight</Text>
        </View>
      </TouchableHighlight>
      <TouchableOpacity onPress={onPressButton}>
        <View style={styles.button}>
          <Text style={styles.buttonText}>TouchableOpacity</Text>
        </View>
      </TouchableOpacity>
      <TouchableNativeFeedback
        onPress={onPressButton}
        background={
          Platform.OS === 'android'
            ? TouchableNativeFeedback.SelectableBackground()
            : undefined
        }>
        <View style={styles.button}>
          <Text style={styles.buttonText}>
            TouchableNativeFeedback{' '}
            {Platform.OS !== 'android' ? '(Android only)' : ''}
          </Text>
        </View>
      </TouchableNativeFeedback>
      <TouchableWithoutFeedback onPress={onPressButton}>
        <View style={styles.button}>
          <Text style={styles.buttonText}>TouchableWithoutFeedback</Text>
        </View>
      </TouchableWithoutFeedback>
      <TouchableHighlight
        onPress={onPressButton}
        onLongPress={onLongPressButton}
        underlayColor="white">
        <View style={styles.button}>
          <Text style={styles.buttonText}>Touchable with Long Press</Text>
        </View>
      </TouchableHighlight>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    paddingTop: 60,
    alignItems: 'center',
  },
  button: {
    marginBottom: 30,
    width: 260,
    alignItems: 'center',
    backgroundColor: '#2196F3',
  },
  buttonText: {
    textAlign: 'center',
    padding: 20,
    color: 'white',
  },
});

export default Touchables;
```

## 滾動與滑動

觸控裝置常見手勢包含滑動(swipe)與拖曳(pan)，用於滾動項目列表或翻頁瀏覽內容。請參考核心元件[滾動視圖](scrollview.md)。

## 已知問題

- [react-native#29308](https://github.com/facebook/react-native/issues/29308#issuecomment-792864162)：觸控區域不會超出父視圖邊界，且Android不支援負邊距