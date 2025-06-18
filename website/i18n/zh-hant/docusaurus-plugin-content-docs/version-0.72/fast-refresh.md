---
id: fast-refresh
title: Fast Refresh
---

Fast Refresh 是 React Native 的一項功能，可讓您幾乎即時獲得 React 組件變更的反饋。Fast Refresh 預設為啟用狀態，您可以在 [React Native 開發者選單](/docs/debugging#accessing-the-in-app-developer-menu) 中切換「啟用 Fast Refresh」。啟用 Fast Refresh 後，大多數編輯應在一兩秒內可見。

## 運作原理

- 如果您編輯的模組**僅導出 React 組件**，Fast Refresh 將僅更新該模組的代碼，並重新渲染您的組件。您可以編輯該文件中的任何內容，包括樣式、渲染邏輯、事件處理程序或效果。
- 如果您編輯的模組導出內容**並非** React 組件，Fast Refresh 將重新執行該模組以及導入它的其他模組。因此，如果 `Button.js` 和 `Modal.js` 都導入 `Theme.js`，編輯 `Theme.js` 將更新這兩個組件。
- 最後，如果您編輯的文件**被 React 樹之外的模組導入**，Fast Refresh **將回退到執行完整重新載入**。您可能有一個文件既渲染 React 組件，又導出一個被**非 React 組件**導入的值。例如，您的組件可能還導出一個常數，而非 React 的實用程序模組導入它。在這種情況下，考慮將該常數遷移到單獨的文件中，並在兩個文件中導入它。這將重新啟用 Fast Refresh 功能。其他情況通常可以通過類似方式解決。

## 錯誤恢復能力

如果在 Fast Refresh 會話期間出現**語法錯誤**，您可以修復它並再次保存文件。紅色錯誤框將消失。具有語法錯誤的模組將被阻止執行，因此您無需重新載入應用程序。

如果在**模組初始化期間發生運行時錯誤**（例如，鍵入 `Style.create` 而不是 `StyleSheet.create`），一旦您修復錯誤，Fast Refresh 會話將繼續。紅色錯誤框將消失，模組將被更新。

如果您的錯誤導致**組件內部發生運行時錯誤**，Fast Refresh 會話在您修復錯誤後**也將**繼續。在這種情況下，React 將使用更新後的代碼重新掛載您的應用程序。

如果您的應用中有[錯誤邊界](https://reactjs.org/docs/error-boundaries.html)（這對於生產環境中的優雅失敗是個好主意），它們將在下一次編輯後嘗試重新渲染。從這個意義上說，擁有錯誤邊界可以防止您總是返回到根應用屏幕。但是，請記住，錯誤邊界不應**過於**細粒度。它們在生產環境中被 React 使用，應始終有意識地設計。

## 限制

Fast Refresh 嘗試保留您正在編輯的組件中的本地 React 狀態，但僅在安全的情況下才會這樣做。以下是您可能會看到每次編輯文件時本地狀態被重置的幾個原因：

- 本地狀態不會保留給類組件（僅函數組件和 Hooks 保留狀態）。
- 您正在編輯的模組可能**除了** React 組件外還有其他導出。
- 有時，模組會導出調用高階組件的結果，例如 `createNavigationContainer(MyScreen)`。如果返回的組件是一個類，狀態將被重置。

長期來看，隨著更多代碼庫遷移到函數組件和 Hooks，您可以預期在更多情況下保留狀態。

## 提示

- Fast Refresh 預設保留函數組件（和 Hooks）中的 React 本地狀態。
- 有時您可能希望**強制**重置狀態並重新掛載組件。例如，這對於調整僅在掛載時發生的動畫非常有用。為此，您可以在正在編輯的文件中的任何位置添加 `// @refresh reset`。此指令僅限於該文件，並指示 Fast Refresh 在每次編輯時重新掛載該文件中定義的組件。

## Fast Refresh 與 Hooks

在可能的情況下，Fast Refresh 會嘗試保留元件狀態於編輯之間。特別是 `useState` 和 `useRef` 這類 Hook，只要你不變更其參數或 Hook 呼叫的順序，它們就會保留先前的值。

具有依賴項的 Hook（例如 `useEffect`、`useMemo` 和 `useCallback`）在 Fast Refresh 期間「必定」會更新。當 Fast Refresh 進行時，它們的依賴項列表將被忽略。

舉例來說，當你將 `useMemo(() => x * 2, [x])` 編輯為 `useMemo(() => x * 10, [x])` 時，即使 `x`（依賴項）沒有變更，它仍會重新執行。如果 React 不這麼做，你的編輯就不會反映在畫面上！

有時這可能導致非預期的結果。例如，即使是一個具有空依賴項陣列的 `useEffect`，在 Fast Refresh 期間仍會重新執行一次。然而，撰寫能承受 `useEffect` 偶發重新執行的程式碼，即便沒有 Fast Refresh 也是良好的實踐，這能讓你在後續更容易為其新增依賴項。