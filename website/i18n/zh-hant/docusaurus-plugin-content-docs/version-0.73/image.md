---
id: image
title: Image
---

一個用於顯示不同類型圖片的 React 組件，包括網絡圖片、靜態資源、臨時本地圖片以及來自本地磁盤的圖片（例如相機膠卷）。

此範例展示如何從本地存儲加載並顯示圖片，以及從網絡和以 `'data:'` URI 方案提供的數據中加載圖片。

> 請注意，對於網絡和數據圖片，您需要手動指定圖片的尺寸！

## 範例

```SnackPlayer name=Example
import React from 'react';
import {View, Image, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    paddingTop: 50,
  },
  tinyLogo: {
    width: 50,
    height: 50,
  },
  logo: {
    width: 66,
    height: 58,
  },
});

const DisplayAnImage = () => {
  return (
    <View style={styles.container}>
      <Image
        style={styles.tinyLogo}
        source={require('@expo/snack-static/react-native-logo.png')}
      />
      <Image
        style={styles.tinyLogo}
        source={{
          uri: 'https://reactnative.dev/img/tiny_logo.png',
        }}
      />
      <Image
        style={styles.logo}
        source={{
          uri: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg==',
        }}
      />
    </View>
  );
};

export default DisplayAnImage;
```

您也可以為圖片添加 `style`：

```SnackPlayer name=Example
import React from 'react';
import {View, Image, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    paddingTop: 50,
  },
  stretch: {
    width: 50,
    height: 200,
    resizeMode: 'stretch',
  },
});

const DisplayAnImageWithStyle = () => {
  return (
    <View style={styles.container}>
      <Image
        style={styles.stretch}
        source={require('@expo/snack-static/react-native-logo.png')}
      />
    </View>
  );
};

export default DisplayAnImageWithStyle;
```

## Android 上的 GIF 和 WebP 支持

在構建自己的原生代碼時，Android 默認不支持 GIF 和 WebP。

您需要根據應用需求，在 `android/app/build.gradle` 中添加一些可選模塊。

```groovy
dependencies {
  // If your app supports Android versions before Ice Cream Sandwich (API level 14)
  implementation 'com.facebook.fresco:animated-base-support:1.3.0'

  // For animated GIF support
  implementation 'com.facebook.fresco:animated-gif:2.5.0'

  // For WebP support, including animated WebP
  implementation 'com.facebook.fresco:animated-webp:2.5.0'
  implementation 'com.facebook.fresco:webpsupport:2.5.0'

  // For WebP support, without animations
  implementation 'com.facebook.fresco:webpsupport:2.5.0'
}
```

> 注意：上述列出的版本可能未及時更新。請查閱主倉庫中的 [`ReactAndroid/gradle.properties`](https://github.com/facebook/react-native/blob/main/packages/react-native/ReactAndroid/gradle.properties) 以查看特定標記版本中使用的 fresco 版本。

---

# 參考

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view#props)。

---

### `accessible`

當為 true 時，表示該圖片是一個可訪問性元素。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `accessibilityLabel`

屏幕閱讀器在用戶與圖片交互時讀取的文本。

| Type   |
| ------ |
| string |

---

### `alt`

定義圖片替代文本描述的字符串，屏幕閱讀器將在用戶與圖片交互時讀取該文本。使用此屬性會自動將此元素標記為可訪問。

| Type   |
| ------ |
| string |

---

### `blurRadius`

blurRadius：添加到圖片的模糊濾鏡的模糊半徑。

| Type   |
| ------ |
| number |

> 提示：在 IOS 上，您需要將 `blurRadius` 設置為大於 `5` 的值。

---

### `capInsets` <div class="label ios">iOS</div>

當圖片調整大小時，由 `capInsets` 指定大小的角落將保持固定大小，但圖片的中心內容和邊框將被拉伸。這對於創建可調整大小的圓角按鈕、陰影和其他可調整大小的資源非常有用。更多信息請參閱 [官方 Apple 文檔](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImage_Class/index.html#//apple_ref/occ/instm/UIImage/resizableImageWithCapInsets)。

| Type         |
| ------------ |
| [Rect](rect) |

---

### `crossOrigin`

一個指定在獲取圖片資源時使用的 CORS 模式的關鍵字字符串。其功能類似於 HTML 中的 crossorigin 屬性。

- `anonymous`：在圖片請求中不交換用戶憑證。
- `use-credentials`：在圖片請求中將 `Access-Control-Allow-Credentials` 標頭值設置為 `true`。

| Type                                     | Default       |
| ---------------------------------------- | ------------- |
| enum(`'anonymous'`, `'use-credentials'`) | `'anonymous'` |

---

### `defaultSource`

在加載圖片源時顯示的靜態圖片。

| Type                             |
| -------------------------------- |
| [ImageSource](image#imagesource) |

> **注意：** 在 Android 上，debug 版本會忽略預設的 source 屬性。

---

### `fadeDuration` <div class="label android">Android</div>

淡入淡出動畫的持續時間（毫秒）。

| Type   | Default |
| ------ | ------- |
| number | `300`   |

---

### `height`

圖片元件的高度。

| Type   |
| ------ |
| number |

---

### `loadingIndicatorSource`

與 `source` 類似，此屬性代表用於渲染圖片載入指示器的資源。載入指示器會顯示直到圖片準備好顯示，通常是在圖片下載完成後。

| Type                                                  |
| ----------------------------------------------------- |
| [ImageSource](image#imagesource) (`uri` only), number |

---

### `onError`

載入錯誤時觸發。

| Type                                |
| ----------------------------------- |
| (`{nativeEvent: {error} }`) => void |

---

### `onLayout`

在掛載時和佈局變化時觸發。

| Type                                                    |
| ------------------------------------------------------- |
| `md ({nativeEvent: [LayoutEvent](layoutevent)} => void` |

---

### `onLoad`

載入成功完成時觸發。

**範例：** `onLoad={({nativeEvent: {source: {width, height}}}) => setImageRealSize({width, height})}`

| Type                                                                |
| ------------------------------------------------------------------- |
| `md ({nativeEvent: [ImageLoadEvent](image#imageloadevent)} => void` |

---

### `onLoadEnd`

無論載入成功或失敗時都會觸發。

| Type       |
| ---------- |
| () => void |

---

### `onLoadStart`

載入開始時觸發。

**範例：** `onLoadStart={() => this.setState({loading: true})}`

| Type       |
| ---------- |
| () => void |

---

### `onPartialLoad` <div class="label ios">iOS</div>

當圖片部分載入完成時觸發。「部分載入」的定義取決於載入器，但這通常用於漸進式 JPEG 載入。

| Type       |
| ---------- |
| () => void |

---

### `onProgress`

下載進度更新時觸發。

| Type                                        |
| ------------------------------------------- |
| (`{nativeEvent: {loaded, total} }`) => void |

---

### `progressiveRenderingEnabled` <div class="label android">Android</div>

設為 `true` 時，啟用漸進式 JPEG 串流 - https://frescolib.org/docs/progressive-jpegs。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `resizeMethod` <div class="label android">Android</div>

當圖片的尺寸與圖片視圖的尺寸不同時，用於調整圖片大小的機制。預設為 `auto`。

- `auto`：使用啟發式方法在 `resize` 和 `scale` 之間選擇。

- `resize`：一種軟體操作，在解碼前改變記憶體中的編碼圖片。當圖片比視圖大得多時，應使用此方法而非 `scale`。

- `scale`：圖片被縮小或放大繪製。與 `resize` 相比，`scale` 更快（通常是硬體加速）並產生更高品質的圖片。如果圖片比視圖小，應使用此方法。如果圖片略大於視圖，也應使用此方法。

有關 `resize` 和 `scale` 的更多細節，請參閱 https://frescolib.org/docs/resizing。

| Type                                  | Default  |
| ------------------------------------- | -------- |
| enum(`'auto'`, `'resize'`, `'scale'`) | `'auto'` |

---

### `referrerPolicy`

一個字串，指示在獲取資源時使用哪個引用來源。設定圖片請求中 `Referrer-Policy` 標頭的值，功能類似於 HTML 中的 `referrerpolicy` 屬性。

| Type                                                                                                                                                                                     | Default                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| enum(`'no-referrer'`, `'no-referrer-when-downgrade'`, `'origin'`, `'origin-when-cross-origin'`, `'same-origin'`, `'strict-origin'`, `'strict-origin-when-cross-origin'`, `'unsafe-url'`) | `'strict-origin-when-cross-origin'` |

---

### `resizeMode`

決定當框架與原始圖片尺寸不符時如何調整圖片大小。預設為 `cover`。

- `cover`: 均勻縮放圖片（保持圖片長寬比），使：
  
  - 圖片的兩個維度（寬度和高度）都等於或大於視圖的相應維度（減去內邊距）
  - 縮放後的圖片至少有一個維度與視圖的相應維度（減去內邊距）相等

- `contain`: 均勻縮放圖片（保持圖片長寬比），使圖片的兩個維度（寬度和高度）都等於或小於視圖的相應維度（減去內邊距）。

- `stretch`: 獨立縮放寬度和高度，這可能會改變原始圖片的長寬比。

- `repeat`: 重複圖片以覆蓋視圖的框架。圖片將保持其大小和長寬比，除非它大於視圖，在這種情況下它將被均勻縮小以使其包含在視圖中。

- `center`: 在視圖的兩個維度上居中圖片。如果圖片大於視圖，則均勻縮小以使其包含在視圖中。

| Type                                                              | Default   |
| ----------------------------------------------------------------- | --------- |
| enum(`'cover'`, `'contain'`, `'stretch'`, `'repeat'`, `'center'`) | `'cover'` |

---

### `source`

圖片來源（可以是遠程 URL 或本地文件資源）。

此屬性也可以包含多個遠程 URL，並指定它們的寬度和高度，以及可能的縮放比例/其他 URI 參數。原生端將根據圖片容器的測量大小選擇最佳的 `uri` 來顯示。可以添加 `cache` 屬性來控制網絡請求如何與本地緩存交互。（更多信息請參見[圖片的緩存控制（僅限 iOS）](images#cache-control-ios-only)）。

目前支持的格式有 `png`、`jpg`、`jpeg`、`bmp`、`gif`、`webp`、`psd`（僅限 iOS）。此外，iOS 支持多種 RAW 圖片格式。請參考 Apple 的文檔以獲取當前支持的相機型號列表（對於 iOS 12，請參見 https://support.apple.com/en-ca/HT208967）。

| Type                             |
| -------------------------------- |
| [ImageSource](image#imagesource) |

---

### `src`

一個表示圖片遠程 URL 的字串。此屬性優先於 `source` 屬性。

**範例:** `src={'https://reactnative.dev/img/tiny_logo.png'}`

| Type   |
| ------ |
| string |

---

### `srcSet`

一個表示逗號分隔的候選圖片來源列表的字串。每個圖片來源包含一個圖片的 URL 和一個像素密度描述符。如果未指定描述符，則默認為 `1x` 描述符。

如果 `srcSet` 不包含 `1x` 描述符，則使用 `src` 中的值作為具有 `1x` 描述符的圖片來源（如果提供）。

此屬性優先於 `src` 和 `source` 屬性。

**範例:** `srcSet={'https://reactnative.dev/img/tiny_logo.png 1x, https://reactnative.dev/img/header_logo.svg 2x'}`

| Type   |
| ------ |
| string |

---

### `style`

| Type                                                                                                                                                 |
| ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Image Style Props](image-style-props#props), [Layout Props](layout-props#props), [Shadow Props](shadow-props#props), [Transforms](transforms#props) |

---

### `testID`

用於 UI 自動化測試腳本中識別此元素的唯一標識符。

| Type   |
| ------ |
| string |

---

### `tintColor`

將所有非透明像素的顏色更改為 `tintColor`。

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `width`

圖片元件的寬度。

| Type   |
| ------ |
| number |

## 方法

### `abortPrefetch()` <div class="label android">Android</div>

```tsx
static abortPrefetch(requestId: number);
```

中止預取請求。

**參數：**

| Name                                                       | Type   | Description                             |
| ---------------------------------------------------------- | ------ | --------------------------------------- |
| requestId <div class="label basic required">Required</div> | number | Request id as returned by `prefetch()`. |

---

### `getSize()`

```tsx
static getSize(
  uri: string,
  success: (width: number, height: number) => void,
  failure?: (error: any) => void,
): any;
```

在顯示圖片之前獲取其寬度和高度（以像素為單位）。如果無法找到圖片或下載失敗，此方法可能會失敗。

為了獲取圖片的尺寸，可能需要先加載或下載圖片，之後圖片會被緩存。這意味著原則上你可以使用此方法來預加載圖片，但此方法並非為此目的優化，未來可能會以不完全加載/下載圖片數據的方式實現。預加載圖片的正確、支持方式將作為單獨的API提供。

**參數：**

| <div className="wideColumn">Name</div>                   | Type     | Description                                                                                          |
| -------------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------- |
| uri <div class="label basic required">Required</div>     | string   | The location of the image.                                                                           |
| success <div class="label basic required">Required</div> | function | The function that will be called if the image was successfully found and width and height retrieved. |
| failure                                                  | function | The function that will be called if there was an error, such as failing to retrieve the image.       |

---

### `getSizeWithHeaders()`

```tsx
static getSizeWithHeaders(
  uri: string,
  headers: {[index: string]: string},
  success: (width: number, height: number) => void,
  failure?: (error: any) => void,
): any;
```

在顯示圖片之前獲取其寬度和高度（以像素為單位），並能夠提供請求的標頭。如果無法找到圖片或下載失敗，此方法可能會失敗。此方法也不適用於靜態圖片資源。

為了獲取圖片的尺寸，可能需要先加載或下載圖片，之後圖片會被緩存。這意味著原則上你可以使用此方法來預加載圖片，但此方法並非為此目的優化，未來可能會以不完全加載/下載圖片數據的方式實現。預加載圖片的正確、支持方式將作為單獨的API提供。

**參數：**

| <div className="wideColumn">Name</div>                   | Type     | Description                                                                                          |
| -------------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------- |
| uri <div class="label basic required">Required</div>     | string   | The location of the image.                                                                           |
| headers <div class="label basic required">Required</div> | object   | The headers for the request.                                                                         |
| success <div class="label basic required">Required</div> | function | The function that will be called if the image was successfully found and width and height retrieved. |
| failure                                                  | function | The function that will be called if there was an error, such as failing to retrieve the image.       |

---

### `prefetch()`

```tsx
await Image.prefetch(url);
```

預取遠程圖片以供後續使用，將其下載到磁盤緩存中。返回一個解析為布爾值的Promise。

**參數：**

| Name                                                 | Type                                              | Description                                            |
| ---------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------------ |
| url <div class="label basic required">Required</div> | string                                            | The remote location of the image.                      |
| callback                                             | function <div class="label android">Android</div> | The function that will be called with the `requestId`. |

---

### `queryCache()`

```tsx
static queryCache(
  urls: string[],
): Promise<Record<string, 'memory' | 'disk' | 'disk/memory'>>;
```

執行緩存查詢。返回一個Promise，解析為從URL到緩存狀態的映射，例如"disk"、"memory"或"disk/memory"。如果請求的URL不在映射中，則表示它不在緩存中。

**參數：**

| Name                                                  | Type  | Description                                |
| ----------------------------------------------------- | ----- | ------------------------------------------ |
| urls <div class="label basic required">Required</div> | array | List of image URLs to check the cache for. |

---

### `resolveAssetSource()`

```tsx
static resolveAssetSource(source: ImageSourcePropType): {
  height: number;
  width: number;
  scale: number;
  uri: string;
};
```

將資源引用解析為一個對象，該對象具有`uri`、`scale`、`width`和`height`屬性。

**參數：**

| <div className="wideColumn">Name</div>                  | Type                                     | Description                                                                  |
| ------------------------------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------- |
| source <div class="label basic required">Required</div> | [ImageSource](image#imagesource), number | A number (opaque type returned by `require('./foo.png')`) or an ImageSource. |

## 類型定義

### ImageCacheEnum <div class="label ios">iOS</div>

用於設置潛在緩存響應的緩存處理或策略的枚舉。

| Type                                                               | Default     |
| ------------------------------------------------------------------ | ----------- |
| enum(`'default'`, `'reload'`, `'force-cache'`, `'only-if-cached'`) | `'default'` |

- `default`: 使用原生平台的預設策略。
- `reload`: URL 的資料將從原始來源載入。不應使用任何現有的快取資料來滿足 URL 載入請求。
- `force-cache`: 無論快取資料的新舊或過期日期為何，都將使用現有的快取資料來滿足請求。如果快取中沒有對應請求的現有資料，則從原始來源載入資料。
- `only-if-cached`: 無論快取資料的新舊或過期日期為何，都將使用現有的快取資料來滿足請求。如果快取中沒有對應 URL 載入請求的現有資料，則不會嘗試從原始來源載入資料，並視為載入失敗。

### ImageLoadEvent

在 `onLoad` 回調中返回的物件。

| Type   |
| ------ |
| object |

**屬性:**

| Name   | Type   | Description                         |
| ------ | ------ | ----------------------------------- |
| source | object | The [source object](#source-object) |

#### 來源物件

**屬性:**

| Name   | Type   | Description                                                  |
| ------ | ------ | ------------------------------------------------------------ |
| width  | number | The width of loaded image.                                   |
| height | number | The height of loaded image.                                  |
| uri    | string | A string representing the resource identifier for the image. |

### ImageSource

| Type                             |
| -------------------------------- |
| object, array of objects, number |

**屬性（如果傳遞物件或物件陣列）:**

| <div className="wideColumn">Name</div> | Type                                       | Description                                                                                                                                                                          |
| -------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| uri                                    | string                                     | A string representing the resource identifier for the image, which could be an http address, a local file path, or the name of a static image resource.                              |
| width                                  | number                                     | Can be specified if known at build time, in which case the value will be used to set the default `<Image/>` component dimension.                                                     |
| height                                 | number                                     | Can be specified if known at build time, in which case the value will be used to set the default `<Image/>` component dimension.                                                     |
| scale                                  | number                                     | Used to indicate the scale factor of the image. Defaults to `1.0` if unspecified, meaning that one image pixel equates to one display point / DIP.                                   |
| bundle<div class="label ios">iOS</div> | string                                     | The iOS asset bundle which the image is included in. This will default to `[NSBundle mainBundle]` if not set.                                                                        |
| method                                 | string                                     | The HTTP Method to use. Defaults to `'GET'` if not specified.                                                                                                                        |
| headers                                | object                                     | An object representing the HTTP headers to send along with the request for a remote image.                                                                                           |
| body                                   | string                                     | The HTTP body to send with the request. This must be a valid UTF-8 string, and will be sent exactly as specified, with no additional encoding (e.g. URL-escaping or base64) applied. |
| cache<div class="label ios">iOS</div>  | [ImageCacheEnum](image#imagecacheenum-ios) | Determines how the requests handles potentially cached responses.                                                                                                                    |

**如果傳遞數字:**

- `number` - 由類似 `require('./image.jpg')` 返回的不透明類型。