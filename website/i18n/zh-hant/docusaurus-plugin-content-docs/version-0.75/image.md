---
id: image
title: Image
---

A React component for displaying different types of images, including network images, static resources, temporary local images, and images from local disk, such as the camera roll.

This example shows fetching and displaying an image from local storage as well as one from network and even from data provided in the `'data:'` uri scheme.

> Note that for network and data images, you will need to manually specify the dimensions of your image!

## Examples

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

You can also add `style` to an image:

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

## GIF and WebP support on Android

When building your own native code, GIF and WebP are not supported by default on Android.

You will need to add some optional modules in `android/app/build.gradle`, depending on the needs of your app.

```groovy
dependencies {
  // If your app supports Android versions before Ice Cream Sandwich (API level 14)
  implementation 'com.facebook.fresco:animated-base-support:1.3.0'

  // For animated GIF support
  implementation 'com.facebook.fresco:animated-gif:3.2.0'

  // For WebP support, including animated WebP
  implementation 'com.facebook.fresco:animated-webp:3.2.0'
  implementation 'com.facebook.fresco:webpsupport:3.2.0'

  // For WebP support, without animations
  implementation 'com.facebook.fresco:webpsupport:3.2.0'
}
```

> Note: the version listed above may not be updated in time. Please check [`packages/react-native/gradle/libs.versions.toml`](https://github.com/facebook/react-native/blob/main/packages/react-native/gradle/libs.versions.toml) in the main repo to see which fresco version is being used in a specific tagged version.

---

# Reference

## Props

### [View Props](view.md#props)

Inherits [View Props](view#props).

---

### `accessible`

When true, indicates the image is an accessibility element.

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `accessibilityLabel`

The text that's read by the screen reader when the user interacts with the image.

| Type   |
| ------ |
| string |

---

### `alt`

A string that defines an alternative text description of the image, which will be read by the screen reader when the user interacts with it. Using this will automatically mark this element as accessible.

| Type   |
| ------ |
| string |

---

### `blurRadius`

blurRadius: the blur radius of the blur filter added to the image.

| Type   |
| ------ |
| number |

> Tip: On IOS, you will need to increase `blurRadius` by more than `5`.

---

### `capInsets` <div class="label ios">iOS</div>

When the image is resized, the corners of the size specified by `capInsets` will stay a fixed size, but the center content and borders of the image will be stretched. This is useful for creating resizable rounded buttons, shadows, and other resizable assets. More info in the [official Apple documentation](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImage_Class/index.html#//apple_ref/occ/instm/UIImage/resizableImageWithCapInsets).

| Type         |
| ------------ |
| [Rect](rect) |

---

### `crossOrigin`

A string of a keyword specifying the CORS mode to use when fetching the image resource. It works similar to crossorigin attribute in HTML.

- `anonymous`: No exchange of user credentials in the image request.
- `use-credentials`: Sets `Access-Control-Allow-Credentials` header value to `true` in the image request.

| Type                                     | Default       |
| ---------------------------------------- | ------------- |
| enum(`'anonymous'`, `'use-credentials'`) | `'anonymous'` |

---

### `defaultSource`

A static image to display while loading the image source.

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

類似於 `source`，此屬性代表用於渲染圖片載入指示器的資源。載入指示器會顯示直到圖片準備好顯示，通常是在圖片下載完成後。

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

- `scale`：圖片會被縮小或放大繪製。與 `resize` 相比，`scale` 更快（通常是硬體加速）且產生更高品質的圖片。如果圖片比視圖小，應使用此方法。如果圖片比視圖稍大，也應使用此方法。

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

- `cover`: 均勻縮放圖片（保持圖片長寬比），使

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

圖片來源（可以是遠端 URL 或本地檔案資源）。

此屬性也可以包含多個遠端 URL，並指定它們的寬度和高度，以及可能的縮放比例/其他 URI 參數。原生端將根據圖片容器的測量大小選擇最佳的 `uri` 來顯示。可以添加 `cache` 屬性來控制網路請求如何與本地緩存交互。（更多資訊請參閱 [圖片緩存控制（僅限 iOS）](images#cache-control-ios-only)）。

目前支援的格式有 `png`、`jpg`、`jpeg`、`bmp`、`gif`、`webp`、`psd`（僅限 iOS）。此外，iOS 支援多種 RAW 圖片格式。請參考 Apple 的文件以獲取當前支援的相機型號列表（對於 iOS 12，請參閱 https://support.apple.com/en-ca/HT208967）。

| Type                             |
| -------------------------------- |
| [ImageSource](image#imagesource) |

---

### `src`

一個表示圖片遠端 URL 的字串。此屬性的優先級高於 `source` 屬性。

**範例:** `src={'https://reactnative.dev/img/tiny_logo.png'}`

| Type   |
| ------ |
| string |

---

### `srcSet`

一個表示逗號分隔的潛在候選圖片來源清單的字串。每個圖片來源包含圖片的 URL 和像素密度描述符。如果未指定描述符，則預設為 `1x` 描述符。

如果 `srcSet` 不包含 `1x` 描述符，則使用 `src` 中的值作為具有 `1x` 描述符的圖片來源（如果提供）。

此屬性的優先級高於 `src` 和 `source` 屬性。

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

Changes the color of all non-transparent pixels to the `tintColor`.

| Type               |
| ------------------ |
| [color](colors.md) |

---

### `width`

Width of the image component.

| Type   |
| ------ |
| number |

## Methods

### `abortPrefetch()` <div class="label android">Android</div>

```tsx
static abortPrefetch(requestId: number);
```

Abort prefetch request.

**Parameters:**

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

Retrieve the width and height (in pixels) of an image prior to displaying it. This method can fail if the image cannot be found, or fails to download.

In order to retrieve the image dimensions, the image may first need to be loaded or downloaded, after which it will be cached. This means that in principle you could use this method to preload images, however it is not optimized for that purpose, and may in future be implemented in a way that does not fully load/download the image data. A proper, supported way to preload images will be provided as a separate API.

**Parameters:**

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

Retrieve the width and height (in pixels) of an image prior to displaying it with the ability to provide the headers for the request. This method can fail if the image cannot be found, or fails to download. It also does not work for static image resources.

In order to retrieve the image dimensions, the image may first need to be loaded or downloaded, after which it will be cached. This means that in principle you could use this method to preload images, however it is not optimized for that purpose, and may in future be implemented in a way that does not fully load/download the image data. A proper, supported way to preload images will be provided as a separate API.

**Parameters:**

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

Prefetches a remote image for later use by downloading it to the disk cache. Returns a promise which resolves to a boolean.

**Parameters:**

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

Perform cache interrogation. Returns a promise which resolves to a mapping from URL to cache status, such as "disk", "memory" or "disk/memory". If a requested URL is not in the mapping, it means it's not in the cache.

**Parameters:**

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

Resolves an asset reference into an object which has the properties `uri`, `scale`, `width`, and `height`.

**Parameters:**

| <div className="wideColumn">Name</div>                  | Type                                     | Description                                                                  |
| ------------------------------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------- |
| source <div class="label basic required">Required</div> | [ImageSource](image#imagesource), number | A number (opaque type returned by `require('./foo.png')`) or an ImageSource. |

## Type Definitions

### ImageCacheEnum <div class="label ios">iOS</div>

Enum which can be used to set the cache handling or strategy for the potentially cached responses.

| Type                                                               | Default     |
| ------------------------------------------------------------------ | ----------- |
| enum(`'default'`, `'reload'`, `'force-cache'`, `'only-if-cached'`) | `'default'` |

- `default`: Use the native platforms default strategy.
- `reload`: The data for the URL will be loaded from the originating source. No existing cache data should be used to satisfy a URL load request.
- `force-cache`: The existing cached data will be used to satisfy the request, regardless of its age or expiration date. If there is no existing data in the cache corresponding the request, the data is loaded from the originating source.
- `only-if-cached`: The existing cache data will be used to satisfy a request, regardless of its age or expiration date. If there is no existing data in the cache corresponding to a URL load request, no attempt is made to load the data from the originating source, and the load is considered to have failed.

### ImageLoadEvent

Object returned in the `onLoad` callback.

| Type   |
| ------ |
| object |

**Properties:**

| Name   | Type   | Description                         |
| ------ | ------ | ----------------------------------- |
| source | object | The [source object](#source-object) |

#### Source Object

**Properties:**

| Name   | Type   | Description                                                  |
| ------ | ------ | ------------------------------------------------------------ |
| width  | number | The width of loaded image.                                   |
| height | number | The height of loaded image.                                  |
| uri    | string | A string representing the resource identifier for the image. |

### ImageSource

| Type                             |
| -------------------------------- |
| object, array of objects, number |

**Properties (if passing as object or array of objects):**

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

**If passing a number:**

- `number` - opaque type returned by something like `require('./image.jpg')`.