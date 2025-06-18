---
id: landing-page
title: About the New Architecture
---

自2018年起，React Native團隊便著手重新設計核心架構，旨在讓開發者能打造更高品質的使用體驗。截至2024年，此版本React Native已通過大規模驗證，並成為Meta旗下多款生產級應用的技術支柱。

「新架構」一詞同時指涉框架本身的革新設計，以及將其引入開源生態的相關工作。

新架構自[React Native 0.68](/blog/2022/03/30/version-068#opting-in-to-the-new-architecture)起開放實驗性啟用，並在後續每個版本持續改進。團隊現正致力使其成為React Native開源生態的預設體驗。

## 為何需要新架構？

經過多年React Native開發實踐，團隊識別出原有框架設計中存在根本性限制，這些限制阻礙開發者實現高度精緻的體驗。新架構即是對React Native未來的戰略性投資。

新架構解鎖了傳統架構無法實現的功能與改進。

### 同步佈局與效果

建構自適應UI常需測量視圖尺寸與位置來調整佈局。

現行做法是透過[`onLayout`](/docs/view#onlayout)事件獲取視圖佈局資訊後調整。但在此回調中的狀態更新可能於前次渲染繪製後才生效，導致用戶可能看到初始佈局與調整後佈局間的視覺跳動。

新架構透過同步存取佈局資訊與合理調度更新，能徹底避免此問題，確保用戶不會看到中間狀態。

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

### 支援並行渲染與特性

新架構支援[React 18](https://react.dev/blog/2022/03/29/react-v18)後引入的並行渲染特性。現可在React Native程式碼中使用Suspense資料獲取、Transitions等新API，進一步統一網頁與原生React的開發模式。

並行渲染器還帶來自動批次處理等開箱即用的改進，能減少React不必要的重新渲染。

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

[Transitions](https://react.dev/reference/react/useTransition)等並行特性讓您能標註UI更新的優先級。將更新標記為低優先級時，React可「中斷」其渲染以優先處理高優先級更新，確保關鍵操作保持流暢。

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

新架構以JavaScript介面(JSI)取代原有的[非同步橋接](https://reactnative.dev/blog/2018/06/14/state-of-react-native-2018#architecture)。JSI允許JavaScript與C++物件互相持有記憶體參照，實現無序列化成本的直接方法呼叫。

JSI使熱門相機庫[VisionCamera](https://github.com/mrousavy/react-native-vision-camera)能即時處理影格。典型影格緩衝區約10MB，相當於每秒處理1GB資料量（依影格率而定）。相較於橋接序列化的開銷，JSI能輕鬆處理此類複雜的實例型別交互，如資料庫、影像、音訊樣本等。

新架構全面採用JSI後，所有原生-JavaScript交互（包括`View`、`Text`等核心元件的初始化與重渲染）皆移除此類序列化工作。您可參閱我們對[新架構渲染效能的研究](https://github.com/reactwg/react-native-new-architecture/discussions/123)及實測的改進數據。

## 啟用新架構後能獲得哪些優勢？

雖然新架構帶來了這些功能與改進，但為您的應用程式或函式庫啟用新架構並不會立即提升效能或使用者體驗。

舉例來說，您的程式碼可能需要重構才能充分利用同步佈局效果或並行功能等新特性。儘管JSI能最小化JavaScript與原生記憶體間的開銷，但資料序列化可能並非您應用效能的瓶頸所在。

為應用程式或函式庫啟用新架構，即代表您選擇擁抱React Native的未來發展方向。

團隊正積極研究並開發新架構所解鎖的潛能。例如Meta正在探索的網頁對齊技術，未來將發布至React Native開源生態系統。

- [事件循環模型的更新](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0744-well-defined-event-loop.md)
- [節點與佈局API](https://github.com/react-native-community/discussions-and-proposals/blob/main/proposals/0607-dom-traversal-and-layout-apis.md)
- [樣式與佈局一致性](https://github.com/facebook/yoga/releases/tag/v2.0.0)

您可以在專屬的[討論與提案](https://github.com/react-native-community/discussions-and-proposals/discussions/651)儲存庫參與討論或貢獻想法。

## 現在應該採用新架構嗎？

從0.76版本起，所有React Native專案預設都會啟用新架構。

若您發現任何運作異常，請使用[此模板](https://github.com/facebook/react-native/issues/new?assignees=&labels=Needs%3A+Triage+%3Amag%3A%2CType%3A+New+Architecture&projects=&template=new_architecture_bug_report.yml)提交問題報告。

若因特殊原因無法使用新架構，您仍可透過以下方式選擇退出：

### Android平台

1. 開啟`android/gradle.properties`檔案
2. 將`newArchEnabled`標記從`true`改為`false`

```diff title="gradle.properties"
# Use this property to enable support to the new architecture.
# This will allow you to use TurboModules and the Fabric render in
# your application. You should enable this flag either if you want
# to write custom TurboModules/Fabric components OR use libraries that
# are providing them.
-newArchEnabled=true
+newArchEnabled=false
```

### iOS平台

1. 開啟`ios/Podfile`檔案
2. 在Podfile主作用域添加`ENV['RCT_NEW_ARCH_ENABLED'] = '0'`（參考模板中的[範例Podfile](https://github.com/react-native-community/template/blob/0.76-stable/template/ios/Podfile)）

```diff
+ ENV['RCT_NEW_ARCH_ENABLED'] = '0'
# Resolve react_native_pods.rb with node to allow for hoisting
require Pod::Executable.execute_command('node', ['-p',
  'require.resolve(
```

3. 執行以下指令安裝CocoaPods相依套件：

```shell
bundle exec pod install
```