---
id: modal
title: Modal
---

The Modal component is a basic way to present content above an enclosing view.

## Example

```SnackPlayer name=Modal&supportedPlatforms=android,ios
import React, {useState} from 'react';
import {Alert, Modal, StyleSheet, Text, Pressable, View} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const App = () => {
  const [modalVisible, setModalVisible] = useState(false);
  return (
    <SafeAreaProvider>
      <SafeAreaView style={styles.centeredView}>
        <Modal
          animationType="slide"
          transparent={true}
          visible={modalVisible}
          onRequestClose={() => {
            Alert.alert('Modal has been closed.');
            setModalVisible(!modalVisible);
          }}>
          <View style={styles.centeredView}>
            <View style={styles.modalView}>
              <Text style={styles.modalText}>Hello World!</Text>
              <Pressable
                style={[styles.button, styles.buttonClose]}
                onPress={() => setModalVisible(!modalVisible)}>
                <Text style={styles.textStyle}>Hide Modal</Text>
              </Pressable>
            </View>
          </View>
        </Modal>
        <Pressable
          style={[styles.button, styles.buttonOpen]}
          onPress={() => setModalVisible(true)}>
          <Text style={styles.textStyle}>Show Modal</Text>
        </Pressable>
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

const styles = StyleSheet.create({
  centeredView: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  modalView: {
    margin: 20,
    backgroundColor: 'white',
    borderRadius: 20,
    padding: 35,
    alignItems: 'center',
    shadowColor: '#000',
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.25,
    shadowRadius: 4,
    elevation: 5,
  },
  button: {
    borderRadius: 20,
    padding: 10,
    elevation: 2,
  },
  buttonOpen: {
    backgroundColor: '#F194FF',
  },
  buttonClose: {
    backgroundColor: '#2196F3',
  },
  textStyle: {
    color: 'white',
    fontWeight: 'bold',
    textAlign: 'center',
  },
  modalText: {
    marginBottom: 15,
    textAlign: 'center',
  },
});

export default App;
```

---

# Reference

## Props

### [View Props](view.md#props)

Inherits [View Props](view.md#props).

---

### `animated`

> **Deprecated.** Use the [`animationType`](modal.md#animationtype) prop instead.

---

### `animationType`

The `animationType` prop controls how the modal animates.

Possible values:

- `slide` slides in from the bottom
- `fade` fades into view
- `none` appears without an animation

| Type                                | Default |
| ----------------------------------- | ------- |
| enum(`'none'`, `'slide'`, `'fade'`) | `none`  |

---

### `backdropColor`

The `backdropColor` of the modal (or background color of the modal's container.) Defaults to `white` if not provided and transparent is `false`. Ignored if `transparent` is `true`.

| Type            | Default |
| --------------- | ------- |
| [color](colors) | white   |

---

### `hardwareAccelerated` <div class="label android">Android</div>

The `hardwareAccelerated` prop controls whether to force hardware acceleration for the underlying window.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `navigationBarTranslucent` <div class="label android">Android</div>

The `navigationBarTranslucent` prop determines whether your modal should go under the system navigation bar. However, `statusBarTranslucent` also needs to be set to `true` to make navigation bar translucent.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `onDismiss` <div class="label ios">iOS</div>

The `onDismiss` prop allows passing a function that will be called once the modal has been dismissed.

| Type     |
| -------- |
| function |

---

### `onOrientationChange` <div class="label ios">iOS</div>

The `onOrientationChange` callback is called when the orientation changes while the modal is being displayed. The orientation provided is only 'portrait' or 'landscape'. This callback is also called on initial render, regardless of the current orientation.

| Type     |
| -------- |
| function |

---

### `onRequestClose`

The `onRequestClose` callback is called when the user taps the hardware back button on Android or the menu button on Apple TV. Because of this required prop, be aware that `BackHandler` events will not be emitted as long as the modal is open.
On iOS, this callback is called when a Modal is being dismissed using a drag gesture when `presentationStyle` is `pageSheet or formSheet`

| Type                                                                                                                                                                                           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| function <div className="label basic required">Required</div><div className="label android">Android</div><div className="label tv">TV</div><hr />function <div className="label ios">iOS</div> |

---

### `onShow`

The `onShow` prop allows passing a function that will be called once the modal has been shown.

| Type     |
| -------- |
| function |

---

### `presentationStyle` <div class="label ios">iOS</div>

The `presentationStyle` prop controls how the modal appears (generally on larger devices such as iPad or plus-sized iPhones). See https://developer.apple.com/reference/uikit/uimodalpresentationstyle for details.

Possible values:

- `fullScreen` 完全覆蓋整個螢幕
- `pageSheet` 以垂直寬度視圖居中顯示（僅適用於較大裝置）
- `formSheet` 以窄寬度視圖居中顯示（僅適用於較大裝置）
- `overFullScreen` 完全覆蓋螢幕，但允許透明背景

| Type                                                                   | Default                                                                             |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| enum(`'fullScreen'`, `'pageSheet'`, `'formSheet'`, `'overFullScreen'`) | `fullScreen` if `transparent={false}`<hr />`overFullScreen` if `transparent={true}` |

---

### `statusBarTranslucent` <div class="label android">Android</div>

`statusBarTranslucent` 屬性決定模態視窗是否應延伸至系統狀態列下方。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `supportedOrientations` <div class="label ios">iOS</div>

`supportedOrientations` 屬性允許模態視窗旋轉至指定的任何方向。在 iOS 上，模態視窗仍受應用程式 Info.plist 中 UISupportedInterfaceOrientations 欄位設定的限制。

> 當使用 `pageSheet` 或 `formSheet` 的 `presentationStyle` 時，此屬性將被 iOS 忽略。

| Type                                                                                                           | Default        |
| -------------------------------------------------------------------------------------------------------------- | -------------- |
| array of enums(`'portrait'`, `'portrait-upside-down'`, `'landscape'`, `'landscape-left'`, `'landscape-right'`) | `['portrait']` |

---

### `transparent`

`transparent` 屬性決定模態視窗是否填滿整個視圖。設為 `true` 時，模態視窗將在透明背景上渲染。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `visible`

`visible` 屬性控制模態視窗是否可見。

| Type | Default |
| ---- | ------- |
| bool | `true`  |