---
id: share
title: Share
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 範例

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Example&supportedPlatforms=ios,android&ext=js
import React from 'react';
import {Alert, Share, Button} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const ShareExample = () => {
  const onShare = async () => {
    try {
      const result = await Share.share({
        message:
          'React Native | A framework for building native apps using React',
      });
      if (result.action === Share.sharedAction) {
        if (result.activityType) {
          // shared with activity type of result.activityType
        } else {
          // shared
        }
      } else if (result.action === Share.dismissedAction) {
        // dismissed
      }
    } catch (error) {
      Alert.alert(error.message);
    }
  };
  return (
    <SafeAreaProvider>
      <SafeAreaView>
        <Button onPress={onShare} title="Share" />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

export default ShareExample;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Example&supportedPlatforms=ios,android&ext=tsx
import React from 'react';
import {Alert, Share, Button} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const ShareExample = () => {
  const onShare = async () => {
    try {
      const result = await Share.share({
        message:
          'React Native | A framework for building native apps using React',
      });
      if (result.action === Share.sharedAction) {
        if (result.activityType) {
          // shared with activity type of result.activityType
        } else {
          // shared
        }
      } else if (result.action === Share.dismissedAction) {
        // dismissed
      }
    } catch (error: any) {
      Alert.alert(error.message);
    }
  };
  return (
    <SafeAreaProvider>
      <SafeAreaView>
        <Button onPress={onShare} title="Share" />
      </SafeAreaView>
    </SafeAreaProvider>
  );
};

export default ShareExample;
```

</TabItem>
</Tabs>

# 參考文獻

## 方法

### `share()`

```tsx
static share(content: ShareContent, options?: ShareOptions);
```

開啟對話框以分享文字內容。

在 iOS 中，返回一個 Promise，該 Promise 將被調用並帶有一個包含 `action` 和 `activityType` 的物件。如果用戶關閉了對話框，Promise 仍將被解析，其中 action 為 `Share.dismissedAction`，其他所有鍵均為未定義。請注意，某些分享選項在 iOS 模擬器上不會出現或無法使用。

在 Android 中，返回一個 Promise，該 Promise 將始終被解析，其中 action 為 `Share.sharedAction`。

**屬性：**

| Name                                                         | Type   | Description                                                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| content <div className="label basic required">Required</div> | object | `message` - a message to share<br/>`url` - a URL to share <div class="label ios">iOS</div><br/>`title` - title of the message <div class="label android">Android</div><hr/>At least one of `url` and `message` is required.                                                                                                                                              |
| options                                                      | object | `dialogTitle` <div class="label android">Android</div><br/>`excludedActivityTypes` <div class="label ios">iOS</div><br/>`subject` - a subject to share via email <div class="label ios">iOS</div><br/>`tintColor` <div class="label ios">iOS</div><br/>`anchor` - the node to which the action sheet should be anchored (used for iPad) <div class="label ios">iOS</div> |

---

## 屬性

### `sharedAction`

```tsx
static sharedAction: 'sharedAction';
```

內容已成功分享。

---

### `dismissedAction` <div class="label ios">iOS</div>

```tsx
static dismissedAction: 'dismissedAction';
```

對話框已被關閉。