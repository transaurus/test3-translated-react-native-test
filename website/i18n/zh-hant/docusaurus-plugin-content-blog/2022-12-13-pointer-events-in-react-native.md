---
title: Pointer Events in React Native
authors: [lunaleaps, vincentriemer]
tags: [announcement]
date: 2022-12-13
---

# React Native 中的 Pointer Events

今天我們將分享 React Native 實驗性的跨平台指針 API。我們將探討其動機、運作原理以及對 React Native 使用者的益處。文中包含啟用方法說明，我們期待聽到您的反饋！

自我們發表[多平台願景](https://reactnative.dev/blog/2021/08/26/many-platform-vision)闡述超越行動端的優勢已逾一年，該願景為所有平台設定了更高標準。這段期間，我們加強了對 VR、桌面端和網頁版 React Native 的投入。這些平台在硬體與互動方式上的差異，引發了 React Native 應如何整體處理輸入的思考。

<!--truncate-->

### 超越觸控

桌面端與 VR 長期依賴滑鼠和鍵盤輸入，而行動端主要為觸控。隨著觸控筆電普及，以及行動端對鍵盤和觸控筆支援需求增長，這個敘事正在改變——現有 React Native 觸控事件系統卻無法處理這些輸入方式。

因此，非官方平台的使用者會分叉 React Native 或建立自訂原生元件，以實現懸停偵測或左鍵點擊等關鍵功能。這種分歧導致屬性重複（相似事件處理器需為不同平台分別實作），增加框架複雜度並使跨平台程式碼共享變得繁瑣。這些問題促使團隊決定提供跨平台指針 API。

React Native 旨在提供強大而靈活的 API 來支援多平台開發，同時保持各平台特色體驗。設計此類 API 極具挑戰性，所幸在指針領域已有先例可供參考。

### 借鏡網頁標準

網頁平台同樣面臨擴展至多平台的挑戰，並需兼顧前瞻性設計。萬維網聯盟（W3C）負責制定標準，以建立跨平台、跨瀏覽器的互通性網路環境。

對我們最相關的是，W3C 定義了抽象輸入形式「指針」的行為規範。[Pointer Events](https://www.w3.org/TR/pointerevents3/) 標準以滑鼠事件為基礎，旨在提供統一的跨裝置指針輸入事件與介面，同時允許必要時的裝置特定處理。

遵循 Pointer Events 標準能為 React Native 使用者帶來多重益處。除了解決前述問題，更能提升傳統上無需考慮多輸入類型的平台能力——例如藍牙滑鼠連接 Android 手機，或 Apple Pencil 在 iPad M2 的懸停支援。

符合標準也創造了網頁與 React Native 間的知識共享機會。Pointer Events 的網頁教學可同時服務 React Native 開發者。但我們也認知到 React Native 需求與網頁不同，對標準的實採將採取「盡力而為」原則，並明確記錄差異以釐清預期。相關工作還包括對齊特定網頁標準以[減少 API 碎片化](https://github.com/react-native-community/discussions-and-proposals/pull/496)，涵蓋無障礙與效能 API。

## 移植網頁平台測試

雖然 Pointer Events 標準提供了 API 介面與行為描述，我們發現其規範尚不足以作為修改依據。但網頁瀏覽器採用另一種驗證機制確保合規性與互通性——[網頁平台測試（Web Platform Tests）](https://web-platform-tests.org/)！

網頁平台測試是針對瀏覽器命令式 DOM API 編寫，與 React Native 自有的視圖原語不相容。這意味著我們無法直接共享測試程式碼，而是為 React Native 建立了類比測試 API 來移植這些測試。

我們實作了新的手動測試框架，現正透過 RNTester 驗證實作。這些測試暫稱為 RNTester 平台測試，目前仍屬基礎階段。我們的實作提供 API 將測試案例建構為元件本身，其渲染結果完全透過 UI 回報。

![GIF 並排顯示「Pointer Events hoverable pointer attributes test」在 React Native (iOS) 左側與 Web (原始實作) 右側的執行對比。](/blog/assets/pointer-events-wpt-demo.gif)

這些測試將持續協助我們完善 Pointer Events 的實作。這些測試也能擴展至 Android 和 iOS 以外的平台驗證 Pointer Events 實作。隨著測試套件數量增加，我們將尋求自動化執行這些測試，以更有效捕捉實作中的回歸問題。

## 運作原理

我們的 Pointer Events 實作大多基於現有觸控事件派發基礎架構。在 Android 和 iOS 上，我們分別利用 MotionEvent 和 UITouch 事件。事件派發的整體流程如下圖所示。

![將 Android 和 iOS UI 輸入事件轉譯為 Pointer Events 的程式碼流程圖。Android 端，輸入處理器「onTouchEvent」和「onHoverEvent」觸發「MotionEvents」，經解譯為 Pointer Events 後透過 JSI 派發至 React 渲染器。iOS 採取類似路徑，輸入處理器「touchesBegan」、「touchesMoved」、「touchesEnded」和「hovering」將「UITouch」與「UIEvent」解譯為 Pointer Events。](/blog/assets/pointer-events-code-flow.png)

以 Android 為例，運用平台事件的通用方法為：

1. 遍歷 `MotionEvent` 的所有指標點，執行深度優先搜索以確定每個指標點對應的 React 視圖及其祖先路徑。
2. 將 `MotionEvent` 類別映射至相關的 pointer events。`MotionEvent` 與 `PointerEvent` 存在一對多關係。在關係示意圖中，虛線表示若指向裝置不支援懸浮時觸發的事件。

![圖示 Android MotionEvents 類型轉換為觸發 Pointer Events 的關係圖。部分 pointer events 會因指向裝置是否支援懸浮而條件性觸發。「ACTION_DOWN」與「ACTION_POINTER_DOWN」觸發 pointerdown，並條件性觸發 pointerenter、pointerover。「ACTION_MOVE」和「ACTION_HOVER_MOVE」觸發 pointerover、pointermove、pointerout、pointerup。「ACTION_UP」及「ACTION_POINTER_UP」觸發 pointerup，並條件性觸發 pointerout、pointerleave。](/blog/assets/pointer-events-motionevent-relationship.png)

1. 使用 `MotionEvent` 的平台細節與先前互動的緩存狀態建構 `PointerEvent` 介面。（例如 [`button` 屬性](https://w3c.github.io/pointerevents/#the-button-property)）
2. 將 pointer events 從 Android 派發至 React Native 的[核心事件佇列](https://github.com/facebook/react-native/blob/main/ReactCommon/react/renderer/core/EventQueueProcessor.cpp#L20)，並透過 JSI 呼叫 `react-native-renderer` 中的 [`dispatchEvent`](https://github.com/facebook/react/blob/main/packages/react-native-renderer/src/ReactFabricEventEmitter.js#L83) 方法，該方法會遍歷 React 樹以處理事件的冒泡與捕獲階段。

## 實作進度

目前我們在實作 Pointer Events 規範上的進展，主要聚焦於建立按壓、懸浮與移動等常見事件的穩固基礎實作。

### 事件

| Implemented    | Work in Progress | Yet to be Implemented |
| -------------- | ---------------- | --------------------- |
| onPointerOver  | onPointerCancel  | onClick               |
| onPointerEnter |                  | onContextMenu         |
| onPointerDown  |                  | onGotPointerCapture   |
| onPointerMove  |                  | onLostPointerCapture  |
| onPointerUp    |                  | onPointerRawUpdate    |
| onPointerOut   |                  |                       |
| onPointerLeave |                  |                       |

:::info

onPointerCancel 已連結至原生平台的「cancel」事件，但這不一定對應網頁平台預期的觸發時機。

:::

### 事件屬性

針對上述每個事件，我們已實作 PointerEvent 物件預期的大多數屬性——儘管在 React Native 中這些屬性是透過 `event.nativeEvent` 屬性暴露。您可在[事件物件的 Flowtype 介面定義](https://github.com/facebook/react-native/blob/59ee57352738f030b41589a450209e51e44bbb06/Libraries/Types/CoreEventTypes.js#L175)中找到所有已實作屬性的列舉。其中較明顯的未完整實作部分是 `relatedTarget` 屬性，因為以這種非正規方式暴露原生視圖參照並非易事。

## 未來工作與探索方向

除了上述事件外，還有一些與 Pointer Events 相關的 API。未來我們計劃將這些 API 納入實作範圍，包括：

- 指標捕獲 API (Pointer Capture API)
  - 包含透過元素參考暴露的命令式 API，如 `setPointerCapture()`、`releasePointerCapture()` 和 `hasPointerCapture()`。
- `touch-action` 樣式屬性
  - 網頁使用此 CSS 屬性來宣告式協調瀏覽器與網站自訂事件處理程式之間的手勢。在 React Native 中，可用於協調 View 的指標事件處理器與父級 ScrollView 之間的事件處理。
- `click`、`contextmenu`、`auxclick`
  - `click` 是互動的抽象定義，可能透過無障礙功能模式或其他平台特性互動觸發。

原生 Pointer Events 實作的另一項優勢是能讓我們重新審視並改進目前僅限觸控事件、且由 JavaScript 中 Responder、Pressability 和 PanResponder API 處理的各種手勢處理方式。

此外，我們持續探索為 React Native 宿主元件（即 `add`/`removeEventListener`）實作 `EventTarget` 介面，相信這將使處理指標互動的使用者層抽象化更為可能。

## 試用方式

我們的 Pointer Events 實作仍處於實驗階段，但希望獲得社群對現有成果的反饋。若您想試用此 API，需啟用以下功能標記：

### 啟用功能標記

:::note

Pointer Events 目前僅在[新架構 (Fabric)](https://reactnative.dev/docs/the-new-architecture/use-app-template)中實作，且僅支援 React Native 0.71+ 版本（本文撰寫時為候選發布版）。

:::

在您的 JavaScript 入口檔案（預設 React Native 應用模板中的 index.js）中，需啟用 `shouldEmitW3CPointerEvents` 標記以使用 Pointer Events，並啟用 `shouldPressibilityUseW3CPointerEventsForHover` 以在 `Pressability` 中使用 Pointer Events。

```js
import ReactNativeFeatureFlags from 'react-native/Libraries/ReactNative/ReactNativeFeatureFlags';

// enable the JS-side of the w3c PointerEvent implementation
ReactNativeFeatureFlags.shouldEmitW3CPointerEvents = () => true;

// enable hover events in Pressibility to be backed by the PointerEvent implementation
ReactNativeFeatureFlags.shouldPressibilityUseW3CPointerEventsForHover =
  () => true;
```

### iOS 專屬設定

為確保指標事件能從 iOS 原生渲染器發送，您需在原生應用初始化程式碼（通常是 `AppDelegate.mm`）中切換原生功能標記。

```objc
#import <React/RCTConstants.h>

// ...

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    RCTSetDispatchW3CPointerEvents(YES);

    // ...
}
```

請注意，為使 Pointer Events 實作能區分 iOS 上的滑鼠與觸控指標，您需在 Xcode 專案的 `info.plist` 中加入 [`UIApplicationSupportsIndirectInputEvents`](https://developer.com/apple/documentation/bundleresources/information_property_list/uiapplicationsupportsindirectinputevents)。

### Android 專屬設定

與 iOS 類似，Android 需在應用初始化時（通常是根 React Activity 或 Surface 的 `onCreate` 方法）啟用功能標記。

```java
import com.facebook.react.config.ReactFeatureFlags;

//... somewhere in initialization

@Override
public void onCreate() {
    ReactFeatureFlags.dispatchPointerEvents = true;
}
```

### JavaScript 設定

```js
function onPointerOver(event) {
  console.log(
    'Over blue box offset: ',
    event.nativeEvent.offsetX,
    event.nativeEvent.offsetY,
  );
}

// ... in some component
<View
  onPointerOver={onPointerOver}
  style={{height: 100, width: 100, backgroundColor: 'blue'}}
/>;
```

## 歡迎提供反饋

目前 Pointer Events 已應用於我們的 VR 平台並驅動 Oculus Store，但我們也期待社群對現有實作方法與成果的早期反饋。若您對此工作有任何疑問或想法，歡迎加入[指標事件專屬討論](https://github.com/react-native-community/discussions-and-proposals/discussions/557)。