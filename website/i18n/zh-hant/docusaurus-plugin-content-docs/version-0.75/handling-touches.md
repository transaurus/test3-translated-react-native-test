---
id: handling-touches
title: Handling Touches
---

使用者主要透過觸控與行動應用程式互動，例如點擊按鈕、滾動列表或縮放地圖等手勢組合。React Native 提供處理各種常見手勢的元件，以及全面的[手勢回應系統](gesture-responder-system.md)來實現進階手勢識別，但最常用的基礎元件仍是基本按鈕（Button）。

## 顯示基礎按鈕

[按鈕元件](button.md)提供能在各平台完美渲染的基礎按鈕。最簡範例如下：

```tsx
<Button
  onPress={() => {
    console.log('You tapped the button!');
  }}
  title="Press Me"
/>
```

在 iOS 上會渲染藍色標籤，在 Android 上則顯示藍色圓角矩形與淺色文字。點擊按鈕會觸發 "onPress" 函數，此範例中會顯示警示彈窗。您可透過 "color" 屬性來自訂按鈕顏色。

![](/docs/assets/Button.png)

您可透過下方範例實際操作按鈕元件。點擊右下角切換平台後，再點擊「Tap to Play」即可預覽應用程式效果。

```SnackPlayer name=Button%20Basics
import React, {Component} from 'react';
import {Alert, Button, StyleSheet, View} from 'react-native';

export default class ButtonBasics extends Component {
  _onPressButton() {
    Alert.alert('You tapped the button!');
  }

  render() {
    return (
      <View style={styles.container}>
        <View style={styles.buttonContainer}>
          <Button onPress={this._onPressButton} title="Press Me" />
        </View>
        <View style={styles.buttonContainer}>
          <Button
            onPress={this._onPressButton}
            title="Press Me"
            color="#841584"
          />
        </View>
        <View style={styles.alternativeLayoutButtonContainer}>
          <Button onPress={this._onPressButton} title="This looks great!" />
          <Button onPress={this._onPressButton} title="OK!" color="#841584" />
        </View>
      </View>
    );
  }
}

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
```

## 可觸控元件

若基礎按鈕不符合需求，可使用 React Native 提供的「Touchable」系列元件自訂按鈕。這些元件能捕捉點擊手勢並提供觸控回饋，但需自行設計樣式。

根據所需回饋類型選擇元件：

- 多數情況下可使用 [**TouchableHighlight**](touchablehighlight.md)，效果類似網頁按鈕或連結，按壓時會加深背景色。
- Android 平台建議使用 [**TouchableNativeFeedback**](touchablenativefeedback.md)，能顯示材質設計的波紋觸控回饋。
- [**TouchableOpacity**](touchableopacity.md) 透過降低透明度提供視覺回饋，按壓時會透出背景內容。
- 若需處理點擊但不需視覺回饋，請使用 [**TouchableWithoutFeedback**](touchablewithoutfeedback.md)。

如需偵測長按手勢，可透過任何 Touchable 元件的 `onLongPress` 屬性傳遞處理函數。

實際操作範例：

```SnackPlayer name=Touchables
import React, {Component} from 'react';
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

export default class Touchables extends Component {
  _onPressButton() {
    Alert.alert('You tapped the button!');
  }

  _onLongPressButton() {
    Alert.alert('You long-pressed the button!');
  }

  render() {
    return (
      <View style={styles.container}>
        <TouchableHighlight onPress={this._onPressButton} underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableHighlight</Text>
          </View>
        </TouchableHighlight>
        <TouchableOpacity onPress={this._onPressButton}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableOpacity</Text>
          </View>
        </TouchableOpacity>
        <TouchableNativeFeedback
          onPress={this._onPressButton}
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
        <TouchableWithoutFeedback onPress={this._onPressButton}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableWithoutFeedback</Text>
          </View>
        </TouchableWithoutFeedback>
        <TouchableHighlight
          onPress={this._onPressButton}
          onLongPress={this._onLongPressButton}
          underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>Touchable with Long Press</Text>
          </View>
        </TouchableHighlight>
      </View>
    );
  }
}

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
```

## 滾動與滑動

觸控裝置常見手勢包含滑動（swipe）與拖曳（pan），用於滾動列表或切換頁面內容。請參考核心元件 [ScrollView](scrollview.md)。

## 已知問題

- [react-native#29308](https://github.com/facebook/react-native/issues/29308#issuecomment-792864162)：觸控區域不會超出父視圖邊界，且 Android 不支援負邊距。