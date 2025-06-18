# 測量佈局

有時，您需要測量當前的佈局，以便對整體佈局進行調整或根據測量結果執行特定邏輯。

React Native 提供了一些原生方法來獲取視圖的尺寸資訊。

調用這些方法的最佳時機是在 `useLayoutEffect` 鉤子中：這將提供最新的測量值，並允許您在同一幀中應用變更。

典型程式碼如下：

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
此處描述的方法適用於 React Native 提供的大多數預設元件。但對於未直接由原生視圖支持的複合元件（通常是您應用中自定義的元件），這些方法可能無法使用。
:::

## measure(callback)

測量指定視圖在視窗中的位置（`x` 和 `y`）、寬度（`width`）和高度（`height`）。結果通過異步回調返回，成功時回調參數包括：

- `x`: 被測量視圖左上角在視窗中的 x 座標
- `y`: 被測量視圖左上角在視窗中的 y 座標
- `width`: 視圖的寬度
- `height`: 視圖的高度
- `pageX`: 視圖在整個視窗中的 x 絕對座標（通常是整個屏幕）
- `pageY`: 視圖在整個視窗中的 y 絕對座標（通常是整個屏幕）

注意：`measure()` 返回的 `width` 和 `height` 是元件在視窗中的實際尺寸。

## measureInWindow(callback)

測量指定視圖在當前視窗中的絕對位置（`x` 和 `y`），結果通過異步回調返回。當 React 根視圖嵌入其他原生視圖時，此方法可獲取絕對座標。成功時回調參數包括：

- `x`: 視圖在當前視窗中的 x 座標
- `y`: 視圖在當前視窗中的 y 座標
- `width`: 視圖的寬度
- `height`: 視圖的高度