---
id: handling-touches
title: Handling Touches
---

使用者主要透過觸控與行動應用程式互動。他們可以組合多種手勢操作，例如點擊按鈕、滾動列表或縮放地圖。React Native 提供了處理各種常見手勢的元件，以及一套完整的[手勢回應系統](gesture-responder-system.md)來實現進階手勢識別，但您最可能感興趣的會是基礎的 Button 元件。

## 顯示基礎按鈕

[Button](button.md) 提供了一個在各平台都能美觀渲染的基礎按鈕元件。顯示按鈕的最小範例如下：

```tsx
<Button
  onPress={() => {
    console.log('You tapped the button!');
  }}
  title="Press Me"
/>
```

在 iOS 上會渲染藍色標籤，在 Android 上則會顯示帶淺色文字的藍色圓角矩形。按下按鈕會呼叫 "onPress" 函數，此範例中會顯示警示彈窗。您可透過 "color" 屬性來自訂按鈕顏色。

![](/docs/assets/Button.png)

您可以使用下方範例實際操作 `Button` 元件。透過右下角切換按鈕選擇預覽平台，點擊「Tap to Play」即可預覽應用程式。

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

若基礎按鈕不符合您的應用風格，可以使用 React Native 提供的各種「Touchable」元件來自訂按鈕。這些元件能捕捉點擊手勢並在識別手勢時提供視覺回饋，但需自行設計樣式。

根據所需回饋效果選擇適當的可觸控元件：

- 通常可將 [**TouchableHighlight**](touchablehighlight.md) 用於任何需要網頁按鈕或連結的場景，按壓時會加深元件背景色。
- 在 Android 上可考慮使用 [**TouchableNativeFeedback**](touchablenativefeedback.md) 來顯示材質設計的波紋觸控效果。
- [**TouchableOpacity**](touchableopacity.md) 透過降低元件透明度提供按壓反饋，讓背景能透顯出來。
- 若需處理點擊事件但不需要視覺回饋，請使用 [**TouchableWithoutFeedback**](touchablewithoutfeedback.md)。

某些情境下，您可能需要偵測使用者長按元件超過設定時間的行為。所有「Touchable」元件都可透過 `onLongPress` 屬性來處理長按事件。

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

觸控螢幕裝置常見的手勢包含滑動(swipe)和拖曳(pan)，用於滾動項目列表或翻頁瀏覽內容。請參考核心元件 [ScrollView](scrollview.md) 的相關應用。

## 已知問題

- [react-native#29308](https://github.com/facebook/react-native/issues/29308#issuecomment-792864162)：觸控區域不會超出父元件邊界，且 Android 不支援負邊距設定。