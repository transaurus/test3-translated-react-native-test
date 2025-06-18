---
id: landing-page
title: About the New Architecture
---

:::info
若您正在尋找新架構指南，相關內容已遷移至[工作組頁面](https://github.com/reactwg/react-native-new-architecture#guides)。
:::

自2018年起，React Native團隊便著手重新設計核心架構，旨在讓開發者能打造更高品質的使用體驗。截至2024年，此版本React Native已通過大規模驗證，並支撐著Meta旗下多款生產級應用。

「新架構」一詞同時指涉框架的新架構設計，以及將其引入開源生態的相關工作。

新架構自[React Native 0.68](/blog/2022/03/30/version-068#opting-in-to-the-new-architecture)起開放實驗性啟用，並在後續每個版本持續改進。團隊現正致力使其成為React Native開源生態的預設體驗。

## 為何需要新架構？

歷經多年React Native開發實踐後，團隊識別出若干阻礙開發者實現高品質體驗的根本性限制。這些限制源自框架現有設計，因此新架構成為對React Native未來的戰略投資。

新架構實現了舊架構無法企及的功能與改進：

### 同步佈局與副作用處理

構建自適應UI時，常需測量視圖尺寸位置並動態調整佈局。

現行方案是透過[`onLayout`](/docs/view#onlayout)事件獲取視圖佈局資訊後調整。但`onLayout`回調中的狀態更新可能在前次渲染繪製完成後才生效，導致用戶可能看到初始佈局與調整後狀態之間的視覺跳動。

新架構透過同步獲取佈局資訊與合理調度更新，從根本上消除中間狀態的可見性問題。

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

### 並發渲染器與特性支援

新架構支援[React 18](https://react.dev/blog/2022/03/29/react-v18)引入的並發渲染與特性。您現在可於React Native代碼中使用Suspense數據獲取、Transitions等新API，進一步統一Web與原生React開發的概念體系。

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

[Transitions](https://react.dev/reference/react/useTransition)等並發特性讓您能標註UI更新的優先級。將更新標記為低優先級時，React可「中斷」其渲染以處理更高優先級更新，確保關鍵操作保持流暢。

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

新架構以JavaScript Interface (JSI)取代舊有的[非同步橋接](https://reactnative.dev/blog/2018/06/14/state-of-react-native-2018#architecture)。JSI允許JavaScript與C++對象互相持有引用，透過記憶體引用直接調用方法，消除序列化開銷。

JSI使熱門相機庫[VisionCamera](https://github.com/mrousavy/react-native-vision-camera)能實時處理幀數據——典型幀緩衝達10MB，相當於每秒處理約1GB數據（依幀率而定）。相較橋接序列化的高昂成本，JSI能輕鬆處理此類複雜交互，並可暴露數據庫、圖像、音頻樣本等基於實例的複雜類型。

新架構中採用的JSI技術移除了所有原生與JavaScript交互間的序列化工作。這包括初始化和重新渲染如`View`和`Text`等原生核心元件。您可以在新架構的[渲染效能研究討論](https://github.com/reactwg/react-native-new-architecture/discussions/123)中閱讀更多關於我們測得的改進基準。

### 了解更多

為實現這一點，新架構必須重構React Native基礎設施的多個部分。要了解重構細節及其帶來的其他優勢，請查閱新架構工作組的[文件](https://github.com/reactwg/react-native-new-architecture)。

## 啟用新架構能帶來什麼？

雖然新架構實現了這些功能和改進，但為您的應用程式或函式庫啟用新架構可能不會立即提升效能或用戶體驗。

例如，您的程式碼可能需要重構才能充分利用同步佈局效果或並行功能等新能力。儘管JSI會最小化JavaScript與原生記憶體間的開銷，但資料序列化可能並非您應用效能的瓶頸。

為您的應用程式或函式庫啟用新架構，即是選擇React Native的未來。

團隊正在積極研究和開發新架構解鎖的新能力。例如，與網頁對齊是Meta正在探索的領域，未來將發布至React Native開源生態系統。

- [事件循環模型的更新](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0744-well-defined-event-loop.md)
- [節點與佈局API](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0607-dom-traversal-and-layout-apis.md)
- [樣式與佈局一致性](https://github.com/facebook/yoga/releases/tag/v2.0.0)

您可以在專屬的[討論與提案](https://github.com/react-native-community/discussions-and-proposals/discussions/651)儲存庫中追蹤進度並參與貢獻。

## 我現在應該使用新架構嗎？

目前新架構仍被視為實驗性功能，我們持續改進其向後兼容性以提供更好的採用體驗。

團隊計劃在2024年底的React Native版本中預設啟用新架構。

我們的指導建議如下：

- 對於大多數生產環境應用程式，我們目前_不_建議啟用新架構。等待官方發布將提供最佳體驗。
- 如果您維護React Native函式庫，我們建議啟用並驗證您的使用情境是否被涵蓋。您可以在[此處找到指引](https://github.com/reactwg/react-native-new-architecture#guides)。

### 啟用新架構

如果您有興趣親身體驗新架構，可以在我們專屬的工作組中找到[指引](https://github.com/reactwg/react-native-new-architecture/blob/main/docs/enable-apps.md)。[新架構工作組](https://github.com/reactwg/react-native-new-architecture)是專門支援和協調新架構採用的空間，團隊會在此定期發布更新。