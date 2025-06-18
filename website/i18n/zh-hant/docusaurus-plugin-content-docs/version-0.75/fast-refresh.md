---
id: fast-refresh
title: Fast Refresh
---

Fast Refresh 是 React Native 的一項功能，可讓您幾乎即時獲得 React 組件的變更反饋。Fast Refresh 預設為啟用狀態，您可以在 [React Native 開發者選單](/docs/debugging#accessing-the-in-app-developer-menu) 中切換「啟用 Fast Refresh」。啟用 Fast Refresh 後，大多數編輯應在一兩秒內可見。

## 運作原理

- 如果您編輯的模組**僅導出 React 組件**，Fast Refresh 將僅更新該模組的代碼，並重新渲染您的組件。您可以編輯該文件中的任何內容，包括樣式、渲染邏輯、事件處理程序或效果。
- 如果您編輯的模組導出內容**不包含** React 組件，Fast Refresh 將重新執行該模組以及導入它的其他模組。因此，如果 `Button.js` 和 `Modal.js` 都導入 `Theme.js`，編輯 `Theme.js` 將更新這兩個組件。
- 最後，如果您編輯的**文件**被 **React 樹之外的模組導入**，Fast Refresh **將回退到執行完整重新載入**。您可能有一個文件既渲染 React 組件，又導出一個被**非 React 組件**導入的值。例如，您的組件可能還導出一個常數，而一個非 React 實用模組導入它。在這種情況下，考慮將該常數遷移到單獨的文件中，並在兩個文件中導入它。這將重新啟用 Fast Refresh 功能。其他情況通常可以通過類似方式解決。

## 錯誤恢復能力

如果在 Fast Refresh 會話期間出現**語法錯誤**，您可以修復它並再次保存文件。紅色錯誤框將消失。包含語法錯誤的模組將被阻止執行，因此您無需重新載入應用程序。

如果在**模組初始化期間發生運行時錯誤**（例如鍵入 `Style.create` 而不是 `StyleSheet.create`），一旦您修復錯誤，Fast Refresh 會話將繼續。紅色錯誤框將消失，模組將被更新。

如果您的錯誤導致**組件內部發生運行時錯誤**，Fast Refresh 會話在您修復錯誤後也將繼續。在這種情況下，React 將使用更新後的代碼重新掛載您的應用程序。

如果您的應用中有 [錯誤邊界](https://reactjs.org/docs/error-boundaries.html)（這對於生產環境中的優雅失敗是個好主意），它們將在下一次編輯後嘗試重新渲染。從這個意義上說，擁有錯誤邊界可以防止您總是被踢回根應用屏幕。然而，請記住，錯誤邊界不應**過於**細粒度。它們在生產環境中被 React 使用，應始終有意識地設計。

## 限制

Fast Refresh 嘗試保留您正在編輯的組件中的本地 React 狀態，但僅在安全的情況下才會這樣做。以下是您可能會看到每次編輯文件時本地狀態被重置的幾個原因：

- 類組件的本地狀態不會被保留（僅函數組件和 Hooks 會保留狀態）。
- 您正在編輯的模組可能**除了** React 組件外還有其他導出內容。
- 有時，模組會導出調用高階組件的結果，例如 `createNavigationContainer(MyScreen)`。如果返回的組件是一個類，狀態將被重置。

長期來看，隨著您的代碼庫中更多部分轉向函數組件和 Hooks，您可以預期在更多情況下狀態會被保留。

## 提示

- Fast Refresh 預設保留函數組件（和 Hooks）中的 React 本地狀態。
- 有時您可能希望**強制**重置狀態並重新掛載組件。例如，這對於調整僅在掛載時發生的動畫非常有用。要做到這一點，您可以在您正在編輯的文件中的任何位置添加 `// @refresh reset`。此指令僅對該文件有效，並指示 Fast Refresh 在每次編輯時重新掛載該文件中定義的組件。

## Fast Refresh 與 Hooks

When possible, Fast Refresh attempts to preserve the state of your component between edits. In particular, `useState` and `useRef` preserve their previous values as long as you don't change their arguments or the order of the Hook calls.

Hooks with dependencies—such as `useEffect`, `useMemo`, and `useCallback`—will _always_ update during Fast Refresh. Their list of dependencies will be ignored while Fast Refresh is happening.

For example, when you edit `useMemo(() => x * 2, [x])` to `useMemo(() => x * 10, [x])`, it will re-run even though `x` (the dependency) has not changed. If React didn't do that, your edit wouldn't reflect on the screen!

Sometimes, this can lead to unexpected results. For example, even a `useEffect` with an empty array of dependencies would still re-run once during Fast Refresh. However, writing code resilient to an occasional re-running of `useEffect` is a good practice even without Fast Refresh. This makes it easier for you to later introduce new dependencies to it.