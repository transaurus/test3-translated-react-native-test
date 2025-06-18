---
id: share
title: Share
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

## 範例

<Tabs groupId="syntax" queryString defaultValue={constants.defaultSyntax} values={constants.syntax}>
<TabItem value="functional">

```SnackPlayer name=Function%20Component%20Example&supportedPlatforms=ios,android
import React from 'react';
import { Share, View, Button } from 'react-native';

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
      alert(error.message);
    }
  };
  return (
    <View style={{ marginTop: 50 }}>
      <Button onPress={onShare} title="Share" />
    </View>
  );
};

export default ShareExample;
```

</TabItem>
<TabItem value="classical">

```SnackPlayer name=Class%20Component%20Example&supportedPlatforms=ios,android
import React, { Component } from 'react';
import { Share, View, Button } from 'react-native';

class ShareExample extends Component {
  onShare = async () => {
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
      alert(error.message);
    }
  };

  render() {
    return (
      <View style={{ marginTop: 50 }}>
        <Button onPress={this.onShare} title="Share" />
      </View>
    );
  }
}

export default ShareExample;
```

</TabItem>
</Tabs>

# 參考文獻

## 方法

### `share()`

```jsx
static share(content, options)
```

開啟對話框以分享文字內容。

在 iOS 中，回傳一個 Promise，該 Promise 將以包含 `action` 和 `activityType` 的物件被調用。如果用戶關閉對話框，Promise 仍會以 action 為 `Share.dismissedAction` 且其他鍵為未定義的方式解析。請注意，某些分享選項在 iOS 模擬器上不會顯示或運作。

在 Android 中，回傳一個 Promise，該 Promise 始終以 action 為 `Share.sharedAction` 的方式解析。

**屬性：**

| Name                                                         | Type   | Description                                                                                                                                                                                                                                        |
| ------------------------------------------------------------ | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| content <div className="label basic required">Required</div> | object | `message` - a message to share<br/>`url` - a URL to share <div class="label ios">iOS</div><br/>`title` - title of the message <div class="label android">Android</div><hr/>At least one of `url` and `message` is required.                        |
| options                                                      | object | `dialogTitle` <div class="label android">Android</div><br/>`excludedActivityTypes` <div class="label ios">iOS</div><br/>`subject` - a subject to share via email <div class="label ios">iOS</div><br/>`tintColor` <div class="label ios">iOS</div> |

---

## 屬性

### `sharedAction`

```jsx
static sharedAction
```

內容已成功分享。

---

### `dismissedAction` <div class="label ios">iOS</div>

```jsx
static dismissedAction
```

對話框已被關閉。