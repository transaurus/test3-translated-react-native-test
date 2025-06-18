---
title: 'New Architecture is here'
authors: [reactteam]
tags: [announcement]
date: 2024-10-23T16:01
---

React Native 0.76 版已預設採用新架構，現已於 npm 上發布！

在 [0.76 版本發布部落格文章](/blog/2024/10/23/release-0.76-new-architecture) 中，我們分享了此版本包含的重大變更清單。本文將概述新架構及其如何塑造 React Native 的未來。

新架構全面支援現代 React 功能，包含 [Suspense](https://react.dev/blog/2022/03/29/react-v18#new-suspense-features)、[Transitions](https://react.dev/blog/2022/03/29/react-v18#new-feature-transitions)、[自動批次處理](https://react.dev/blog/2022/03/29/react-v18#new-feature-automatic-batching) 及 [`useLayoutEffect`](https://react.dev/reference/react/useLayoutEffect)。新架構還包含全新的 [原生模組](/docs/next/turbo-native-modules-introduction) 與 [原生元件](/docs/next/fabric-native-components-introduction) 系統，讓您能撰寫具型別安全性的程式碼，並直接存取原生介面而無需透過橋接器。

此版本是我們自 2018 年起從頭改寫 React Native 的成果，我們特別注重讓新架構對多數應用程式而言能逐步遷移。2021 年，我們成立了 [新架構工作小組](https://github.com/reactwg/react-native-new-architecture/)，與社群合作確保整個 React 生態系統都能平順升級。

多數應用程式採用 React Native 0.76 所需投入的精力與其他版本相同。最熱門的 React Native 函式庫已支援新架構。新架構還包含自動互操作性層，可向後相容以舊架構為目標的函式庫。

<!--truncate-->

在過去幾年的開發過程中，我們的團隊已公開分享對新架構的願景。若您錯過這些演講，可在此觀看：

- [React Native EU 2019 - 全新的 React Native](https://www.youtube.com/watch?v=52El0EUI6D0)
- [React Conf 2021 - React 18 主題演講](https://www.youtube.com/watch?v=FZ0cG47msEk)
- [App.js 2022 - 將 React Native 新架構帶入開源社群](https://www.youtube.com/watch?v=Q6TkkzRJfUo)
- [React Conf 2024 - 第二天主題演講](https://www.youtube.com/watch?v=Q5SMmKb7qVI)

## 何謂新架構

新架構是對 React Native 底層主要系統的全面改寫，包含元件渲染方式、JavaScript 抽象層與原生抽象層的通訊機制，以及跨執行緒的工作排程方式。雖然多數使用者無需理解這些系統的運作原理，這些變更仍帶來改進與新功能。

在舊架構中，React Native 透過非同步橋接器與原生平台通訊。要渲染元件或呼叫原生函式時，React Native 需將原生函式呼叫序列化並排入橋接器佇列，這些呼叫會以非同步方式處理。此架構的優點在於主執行緒永遠不會因渲染更新或處理原生模組函式呼叫而阻塞，因為所有工作都在背景執行緒完成。

然而，使用者期望互動能獲得立即反饋，感受如同原生應用程式。這意味著某些更新需同步渲染以回應使用者輸入，可能需中斷進行中的渲染。由於舊架構僅支援非同步，我們需改寫它以同時支援非同步與同步更新。

此外，在舊架構中，透過橋接器序列化函式呼叫很快成為效能瓶頸，尤其對於頻繁更新或大型物件。這使得應用程式難以穩定達到 60+ FPS。還存在同步化問題：當 JavaScript 與原生層不同步時，無法同步協調它們，導致如清單顯示空白畫面、因中間狀態渲染造成視覺 UI 跳動等錯誤。

最後，由於舊架構使用原生層級結構來維護單一 UI 副本，並就地變更該副本，佈局只能在單一執行緒上計算。這使得處理使用者輸入等緊急更新變得不可能，且佈局無法同步讀取，例如在佈局效果中讀取以更新工具提示位置。

所有這些問題意味著無法正確支援 React 的並行功能。為了解決這些問題，新架構包含四個主要部分：

- 新的原生模組系統
- 新的渲染器
- 事件循環
- 移除橋接器

新模組系統讓 React Native 渲染器能同步存取原生層，使其能處理事件、排程更新，並以同步或非同步方式讀取佈局。新的原生模組預設也採用延遲載入，為應用程式帶來顯著的效能提升。

新渲染器能跨多個執行緒處理多個進行中的樹狀結構，讓 React 能處理多個並行更新優先級，無論在主執行緒或背景執行緒上。它還支援從多個執行緒同步或非同步讀取佈局，以實現更流暢的 UI 回應而不會卡頓。

新事件循環能以明確定義的順序在 JavaScript 執行緒上處理任務。這讓 React 能中斷渲染以處理事件，使緊急使用者事件能優先於低優先級的 UI 轉場。事件循環也與網頁規格對齊，因此我們能支援瀏覽器功能如微任務、`MutationObserver` 和 `IntersectionObserver`。

最後，移除橋接器能加快啟動速度，並實現 JavaScript 與原生運行時的直接通訊，從而最小化切換工作的成本。這也改善了錯誤報告、除錯，並減少未定義行為導致的崩潰。

新架構現已準備好用於生產環境。Meta 已在 Facebook 應用程式和其他產品中大規模使用。我們在為 [Quest 裝置](https://engineering.fb.com/2024/10/02/android/react-at-meta-connect-2024/) 開發的 Facebook 和 Instagram 應用中，成功採用了 React Native 和新架構。

我們的合作夥伴已將新架構用於生產環境數月：請參考 [Expensify](https://blog.swmansion.com/sunrising-new-architecture-in-the-new-expensify-app-729d237a02f5) 和 [Kraken](https://blog.kraken.com/product/engineering/how-kraken-fixed-performance-issues-via-incremental-adoption-of-the-react-native-new-architecture) 的成功案例，並試用 [Bluesky](https://github.com/bluesky-social/social-app/releases/tag/1.92.0-na-rc.2) 的新版本。

### 新的原生模組

新的原生模組系統徹底改寫了 JavaScript 與原生平台的通訊方式。它完全以 C++ 編寫，解鎖了許多新功能：

- 與原生運行時的同步存取
- JavaScript 與原生程式碼之間的型別安全
- 跨平台程式碼共享
- 預設延遲模組載入

在新的原生模組系統中，JavaScript 與原生層現在能透過 JavaScript 介面 (JSI) 同步通訊，無需使用非同步橋接器。這意味著您的自訂原生模組現在能同步呼叫函式、回傳值，並將該值傳遞給另一個原生模組函式。

在舊架構中，為了處理原生函式呼叫的回應，您需要提供回呼函式，且回傳值必須可序列化：

```ts
// ❌ Sync callback from Native Module
nativeModule.getValue(value => {
  // ❌ value cannot reference a native object
  nativeModule.doSomething(value);
});
```

在新架構中，您可以對原生函式進行同步呼叫：

```ts
// ✅ Sync response from Native Module
const value = nativeModule.getValue();

// ✅ value can be a reference to a native object
nativeModule.doSomething(value);
```

透過新架構，您終於能充分發揮 C++ 原生實作的威力，同時仍從 JavaScript/TypeScript API 存取它。新模組系統支援[以 C++ 編寫的模組](/docs/next/the-new-architecture/pure-cxx-modules)，因此您只需編寫一次模組，就能在所有平台上運作，包括 Android、iOS、Windows 和 macOS。以 C++ 實作模組能實現更精細的記憶體管理和效能優化。

此外，透過[Codegen](/docs/next/the-new-architecture/what-is-codegen)，您的模組可以定義 JavaScript 層與原生層之間的強型別合約。根據我們的經驗，跨邊界型別錯誤是跨平台應用程式中最常見的當機來源之一。Codegen 讓您能克服這些問題，同時自動生成樣板程式碼。

最後，模組現在採用延遲載入機制：它們僅在實際需要時才載入記憶體，而非在啟動時就載入。這減少了應用程式的啟動時間，並在應用程式複雜度增加時保持低載入時間。

熱門函式庫如[react-native-mmkv](https://github.com/mrousavy/react-native-mmkv)已從遷移至新原生模組中獲益：

>「新原生模組大幅簡化了`react-native-mmkv`的設定、自動連結和初始化流程。得益於新架構，`react-native-mmkv`現在成為純C++原生模組，可在任何平台上運行。新的Codegen使MMKV實現完全型別安全，透過強制空值安全性修復了長期存在的`NullPointerReference`問題，而能同步呼叫原生模組函數的特性讓我們得以用新的原生模組API取代自訂JSI存取方式。」
>
> [Marc Rousavy](https://twitter.com/mrousavy)，`react-native-mmkv`創建者

### 新渲染器

我們也徹底重寫了原生渲染器，帶來多項優勢：

- 更新可在不同執行緒上以不同優先級渲染
- 佈局可跨執行緒同步讀取
- 渲染器以C++編寫並共享於所有平台

更新後的原生渲染器現在將視圖層級儲存為不可變的樹狀結構。這意味著UI以無法直接修改的方式儲存，允許安全地跨執行緒處理更新。因此能同時處理多個進行中的樹，每棵樹代表使用者介面的不同版本。如此一來，更新可在背景渲染而不阻塞UI（例如轉場動畫期間），或在主執行緒渲染（響應使用者輸入）。

透過支援多執行緒，React能中斷低優先級更新來渲染緊急更新（如使用者輸入產生的事件），並在需要時恢復低優先級更新。新渲染器還能跨執行緒同步讀取佈局資訊。這實現了低優先級更新的背景計算，以及需要時的同步讀取（例如重新定位工具提示）。

最後，用C++重寫渲染器使其能共享於所有平台。這確保相同程式碼在iOS、Android、Windows、macOS和其他React Native支援平台上運行，提供一致的渲染能力而無需為每個平台重新實作。

這是實現我們[多平台願景](/blog/2021/08/26/many-platform-vision)的重要一步。例如，視圖扁平化原本是Android專屬的優化技術，用於避免深層佈局樹。具有共享C++核心的新渲染器[將此功能帶到iOS](https://github.com/reactwg/react-native-new-architecture/discussions/110)。此優化是自動的且無需設定，隨共享渲染器免費提供。

透過這些改變，React Native現在完整支援並行React功能如Suspense和Transitions，讓構建複雜使用者介面更為容易，能快速響應使用者輸入而不會出現卡頓、延遲或視覺跳動。未來我們將利用這些新能力為內建元件（如FlatList和TextInput）帶來更多改進。

熱門函式庫如[Reanimated](https://docs.swmansion.com/react-native-reanimated/)已開始利用新渲染器的優勢：

> 「目前開發中的 Reanimated 4 引入了全新動畫引擎，可直接與新渲染器協作，實現跨線程處理動畫與佈局管理。新渲染器的設計正是這些功能得以實現的關鍵，無需依賴繁瑣的變通方案。此外，由於採用 C++ 編寫且跨平台共享，Reanimated 大部分代碼只需編寫一次，既能減少平台特定問題，又可精簡代碼庫，更有利於非標準平台的整合。」
>
> [Krzysztof Magiera](https://x.com/kzzzf)，[Reanimated](https://docs.swmansion.com/react-native-reanimated/) 創建者

### 事件循環機制

新架構讓我們能實作明確定義的事件循環處理模型，如這份 [RFC](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0744-well-defined-event-loop.md) 所述。該提案遵循 [HTML 標準](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model) 規範，明確規範 React Native 在 JavaScript 線程上執行任務的方式。

明確定義的事件循環彌合了 React DOM 與 React Native 間的差異：React Native 應用的行為現在更接近 React DOM 應用，實現「學習一次，隨處編寫」的理念。

事件循環為 React Native 帶來諸多優勢：

- 可中斷渲染以處理事件和任務
- 更貼近網頁技術規範
- 為實現更多瀏覽器功能奠定基礎

透過事件循環，React 能更精準地排序更新與事件。這使得 React 可暫停低優先級更新來處理緊急用戶事件，而新渲染器則允許我們獨立處理這些更新。

事件循環也使計時器等任務行為與網頁規範保持一致，意味著 React Native 的運作方式更貼近開發者熟悉的網頁環境，有利於 React DOM 與 React Native 間的代碼共享。

此機制還為實現更符合標準的瀏覽器功能（如微任務、`MutationObserver` 和 `IntersectionObserver`）鋪路。這些功能尚未在 React Native 中開放使用，但我們正著手未來引進。

最後，事件循環與新渲染器支援同步讀取佈局的特性，使 React Native 能完整實作 `useLayoutEffect`，在同幀內同步讀取佈局資訊並更新介面。這讓開發者能在元素顯示給用戶前精確定位。

詳見 [`useLayoutEffect`](/blog/2024/10/23/the-new-architecture-is-here#uselayouteffect) 章節說明。

### 移除橋接機制

新架構中，我們徹底移除了 React Native 對橋接器的依賴，改採 JSI 實現 JavaScript 與原生代碼間的直接高效通訊：

![](/blog/assets/0.76-bridge-diagram.png)

移除橋接器可避免初始化延遲，從而改善啟動速度。例如在舊架構中，為向 JavaScript 提供全局方法，必須在啟動時初始化 JavaScript 模組，導致輕微延遲：

```js
// ❌ Slow initialization
import {NativeTimingModule} from 'NativeTimingModule';
global.setTimeout = timer => {
  NativeTimingModule.setTimeout(timer);
};

// App.js
setTimeout(() => {}, 100);
```

新架構中，我們可直接從 C++ 綁定方法：

```cpp
// ✅ Initialize directly in C++
runtime.global().setProperty(runtime, "setTimeout", createTimer);
```

```js
// App.js
setTimeout(() => {}, 100);
```

此重寫也強化了錯誤報告機制，特別是針對啟動階段的 JavaScript 崩潰，並減少未定義行為導致的崩潰。若發生崩潰，新版 [React Native 開發者工具](/docs/next/react-native-devtools) 能簡化除錯流程並完整支援新架構。

目前仍保留橋接器以支援漸進式遷移。未來我們將完全移除橋接器代碼。

### 漸進式遷移

我們預期多數應用程式升級至 0.76 版本的難度與常規版本更新相當。

當您升級至 0.76 版本時，新架構與 React 18 將預設啟用。然而，若要使用並行功能並充分獲得新架構的優勢，您的應用程式與函式庫需逐步遷移以完整支援新架構。

初次升級時，您的應用程式會在新架構上運行，並透過自動的舊架構互操作性層進行銜接。多數應用程式無需修改即可運作，但互操作性層存在[已知限制](https://github.com/reactwg/react-native-new-architecture/discussions/237)，例如不支援存取自訂陰影節點或並行功能。

若要使用並行功能，應用程式還需更新以支援[並行 React](https://react.dev/blog/2022/03/29/react-v18#what-is-concurrent-react)，並遵循 [React 規則](https://react.dev/reference/rules)。遷移 JavaScript 程式碼至 React 18 及其語義時，請參照 [React 18 升級指南](https://react.dev/blog/2022/03/08/react-18-upgrade-guide)。

整體策略是讓應用程式在新架構上運行而不破壞現有程式碼。之後可依自身步調逐步遷移。對於已完全遷移所有模組至新架構的新介面，可立即開始使用並行功能；現有介面則需先解決部分問題並遷移模組，才能新增並行功能。

我們已與最熱門的 React Native 函式庫合作，確保其支援新架構。目前超過 850 個函式庫已相容，包含所有每週下載量超過 20 萬次的函式庫（約佔下載量的 10%）。您可透過 [reactnative.directory](https://reactnative.directory) 網站查詢函式庫與新架構的相容性：

![](/blog/assets/0.76-directory.png)

更多升級細節，請參閱下方的[如何升級](/blog/2024/10/23/the-new-architecture-is-here#how-to-upgrade)。

## 新功能

新架構完整支援 React 18、並行功能，以及 React Native 中的 `useLayoutEffect`。完整 React 18 功能清單請參閱 [React 18 部落格文章](https://react.dev/blog/2021/12/17/react-conf-2021-recap#react-18-and-concurrent-features)。

### 轉場效果

轉場效果是 React 18 的新概念，用於區分緊急與非緊急更新。

- **緊急更新**反映直接互動，例如輸入與點擊。
- **轉場更新**則是用於介面視圖之間的切換。

緊急更新需立即回應以符合我們對實體物件行為的直覺。但轉場效果不同，因為使用者不預期在畫面上看到每個中間值。在新架構中，React Native 能分別渲染緊急更新與轉場更新。

通常，為達最佳使用者體驗，單一使用者輸入應同時觸發緊急與非緊急更新。與 ReactDOM 類似，`press` 或 `change` 等事件會被視為緊急處理並立即渲染。您可在輸入事件中使用 `startTransition` API 來標記哪些更新屬於可延遲至背景處理的「轉場」：

```jsx
import {startTransition} from 'react';

// Urgent: Show the slider value
setCount(input);

// Mark any state updates inside as transitions
startTransition(() => {
  // Transition: Show the results
  setNumberOfTiles(input);
});
```

區分緊急事件與轉場效果能讓使用者介面更靈敏，並提供更直覺的使用體驗。

此處比較舊架構（無轉場效果）與新架構（含轉場效果）。假設每個區塊並非僅是帶背景色的簡單視圖，而是包含圖片與其他高渲染成本元件的複雜元件。**使用** `useTransition` 後，您能避免應用程式因過多更新而卡頓或效能低落。

<div className="TwoColumns TwoFigures">
<figure>
 <img src="/img/new-architecture/without-transitions.gif" alt="A video demonstrating an app rendering many views (tiles) according to a slider input. The views are rendered in batches as the slider is quickly adjusted from 0 to 1000." />
 <figcaption><b>Before:</b> rendering tiles without marking it as a transition.</figcaption>
</figure>
<figure>
 <img src="/img/new-architecture/with-transitions.gif" alt="A video demonstrating an app rendering many views (tiles) according to a slider input. The views are rendered in batches as the slider is quickly adjusted from 0 to 1000. There are less batch renders in comparison to the next video." />
 <figcaption><b>After:</b> rendering tiles <em>with transitions</em> to interrupt in-progress renders of stale state.</figcaption>
</figure>
</div>

更多資訊請參閱[並行渲染器與功能支援](/docs/0.75/the-new-architecture/landing-page#support-for-concurrent-renderer-and-features)。

### 自動批次處理

當您升級至新架構時，將能受益於 React 18 的自動批次處理功能。

自動批次處理允許 React 在渲染時將多個狀態更新批次處理，避免渲染中間狀態。這使得 React Native 運行更快速且不易延遲，無需開發者編寫額外程式碼。

<div className="TwoColumns TwoFigures">
<figure>
 <img src="/img/new-architecture/legacy-renderer.gif" alt="A video demonstrating an app rendering many views according to a slider input. The slider value is adjusted from 0 to 1000 and the UI slowly catches up to rendering 1000 views." />
 <figcaption><b>Before:</b> rendering frequent state updates with legacy renderer.</figcaption>
</figure>
<figure>
 <img src="/img/new-architecture/react18-renderer.gif" alt="A video demonstrating an app rendering many views according to a slider input. The slider value is adjusted from 0 to 1000 and the UI resolves to 1000 views faster than the previous example, without as many intermediate states." />
 <figcaption><b>After:</b> rendering frequent state updates with <em>automatic batching</em>.</figcaption>
</figure>
</div>

在舊架構中，會渲染更多中間狀態，即使滑桿停止移動，UI 仍持續更新。新架構透過自動批次處理更新，渲染更少中間狀態並更快完成渲染。

更多資訊請參閱[並發渲染器與功能支援](/docs/0.75/the-new-architecture/landing-page#support-for-concurrent-renderer-and-features)。

### useLayoutEffect

基於事件循環與同步讀取佈局的能力，新架構中我們新增了對 React Native 中 `useLayoutEffect` 的完整支援。

舊架構中需使用非同步的 `onLayout` 事件讀取視圖佈局資訊（同樣是非同步的）。這會導致至少有一幀的時間佈局不正確，直到佈局被讀取並更新，造成如工具提示位置錯誤等問題：

```tsx
// ❌ async onLayout after commit
const onLayout = React.useCallback(event => {
  // ❌ async callback to read layout
  ref.current?.measureInWindow((x, y, width, height) => {
    setPosition({x, y, width, height});
  });
}, []);

// ...
<ViewWithTooltip
  onLayout={onLayout}
  ref={ref}
  position={position}
/>;
```

新架構透過允許在 `useLayoutEffect` 中同步存取佈局資訊解決此問題：

```tsx
// ✅ sync layout effect during commit
useLayoutEffect(() => {
  // ✅ sync call to read layout
  const rect = ref.current?.getBoundingClientRect();
  setPosition(rect);
}, []);

// ...
<ViewWithTooltip ref={ref} position={position} />;
```

此變更讓您能同步讀取佈局資訊並在同一幀更新 UI，使元素在顯示給使用者前就能正確定位：

<div className="TwoColumns TwoFigures">
<figure>
 <img src="/img/new-architecture/async-on-layout.gif" alt="A view that is moving to the corners of the viewport and center with a tooltip rendered either above or below it. The tooltip is rendered after a short delay after the view moves" />
 <figcaption>In the old architecture, layout was read asynchronously in `onLayout`, causing the position of the tooltip to be delayed.</figcaption>
</figure>
<figure>
 <img src="/img/new-architecture/sync-use-layout-effect.gif" alt="A view that is moving to the corners of the viewport and center with a tooltip rendered either above or below it. The view and tooltip move in unison." />
 <figcaption>In the New Architecture, layout can be read in `useLayoutEffect` synchronously, updating the tooltip position before displaying.</figcaption>
</figure>
</div>

更多資訊請參閱[同步佈局與效果](/docs/0.75/the-new-architecture/landing-page#synchronous-layout-and-effects)文件。

### 完整支援 Suspense

Suspense 讓您能宣告式指定元件樹中尚未準備好顯示部分的載入狀態：

```jsx
<Suspense fallback={<Spinner />}>
  <Comments />
</Suspense>
```

我們數年前引入了有限版本的 Suspense，而 React 18 新增了完整支援。在此之前，React Native 無法支援 Suspense 的並發渲染。

新架構包含 React 18 引入的完整 Suspense 支援。這意味著您現在可在 React Native 中使用 Suspense 處理元件載入狀態，且暫停內容會在背景渲染同時顯示載入狀態，對可見內容的使用者輸入給予更高優先級。

更多內容請參閱[React 18 中 Suspense 的 RFC](https://github.com/reactjs/rfcs/blob/main/text/0213-suspense-in-react-18.md)。

## 如何升級

升級至 0.76 版請遵循[發布公告](/blog/2024/10/23/release-0.76-new-architecture#upgrade-to-076)中的步驟。由於此版本同時升級至 React 18，您還需遵循[React 18 升級指南](https://react.dev/blog/2022/03/08/react-18-upgrade-guide)。

對多數應用程式而言，這些步驟配合與舊架構的互操作層即可完成升級。但要充分發揮新架構優勢並使用並發功能，您需遷移自訂原生模組與原生元件以支援新的 API。

若不遷移自訂原生模組，您將無法獲得共享 C++、同步方法呼叫或代碼生成類型安全等優勢。不遷移原生元件則無法使用並發功能。我們建議盡快將所有原生元件與模組遷移至新架構。

:::note
未來版本中我們將移除互操作層，模組需支援新架構。
:::

### 應用程式

如果您是應用程式開發者，要完整支援新架構，您需要將函式庫、自訂原生元件和自訂原生模組升級以全面支援新架構。

我們已與最受歡迎的 React Native 函式庫合作，確保其支援新架構。您可以在 [reactnative.directory](https://reactnative.directory) 網站上查閱函式庫與新架構的相容性。

如果您的應用程式依賴的函式庫尚未相容，您可以：

- 向函式庫提交問題，請作者遷移至新架構。
- 若函式庫已無人維護，考慮改用具備相同功能的替代函式庫。
- 在這些函式庫完成遷移前，[選擇退出新架構](/blog/2024/10/23/the-new-architecture-is-here#opt-out)。

如果您的應用程式有自訂原生模組或自訂原生元件，由於我們的[互操作層](https://github.com/reactwg/react-native-new-architecture/discussions/135)，它們應該能正常運作。但我們建議將其升級至新的原生模組和原生元件 API，以完整支援新架構並採用並行功能。

請遵循以下指南將您的模組和元件遷移至新架構：

- [原生模組](/docs/next/turbo-native-modules-introduction)
- [原生元件](/docs/next/fabric-native-components-introduction)

### 函式庫

如果您是函式庫維護者，請先測試您的函式庫是否能與互操作層協同運作。若無法運作，請在[新架構工作小組](https://github.com/reactwg/react-native-new-architecture/)提交問題。

為完整支援新架構，我們建議您盡快將函式庫遷移至新的原生模組和原生元件 API。這將讓您的函式庫使用者能充分運用新架構的優勢並支援並行功能。

您可以遵循以下指南將模組和元件遷移至新架構：

- [原生模組](/docs/next/turbo-native-modules-introduction)
- [原生元件](/docs/next/fabric-native-components-introduction)

### 選擇退出

若因任何原因，新架構在您的應用程式中運作異常，您隨時可以選擇退出，直到準備好再次啟用。

要選擇退出新架構：

- 在 Android 上，修改 `android/gradle.properties` 檔案並關閉 `newArchEnabled` 標記

```diff title=”gradle.properties”
-newArchEnabled=true
+newArchEnabled=false
```

- 在 iOS 上，可執行以下命令重新安裝依賴項：

```shell
RCT_NEW_ARCH_ENABLED=0 bundle exec pod install
```

## 致謝

將新架構交付給開源社群是一項耗時數年研究與開發的巨大工程。我們要特別感謝所有現任與前任 React 團隊成員協助達成此成果。

我們也衷心感謝所有與我們合作的夥伴，特別是：

- [Expo](https://expo.dev/)，率先採用新架構，並協助遷移最熱門的函式庫。
- [Software Mansion](https://swmansion.com/)，維護生態系關鍵函式庫、提早遷移至新架構，並協助調查修復各類問題。
- [Callstack](https://www.callstack.com/)，維護生態系關鍵函式庫、提早遷移至新架構，並支援社群CLI開發工作。
- [Microsoft](https://opensource.microsoft.com/)，為`react-native-windows`和`react-native-macos`實作新架構，並貢獻多項開發者工具。
- [Expensify](https://www.expensify.com/)、[Kraken](https://www.kraken.com/)、[Bluesky](https://bsky.app/)與[Brigad](https://www.brigad.co/)率先採用新架構，回報各類問題讓我們能為所有人修復。
- 所有獨立函式庫維護者與開發者，透過測試新架構、修復問題、提出疑問協助釐清，共同推動新架構發展。