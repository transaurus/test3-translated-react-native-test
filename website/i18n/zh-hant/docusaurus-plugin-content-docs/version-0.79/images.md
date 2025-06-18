---
id: images
title: Images
---

## 靜態圖片資源

React Native 提供了一種統一的方式來管理 Android 和 iOS 應用中的圖片及其他媒體資源。若要為應用添加靜態圖片，請將其置於原始碼樹中的某處，並按如下方式引用：

```tsx
<Image source={require('./my-icon.png')} />
```

圖片名稱的解析方式與 JS 模組相同。在上面的例子中，打包工具會尋找與引用它的組件同目錄下的 `my-icon.png`。

你可以使用 `@2x` 和 `@3x` 後綴來為不同螢幕密度提供圖片。如果有以下文件結構：

```
.
├── button.js
└── img
    ├── check.png
    ├── check@2x.png
    └── check@3x.png
```

...且 `button.js` 代碼中包含：

```tsx
<Image source={require('./img/check.png')} />
```

...打包工具會根據設備的螢幕密度打包並提供相應的圖片。例如，iPhone 7 會使用 `check@2x.png`，而 iPhone 7 Plus 或 Nexus 5 會使用 `check@3x.png`。如果沒有與螢幕密度匹配的圖片，則會選擇最接近的選項。

在 Windows 上，如果向專案中添加了新圖片，可能需要重啟打包工具。

這樣做有以下好處：

1. Android 和 iOS 使用相同的系統。
2. 圖片與 JavaScript 代碼位於同一目錄中。組件是自包含的。
3. 沒有全局命名空間，即無需擔心名稱衝突。
4. 只有實際使用的圖片才會被打包到應用中。
5. 添加和更改圖片無需重新編譯應用，可以像平常一樣刷新模擬器。
6. 打包工具知道圖片的尺寸，無需在代碼中重複。
7. 圖片可以通過 [npm](https://www.npmjs.com/) 包分發。

為了使此功能正常工作，`require` 中的圖片名稱必須是靜態已知的。

```tsx
// GOOD
<Image source={require('./my-icon.png')} />;

// BAD
const icon = this.props.active
  ? 'my-icon-active'
  : 'my-icon-inactive';
<Image source={require('./' + icon + '.png')} />;

// GOOD
const icon = this.props.active
  ? require('./my-icon-active.png')
  : require('./my-icon-inactive.png');
<Image source={icon} />;
```

請注意，以這種方式引用的圖片源包含圖片的尺寸（寬度、高度）信息。如果需要動態縮放圖片（例如通過 flex），可能需要在樣式屬性中手動設置 `{width: undefined, height: undefined}`。

## 靜態非圖片資源

上述 `require` 語法也可用於靜態包含音頻、視頻或文檔文件到專案中。支持大多數常見文件類型，包括 `.mp3`、`.wav`、`.mp4`、`.mov`、`.html` 和 `.pdf`。完整列表請參見 [打包工具默認配置](https://github.com/facebook/metro/blob/master/packages/metro-config/src/defaults/defaults.js#L14-L44)。

可以通過在 [Metro 配置](https://metrobundler.dev/docs/configuration) 中添加 [`assetExts` 解析器選項](https://metrobundler.dev/docs/configuration#resolver-options) 來支持其他類型。

需要注意的是，視頻必須使用絕對定位而非 `flexGrow`，因為目前不會傳遞非圖片資源的尺寸信息。對於直接鏈接到 Xcode 或 Android Assets 文件夾中的視頻，此限制不適用。

## 混合應用中的資源圖片

如果你正在構建混合應用（部分 UI 使用 React Native，部分 UI 使用平台代碼），仍然可以使用已打包到應用中的圖片。

對於通過 Xcode 資產目錄或 Android drawable 文件夾包含的圖片，使用不帶擴展名的圖片名稱：

```tsx
<Image
  source={{uri: 'app_icon'}}
  style={{width: 40, height: 40}}
/>
```

對於 Android assets 文件夾中的圖片，使用 `asset:/` 方案：

```tsx
<Image
  source={{uri: 'asset:/app_icon.png'}}
  style={{width: 40, height: 40}}
/>
```

這些方法不提供安全性檢查。你需要確保這些圖片在應用中可用。此外，還需要手動指定圖片尺寸。

## 網絡圖片

您應用中顯示的許多圖片在編譯時並不可用，或者您可能希望動態加載某些圖片以減少二進制文件大小。與靜態資源不同，_您需要手動指定圖片的尺寸_。強烈建議您同時使用 https 以滿足 iOS 上的 [App Transport Security](publishing-to-app-store.md#1-enable-app-transport-security) 要求。

```tsx
// GOOD
<Image source={{uri: 'https://reactjs.org/logo-og.png'}}
       style={{width: 400, height: 400}} />

// BAD
<Image source={{uri: 'https://reactjs.org/logo-og.png'}} />
```

### 圖片的網路請求

如果您想為圖片請求設置 HTTP 方法、標頭或請求體等內容，可以通過在源對象上定義這些屬性來實現：

```tsx
<Image
  source={{
    uri: 'https://reactjs.org/logo-og.png',
    method: 'POST',
    headers: {
      Pragma: 'no-cache',
    },
    body: 'Your Body goes here',
  }}
  style={{width: 400, height: 400}}
/>
```

## URI 數據圖片

有時，您可能會從 REST API 調用中獲取編碼的圖片數據。您可以使用 `'data:'` URI 方案來使用這些圖片。與網路資源一樣，_您需要手動指定圖片的尺寸_。

:::info
這僅推薦用於非常小且動態的圖片，例如來自數據庫列表中的圖標。
:::

```tsx
// include at least width and height!
<Image
  style={{
    width: 51,
    height: 51,
    resizeMode: 'contain',
  }}
  source={{
    uri: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg==',
  }}
/>
```

### 緩存控制

在某些情況下，您可能只想顯示本地緩存中已有的圖片，例如在更高分辨率的圖片可用之前顯示低分辨率的佔位圖。在其他情況下，您可能不關心圖片是否過時，並願意顯示過時的圖片以節省帶寬。`cache` 源屬性讓您可以控制網路層如何與緩存交互。

- `default`: 使用原生平台的默認策略。
- `reload`: URL 的數據將從原始來源加載。不應使用現有的緩存數據來滿足 URL 加載請求。
- `force-cache`: 無論其年齡或過期日期如何，都將使用現有的緩存數據來滿足請求。如果緩存中沒有與請求相對應的現有數據，則從原始來源加載數據。
- `only-if-cached`: 無論其年齡或過期日期如何，都將使用現有的緩存數據來滿足請求。如果緩存中沒有與 URL 加載請求相對應的現有數據，則不會嘗試從原始來源加載數據，並且加載被視為失敗。

```tsx
<Image
  source={{
    uri: 'https://reactjs.org/logo-og.png',
    cache: 'only-if-cached',
  }}
  style={{width: 400, height: 400}}
/>
```

## 本地文件系統圖片

請參閱 [CameraRoll](https://github.com/react-native-community/react-native-cameraroll) 以了解如何使用 `Images.xcassets` 之外的本地資源的示例。

### Drawable 資源

Android 支持通過 `xml` 文件類型加載 [drawable 資源](https://developer.android.com/guide/topics/resources/drawable-resource)。這意味著您可以使用 [向量 drawables](https://developer.android.com/develop/ui/views/graphics/vector-drawable-resources) 來渲染圖標，或使用 [形狀 drawables](https://developer.android.com/guide/topics/resources/drawable-resource#Shape) 來繪製形狀！您可以像導入和使用任何其他 [靜態資源](#static-image-resources) 或 [混合資源](#images-from-hybrid-apps-resources) 一樣導入和使用這些資源類型。您需要手動指定圖片尺寸。

對於與您的 JS 代碼並存的靜態 drawables，請使用 `require` 或 `import` 語法（兩者效果相同）：

```tsx
<Image
  source={require('./img/my_icon.xml')}
  style={{width: 40, height: 40}}
/>
```

對於包含在 Android drawable 文件夾（即 `res/drawable`）中的 drawables，請使用不帶擴展名的資源名稱：

```tsx
<Image
  source={{uri: 'my_icon'}}
  style={{width: 40, height: 40}}
/>
```

drawable 資源與其他圖片類型的一個關鍵區別在於，必須在 Android 應用程序的編譯時引用該資源，因為 Android 需要運行 [Android Asset Packaging Tool (AAPT)](https://developer.android.com/tools/aapt2) 來打包資源。AAPT 創建的二進制 XML 文件格式無法通過 Metro 在網路上加載。如果您更改資源的目錄或名稱，則每次都需要重新構建 Android 應用程序。

#### 創建 XML drawable 資源

Android 在其[可繪製資源指南](https://developer.android.com/guide/topics/resources/drawable-resource)中針對每種支援的可繪製資源類型提供了完整說明，並附有原始 XML 檔案範例。您可以使用 Android Studio 的工具（如[向量資源工作室](https://developer.android.com/studio/write/vector-asset-studio)）從可縮放向量圖形（SVG）和 Adobe Photoshop 文件（PSD）建立向量可繪製資源。

:::info
若您希望將 XML 檔案作為靜態圖片資源（即透過 `import` 或 `require` 語句）使用，應盡量避免在建立的 XML 檔案中引用其他資源。如需引用其他可繪製資源或屬性（如[顏色狀態列表](https://developer.android.com/guide/topics/resources/color-list-resource)或[尺寸資源](https://developer.android.com/guide/topics/resources/more-resources#Dimension)），應將可繪製資源作為[混合應用資源](#images-from-hybrid-apps-resources)包含，並透過名稱導入。
:::

### 最佳相機膠卷圖片

iOS 會在相機膠卷中為同一張圖片儲存多種尺寸，出於效能考量，選擇最接近需求的尺寸至關重要。例如顯示 200x200 縮圖時，不應使用完整畫質的 3264x2448 圖片作為來源。若存在完全匹配的尺寸，React Native 會自動選用；否則會選擇至少大 50% 的第一個可用尺寸，以避免從相近尺寸調整時產生模糊效果。這一切均預設自動處理，無需手動編寫繁瑣（且易出錯）的程式碼。

## 為何不自動調整所有內容尺寸？

_在瀏覽器中_，若未指定圖片尺寸，瀏覽器會先渲染 0x0 元素，下載圖片後再根據正確尺寸重新渲染。此行為的最大問題在於圖片載入時會導致介面跳動，造成糟糕的使用者體驗。這被稱為[累積版面偏移（CLS）](https://web.dev/cls/)。

_在 React Native 中_，我們刻意不實作此行為。雖然開發者需預先知道遠端圖片的尺寸（或長寬比）會增加工作量，但我們認為這能帶來更好的使用者體驗。透過 `require('./my-icon.png')` 語法從應用程式套件載入的靜態圖片_可自動調整尺寸_，因為其尺寸在掛載時立即可用。

例如，`require('./my-icon.png')` 的結果可能如下：

```tsx
{"__packager_asset":true,"uri":"my-icon.png","width":591,"height":573}
```

## 作為物件的來源

React Native 中一個有趣的設計決策是將 `src` 屬性命名為 `source`，且不接受字串，而是帶有 `uri` 屬性的物件。

```tsx
<Image source={{uri: 'something.jpg'}} />
```

在基礎架構層面，這使我們能為此物件附加元數據。例如使用 `require('./my-icon.png')` 時，我們會加入其實際位置和尺寸的資訊（請勿依賴此特性，未來可能變更！）。這也為未來功能預留空間，例如未來可能支援雪碧圖，此時輸出的不是 `{uri: ...}`，而是 `{uri: ..., crop: {left: 10, top: 50, width: 20, height: 40}}`，從而無縫支援所有現有呼叫點的雪碧圖功能。

對使用者而言，可透過此物件儲存有用屬性（如圖片尺寸）以計算顯示大小。歡迎將其作為資料結構來儲存更多圖片相關資訊。

## 透過嵌套實現背景圖片

熟悉網頁開發的開發者常要求 `background-image` 功能。為滿足此需求，可使用 `<ImageBackground>` 元件（其屬性與 `<Image>` 相同），並在頂層添加任意子元件。

在某些情況下，您可能不想使用 `<ImageBackground>`，因為其實作較為基礎。如需更多資訊，請參考 `<ImageBackground>` 的[文件](imagebackground.md)，並在需要時建立您自己的自訂元件。

```tsx
return (
  <ImageBackground source={...} style={{width: '100%', height: '100%'}}>
    <Text>Inside</Text>
  </ImageBackground>
);
```

請注意，您必須指定一些寬度和高度的樣式屬性。

## iOS 邊框半徑樣式

請注意，iOS 的圖片元件可能會忽略以下特定角落的邊框半徑樣式屬性：

- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomLeftRadius`
- `borderBottomRightRadius`

## 線程外解碼

圖片解碼可能需要超過一幀的時間。這是網頁上幀率下降的主要原因之一，因為解碼是在主線程中進行的。在 React Native 中，圖片解碼是在不同的線程中完成的。實際上，您已經需要處理圖片尚未下載的情況，因此在解碼時多顯示幾幀的佔位圖不需要任何程式碼變更。

## 設定 iOS 圖片快取限制

在 iOS 上，我們提供了一個 API 來覆寫 React Native 的預設圖片快取限制。這應該從您的原生 AppDelegate 程式碼中呼叫（例如在 `didFinishLaunchingWithOptions` 中）。

```objectivec
RCTSetImageCacheLimits(4*1024*1024, 200*1024*1024);
```

**參數：**

| Name           | Type   | Required | Description             |
| -------------- | ------ | -------- | ----------------------- |
| imageSizeLimit | number | Yes      | Image cache size limit. |
| totalCostLimit | number | Yes      | Total cache cost limit. |

在上述程式碼範例中，圖片大小限制設為 4 MB，總成本限制設為 200 MB。