---
id: modal
title: Modal
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

Modal 元件是一種在封裝視圖上方呈現內容的基本方式。

## 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Modal&supportedPlatforms=android,ios
import React, {useState} from 'react';
import {Alert, Modal, StyleSheet, Text, Pressable, View} from 'react-native';

const App = () => {
  const [modalVisible, setModalVisible] = useState(false);
  return (
    <View style={styles.centeredView}>
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
    </View>
  );
};

const styles = StyleSheet.create({
  centeredView: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    marginTop: 22,
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

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Modal&supportedPlatforms=android,ios
import React, {Component} from 'react';
import {Alert, Modal, StyleSheet, Text, Pressable, View} from 'react-native';

class App extends Component {
  state = {
    modalVisible: false,
  };

  render() {
    const {modalVisible} = this.state;
    return (
      <View style={styles.centeredView}>
        <Modal
          animationType="slide"
          transparent={true}
          visible={modalVisible}
          onRequestClose={() => {
            Alert.alert('Modal has been closed.');
            this.setState({modalVisible: !modalVisible});
          }}>
          <View style={styles.centeredView}>
            <View style={styles.modalView}>
              <Text style={styles.modalText}>Hello World!</Text>
              <Pressable
                style={[styles.button, styles.buttonClose]}
                onPress={() => this.setState({modalVisible: !modalVisible})}>
                <Text style={styles.textStyle}>Hide Modal</Text>
              </Pressable>
            </View>
          </View>
        </Modal>
        <Pressable
          style={[styles.button, styles.buttonOpen]}
          onPress={() => this.setState({modalVisible: true})}>
          <Text style={styles.textStyle}>Show Modal</Text>
        </Pressable>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  centeredView: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    marginTop: 22,
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

</TabItem>
</Tabs>

---

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承自 [View 屬性](view.md#props)。

---

### `animated`

> **已棄用。** 請改用 [`animationType`](modal.md#animationtype) 屬性。

---

### `animationType`

`animationType` 屬性控制模態框的動畫方式。

可能的值：

- `slide` 從底部滑入，
- `fade` 淡入視圖，
- `none` 無動畫直接出現。

| Type                                | Default |
| ----------------------------------- | ------- |
| enum(`'none'`, `'slide'`, `'fade'`) | `none`  |

---

### `hardwareAccelerated` <div class="label android">Android</div>

`hardwareAccelerated` 屬性控制是否強制底層視窗使用硬體加速。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `onDismiss` <div class="label ios">iOS</div>

`onDismiss` 屬性允許傳遞一個函式，該函式將在模態框被關閉後呼叫。

| Type     |
| -------- |
| function |

---

### `onOrientationChange` <div class="label ios">iOS</div>

`onOrientationChange` 回調函式會在模態框顯示期間方向改變時被呼叫。提供的方向僅為 'portrait' 或 'landscape'。此回調也會在初始渲染時呼叫，無論當前方向為何。

| Type     |
| -------- |
| function |

---

### `onRequestClose`

`onRequestClose` 回調函式會在 Android 裝置上使用者點擊硬體返回按鈕或 Apple TV 上點擊選單按鈕時被呼叫。由於此屬性是必需的，請注意只要模態框處於開啟狀態，`BackHandler` 事件將不會被觸發。
在 iOS 上，當 `presentationStyle` 為 `pageSheet` 或 `formSheet` 時，此回調會在透過拖曳手勢關閉模態框時被呼叫。

| Type                                                                                                                                                                                           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| function <div className="label basic required">Required</div><div className="label android">Android</div><div className="label tv">TV</div><hr />function <div className="label ios">iOS</div> |

---

### `onShow`

`onShow` 屬性允許傳遞一個函式，該函式將在模態框顯示後被呼叫。

| Type     |
| -------- |
| function |

---

### `presentationStyle` <div class="label ios">iOS</div>

`presentationStyle` 屬性控制模態框的顯示方式（通常在較大的裝置上，如 iPad 或加大版 iPhone）。詳情請參閱 https://developer.apple.com/reference/uikit/uimodalpresentationstyle。

可能的值：

- `fullScreen` 完全覆蓋整個螢幕
- `pageSheet` 在較大裝置上以直向寬度居中顯示
- `formSheet` 在較大裝置上以窄寬度居中顯示
- `overFullScreen` 完全覆蓋螢幕，但允許透明效果

| Type                                                                   | Default                                                                             |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| enum(`'fullScreen'`, `'pageSheet'`, `'formSheet'`, `'overFullScreen'`) | `fullScreen` if `transparent={false}`<hr />`overFullScreen` if `transparent={true}` |

---

### `statusBarTranslucent` <div class="label android">Android</div>

`statusBarTranslucent` 屬性決定模態框是否應延伸至系統狀態列下方。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `supportedOrientations` <div class="label ios">iOS</div>

`supportedOrientations` 屬性允許模態視窗旋轉至指定的任何方向。在 iOS 上，模態視窗仍會受到應用程式 Info.plist 中 UISupportedInterfaceOrientations 欄位所指定內容的限制。

> 當使用 `pageSheet` 或 `formSheet` 的 `presentationStyle` 時，iOS 將忽略此屬性。

| Type                                                                                                           | Default        |
| -------------------------------------------------------------------------------------------------------------- | -------------- |
| array of enums(`'portrait'`, `'portrait-upside-down'`, `'landscape'`, `'landscape-left'`, `'landscape-right'`) | `['portrait']` |

---

### `transparent`

`transparent` 屬性決定模態視窗是否會填滿整個視圖。設為 `true` 時，模態視窗將在透明背景上渲染。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `visible`

`visible` 屬性決定模態視窗是否可見。

| Type | Default |
| ---- | ------- |
| bool | `true`  |