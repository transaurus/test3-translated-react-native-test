---
id: fast-refresh
title: Fast Refresh
---

Fast Refresh 是 React Native 的一項功能，可讓您即時獲得 React 元件變更的反饋。Fast Refresh 預設啟用，您可以在 [React Native 開發者選單](/docs/debugging#accessing-the-in-app-developer-menu) 中切換「啟用 Fast Refresh」。啟用後，大多數編輯應能在一兩秒內顯示變更。

## 運作原理

- 若編輯的模組**僅導出 React 元件**，Fast Refresh 僅更新該模組的程式碼並重新渲染元件。您可修改該檔案中的任何內容，包括樣式、渲染邏輯、事件處理器或副作用。
- 若編輯的模組導出內容包含_非_ React 元件，Fast Refresh 會重新執行該模組及其導入模組。例如 `Button.js` 和 `Modal.js` 均導入 `Theme.js`，則編輯 `Theme.js` 會同時更新兩個元件。
- 最後，若編輯的檔案被**React 樹外的模組導入**，Fast Refresh **會退回完整重新載入**。例如您的元件檔案同時導出一個常數，且該常數被**非 React 工具模組**導入。此時可考慮將常數移至獨立檔案，再分別導入以恢復 Fast Refresh 功能。其他情況通常可依此類推解決。

## 錯誤恢復能力

若在 Fast Refresh 過程中發生**語法錯誤**，修正後重新存檔即可。紅屏錯誤會消失，且含語法錯誤的模組不會執行，無需重新載入應用程式。

若在**模組初始化時發生運行時錯誤**（例如誤輸入 `Style.create` 而非 `StyleSheet.create`），修正錯誤後 Fast Refresh 會繼續運作。紅屏消失且模組會更新。

若錯誤導致**元件內部運行時錯誤**，修正後 Fast Refresh 同樣會繼續運作。React 會使用更新後的程式碼重新掛載應用程式。

若應用程式中設有[錯誤邊界](https://reactjs.org/docs/error-boundaries.html)（建議用於生產環境的優雅降級），紅屏後的下次編輯會重試渲染。適當的錯誤邊界可避免您被強制返回根畫面，但需注意錯誤邊界不應_過度細分_，因其在生產環境中會被 React 使用，應謹慎設計。

## 限制

Fast Refresh 會嘗試保留編輯中元件的本地 React 狀態，但僅在安全情況下進行。以下情況可能導致每次編輯時本地狀態被重置：

- 類別元件不保留本地狀態（僅函式元件和 Hooks 保留狀態）。
- 編輯的模組可能除 React 元件外還導出_其他內容_。
- 有時模組會導出高階元件調用結果（如 `createNavigationContainer(MyScreen)`）。若返回元件為類別，狀態將被重置。

長期而言，隨著程式碼庫逐漸改用函式元件和 Hooks，狀態保留的適用情況將會增加。

## 技巧

- Fast Refresh 預設保留函式元件（及 Hooks）的 React 本地狀態。
- 有時您可能需要_強制_重置狀態並重新掛載元件（例如調整僅在掛載時執行的動畫）。可在編輯檔案中添加 `// @refresh reset` 指令。此指令僅作用於當前檔案，會使 Fast Refresh 在每次編輯時重新掛載該檔案定義的元件。

## Fast Refresh 與 Hooks

在可能的情況下，Fast Refresh 會嘗試保留元件在編輯之間的狀態。特別是 `useState` 和 `useRef`，只要你不改變它們的參數或 Hook 調用的順序，它們就會保留先前的值。

具有依賴項的 Hook——例如 `useEffect`、`useMemo` 和 `useCallback`——在 Fast Refresh 期間總是會更新。在 Fast Refresh 進行時，它們的依賴項列表會被忽略。

例如，當你將 `useMemo(() => x * 2, [x])` 編輯為 `useMemo(() => x * 10, [x])` 時，即使 `x`（依賴項）沒有改變，它也會重新運行。如果 React 不這樣做，你的編輯就不會反映在螢幕上！

有時，這可能會導致意想不到的結果。例如，即使是一個具有空依賴項數組的 `useEffect` 也會在 Fast Refresh 期間重新運行一次。然而，即使沒有 Fast Refresh，編寫能夠偶爾重新運行 `useEffect` 的彈性程式碼也是一個好習慣。這使得你以後更容易為它引入新的依賴項。