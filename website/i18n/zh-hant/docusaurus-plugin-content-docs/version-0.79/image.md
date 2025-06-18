---
id: image
title: Image
---

一個用於顯示不同類型圖片的 React 元件，包括網路圖片、靜態資源、臨時本地圖片以及來自本地磁碟的圖片（例如相機膠卷）。

此範例展示如何從本地儲存空間、網路甚至透過「data:」URI 方案提供的數據中獲取並顯示圖片。

> 請注意，對於網路和數據圖片，您需要手動指定圖片的尺寸！

## 範例

```SnackPlayer name=Image%20Example
import React from 'react';
import {Image, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const styles = StyleSheet.create({
  container: {
    flex: 1,
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

const DisplayAnImage = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
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
    </SafeAreaView>
  </SafeAreaProvider>
);

export default DisplayAnImage;
```

您也可以為圖片添加 `style`：

```SnackPlayer name=Styled%20Image%20Example
import React from 'react';
import {Image, StyleSheet} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  stretch: {
    width: 50,
    height: 200,
    resizeMode: 'stretch',
  },
});

const DisplayAnImageWithStyle = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <Image
        style={styles.stretch}
        source={require('@expo/snack-static/react-native-logo.png')}
      />
    </SafeAreaView>
  </SafeAreaProvider>
);

export default DisplayAnImageWithStyle;
```

## Android 上的 GIF 和 WebP 支援

在自行構建原生代碼時，Android 預設不支援 GIF 和 WebP 格式。

您需要根據應用需求，在 `android/app/build.gradle` 中添加一些可選模組。

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

> 注意：上述列出的版本可能未及時更新。請查閱主代碼庫中的 [`packages/react-native/gradle/libs.versions.toml`](https://github.com/facebook/react-native/blob/main/packages/react-native/gradle/libs.versions.toml) 以確認特定標籤版本使用的 fresco 版本。

---

# 參考文檔

## 屬性

### [View 屬性](view.md#props)

繼承 [View 屬性](view#props)。

---

### `accessible`

設為 true 時，表示該圖片是一個無障礙元素。

| Type | Default |
| ---- | ------- |
| bool | `false` |

---

### `accessibilityLabel`

當用戶與圖片互動時，螢幕閱讀器將朗讀此文字。

| Type   |
| ------ |
| string |

---

### `alt`

定義圖片替代文字描述的字符串，當用戶與圖片互動時將由螢幕閱讀器朗讀。使用此屬性會自動將該元素標記為可訪問。

| Type   |
| ------ |
| string |

---

### `blurRadius`

blurRadius：添加到圖片的模糊濾鏡的模糊半徑。

| Type   |
| ------ |
| number |

> 提示：在 iOS 上，您需要將 `blurRadius` 設置為大於 `5` 的值。

---

### `capInsets` <div class="label ios">iOS</div>

當圖片調整大小時，由 `capInsets` 指定的尺寸的角落將保持固定大小，但圖片的中心內容和邊框將被拉伸。這對於創建可調整大小的圓角按鈕、陰影和其他可調整大小的資源非常有用。更多資訊請參閱 [Apple 官方文檔](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImage_Class/index.html#//apple_ref/occ/instm/UIImage/resizableImageWithCapInsets)。

| Type         |
| ------------ |
| [Rect](rect) |

---

### `crossOrigin`

指定獲取圖片資源時使用的 CORS 模式的關鍵字字符串。其功能類似於 HTML 中的 crossorigin 屬性。

- `anonymous`：圖片請求中不交換用戶憑證。
- `use-credentials`：在圖片請求中將 `Access-Control-Allow-Credentials` 標頭值設為 `true`。

| Type                                     | Default       |
| ---------------------------------------- | ------------- |
| enum(`'anonymous'`, `'use-credentials'`) | `'anonymous'` |

---

### `defaultSource`

載入圖片來源時顯示的靜態圖片。

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

在載入錯誤時觸發。

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

在載入成功完成時觸發。

**範例：** `onLoad={({nativeEvent: {source: {width, height}}}) => setImageRealSize({width, height})}`

| Type                                                                |
| ------------------------------------------------------------------- |
| `md ({nativeEvent: [ImageLoadEvent](image#imageloadevent)} => void` |

---

### `onLoadEnd`

在載入成功或失敗時觸發。

| Type       |
| ---------- |
| () => void |

---

### `onLoadStart`

在載入開始時觸發。

**範例：** `onLoadStart={() => this.setState({loading: true})}`

| Type       |
| ---------- |
| () => void |

---

### `onPartialLoad` <div class="label ios">iOS</div>

在圖片部分載入完成時觸發。「部分載入」的定義取決於載入器，但這通常用於漸進式 JPEG 載入。

| Type       |
| ---------- |
| () => void |

---

### `onProgress`

在下載進度更新時觸發。

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

### `referrerPolicy`

一個字串，指示在獲取資源時使用哪個 referrer。設定圖片請求中 `Referrer-Policy` 標頭的值。功能類似於 HTML 中的 `referrerpolicy` 屬性。

| Type                                                                                                                                                                                     | Default                             |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| enum(`'no-referrer'`, `'no-referrer-when-downgrade'`, `'origin'`, `'origin-when-cross-origin'`, `'same-origin'`, `'strict-origin'`, `'strict-origin-when-cross-origin'`, `'unsafe-url'`) | `'strict-origin-when-cross-origin'` |

---

### `resizeMethod` <div class="label android">Android</div>

當圖片的尺寸與圖片視圖的尺寸不同時，用於調整圖片大小的機制。預設為 `auto`。

- `auto`：使用啟發式方法在 `resize` 和 `scale` 之間選擇。

- `resize`：一種軟體操作，在圖像解碼前改變記憶體中的編碼圖像。當圖像遠大於視圖時，應使用此方法而非 `scale`。

- `scale`：圖像會被縮小或放大繪製。與 `resize` 相比，`scale` 更快（通常由硬體加速）且產生更高品質的圖像。如果圖像小於視圖，應使用此方法。如果圖像略大於視圖，也應使用此方法。

- `none`：不進行採樣，圖像以其完整解析度顯示。這僅應在極少數情況下使用，因為它被視為不安全，當 Android 嘗試渲染消耗過多記憶體的圖像時會拋出運行時異常。

有關 `resize` 和 `scale` 的更多詳細資訊，請參閱 https://frescolib.org/docs/resizing。

| Type                                            | Default  |
| ----------------------------------------------- | -------- |
| enum(`'auto'`, `'resize'`, `'scale'`, `'none'`) | `'auto'` |

---

### `resizeMode`

決定當框架與原始圖像尺寸不匹配時如何調整圖像大小。預設為 `cover`。

- `cover`：均勻縮放圖像（保持圖像的長寬比），使得：

  - 圖像的兩個尺寸（寬度和高度）都等於或大於視圖的相應尺寸（減去內邊距）
  - 縮放後的圖像至少有一個尺寸等於視圖的相應尺寸（減去內邊距）

- `contain`：均勻縮放圖像（保持圖像的長寬比），使得圖像的兩個尺寸（寬度和高度）都等於或小於視圖的相應尺寸（減去內邊距）。

- `stretch`：獨立縮放寬度和高度，這可能會改變圖像的長寬比。

- `repeat`：重複圖像以覆蓋視圖的框架。圖像將保持其大小和長寬比，除非它大於視圖，在這種情況下，它將被均勻縮小以適應視圖。

- `center`：在視圖的兩個維度上居中圖像。如果圖像大於視圖，則均勻縮小以適應視圖。

| Type                                                              | Default   |
| ----------------------------------------------------------------- | --------- |
| enum(`'cover'`, `'contain'`, `'stretch'`, `'repeat'`, `'center'`) | `'cover'` |

---

### `resizeMultiplier` <div class="label android">Android</div>

當 `resizeMethod` 設為 `resize` 時，目標尺寸會乘以此值。`scale` 方法用於執行剩餘的調整大小。預設值 `1.0` 表示位圖大小設計為適應目標尺寸。大於 `1.0` 的乘數會將調整選項設為大於目標尺寸，生成的位圖將從硬體大小縮小。預設為 `1.0`。

此屬性在目標尺寸非常小且源圖像顯著較大的情況下最為有用。`resize` 調整方法執行下採樣，源圖像和目標圖像尺寸之間會損失顯著的圖像品質，通常導致圖像模糊。通過使用乘數，解碼後的圖像略大於目標大小但小於源圖像（如果源圖像足夠大）。這允許通過縮放操作在乘數圖像上產生偽品質的鋸齒偽影。

如果您有一個尺寸為 200x200 的源圖像和 24x24 的目標尺寸，`resizeMultiplier` 為 `2.0` 將告訴 Fresco 將圖像下採樣至 48x48。Fresco 選擇最接近的 2 的冪次（即 50x50）並將圖像解碼為該大小的位圖。沒有乘數時，最接近的 2 的冪次將是 25x25。生成的圖像由系統縮小。

| Type   | Default |
| ------ | ------- |
| number | `1.0`   |

---

### `source`

圖像來源（可以是遠程 URL 或本地文件資源）。

此屬性也可以包含多個遠端 URL，並指定其寬度和高度，可能還包括縮放比例或其他 URI 參數。原生端會根據圖像容器的測量尺寸選擇最佳的 `uri` 來顯示。可以添加 `cache` 屬性來控制網路請求如何與本地緩存交互。（更多信息請參見[圖像的緩存控制](images#cache-control)）。

目前支援的格式有 `png`、`jpg`、`jpeg`、`bmp`、`gif`、`webp`、`psd`（僅限 iOS）。此外，iOS 還支援多種 RAW 圖像格式。請參考 Apple 的文檔以獲取當前支援的相機型號列表（對於 iOS 12，請參見 https://support.apple.com/en-ca/HT208967）。

請注意，`webp` 格式在 iOS 上**僅**在與 JavaScript 代碼捆綁時才受支援。

| Type                             |
| -------------------------------- |
| [ImageSource](image#imagesource) |

---

### `src`

表示圖像遠端 URL 的字串。此屬性優先於 `source` 屬性。

**範例：** `src={'https://reactnative.dev/img/tiny_logo.png'}`

| Type   |
| ------ |
| string |

---

### `srcSet`

表示逗號分隔的可能候選圖像源的字串。每個圖像源包含圖像的 URL 和像素密度描述符。如果未指定描述符，則默認為 `1x` 描述符。

如果 `srcSet` 不包含 `1x` 描述符，則使用 `src` 中的值作為 `1x` 描述符的圖像源（如果提供）。

此屬性優先於 `src` 和 `source` 屬性。

**範例：** `srcSet={'https://reactnative.dev/img/tiny_logo.png 1x, https://reactnative.dev/img/header_logo.svg 2x'}`

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

用於 UI 自動化測試腳本中標識此元素的唯一標識符。

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

圖像組件的寬度。

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
static getSize(uri: string): Promise<{width: number, height: number}>;
```

在顯示圖像之前獲取其寬度和高度（以像素為單位）。如果無法找到圖像或下載失敗，此方法可能會失敗。

為了獲取圖像尺寸，可能需要先加載或下載圖像，然後將其緩存。這意味著原則上可以使用此方法預加載圖像，但此方法並非為此目的而優化，未來可能會以不完全加載/下載圖像數據的方式實現。將提供一個單獨的 API 作為預加載圖像的正確支援方式。

**參數：**

| <div className="wideColumn">Name</div>               | Type   | Description                |
| ---------------------------------------------------- | ------ | -------------------------- |
| uri <div class="label basic required">Required</div> | string | The location of the image. |

---

### `getSizeWithHeaders()`

```tsx
static getSizeWithHeaders(
  uri: string,
  headers: {[index: string]: string}
): Promise<{width: number, height: number}>;
```

在顯示圖片之前，透過提供請求標頭來取得圖片的寬度和高度（以像素為單位）。如果圖片無法找到或下載失敗，此方法可能會失敗。此方法也不適用於靜態圖片資源。

為了取得圖片的尺寸，可能需要先載入或下載圖片，之後會將其緩存。這意味著原則上你可以使用此方法來預載圖片，但此方法並非為此目的而優化，且未來可能會以不完全載入/下載圖片資料的方式實現。預載圖片的正式支援方法將作為獨立的API提供。

**參數：**

| <div className="wideColumn">Name</div>                   | Type   | Description                  |
| -------------------------------------------------------- | ------ | ---------------------------- |
| uri <div class="label basic required">Required</div>     | string | The location of the image.   |
| headers <div class="label basic required">Required</div> | object | The headers for the request. |

---

### `prefetch()`

```tsx
await Image.prefetch(url);
```

預取遠端圖片以供後續使用，方法是將其下載到磁碟快取中。返回一個解析為布林值的Promise。

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

執行快取查詢。返回一個Promise，解析為從URL到快取狀態的映射，例如「disk」、「memory」或「disk/memory」。如果請求的URL不在映射中，表示它不在快取中。

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

將資源引用解析為一個物件，該物件具有`uri`、`scale`、`width`和`height`屬性。

**參數：**

| <div className="wideColumn">Name</div>                  | Type                                     | Description                                                                  |
| ------------------------------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------- |
| source <div class="label basic required">Required</div> | [ImageSource](image#imagesource), number | A number (opaque type returned by `require('./foo.png')`) or an ImageSource. |

## 類型定義

### ImageCacheEnum <div class="label ios">iOS</div>

用於設定快取處理或策略的枚舉，適用於可能被快取的回應。

| Type                                                               | Default     |
| ------------------------------------------------------------------ | ----------- |
| enum(`'default'`, `'reload'`, `'force-cache'`, `'only-if-cached'`) | `'default'` |

- `default`: 使用原生平台的預設策略。
- `reload`: 從原始來源載入URL的資料。不應使用現有的快取資料來滿足URL載入請求。
- `force-cache`: 無論其年齡或過期日期如何，將使用現有的快取資料來滿足請求。如果快取中沒有對應請求的資料，則從原始來源載入資料。
- `only-if-cached`: 無論其年齡或過期日期如何，將使用現有的快取資料來滿足請求。如果快取中沒有對應URL載入請求的資料，則不會嘗試從原始來源載入資料，且載入被視為失敗。

### ImageLoadEvent

在`onLoad`回調中返回的物件。

| Type   |
| ------ |
| object |

**屬性：**

| Name   | Type   | Description                         |
| ------ | ------ | ----------------------------------- |
| source | object | The [source object](#source-object) |

#### 來源物件

**屬性：**

| Name   | Type   | Description                                                  |
| ------ | ------ | ------------------------------------------------------------ |
| width  | number | The width of loaded image.                                   |
| height | number | The height of loaded image.                                  |
| uri    | string | A string representing the resource identifier for the image. |

### ImageSource

| Type                             |
| -------------------------------- |
| object, array of objects, number |

**屬性（如果傳遞物件或物件陣列）：**

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

**如果傳遞數字：**

- `number` - 由類似`require('./image.jpg')`返回的不透明類型。