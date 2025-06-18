---
id: network
title: Networking
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

許多行動應用程式需要從遠端 URL 載入資源。您可能需要向 REST API 發送 POST 請求，或是從其他伺服器獲取靜態內容片段。

## 使用 Fetch

React Native 提供 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 來滿足網路請求需求。如果您曾使用過 `XMLHttpRequest` 或其他網路 API，會對 Fetch 感到熟悉。更多資訊可參考 MDN 的 [使用 Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) 指南。

### 發送請求

要從任意 URL 獲取內容，只需將 URL 傳遞給 fetch：

```tsx
fetch('https://mywebsite.com/mydata.json');
```

Fetch 還接受可選的第二個參數，用於自訂 HTTP 請求。您可以指定額外標頭或發送 POST 請求：

```tsx
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  }),
});
```

完整屬性列表請參閱 [Fetch Request 文檔](https://developer.mozilla.org/en-US/docs/Web/API/Request)。

### 處理回應

上述範例展示了如何發送請求。多數情況下，您會需要對回應進行處理。

網路請求本質上是非同步操作。Fetch 方法會返回一個 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)，讓您能輕鬆編寫非同步程式碼：

```tsx
const getMoviesFromApi = () => {
  return fetch('https://reactnative.dev/movies.json')
    .then(response => response.json())
    .then(json => {
      return json.movies;
    })
    .catch(error => {
      console.error(error);
    });
};
```

在 React Native 應用中也可使用 `async` / `await` 語法：

```tsx
const getMoviesFromApiAsync = async () => {
  try {
    const response = await fetch(
      'https://reactnative.dev/movies.json',
    );
    const json = await response.json();
    return json.movies;
  } catch (error) {
    console.error(error);
  }
};
```

請務必捕獲 `fetch` 可能拋出的錯誤，否則這些錯誤會被靜默忽略。

<Tabs groupId="language" queryString defaultValue={constants.defaultSnackLanguage} values={constants.snackLanguages}>
<TabItem value="javascript">

```SnackPlayer name=Fetch%20Example&ext=js
import React, {useEffect, useState} from 'react';
import {ActivityIndicator, FlatList, Text, View} from 'react-native';

const App = () => {
  const [isLoading, setLoading] = useState(true);
  const [data, setData] = useState([]);

  const getMovies = async () => {
    try {
      const response = await fetch('https://reactnative.dev/movies.json');
      const json = await response.json();
      setData(json.movies);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    getMovies();
  }, []);

  return (
    <View style={{flex: 1, padding: 24}}>
      {isLoading ? (
        <ActivityIndicator />
      ) : (
        <FlatList
          data={data}
          keyExtractor={({id}) => id}
          renderItem={({item}) => (
            <Text>
              {item.title}, {item.releaseYear}
            </Text>
          )}
        />
      )}
    </View>
  );
};

export default App;
```

</TabItem>
<TabItem value="typescript">

```SnackPlayer name=Fetch%20Example&ext=tsx
import React, {useEffect, useState} from 'react';
import {ActivityIndicator, FlatList, Text, View} from 'react-native';

type Movie = {
  id: string;
  title: string;
  releaseYear: string;
};

const App = () => {
  const [isLoading, setLoading] = useState(true);
  const [data, setData] = useState<Movie[]>([]);

  const getMovies = async () => {
    try {
      const response = await fetch('https://reactnative.dev/movies.json');
      const json = await response.json();
      setData(json.movies);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    getMovies();
  }, []);

  return (
    <View style={{flex: 1, padding: 24}}>
      {isLoading ? (
        <ActivityIndicator />
      ) : (
        <FlatList
          data={data}
          keyExtractor={({id}) => id}
          renderItem={({item}) => (
            <Text>
              {item.title}, {item.releaseYear}
            </Text>
          )}
        />
      )}
    </View>
  );
};

export default App;
```

</TabItem>
</Tabs>

> 預設情況下，iOS 9.0 及以上版本強制執行 App Transport Security (ATS)。ATS 要求所有 HTTP 連接必須使用 HTTPS。若需從明文字元 URL（以 `http` 開頭）獲取資料，您需要先 [添加 ATS 例外](integration-with-existing-apps.md#test-your-integration)。若事先知道需要存取的網域，僅為這些網域添加例外會更安全；若網域需在運行時才能確定，可 [完全禁用 ATS](publishing-to-app-store.md#1-enable-app-transport-security)。但請注意，自 2017 年 1 月起，[Apple App Store 審核會要求提供禁用 ATS 的合理理由](https://forums.developer.apple.com/thread/48979)。詳見 [Apple 文檔](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33)。

> 在 Android 上，API 等級 28 起也預設封鎖明文字元流量。可透過在應用清單文件中設定 [`android:usesCleartextTraffic`](https://developer.android.com/guide/topics/manifest/application-element#usesCleartextTraffic) 來覆寫此行為。

## 使用其他網路函式庫

[XMLHttpRequest API](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 已內建於 React Native。這意味著您可以使用依賴該 API 的第三方函式庫，如 [frisbee](https://github.com/niftylettuce/frisbee) 或 [axios](https://github.com/axios/axios)，也可以直接使用 XMLHttpRequest API。

```tsx
const request = new XMLHttpRequest();
request.onreadystatechange = e => {
  if (request.readyState !== 4) {
    return;
  }

  if (request.status === 200) {
    console.log('success', request.responseText);
  } else {
    console.warn('error');
  }
};

request.open('GET', 'https://mywebsite.com/endpoint/');
request.send();
```

> 原生應用中 XMLHttpRequest 的安全模型與網頁不同，因為不存在 [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 概念。

## WebSocket 支援

React Native 也支援 [WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)，這是一種透過單一 TCP 連線提供全雙工通訊通道的協定。

```tsx
const ws = new WebSocket('ws://host.com/path');

ws.onopen = () => {
  // connection opened
  ws.send('something'); // send a message
};

ws.onmessage = e => {
  // a message was received
  console.log(e.data);
};

ws.onerror = e => {
  // an error occurred
  console.log(e.message);
};

ws.onclose = e => {
  // connection closed
  console.log(e.code, e.reason);
};
```

## 已知 `fetch` 與基於 cookie 驗證的問題

以下選項目前在 `fetch` 中無法正常運作：

- `redirect:manual`
- `credentials:omit`

* 在 Android 上使用相同名稱的標頭會導致只有最後一個標頭被保留。臨時解決方案可參考：https://github.com/facebook/react-native/issues/18837#issuecomment-398779994。
* 基於 cookie 的驗證目前不穩定。您可以在這裡查看部分相關問題：https://github.com/facebook/react-native/issues/23185。
* 至少在 iOS 上，當透過 `302` 重新導向時，如果存在 `Set-Cookie` 標頭，cookie 不會被正確設定。由於無法手動處理重新導向，這可能導致會話過期時引發無限請求的狀況。

## 在 iOS 上配置 NSURLSession

對於某些應用程式，可能需要為 iOS 上運行的 React Native 應用程式中的底層 `NSURLSession` 提供自訂的 `NSURLSessionConfiguration`。例如，可能需要為應用程式的所有網路請求設定自訂的使用者代理字串，或為 `NSURLSession` 提供暫時性的 `NSURLSessionConfiguration`。函式 `RCTSetCustomNSURLSessionConfigurationProvider` 允許進行此類自訂。請記得在呼叫 `RCTSetCustomNSURLSessionConfigurationProvider` 的檔案中加入以下導入：

```objectivec
#import <React/RCTHTTPRequestHandler.h>
```

`RCTSetCustomNSURLSessionConfigurationProvider` 應在應用程式生命週期的早期呼叫，以便 React 在需要時能夠立即使用，例如：

```objectivec
-(void)application:(__unused UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

  // set RCTSetCustomNSURLSessionConfigurationProvider
  RCTSetCustomNSURLSessionConfigurationProvider(^NSURLSessionConfiguration *{
     NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
     // configure the session
     return configuration;
  });

  // set up React
  _bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:launchOptions];
}
```