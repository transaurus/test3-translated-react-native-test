---
title: Introducing Hot Reloading
author: Martín Bigio
authorTitle: Software Engineer at Instagram
authorURL: 'https://twitter.com/martinbigio'
authorImageURL: 'https://avatars3.githubusercontent.com/u/535661?v=3&s=128'
authorTwitter: martinbigio
tags: [engineering]
---

React Native 的目標是為開發者提供最佳的開發體驗。其中關鍵部分在於從保存檔案到看到變更所需的時間。我們的目標是將這個反饋循環縮短至 1 秒內，即使您的應用程式不斷成長。

我們透過三大主要功能接近這個理想：

- 使用 JavaScript 作為開發語言，因其不需耗時的編譯週期。
- 實作名為 Packager 的工具，將 es6/flow/jsx 檔案轉換為虛擬機器可理解的標準 JavaScript。其設計為伺服器模式，將中間狀態保留在記憶體中以實現快速增量更新，並充分利用多核心運算。
- 建立稱為 Live Reload 的功能，可在保存時重新載入應用程式。

至此，開發者的瓶頸不再是重新載入應用程式的時間，而是應用程式狀態的遺失。常見情境是開發一個需要多次點擊才能到達的功能頁面。每次重新載入後，您必須重複相同的操作路徑才能回到開發中的功能，導致整個循環耗時數秒。

## 熱重載

熱重載的核心概念是保持應用程式運行狀態，並在執行時期注入您編輯過的新版檔案。這種方式能保留所有狀態，對於調整使用者介面特別有用。

影片勝過千言萬語。請觀看 Live Reload（現有）與 Hot Reload（新增）的實際差異。

<iframe
  width="100%"
  height="315"
  src="https://www.youtube.com/embed/2uQzVi-KFuc"
  frameborder="0"
  allowfullscreen></iframe>

仔細觀察可發現，系統能從紅屏錯誤中恢復，也能開始導入先前不存在的模組，而無需完整重新載入。

**重要提醒：** 由於 JavaScript 是高度狀態化的語言，熱重載無法完美實作。實務上我們發現當前設定能妥善處理多數常見使用情境，若發生問題隨時可進行完整重新載入。

熱重載功能自 0.22 版起提供，啟用方式如下：

- 開啟開發者選單
- 點選「啟用熱重載」

## 實作原理簡述

瞭解其價值與使用方法後，現在進入最有趣的部分：實際運作原理。

熱重載建構於 [Hot Module Replacement](https://webpack.js.org/guides/hot-module-replacement/)（HMR）功能之上，該技術最初由 webpack 提出，我們在 React Native Packager 中實作了相同機制。HMR 會監控檔案變更，並將更新內容發送至應用程式中內建的輕量 HMR 執行環境。

簡而言之，HMR 更新包含已變更 JS 模組的新程式碼。當執行環境接收後，會用新程式碼替換舊模組：

![](/blog/assets/hmr-architecture.png)

HMR 更新內容不僅包含欲變更的模組程式碼，因為單純替換並不足以讓執行環境獲取變更。問題在於模組系統可能已快取我們欲更新模組的_匯出內容_。例如，假設您的應用程式由以下兩個模組組成：

```
// log.js
function log(message) {
  const time = require('./time');
  console.log(`[${time()}] ${message}`);
}

module.exports = log;
```

```
// time.js
function time() {
  return new Date().getTime();
}

module.exports = time;
```

模組 `log` 會輸出包含 `time` 模組提供之當前日期的訊息。

當應用程式打包時，React Native 會使用 `__d` 函式在模組系統中註冊每個模組。在此應用中，眾多 `__d` 定義裡會包含 `log` 的定義：

```
__d('log', function() {
  ... // module's code
});
```

此調用將每個模組的代碼封裝到一個匿名函數中，我們通常稱之為工廠函數。模組系統運行時會追蹤每個模組的工廠函數、是否已執行過，以及執行結果（導出內容）。當需要某個模組時，模組系統會提供已緩存的導出內容，或首次執行該模組的工廠函數並保存結果。

假設你啟動應用並引入 `log`。此時，`log` 和 `time` 的工廠函數均未執行，因此沒有導出內容被緩存。接著，用戶修改 `time` 使其返回 `MM/DD` 格式的日期：

```js
// time.js
function bar() {
  const date = new Date();
  return `${date.getMonth() + 1}/${date.getDate()}`;
}

module.exports = bar;
```

打包工具會將 `time` 的新代碼發送給運行時（步驟1），當 `log` 最終被引入時，其導出的函數會執行並應用 `time` 的變更（步驟2）：

![](/blog/assets/hmr-step.png)

現在假設 `log` 的代碼在頂層引入 `time`：

```
const time = require('./time'); // top level require

// log.js
function log(message) {
  console.log(`[${time()}] ${message}`);
}

module.exports = log;
```

當 `log` 被引入時，運行時會緩存其導出內容及 `time` 的導出內容（步驟1）。之後若 `time` 被修改，HMR 流程不能僅替換 `time` 的代碼就結束。如果這樣做，當 `log` 執行時，會使用緩存的 `time`（舊代碼）。

為了讓 `log` 獲取 `time` 的變更，我們需要清除其緩存的導出內容，因為它依賴的模組之一已被熱替換（步驟3）。最終，當 `log` 再次被引入時，其工廠函數會執行並引入 `time`，從而獲取新代碼。

![](/blog/assets/hmr-log.png)

## HMR API

React Native 中的 HMR 通過引入 `hot` 物件擴展了模組系統。此 API 基於 [webpack](https://webpack.github.io/hot-module-replacement.md) 的同名 API。`hot` 物件公開了一個名為 `accept` 的函數，允許你定義一個回調函數，當模組需要熱替換時執行。例如，如果我們將 `time` 的代碼修改如下，每次保存時都會在控制台看到「time changed」：

```
// time.js
function time() {
  ... // new code
}

module.hot.accept(() => {
  console.log('time changed');
});

module.exports = time;
```

注意，僅在極少數情況下需要手動使用此 API。熱重載在大多數常見用例中應能直接使用。

## HMR 運行時

如前所述，有時僅接受 HMR 更新是不夠的，因為使用被熱替換模組的其他模組可能已執行且其引入內容已被緩存。例如，假設電影應用範例的依賴樹中有一個頂層 `MovieRouter`，它依賴於 `MovieSearch` 和 `MovieScreen` 視圖，而這些視圖又依賴於前述範例中的 `log` 和 `time` 模組：

![](/blog/assets/hmr-diamond.png)

如果用戶訪問了電影搜索視圖但未訪問其他視圖，除 `MovieScreen` 外的所有模組都會有緩存的導出內容。若對 `time` 模組進行修改，運行時需清除 `log` 的導出內容以使其獲取 `time` 的變更。流程不會就此結束：運行時會遞歸向上重複此過程，直到所有父模組都被接受。因此，它會抓取依賴 `log` 的模組並嘗試接受它們。對於 `MovieScreen`，可以跳過，因為它尚未被引入。對於 `MovieSearch`，則需清除其導出內容並遞歸處理其父模組。最終，對 `MovieRouter` 執行相同操作並結束，因為沒有模組依賴於它。

為了遍歷依賴樹，運行時會在 HMR 更新中從打包工具接收逆依賴樹。對於此範例，運行時會收到如下 JSON 物件：

```
{
  modules: [
    {
      name: 'time',
      code: /* time's new code */
    }
  ],
  inverseDependencies: {
    MovieRouter: [],
    MovieScreen: ['MovieRouter'],
    MovieSearch: ['MovieRouter'],
    log: ['MovieScreen', 'MovieSearch'],
    time: ['log'],
  }
}
```

## React 元件

React 元件要實現熱重載（Hot Reloading）會稍微困難一些。問題在於我們無法單純替換舊程式碼，否則會遺失元件的狀態。針對 React 網頁應用，[Dan Abramov](https://twitter.com/dan_abramov) 實作了一個 Babel [轉換器](https://gaearon.github.io/react-hot-loader/)，透過 webpack 的 HMR API 來解決此問題。簡而言之，他的解決方案是在「轉換階段」為每個 React 元件建立代理（proxy）。這些代理會保存元件狀態，並將生命週期方法委派給實際的元件（即我們要熱重載的目標）：

![](/blog/assets/hmr-proxy.png)

除了建立代理元件外，此轉換器還會定義 `accept` 函數，其中包含強制 React 重新渲染元件的程式碼。如此一來，我們就能在保留應用程式狀態的前提下，熱重載渲染相關的程式碼。

React Native 內建的[轉換器](https://github.com/facebook/react-native/blob/master/packager/transformer.js#L92-L95)使用 `babel-preset-react-native`，其[配置](https://github.com/facebook/react-native/blob/master/babel-preset/configs/hmr.js#L24-L31)會啟用 `react-transform`，用法與在基於 webpack 的 React 網頁專案中相同。

## Redux Stores

若要為 [Redux](https://redux.js.org/) store 啟用熱重載，只需像在基於 webpack 的網頁專案中那樣使用 HMR API：

```
// configureStore.js
import { createStore, applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk';
import reducer from '@site/reducers';

export default function configureStore(initialState) {
  const store = createStore(
    reducer,
    initialState,
    applyMiddleware(thunk),
  );

  if (module.hot) {
    module.hot.accept(() => {
      const nextRootReducer = require('../reducers/index').default;
      store.replaceReducer(nextRootReducer);
    });
  }

  return store;
};
```

當你修改 reducer 時，接受該 reducer 的程式碼會被傳送至用戶端。用戶端會發現該 reducer 本身無法處理熱更新，因此會查找所有引用它的模組並嘗試接受更新。最終，流程會抵達單一的 store 模組（即 `configureStore`），該模組將接受此次 HMR 更新。

## 結論

若你有興趣協助改進熱重載功能，建議閱讀 [Dan Abramov 關於熱重載未來的文章](https://medium.com/@dan_abramov/hot-reloading-in-react-1140438583bf#.jmivpvmz4)並參與貢獻。例如，Johny Days 正著手[實現多客戶端同步熱重載](https://github.com/facebook/react-native/pull/6179)。我們需要大家共同維護與改進此功能。

React Native 讓我們能重新思考應用程式的開發方式，以打造更優異的開發者體驗。熱重載只是其中一環，我們還能運用哪些創新技巧來讓它更完善？