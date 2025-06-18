---
id: permissionsandroid
title: PermissionsAndroid
---

<div className="banner-native-code-required">
  <h3>Project with Native Code Required</h3>
  <p>The following section only applies to projects with native code exposed. If you are using the managed Expo workflow, see the guide on <a href="https://docs.expo.dev/guides/permissions/">Permissions</a> in the Expo documentation for the appropriate alternative.</p>
</div>

`PermissionsAndroid` 提供了對 Android M 新權限模型的存取權限。所謂「一般」權限會在應用程式安裝時自動授予，只要它們出現在 `AndroidManifest.xml` 中即可。然而，「危險」權限需要透過對話框提示來取得。您應該使用此模組來處理這些權限。

在 SDK 版本 23 之前的裝置上，只要權限出現在清單中就會自動授予，因此 `check` 應該總是回傳 `true`，而 `request` 應該總是解析為 `PermissionsAndroid.RESULTS.GRANTED`。

如果使用者之前關閉了您請求的權限，作業系統會建議您的應用程式顯示需要該權限的理由。可選的 `rationale` 參數僅在必要時顯示對話框提示，否則將顯示正常的權限提示。

### 範例

```SnackPlayer name=PermissionsAndroid%20Example&supportedPlatforms=android
import React from 'react';
import {
  Button,
  PermissionsAndroid,
  StatusBar,
  StyleSheet,
  Text,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const requestCameraPermission = async () => {
  try {
    const granted = await PermissionsAndroid.request(
      PermissionsAndroid.PERMISSIONS.CAMERA,
      {
        title: 'Cool Photo App Camera Permission',
        message:
          'Cool Photo App needs access to your camera ' +
          'so you can take awesome pictures.',
        buttonNeutral: 'Ask Me Later',
        buttonNegative: 'Cancel',
        buttonPositive: 'OK',
      },
    );
    if (granted === PermissionsAndroid.RESULTS.GRANTED) {
      console.log('You can use the camera');
    } else {
      console.log('Camera permission denied');
    }
  } catch (err) {
    console.warn(err);
  }
};

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Text style={styles.item}>Try permissions</Text>
      <Button title="request permissions" onPress={requestCameraPermission} />
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    paddingTop: StatusBar.currentHeight,
    backgroundColor: '#ecf0f1',
    padding: 8,
  },
  item: {
    margin: 24,
    fontSize: 18,
    fontWeight: 'bold',
    textAlign: 'center',
  },
});

export default App;
```

### 需要提示使用者的權限

可透過 `PermissionsAndroid.PERMISSIONS` 下的常數取得：

- `READ_CALENDAR`: 'android.permission.READ_CALENDAR'
- `WRITE_CALENDAR`: 'android.permission.WRITE_CALENDAR'
- `CAMERA`: 'android.permission.CAMERA'
- `READ_CONTACTS`: 'android.permission.READ_CONTACTS'
- `WRITE_CONTACTS`: 'android.permission.WRITE_CONTACTS'
- `GET_ACCOUNTS`: 'android.permission.GET_ACCOUNTS'
- `ACCESS_FINE_LOCATION`: 'android.permission.ACCESS_FINE_LOCATION'
- `ACCESS_COARSE_LOCATION`: 'android.permission.ACCESS_COARSE_LOCATION'
- `ACCESS_BACKGROUND_LOCATION`: 'android.permission.ACCESS_BACKGROUND_LOCATION'
- `RECORD_AUDIO`: 'android.permission.RECORD_AUDIO'
- `READ_PHONE_STATE`: 'android.permission.READ_PHONE_STATE'
- `CALL_PHONE`: 'android.permission.CALL_PHONE'
- `READ_CALL_LOG`: 'android.permission.READ_CALL_LOG'
- `WRITE_CALL_LOG`: 'android.permission.WRITE_CALL_LOG'
- `ADD_VOICEMAIL`: 'com.android.voicemail.permission.ADD_VOICEMAIL'
- `USE_SIP`: 'android.permission.USE_SIP'
- `PROCESS_OUTGOING_CALLS`: 'android.permission.PROCESS_OUTGOING_CALLS'
- `BODY_SENSORS`: 'android.permission.BODY_SENSORS'
- `SEND_SMS`: 'android.permission.SEND_SMS'
- `RECEIVE_SMS`: 'android.permission.RECEIVE_SMS'
- `READ_SMS`: 'android.permission.READ_SMS'
- `RECEIVE_WAP_PUSH`: 'android.permission.RECEIVE_WAP_PUSH'
- `RECEIVE_MMS`: 'android.permission.RECEIVE_MMS'
- `READ_EXTERNAL_STORAGE`: 'android.permission.READ_EXTERNAL_STORAGE'
- `WRITE_EXTERNAL_STORAGE`: 'android.permission.WRITE_EXTERNAL_STORAGE'
- `BLUETOOTH_CONNECT`: 'android.permission.BLUETOOTH_CONNECT'
- `BLUETOOTH_SCAN`: 'android.permission.BLUETOOTH_SCAN'
- `BLUETOOTH_ADVERTISE`: 'android.permission.BLUETOOTH_ADVERTISE'
- `ACCESS_MEDIA_LOCATION`: 'android.permission.ACCESS_MEDIA_LOCATION'
- `ACCEPT_HANDOVER`: 'android.permission.ACCEPT_HANDOVER'
- `ACTIVITY_RECOGNITION`: 'android.permission.ACTIVITY_RECOGNITION'
- `ANSWER_PHONE_CALLS`: 'android.permission.ANSWER_PHONE_CALLS'
- `READ_PHONE_NUMBERS`: 'android.permission.READ_PHONE_NUMBERS'
- `UWB_RANGING`: 'android.permission.UWB_RANGING'
- `BODY_SENSORS_BACKGROUND`: 'android.permission.BODY_SENSORS_BACKGROUND'
- `READ_MEDIA_IMAGES`: 'android.permission.READ_MEDIA_IMAGES'
- `READ_MEDIA_VIDEO`: 'android.permission.READ_MEDIA_VIDEO'
- `READ_MEDIA_AUDIO`: 'android.permission.READ_MEDIA_AUDIO'
- `POST_NOTIFICATIONS`: 'android.permission.POST_NOTIFICATIONS'
- `NEARBY_WIFI_DEVICES`: 'android.permission.NEARBY_WIFI_DEVICES'
- `READ_VOICEMAIL`: 'com.android.voicemail.permission.READ_VOICEMAIL',
- `WRITE_VOICEMAIL`: 'com.android.voicemail.permission.WRITE_VOICEMAIL',

### 請求權限的結果字串

可透過 `PermissionsAndroid.RESULTS` 下的常數取得：

- `GRANTED`: 'granted'
- `DENIED`: 'denied'
- `NEVER_ASK_AGAIN`: 'never_ask_again'

---

# 參考

## 方法

### `check()`

```tsx
static check(permission: Permission): Promise<boolean>;
```

回傳一個解析為布林值的 Promise，表示指定權限是否已被授予。

**參數：**

| Name       | Type   | Required | Description                  |
| ---------- | ------ | -------- | ---------------------------- |
| permission | string | Yes      | The permission to check for. |

---

### `request()`

```tsx
static request(
  permission: Permission,
  rationale?: Rationale,
): Promise<PermissionStatus>;
```

提示用戶啟用權限，並回傳一個解析為字串值的 Promise（參見上方的結果字串），表示用戶是允許、拒絕請求或選擇不再詢問。

若提供 `rationale`，此函式會向作業系統確認是否需要顯示對話框說明為何需要該權限（https://developer.android.com/training/permissions/requesting.html#explain），接著顯示系統權限對話框。

**參數：**

| Name       | Type   | Required | Description                |
| ---------- | ------ | -------- | -------------------------- |
| permission | string | Yes      | The permission to request. |
| rationale  | object | No       | See `rationale` below.     |

**Rationale：**

| Name           | Type   | Required | Description                      |
| -------------- | ------ | -------- | -------------------------------- |
| title          | string | Yes      | The title of the dialog.         |
| message        | string | Yes      | The message of the dialog.       |
| buttonPositive | string | Yes      | The text of the positive button. |
| buttonNegative | string | No       | The text of the negative button. |
| buttonNeutral  | string | No       | The text of the neutral button.  |

---

### `requestMultiple()`

```tsx
static requestMultiple(
  permissions: Permission[],
): Promise<{[key in Permission]: PermissionStatus}>;
```

在同一對話框中提示用戶啟用多個權限，並回傳一個物件，其鍵為權限名稱，值為字串（參見上方的結果字串），表示用戶是允許、拒絕請求或選擇不再詢問。

**參數：**

| Name        | Type  | Required | Description                      |
| ----------- | ----- | -------- | -------------------------------- |
| permissions | array | Yes      | Array of permissions to request. |