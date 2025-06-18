---
id: landing-page
title: About the New Architecture
---

:::info
若您需要新架構的指南，請移步至[工作組](https://github.com/reactwg/react-native-new-architecture#guides)查閱。
:::

自2018年起，React Native團隊便著手重新設計核心架構，旨在讓開發者能打造更高品質的使用體驗。截至2024年，此版本React Native已通過大規模驗證，並支撐著Meta旗下多款生產環境應用。

所謂「新架構」，既指新的框架架構設計，亦包含將其引入開源生態的相關工作。

自[React Native 0.68](/blog/2022/03/30/version-068#opting-in-to-the-new-architecture)起，新架構已開放實驗性啟用選項，並在後續每個版本持續改進。團隊現正致力使其成為React Native開源生態的預設體驗。

## 為何需要新架構？

經過多年React Native開發實踐，團隊識別出一系列阻礙開發者打造高品質體驗的根本性限制。這些限制源於框架現有設計，因此新架構成為對React Native未來的戰略投資。

新架構實現了傳統架構無法企及的功能與改進。

### 同步佈局與副作用

構建自適應UI常需測量視圖尺寸位置並調整佈局。

現階段開發者需使用[`onLayout`](/docs/view#onlayout)事件獲取視圖佈局資訊後調整。但`onLayout`回調中的狀態更新可能在前次渲染繪製後才生效，導致用戶可能看到初始佈局與調整結果之間的過渡狀態或視覺跳動。

新架構通過同步獲取佈局資訊與合理調度更新，可徹底避免此問題，確保用戶不會看到中間狀態。

<details>
<summary>Example: Rendering a Tooltip</summary>

Measuring and placing a tooltip above a view allows us to showcase what synchronous rendering unlocks. The tooltip needs to know the position of its target view to determine where it should render.

In the current architecture, we use `onLayout` to get the measurements of the view and then update the positioning of the tooltip based on where the view is.

```jsx
function ViewWithTooltip() {
  // ...

  // We get the layout information and pass to ToolTip to position itself
  const onLayout = React.useCallback(event => {
    targetRef.current?.measureInWindow((x, y, width, height) => {
      // This state update is not guaranteed to run in the same commit
      // This results in a visual "jump" as the ToolTip repositions itself
      setTargetRect({x, y, width, height});
    });
  }, []);

  return (
    <>
      <View ref={targetRef} onLayout={onLayout}>
        <Text>Some content that renders a tooltip above</Text>
      </View>
      <Tooltip targetRect={targetRect} />
    </>
  );
}
```

With the New Architecture, we can use [`useLayoutEffect`](https://react.dev/reference/react/useLayoutEffect) to synchronously measure and apply layout updates in a single commit, avoiding the visual "jump".

```jsx
function ViewWithTooltip() {
  // ...

  useLayoutEffect(() => {
    // The measurement and state update for `targetRect` happens in a single commit
    // allowing ToolTip to position itself without intermediate paints
    targetRef.current?.measureInWindow((x, y, width, height) => {
      setTargetRect({x, y, width, height});
    });
  }, [setTargetRect]);

  return (
    <>
      <View ref={targetRef}>
        <Text>Some content that renders a tooltip above</Text>
      </View>
      <Tooltip targetRect={targetRect} />
    </>
  );
}
```

<div className="TwoColumns TwoFigures">
 <figure>
  <img src="/img/new-architecture/async-on-layout.gif" alt="A view that is moving to the corners of the viewport and center with a tooltip rendered either above or below it. The tooltip is rendered after a short delay after the view moves" />
  <figcaption>Asynchronous measurement and render of the ToolTip. [See code](https://gist.github.com/lunaleaps/eabd653d9864082ac1d3772dac217ab9).</figcaption>
</figure>
<figure>
  <img src="/img/new-architecture/sync-use-layout-effect.gif" alt="A view that is moving to the corners of the viewport and center with a tooltip rendered either above or below it. The view and tooltip move in unison." />
  <figcaption>Synchronous measurement and render of the ToolTip. [See code](https://gist.github.com/lunaleaps/148756563999c83220887757f2e549a3).</figcaption>
</figure>
</div>

</details>

### 支援並發渲染器與特性

新架構支援[React 18](https://react.dev/blog/2022/03/29/react-v18)引入的並發渲染與特性。開發者現可在React Native代碼中使用Suspense數據獲取、Transitions等新API，進一步統一網頁與原生React開發的概念體系。

並發渲染器還帶來開箱即用的改進，如自動批處理可減少React的重渲染次數。

<details>
<summary>Example: Automatic Batching</summary>

With the New Architecture, you'll get automatic batching with the React 18 renderer.

In this example, a slider specifies how many tiles to render. Dragging the slider from 0 to 1000 will fire off a quick succession of state updates and re-renders.

In comparing the renderers for the [same code](https://gist.github.com/lunaleaps/79bb6f263404b12ba57db78e5f6f28b2), you can visually notice the renderer provides a smoother UI, with less intermediate UI updates. State updates from native event handlers, like this native Slider component, are now batched.

<div className="TwoColumns TwoFigures">
 <figure>
  <img src="/img/new-architecture/legacy-renderer.gif" alt="A video demonstrating an app rendering many views according to a slider input. The slider value is adjusted from 0 to 1000 and the UI slowly catches up to rendering 1000 views." />
  <figcaption>Rendering frequent state updates with legacy renderer.</figcaption>
</figure>
<figure>
  <img src="/img/new-architecture/react18-renderer.gif" alt="A video demonstrating an app rendering many views according to a slider input. The slider value is adjusted from 0 to 1000 and the UI resolves to 1000 views faster than the previous example, without as many intermediate states." />
  <figcaption>Rendering frequent state updates with React 18 renderer.</figcaption>
</figure>
</div>
</details>

[Transitions](https://react.dev/reference/react/useTransition)等並發新特性讓您能定義UI更新的優先級。將更新標記為低優先級時，React可「中斷」其渲染以處理更高優先級更新，確保關鍵操作保持流暢響應。

<details>
<summary>Example: Using `startTransition`</summary>

We can build on the previous example to showcase how transitions can interrupt in-progress rendering to handle a newer state update.

We wrap the tile number state update with `startTransition` to indicate that rendering the tiles can be interrupted. `startTransition` also provides a `isPending` flag to tell us when the transition is complete.

```jsx
function TileSlider({value, onValueChange}) {
  const [isPending, startTransition] = useTransition();

  return (
    <>
      <View>
        <Text>
          Render {value} Tiles
        </Text>
        <ActivityIndicator animating={isPending} />
      </View>
      <Slider
        value={1}
        minimumValue={1}
        maximumValue={1000}
        step={1}
        onValueChange={newValue => {
          startTransition(() => {
            onValueChange(newValue);
          });
        }}
      />
    </>
  );
}

function ManyTiles() {
  const [value, setValue] = useState(1);
  const tiles = generateTileViews(value);
  return (
      <TileSlider onValueChange={setValue} value={value} />
      <View>
        {tiles}
      </View>
  )
}
```

You'll notice that with the frequent updates in a transition, React renders fewer intermediate states because it bails out of rendering the state as soon as it becomes stale. In comparison, without transitions, more intermediate states are rendered. Both examples still use automatic batching. Still, transitions give even more power to developers to batch in-progress renders.

<div className="TwoColumns TwoFigures">
<figure>
  <img src="/img/new-architecture/with-transitions.gif" alt="A video demonstrating an app rendering many views (tiles) according to a slider input. The views are rendered in batches as the slider is quickly adjusted from 0 to 1000. There are less batch renders in comparison to the next video." />
  <figcaption>Rendering tiles with transitions to interrupt in-progress renders of stale state. [See code](https://gist.github.com/lunaleaps/eac391bf3fe4c85953cefeb74031bab0/revisions).</figcaption>
</figure>
<figure>
  <img src="/img/new-architecture/without-transitions.gif" alt="A video demonstrating an app rendering many views (tiles) according to a slider input. The views are rendered in batches as the slider is quickly adjusted from 0 to 1000." />
  <figcaption>Rendering tiles without marking it as a transition. [See code](https://gist.github.com/lunaleaps/eac391bf3fe4c85953cefeb74031bab0/revisions).</figcaption>
</figure>
</div>
</details>

### 高效的JavaScript/原生交互

新架構以JavaScript接口(JSI)取代原有的[異步橋接](https://reactnative.dev/blog/2018/06/14/state-of-react-native-2018#architecture)。JSI允許JavaScript與C++對象互相持有引用，透過記憶體引用可直接調用方法，消除序列化開銷。

JSI使熱門相機庫[VisionCamera](https://github.com/mrousavy/react-native-vision-camera)能實時處理幀數據。典型幀緩衝區約10MB，以每秒幀率計算相當於處理約1GB數據。相較橋接的序列化成本，JSI能輕鬆處理此量級的交互數據，並可暴露其他基於實例的複雜類型如數據庫、圖像、音頻樣本等。

新架構中採用的 JSI 移除了所有原生-JavaScript 互通中的這類序列化工作。這包括初始化和重新渲染如 `View` 和 `Text` 等原生核心元件。您可以在新架構的[渲染效能調查](https://github.com/reactwg/react-native-new-architecture/discussions/123)中閱讀更多關於我們測量的改進基準。

### 了解更多

為實現這一點，新架構必須重構 React Native 基礎設施的多個部分。要了解更多關於重構及其帶來的其他好處，請查看新架構工作組的[文件](https://github.com/reactwg/react-native-new-architecture)。

## 啟用新架構後我能期待什麼？

雖然新架構啟用了這些功能和改進，但為您的應用或函式庫啟用新架構可能不會立即提升效能或用戶體驗。

例如，您的程式碼可能需要重構以利用同步佈局效果或並發功能等新能力。儘管 JSI 將最小化 JavaScript 和原生記憶體之間的開銷，但資料序列化可能並非您應用效能的瓶頸。

在您的應用或函式庫中啟用新架構，就是選擇進入 React Native 的未來。

團隊正在積極研究和開發新架構解鎖的新能力。例如，網頁對齊是 Meta 正在探索的一個活躍領域，未來將發布到 React Native 開源生態系統。

- [事件循環模型的更新](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0744-well-defined-event-loop.md)
- [節點和佈局 API](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0607-dom-traversal-and-layout-apis.md)
- [樣式和佈局一致性](https://github.com/facebook/yoga/releases/tag/v2.0.0)

您可以在我們專屬的[討論與提案](https://github.com/react-native-community/discussions-and-proposals/discussions/651)儲存庫中跟進並貢獻。

## 我現在應該使用新架構嗎？

在 [React Conf 2024](https://youtu.be/Q5SMmKb7qVI?feature=shared&t=1219) 上，我們宣布 React Native 的[新架構現已進入 Beta 階段](https://github.com/reactwg/react-native-new-architecture/discussions/189)。

我們相信新架構已非常接近可用於生產環境。

我們的指導如下：

- 對於大多數生產應用，我們目前_不_建議啟用新架構。等待官方發布將提供最佳體驗。
- 然而，我們建議您規劃遷移並開始嘗試。
- 如果您維護一個 React Native 函式庫，我們建議啟用它並驗證您的使用案例是否涵蓋。您可以在[這裡找到說明](https://github.com/reactwg/react-native-new-architecture#guides)。

### 啟用新架構

如果您有興趣親身體驗新架構，可以在我們的專屬工作組中找到[說明](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/enable-apps.md)。[新架構工作組](https://github.com/reactwg/react-native-new-architecture)是一個專為新架構採用提供支援和協調的空間，團隊會在此發布定期更新。