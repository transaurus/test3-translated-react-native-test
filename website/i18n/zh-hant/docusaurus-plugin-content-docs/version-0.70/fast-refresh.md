---
id: fast-refresh
title: Fast Refresh
---

Fast Refresh is a React Native feature that allows you to get near-instant feedback for changes in your React components. Fast Refresh is enabled by default, and you can toggle "Enable Fast Refresh" in the [React Native developer menu](/docs/debugging#accessing-the-in-app-developer-menu). With Fast Refresh enabled, most edits should be visible within a second or two.

## How It Works

- If you edit a module that **only exports React component(s)**, Fast Refresh will update the code only for that module, and re-render your component. You can edit anything in that file, including styles, rendering logic, event handlers, or effects.
- If you edit a module with exports that _aren't_ React components, Fast Refresh will re-run both that module, and the other modules importing it. So if both `Button.js` and `Modal.js` import `Theme.js`, editing `Theme.js` will update both components.
- Finally, if you **edit a file** that's **imported by modules outside of the React tree**, Fast Refresh **will fall back to doing a full reload**. You might have a file which renders a React component but also exports a value that is imported by a **non-React component**. For example, maybe your component also exports a constant, and a non-React utility module imports it. In that case, consider migrating the constant to a separate file and importing it into both files. This will re-enable Fast Refresh to work. Other cases can usually be solved in a similar way.

## Error Resilience

If you make a **syntax error** during a Fast Refresh session, you can fix it and save the file again. The redbox will disappear. Modules with syntax errors are prevented from running, so you won't need to reload the app.

If you make a **runtime error during the module initialization** (for example, typing `Style.create` instead of `StyleSheet.create`), the Fast Refresh session will continue once you fix the error. The redbox will disappear, and the module will be updated.

If you make a mistake that leads to a **runtime error inside your component**, the Fast Refresh session will _also_ continue after you fix the error. In that case, React will remount your application using the updated code.

If you have [error boundaries](https://reactjs.org/docs/error-boundaries.html) in your app (which is a good idea for graceful failures in production), they will retry rendering on the next edit after a redbox. In that sense, having an error boundary can prevent you from always getting kicked out to the root app screen. However, keep in mind that error boundaries shouldn't be _too_ granular. They are used by React in production, and should always be designed intentionally.

## Limitations

Fast Refresh tries to preserve local React state in the component you're editing, but only if it's safe to do so. Here's a few reasons why you might see local state being reset on every edit to a file:

- Local state is not preserved for class components (only function components and Hooks preserve state).
- The module you're editing might have _other_ exports in addition to a React component.
- Sometimes, a module would export the result of calling higher-order component like `createNavigationContainer(MyScreen)`. If the returned component is a class, state will be reset.

In the longer term, as more of your codebase moves to function components and Hooks, you can expect state to be preserved in more cases.

## Tips

- Fast Refresh preserves React local state in function components (and Hooks) by default.
- Sometimes you might want to _force_ the state to be reset, and a component to be remounted. For example, this can be handy if you're tweaking an animation that only happens on mount. To do this, you can add `// @refresh reset` anywhere in the file you're editing. This directive is local to the file, and instructs Fast Refresh to remount components defined in that file on every edit.

## Fast Refresh and Hooks

在可能的情況下，Fast Refresh 會嘗試保留元件狀態於編輯之間。特別是 `useState` 和 `useRef` 會保留其先前的值，只要你不變更其參數或 Hook 呼叫的順序。

具有依賴項的 Hook——例如 `useEffect`、`useMemo` 和 `useCallback`——在 Fast Refresh 期間將_總是_更新。當 Fast Refresh 進行時，它們的依賴項列表會被忽略。

舉例來說，當你將 `useMemo(() => x * 2, [x])` 編輯為 `useMemo(() => x * 10, [x])` 時，即使 `x`（依賴項）沒有變更，它也會重新執行。如果 React 不這麼做，你的編輯就不會反映在畫面上！

有時，這可能會導致意想不到的結果。例如，即使是一個具有空依賴項陣列的 `useEffect`，在 Fast Refresh 期間仍會重新執行一次。然而，撰寫能夠偶爾應對 `useEffect` 重新執行的程式碼是一種良好的實踐，即使沒有 Fast Refresh 也是如此。這使得你之後更容易為它引入新的依賴項。