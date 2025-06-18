---
id: images
title: Images
---

## 靜態圖片資源

React Native 提供統一的方式來管理 Android 和 iOS 應用中的圖片及其他媒體資源。若要添加靜態圖片至您的應用，請將其置於原始碼樹中的某處，並按以下方式引用：

```tsx
<Image source={require('./my-icon.png')} />
```

圖片名稱的解析方式與 JS 模組相同。在上面的範例中，打包工具會尋找與引用它的組件同目錄下的 `my-icon.png`。

您可以使用 `@2x` 和 `@3x` 後綴來為不同螢幕密度提供圖片。若您有以下檔案結構：

```
.
├── button.js
└── img
    ├── check.png
    ├── check@2x.png
    └── check@3x.png
```

...且 `button.js` 程式碼中包含：

```tsx
<Image source={require('./img/check.png')} />
```

...打包工具將根據設備螢幕密度打包並提供對應圖片。例如，iPhone 7 會使用 `check@2x.png`，而 iPhone 7 Plus 或 Nexus 5 會使用 `check@3x.png`。若無完全匹配的圖片，則會選擇最接近的選項。

在 Windows 上，若您向專案添加了新圖片，可能需要重啟打包工具。

以下是您將獲得的一些優勢：

1. Android 和 iOS 使用相同系統。
2. 圖片與 JavaScript 程式碼位於同一目錄。組件自包含。
3. 無全局命名空間，即您無需擔心名稱衝突。
4. 只有實際使用的圖片會被打包進應用。
5. 添加和變更圖片無需重新編譯應用，可像往常一樣刷新模擬器。
6. 打包工具知曉圖片尺寸，無需在程式碼中重複指定。
7. 圖片可透過 [npm](https://www.npmjs.com/) 套件分發。

為使此機制生效，`require` 中的圖片名稱必須是靜態已知的。

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

請注意，以此方式引入的圖片源包含圖片的尺寸（寬度、高度）資訊。若您需要動態縮放圖片（例如透過 flex），可能需手動在樣式屬性中設定 `{width: undefined, height: undefined}`。

## 靜態非圖片資源

上述 `require` 語法也可用於靜態引入音訊、視訊或文件檔案至您的專案。支援大多數常見檔案類型，包括 `.mp3`、`.wav`、`.mp4`、`.mov`、`.html` 和 `.pdf`。完整清單請參閱 [打包工具預設值](https://github.com/facebook/metro/blob/master/packages/metro-config/src/defaults/defaults.js#L14-L44)。

您可透過在 [Metro 配置](https://metrobundler.dev/docs/configuration) 中添加 [`assetExts` 解析器選項](https://metrobundler.dev/docs/configuration#resolver-options) 來支援其他類型。

需注意的是，視訊必須使用絕對定位而非 `flexGrow`，因為目前未傳遞非圖片資源的尺寸資訊。此限制不適用於直接鏈接至 Xcode 或 Android 資源資料夾的視訊。

## 混合應用資源中的圖片

若您正在構建混合應用（部分 UI 使用 React Native，部分使用平台原生程式碼），仍可使用已打包至應用中的圖片。

對於透過 Xcode 資源目錄或 Android drawable 資料夾包含的圖片，請使用不帶副檔名的圖片名稱：

```tsx
<Image
  source={{uri: 'app_icon'}}
  style={{width: 40, height: 40}}
/>
```

對於 Android assets 資料夾中的圖片，請使用 `asset:/` 方案：

```tsx
<Image
  source={{uri: 'asset:/app_icon.png'}}
  style={{width: 40, height: 40}}
/>
```

這些方法不提供安全性檢查。您需自行確保這些圖片在應用中可用。此外，您還需手動指定圖片尺寸。

## 網路圖片

您應用中顯示的許多圖片在編譯時並不可用，或者您可能希望動態加載某些圖片以減少二進制文件大小。與靜態資源不同，_您需要手動指定圖片的尺寸_。強烈建議您同時使用 https 以滿足 iOS 上 [App Transport Security](publishing-to-app-store.md#1-enable-app-transport-security) 的要求。

```tsx
// GOOD
<Image source={{uri: 'https://reactjs.org/logo-og.png'}}
       style={{width: 400, height: 400}} />

// BAD
<Image source={{uri: 'https://reactjs.org/logo-og.png'}} />
```

### 圖片的網路請求

如果您希望在圖片請求中設置 HTTP 動詞、標頭或請求體等內容，可以通過在源對象上定義這些屬性來實現：

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

有時，您可能從 REST API 調用中獲取編碼的圖片數據。您可以使用 `'data:'` URI 方案來使用這些圖片。與網路資源相同，_您需要手動指定圖片的尺寸_。

:::info
這僅推薦用於非常小且動態的圖片，例如來自數據庫的列表中的圖標。
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

### 緩存控制（僅限 iOS）

在某些情況下，您可能只希望在本地緩存中已有圖片時顯示它，例如在更高分辨率的圖片可用之前顯示低分辨率的佔位圖。在其他情況下，您不關心圖片是否過時，並願意顯示過時的圖片以節省帶寬。`cache` 源屬性讓您可以控制網路層如何與緩存交互。

- `default`：使用原生平台的默認策略。
- `reload`：從原始源加載 URL 的數據。不應使用任何現有的緩存數據來滿足 URL 加載請求。
- `force-cache`：無論其年齡或過期日期如何，都將使用現有的緩存數據來滿足請求。如果緩存中沒有對應請求的現有數據，則從原始源加載數據。
- `only-if-cached`：無論其年齡或過期日期如何，都將使用現有的緩存數據來滿足請求。如果緩存中沒有對應 URL 加載請求的現有數據，則不會嘗試從原始源加載數據，並且加載被視為失敗。

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

有關使用 `Images.xcassets` 之外的本地資源的示例，請參見 [CameraRoll](https://github.com/react-native-community/react-native-cameraroll)。

### 最佳相機膠卷圖片

iOS 會在您的相機膠卷中保存同一圖片的多個尺寸，為了性能考慮，選擇最接近的尺寸非常重要。當顯示 200x200 的縮略圖時，您不會希望使用全質量 3264x2448 的圖片作為源。如果有完全匹配的尺寸，React Native 會選擇它，否則它會使用至少大 50% 的第一個尺寸，以避免從接近的尺寸調整大小時出現模糊。所有這些都是默認完成的，因此您無需擔心編寫繁瑣（且容易出錯）的代碼來自行處理。

## 為什麼不自動調整所有內容的大小？

_在瀏覽器中_，如果您不給圖片指定大小，瀏覽器將渲染一個 0x0 的元素，下載圖片，然後根據正確的尺寸渲染圖片。這種行為的主要問題是，隨著圖片的加載，您的 UI 會到處跳動，這會導致非常糟糕的用戶體驗。這被稱為 [累積佈局偏移](https://web.dev/cls/)。

_在 React Native 中_，這種行為是故意不實現的。開發人員需要提前知道遠程圖片的尺寸（或寬高比）會增加工作量，但我們相信這會帶來更好的用戶體驗。通過 `require('./my-icon.png')` 語法從應用包加載的靜態圖片_可以自動調整大小_，因為它們的尺寸在掛載時立即可用。

例如，`require('./my-icon.png')` 的結果可能是：

```tsx
{"__packager_asset":true,"uri":"my-icon.png","width":591,"height":573}
```

## 來源作為物件

在 React Native 中，一個有趣的設計決策是將 `src` 屬性命名為 `source`，並且不接受字串，而是一個帶有 `uri` 屬性的物件。

```tsx
<Image source={{uri: 'something.jpg'}} />
```

在基礎架構方面，這樣做的原因是它允許我們將元數據附加到此物件上。例如，如果您使用 `require('./my-icon.png')`，那麼我們會添加有關其實際位置和大小的信息（請不要依賴這一點，未來可能會改變！）。這也是一種未來證明的方式，例如我們可能希望在某個時候支持精靈圖（sprites），而不是輸出 `{uri: ...}`，我們可以輸出 `{uri: ..., crop: {left: 10, top: 50, width: 20, height: 40}}`，並透明地支持所有現有調用點的精靈圖。

在用戶端，這讓您可以為物件添加有用的屬性，例如圖像的尺寸，以便計算其顯示的大小。您可以自由地將其用作數據結構來存儲有關圖像的更多信息。

## 通過嵌套實現背景圖像

熟悉網頁開發的開發者經常會提出 `background-image` 的功能需求。為了處理這種用例，您可以使用 `<ImageBackground>` 組件，它擁有與 `<Image>` 相同的屬性，並且可以在其上添加任何您希望疊加的內容。

在某些情況下，您可能不希望使用 `<ImageBackground>`，因為其實現較為基礎。請參考 `<ImageBackground>` 的[文檔](imagebackground.md)以獲取更多見解，並在需要時創建自己的自定義組件。

```tsx
return (
  <ImageBackground source={...} style={{width: '100%', height: '100%'}}>
    <Text>Inside</Text>
  </ImageBackground>
);
```

請注意，您必須指定一些寬度和高度的樣式屬性。

## iOS 邊框半徑樣式

請注意，以下特定於角落的邊框半徑樣式屬性可能會被 iOS 的圖像組件忽略：

- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomLeftRadius`
- `borderBottomRightRadius`

## 線程外解碼

圖像解碼可能需要超過一幀的時間。這是網頁上掉幀的主要原因之一，因為解碼是在主線程中完成的。在 React Native 中，圖像解碼是在不同的線程中完成的。實際上，您已經需要處理圖像尚未下載的情況，因此在解碼過程中多顯示幾幀的佔位圖不需要任何代碼更改。

## 配置 iOS 圖像緩存限制

在 iOS 上，我們提供了一個 API 來覆蓋 React Native 的默認圖像緩存限制。這應該從您的原生 AppDelegate 代碼中調用（例如在 `didFinishLaunchingWithOptions` 中）。

```objectivec
RCTSetImageCacheLimits(4*1024*1024, 200*1024*1024);
```

**參數：**

| Name           | Type   | Required | Description             |
| -------------- | ------ | -------- | ----------------------- |
| imageSizeLimit | number | Yes      | Image cache size limit. |
| totalCostLimit | number | Yes      | Total cache cost limit. |

在上面的代碼示例中，圖像大小限制設置為 4 MB，總成本限制設置為 200 MB。