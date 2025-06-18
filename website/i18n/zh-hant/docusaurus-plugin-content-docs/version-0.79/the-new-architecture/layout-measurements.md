# 測量佈局

有時，您需要測量當前的佈局，以便對整體佈局進行調整或根據測量結果執行特定邏輯。

React Native 提供了一些原生方法來獲取視圖的尺寸資訊。

調用這些方法的最佳時機是在 `useLayoutEffect` 鉤子中：這將提供最新的測量值，並讓您能在同一幀中應用基於這些測量的變更。

典型程式碼如下所示：

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
此處描述的方法適用於 React Native 提供的大多數預設元件。但對於未直接由原生視圖支持的複合元件（通常包括您在應用中自定義的大多數元件），這些方法不可用。
:::

## measure(callback)

測量指定視圖在視口中的位置（`x` 和 `y`）、`寬度` 和 `高度`。通過異步回調返回結果。若成功，回調函數將接收以下參數：

- `x`: 被測量視圖原點（左上角）在視口中的 x 座標。
- `y`: 被測量視圖原點（左上角）在視口中的 y 座標。
- `width`: 視圖的寬度。
- `height`: 視圖的高度。
- `pageX`: 視圖在視口（通常是整個屏幕）中的 x 座標。
- `pageY`: 視圖在視口（通常是整個屏幕）中的 y 座標。

`measure()` 返回的 `width` 和 `height` 是元件在視口中的實際尺寸。

## measureInWindow(callback)

測量指定視圖在當前窗口中的位置（`x` 和 `y`），通過異步回調返回結果。若 React 根視圖嵌入在其他原生視圖中，此方法將提供絕對座標。若成功，回調函數將接收以下參數：

- `x`: 視圖在當前窗口中的 x 座標。
- `y`: 視圖在當前窗口中的 y 座標。
- `width`: 視圖的寬度。
- `height`: 視圖的高度。