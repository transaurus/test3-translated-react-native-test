# 測量佈局

有時，您需要測量當前的佈局，以便對整體佈局進行一些調整，或是根據測量結果做出決策並調用特定邏輯。

React Native 提供了一些原生方法來獲取視圖的測量值。

調用這些方法的最佳時機是在 `useLayoutEffect` 鉤子中：這將為您提供最新的測量值，並讓您能夠在計算出測量值的同一幀中應用變更。

典型的程式碼如下所示：

```tsx
function AComponent(children) {
  const targetRef = React.useRef(null)

  useLayoutEffect(() => {
    targetRef.current?.measure((x, y, width, height, pageX, pageY) => {
      //do something with the measurements
    });
  }, [ /* add dependencies here */]);

  return (
    <View ref={targetRef}>
     {children}
    <View />
  );
}
```

:::note
這裡描述的方法在 React Native 提供的大多數預設元件中都可使用。然而，對於那些沒有直接由原生視圖支持的複合元件，這些方法是不可用的。這通常包括您在應用中自行定義的大多數元件。
:::

## measure(callback)

確定給定視圖在視口中的位置（`x` 和 `y`）、`寬度` 和 `高度`。通過異步回調返回這些值。如果成功，回調將被調用並傳入以下參數：

- `x`：測量視圖在視口中的原點（左上角）的 `x` 座標。
- `y`：測量視圖在視口中的原點（左上角）的 `y` 座標。
- `width`：視圖的 `寬度`。
- `height`：視圖的 `高度`。
- `pageX`：視圖在視口中的 `x` 座標（通常是整個屏幕）。
- `pageY`：視圖在視口中的 `y` 座標（通常是整個屏幕）。

此外，`measure()` 返回的 `寬度` 和 `高度` 是元件在視口中的實際尺寸。

## measureInWindow(callback)

確定給定視圖在當前窗口中的位置（`x` 和 `y`），並通過異步回調返回這些值。如果 React 根視圖嵌入在另一個原生視圖中，這將為您提供絕對座標。如果成功，回調將被調用並傳入以下參數：

- `x`：視圖在當前窗口中的 `x` 座標。
- `y`：視圖在當前窗口中的 `y` 座標。
- `width`：視圖的 `寬度`。
- `height`：視圖的 `高度`。