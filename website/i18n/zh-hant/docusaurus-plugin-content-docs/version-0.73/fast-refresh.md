---
id: fast-refresh
title: Fast Refresh
---

Fast Refresh 是 React Native 的一項功能，可讓您幾乎即時獲得 React 元件變更的反饋。Fast Refresh 預設啟用，您可以在 [React Native 開發者選單](/docs/debugging#accessing-the-in-app-developer-menu) 中切換「啟用 Fast Refresh」。啟用 Fast Refresh 後，大多數編輯應在一兩秒內可見。

## 運作原理

- 如果您編輯的模組**僅導出 React 元件**，Fast Refresh 將僅更新該模組的程式碼，並重新渲染您的元件。您可以編輯該檔案中的任何內容，包括樣式、渲染邏輯、事件處理程序或效果。
- 如果您編輯的模組導出內容**非** React 元件，Fast Refresh 將重新執行該模組以及導入它的其他模組。因此，如果 `Button.js` 和 `Modal.js` 都導入 `Theme.js`，編輯 `Theme.js` 將更新這兩個元件。
- 最後，如果您編輯的檔案**被 React 樹之外的模組導入**，Fast Refresh **將回退到執行完整重新載入**。您可能有一個檔案，它渲染一個 React 元件，但也導出一個值，該值被**非 React 元件**導入。例如，您的元件可能還導出一個常數，而非 React 的實用模組導入它。在這種情況下，考慮將該常數遷移到單獨的檔案中，並在兩個檔案中導入它。這將重新啟用 Fast Refresh 功能。其他情況通常可以通過類似方式解決。

## 錯誤恢復能力

如果您在 Fast Refresh 會話期間出現**語法錯誤**，您可以修復它並再次儲存檔案。紅色錯誤框將消失。具有語法錯誤的模組將被阻止執行，因此您無需重新載入應用程式。

如果您在模組初始化期間出現**運行時錯誤**（例如，輸入 `Style.create` 而不是 `StyleSheet.create`），一旦您修復錯誤，Fast Refresh 會話將繼續。紅色錯誤框將消失，模組將被更新。

如果您犯了一個導致**元件內部運行時錯誤**的錯誤，Fast Refresh 會話**也將**在您修復錯誤後繼續。在這種情況下，React 將使用更新後的程式碼重新掛載您的應用程式。

如果您的應用程式中設有[錯誤邊界](https://reactjs.org/docs/error-boundaries.html)（這對於生產環境中的優雅失敗是個好主意），它們將在下一次編輯後嘗試重新渲染。從這個意義上說，擁有錯誤邊界可以防止您總是返回到根應用畫面。然而，請記住，錯誤邊界不應**過於**細粒度。它們在生產環境中被 React 使用，應始終有意識地設計。

## 限制

Fast Refresh 嘗試保留您正在編輯的元件中的本地 React 狀態，但僅在安全的情況下才會這樣做。以下是您可能會看到每次編輯檔案時本地狀態被重置的幾個原因：

- 類別元件的本地狀態不會被保留（僅函式元件和 Hooks 保留狀態）。
- 您正在編輯的模組可能**除了** React 元件之外還有其他導出。
- 有時，模組會導出調用高階元件的結果，例如 `createNavigationContainer(MyScreen)`。如果返回的元件是一個類別，狀態將被重置。

長期來看，隨著您的程式碼庫更多地轉向函式元件和 Hooks，您可以預期在更多情況下保留狀態。

## 提示

- Fast Refresh 預設保留函式元件（和 Hooks）中的 React 本地狀態。
- 有時您可能希望**強制**重置狀態並重新掛載元件。例如，這對於調整僅在掛載時發生的動畫非常有用。要做到這一點，您可以在您正在編輯的檔案中的任何位置添加 `// @refresh reset`。此指令僅限於該檔案，並指示 Fast Refresh 在每次編輯時重新掛載該檔案中定義的元件。

## Fast Refresh 與 Hooks

在可能的情況下，Fast Refresh 會嘗試保留元件狀態。特別是 `useState` 和 `useRef` 這類 Hook，只要不修改其參數或 Hook 的調用順序，就會維持先前的值。

具有依賴項的 Hook（如 `useEffect`、`useMemo` 和 `useCallback`）在 Fast Refresh 期間「必定」會更新。此時它們的依賴項列表會被忽略。

例如，當你將 `useMemo(() => x * 2, [x])` 改為 `useMemo(() => x * 10, [x])` 時，即使依賴項 `x` 未改變也會重新執行。若 React 未如此處理，你的修改將無法反映在畫面上！

這有時可能導致非預期結果。例如，即使 `useEffect` 設定了空依賴陣列，仍會在 Fast Refresh 時執行一次。但撰寫能承受 `useEffect` 偶發重新執行的程式碼，本就是良好的開發實踐（即使不考慮 Fast Refresh），這能為後續新增依賴項預留彈性。