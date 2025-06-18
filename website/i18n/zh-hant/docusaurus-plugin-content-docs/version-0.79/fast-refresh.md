---
id: fast-refresh
title: Fast Refresh
---

Fast Refresh 是 React Native 的一項功能，可讓您即時獲得 React 元件變更的反饋。Fast Refresh 預設為啟用狀態，您可以在 [React Native 開發者選單](/docs/debugging#accessing-the-in-app-developer-menu) 中切換「啟用 Fast Refresh」。啟用後，大多數編輯應能在一兩秒內顯示變更。

## 運作原理

- 若您編輯的模組**僅導出 React 元件**，Fast Refresh 僅會更新該模組的程式碼，並重新渲染您的元件。您可以編輯該檔案中的任何內容，包括樣式、渲染邏輯、事件處理程序或副作用。
- 若您編輯的模組導出內容包含_非_ React 元件，Fast Refresh 會重新執行該模組及其導入模組。例如若 `Button.js` 和 `Modal.js` 都導入 `Theme.js`，編輯 `Theme.js` 將同時更新兩個元件。
- 最後，若您編輯的檔案被**React 樹之外的模組導入**，Fast Refresh **將退回完整重新載入**。您可能有個檔案既渲染 React 元件，又導出被**非 React 元件**導入的值。例如您的元件可能同時導出一個常數，而某個非 React 工具模組導入該常數。此時可考慮將常數移至獨立檔案，並讓兩個檔案分別導入，這樣就能重新啟用 Fast Refresh。其他情況通常也能用類似方式解決。

## 錯誤恢復能力

若在 Fast Refresh 過程中發生**語法錯誤**，修正後再次儲存檔案即可。紅色錯誤框會消失。系統會阻止執行含語法錯誤的模組，因此您無需重新載入應用程式。

若在**模組初始化時發生運行時錯誤**（例如誤將 `StyleSheet.create` 打成 `Style.create`），修正錯誤後 Fast Refresh 會繼續運作。紅色錯誤框會消失，模組將被更新。

若錯誤導致**元件內部發生運行時錯誤**，修正後 Fast Refresh 仍會繼續運作。此時 React 會使用更新後的程式碼重新掛載您的應用程式。

若應用程式中設有[錯誤邊界](https://reactjs.org/docs/error-boundaries.html)（這對生產環境的優雅降級是個好做法），下次編輯時會重新嘗試渲染。從這個角度來說，錯誤邊界能避免您總是被踢回應用程式根畫面。但請注意錯誤邊界不應_過於_細碎。React 在生產環境會使用這些邊界，應始終有意識地設計它們。

## 限制

Fast Refresh 會嘗試保留您正在編輯的元件中的 React 本地狀態，但僅在安全情況下進行。以下是您可能發現每次編輯檔案時本地狀態被重置的原因：

- 類別元件的本地狀態不會被保留（僅函式元件和 Hooks 能保留狀態）。
- 您編輯的模組可能_除了_ React 元件外還導出其他內容。
- 有時模組會導出高階元件的調用結果，例如 `createNavigationContainer(MyScreen)`。若返回的元件是類別，狀態將被重置。

長期來看，隨著更多程式碼改用函式元件和 Hooks，預期會有更多情況能保留狀態。

## 技巧

- Fast Refresh 預設會保留函式元件（及 Hooks）中的 React 本地狀態。
- 有時您可能想_強制_重置狀態並重新掛載元件。例如在調整僅在掛載時執行的動畫時就很實用。要做到這點，可在編輯的檔案中任意位置加入 `// @refresh reset`。此指令僅作用於該檔案，會指示 Fast Refresh 在每次編輯時重新掛載該檔案定義的元件。

## Fast Refresh 與 Hooks

在可能的情況下，Fast Refresh 會嘗試保留元件在編輯之間的狀態。特別是 `useState` 和 `useRef`，只要你不改變它們的參數或 Hook 調用的順序，它們就會保留先前的值。

具有依賴項的 Hook——例如 `useEffect`、`useMemo` 和 `useCallback`——在 Fast Refresh 期間總是會更新。在 Fast Refresh 進行時，它們的依賴項列表會被忽略。

例如，當你將 `useMemo(() => x * 2, [x])` 編輯為 `useMemo(() => x * 10, [x])` 時，即使 `x`（依賴項）沒有改變，它也會重新運行。如果 React 不這樣做，你的編輯就不會反映在螢幕上！

有時，這可能會導致意想不到的結果。例如，即使是一個具有空依賴項數組的 `useEffect` 也會在 Fast Refresh 期間重新運行一次。然而，即使沒有 Fast Refresh，編寫能夠偶爾重新運行 `useEffect` 的彈性程式碼也是一個好習慣。這使得你以後更容易為它引入新的依賴項。