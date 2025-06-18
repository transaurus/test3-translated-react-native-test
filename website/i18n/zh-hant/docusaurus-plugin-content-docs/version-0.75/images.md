---
id: images
title: Images
---

## 靜態圖片資源

React Native 提供統一方式管理 Android 和 iOS 應用中的圖片與其他媒體資源。若要添加靜態圖片，請將其置於原始碼目錄中並按以下方式引用：

```tsx
<Image source={require('./my-icon.png')} />
```

圖片名稱的解析方式與 JS 模組相同。上例中，打包工具會在同層目錄尋找 `my-icon.png`。

您可使用 `@2x` 和 `@3x` 後綴提供不同螢幕密度的圖片。若檔案結構如下：

```
.
├── button.js
└── img
    ├── check.png
    ├── check@2x.png
    └── check@3x.png
```

且 `button.js` 中包含：

```tsx
<Image source={require('./img/check.png')} />
```

打包工具會根據設備螢幕密度選擇對應圖片。例如 iPhone 7 會使用 `check@2x.png`，而 iPhone 7 Plus 或 Nexus 5 會使用 `check@3x.png`。若無完全匹配的圖片，則選擇最接近的選項。

在 Windows 上，若添加新圖片至專案，可能需要重啟打包工具。

此機制帶來以下優勢：

1. Android 與 iOS 使用相同系統
2. 圖片與 JavaScript 代碼同目錄存放，元件自包含
3. 無全局命名空間衝突風險
4. 僅打包實際使用的圖片
5. 增改圖片無需重新編譯應用，可正常刷新模擬器
6. 打包工具知曉圖片尺寸，無需在代碼中重複聲明
7. 可透過 [npm](https://www.npmjs.com/) 分發圖片

注意：`require` 中的圖片名稱必須是靜態已知的。

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

此方式引入的圖片會自動包含尺寸資訊。若需動態縮放（如透過 flex），可能需手動設置樣式屬性為 `{width: undefined, height: undefined}`。

## 靜態非圖片資源

上述 `require` 語法也可用於靜態引入音頻、視頻或文檔文件，支援格式包含 `.mp3`、`.wav`、`.mp4`、`.mov`、`.html` 和 `.pdf` 等。完整列表請參閱 [打包工具默認配置](https://github.com/facebook/metro/blob/master/packages/metro-config/src/defaults/defaults.js#L14-L44)。

可透過在 [Metro 配置](https://metrobundler.dev/docs/configuration) 中添加 [`assetExts` 解析器選項](https://metrobundler.dev/docs/configuration#resolver-options) 來支援其他類型。

注意：視頻必須使用絕對定位而非 `flexGrow`，因非圖片資源目前不傳遞尺寸資訊。此限制不適用於直接鏈接至 Xcode 或 Android Assets 目錄的視頻。

## 混合應用的資源圖片

若開發混合應用（部分 UI 使用 React Native，部分使用平台原生代碼），仍可引用已打包的圖片資源。

對於透過 Xcode 資產目錄或 Android drawable 文件夾引入的圖片，使用不帶副檔名的名稱：

```tsx
<Image
  source={{uri: 'app_icon'}}
  style={{width: 40, height: 40}}
/>
```

對於 Android assets 文件夾中的圖片，使用 `asset:/` 前綴：

```tsx
<Image
  source={{uri: 'asset:/app_icon.png'}}
  style={{width: 40, height: 40}}
/>
```

這些方式不進行安全性檢查，需自行確保圖片存在，且必須手動指定圖片尺寸。

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

如果您想在圖片請求中設置 HTTP 動詞、標頭或主體等內容，可以通過在來源物件上定義這些屬性來實現：

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

### 快取控制（僅限 iOS）

在某些情況下，您可能只想顯示已存在於本地快取中的圖片，例如在更高解析度的圖片可用之前顯示低解析度的佔位圖。在其他情況下，您不介意圖片是否過時，並願意顯示過時的圖片以節省頻寬。`cache` 來源屬性讓您可以控制網路層如何與快取交互。

- `default`：使用原生平台的默認策略。
- `reload`：URL 的數據將從原始來源加載。不應使用任何現有的快取數據來滿足 URL 加載請求。
- `force-cache`：無論其年齡或過期日期如何，都將使用現有的快取數據來滿足請求。如果快取中沒有對應請求的現有數據，則從原始來源加載數據。
- `only-if-cached`：無論其年齡或過期日期如何，都將使用現有的快取數據來滿足請求。如果快取中沒有對應 URL 加載請求的現有數據，則不會嘗試從原始來源加載數據，並且加載被視為失敗。

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

### 最佳相機膠卷圖片

iOS 會在您的相機膠卷中為同一張圖片保存多個尺寸，為了性能考慮，選擇最接近的尺寸非常重要。您不會希望使用全解析度的 3264x2448 圖片作為來源來顯示 200x200 的縮略圖。如果有完全匹配的尺寸，React Native 會選擇它，否則它會使用至少大 50% 的第一個尺寸，以避免從接近的尺寸調整大小時出現模糊。所有這些都是默認完成的，因此您無需擔心編寫繁瑣（且容易出錯）的代碼來自行處理。

## 為什麼不自動調整所有內容的大小？

_在瀏覽器中_，如果您不指定圖片的大小，瀏覽器會渲染一個 0x0 的元素，下載圖片，然後根據正確的尺寸渲染圖片。這種行為的主要問題是，您的 UI 會在圖片加載時四處跳動，這會導致非常糟糕的用戶體驗。這被稱為 [累積佈局偏移](https://web.dev/cls/)。

_在 React Native 中_，這種行為是故意不實現的。開發者需要提前知道遠程圖片的尺寸（或寬高比）會增加工作量，但我們相信這會帶來更好的用戶體驗。通過 `require('./my-icon.png')` 語法從應用包加載的靜態圖片 _可以自動調整大小_，因為它們的尺寸在掛載時立即可用。

例如，`require('./my-icon.png')` 的執行結果可能是：

```tsx
{"__packager_asset":true,"uri":"my-icon.png","width":591,"height":573}
```

## 以物件形式指定來源

在 React Native 中，一個有趣的設計決策是將 `src` 屬性命名為 `source`，且不接受字串，而是接受一個帶有 `uri` 屬性的物件。

```tsx
<Image source={{uri: 'something.jpg'}} />
```

在基礎架構層面，這種設計允許我們為此物件附加元數據。例如，當你使用 `require('./my-icon.png')` 時，我們會加入關於其實際位置和大小的資訊（請勿依賴此特性，未來可能變更！）。這也具有前瞻性，例如未來若需支援雪碧圖，我們可以輸出 `{uri: ..., crop: {left: 10, top: 50, width: 20, height: 40}}`，無縫在所有現有呼叫點實現雪碧圖支援。

對使用者而言，這讓你能為物件添加實用屬性（例如圖像尺寸）以計算顯示大小。可自由將其作為資料結構來儲存更多關於圖像的資訊。

## 透過嵌套實現背景圖像

熟悉網頁開發的開發者常要求 `background-image` 功能。為滿足此需求，可使用 `<ImageBackground>` 元件，其 props 與 `<Image>` 相同，並可在其上疊加任意子元件。

某些情況下可能不適合使用 `<ImageBackground>`，因其實作較為基礎。詳見 `<ImageBackground>` 的[文件](imagebackground.md)，必要時可自行建立客製化元件。

```tsx
return (
  <ImageBackground source={...} style={{width: '100%', height: '100%'}}>
    <Text>Inside</Text>
  </ImageBackground>
);
```

請注意必須指定某些寬度和高度的樣式屬性。

## iOS 圓角邊框樣式

請注意以下特定圓角邊框樣式屬性可能被 iOS 的圖像元件忽略：

- `borderTopLeftRadius`
- `borderTopRightRadius`
- `borderBottomLeftRadius`
- `borderBottomRightRadius`

## 線程外解碼

圖像解碼可能耗時超過一幀。這在網頁上是導致幀率下降的主因之一，因為解碼在主線程進行。React Native 中，圖像解碼在獨立線程完成。實務上，你已需處理圖像未下載完成的情況，因此在解碼期間多顯示幾幀佔位圖無需任何程式碼變更。

## 設定 iOS 圖像快取限制

在 iOS 上，我們提供 API 來覆寫 React Native 預設的圖像快取限制。應從原生 AppDelegate 程式碼（例如 `didFinishLaunchingWithOptions` 內）呼叫此 API。

```objectivec
RCTSetImageCacheLimits(4*1024*1024, 200*1024*1024);
```

**參數：**

| Name           | Type   | Required | Description             |
| -------------- | ------ | -------- | ----------------------- |
| imageSizeLimit | number | Yes      | Image cache size limit. |
| totalCostLimit | number | Yes      | Total cache cost limit. |

上述程式碼範例中，單一圖像大小限制設為 4 MB，總成本限制設為 200 MB。