---
id: fast-refresh
title: Fast Refresh
---

Fast Refresh 是 React Native 的一項功能，可讓您即時獲得 React 元件變更的反饋。Fast Refresh 預設啟用，您可以在 [React Native 開發者選單](/docs/debugging#accessing-the-in-app-developer-menu) 中切換「啟用 Fast Refresh」。啟用後，大多數編輯應能在一兩秒內顯示變更。

## 運作原理

- 若編輯**僅導出 React 元件**的模組，Fast Refresh 僅更新該模組的程式碼並重新渲染元件。您可以修改該檔案中的任何內容，包括樣式、渲染邏輯、事件處理器或副作用。
- 若編輯導出內容**非 React 元件**的模組，Fast Refresh 會重新執行該模組及其導入模組。例如 `Button.js` 和 `Modal.js` 都導入 `Theme.js`，則編輯 `Theme.js` 會同時更新兩個元件。
- 最後，若編輯的檔案被**非 React 樹狀結構的模組導入**，Fast Refresh **會退回完整重新載入**。例如某檔案同時導出 React 元件和常數值，而該常數被非 React 工具模組導入。此時可考慮將常數移至獨立檔案並分別導入，以重新啟用 Fast Refresh 功能。其他情況通常也能用類似方式解決。

## 錯誤恢復能力

若在 Fast Refresh 階段發生**語法錯誤**，修正後重新儲存檔案即可。紅屏錯誤會消失，且含有語法錯誤的模組會被阻止執行，無需重新載入應用程式。

若在**模組初始化時發生運行時錯誤**（例如誤將 `StyleSheet.create` 打成 `Style.create`），修正錯誤後 Fast Refresh 會繼續運作。紅屏錯誤消失，模組會更新。

若錯誤導致**元件內部發生運行時錯誤**，修正後 Fast Refresh 同樣會繼續運作。此時 React 會使用更新後的程式碼重新掛載應用程式。

若應用程式中設有 [錯誤邊界](https://reactjs.org/docs/error-boundaries.html)（建議在生產環境中用於優雅降級），紅屏錯誤後的下次編輯會觸發重新渲染。因此錯誤邊界能避免您被強制退回根畫面。但請注意錯誤邊界不應過度細分，它們在生產環境中會被 React 使用，應謹慎設計。

## 限制

Fast Refresh 會嘗試保留編輯中元件的 React 本地狀態，但僅在安全情況下進行。以下情況可能導致每次編輯時本地狀態被重置：

- 類別元件不保留本地狀態（僅函式元件和 Hooks 會保留）
- 編輯的模組可能**同時導出其他內容**（非僅 React 元件）
- 有時模組會導出高階元件調用結果（如 `createNavigationContainer(MyScreen)`），若返回元件是類別，狀態會被重置

長期而言，隨著程式碼庫逐漸改用函式元件和 Hooks，預期更多情況下能保留狀態。

## 技巧

- Fast Refresh 預設保留函式元件（及 Hooks）的 React 本地狀態
- 有時您可能需要**強制重置狀態並重新掛載元件**（例如調整僅在掛載時執行的動畫）。此時可在編輯檔案中加入 `// @refresh reset` 註解指令。此指令僅作用於當前檔案，會使 Fast Refresh 在每次編輯時重新掛載該檔案定義的元件

## Fast Refresh 與 Hooks

在可能的情況下，Fast Refresh 會嘗試保留元件在編輯之間的狀態。特別是 `useState` 和 `useRef`，只要你不改變它們的參數或 Hook 呼叫的順序，就會保留先前的值。

具有依賴項的 Hook（例如 `useEffect`、`useMemo` 和 `useCallback`）在 Fast Refresh 期間_總是_會更新。在 Fast Refresh 進行時，它們的依賴項列表會被忽略。

例如，當你將 `useMemo(() => x * 2, [x])` 編輯為 `useMemo(() => x * 10, [x])` 時，即使 `x`（依賴項）沒有改變，它也會重新執行。如果 React 不這樣做，你的編輯就不會反映在畫面上！

有時，這可能會導致意想不到的結果。例如，即使是一個具有空依賴項陣列的 `useEffect`，在 Fast Refresh 期間仍然會重新執行一次。然而，撰寫能夠偶爾應對 `useEffect` 重新執行的程式碼是一種良好的實踐，即使沒有 Fast Refresh 也是如此。這使得你之後更容易為它引入新的依賴項。