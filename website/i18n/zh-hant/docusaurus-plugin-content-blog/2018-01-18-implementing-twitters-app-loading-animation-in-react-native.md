---
title: 'Implementing Twitter’s App Loading Animation in React Native'
authors: [Eli White]
tags: [engineering]
---

Twitter’s iOS app has a loading animation I quite enjoy.

<img src="/blog/assets/loading-screen-01.gif" style={{float: 'left', paddingRight: 80, paddingBottom: 20}} />

Once the app is ready, the Twitter logo delightfully expands, revealing the app.

I wanted to figure out how to recreate this loading animation with React Native.

<hr style={{clear: 'both', marginBottom: 40, width: 80}} />

To understand _how_ to build it, I first had to understand the difference pieces of the loading animation. The easiest way to see the subtlety is to slow it down.

<img src="/blog/assets/loading-screen-02.gif" style={{marginTop: 20, float: 'left', paddingRight: 80, paddingBottom: 20}} />

There are a few major pieces in this that we will need to figure out how to build.

1. Scaling the bird.
1. As the bird grows, showing the app underneath
1. Scaling the app down slightly at the end

It took me quite a while to figure out how to make this animation.

I started with an _incorrect_ assumption that the blue background and Twitter bird were a layer on _top_ of the app and that as the bird grew, it became transparent which revealed the app underneath. This approach doesn’t work because the Twitter bird becoming transparent would show the blue layer, not the app underneath!

Luckily for you, dear reader, you don’t have to go through the same frustration I did. You get this nice tutorial skipping to the good stuff!

<hr style={{clear: 'both', marginBottom: 40, width: 80}} />

## The right way

Before we get to code, it is important to understand how to break this down. To help visualize this effect, I recreated it in [CodePen](https://codepen.io/TheSavior/pen/NXNoJM) (embedded in a few paragraphs) so you can interactively see the different layers.

<img src="/blog/assets/loading-screen-03.png" style={{float: 'left', paddingRight: 80, paddingBottom: 20}} />

There are three main layers to this effect. The first is the blue background layer. Even though this seems to appear on top of the app, it is actually in the back.

We then have a plain white layer. And then lastly, in the very front, is our app.

<hr style={{clear: 'both', marginBottom: 40, width: 80}} />

<img src="/blog/assets/loading-screen-04.png" style={{float: 'left', paddingRight: 80, paddingBottom: 20}} />

The main trick to this animation is using the Twitter logo as a `mask` and masking both the app, and the white layer. I won’t go too deep on the details of masking, there are [plenty](https://www.html5rocks.com/en/tutorials/masking/adobe/) of [resources](https://designshack.net/articles/graphics/a-complete-beginners-guide-to-masking-in-photoshop/) [online](https://www.sketchapp.com/docs/shapes/masking/) for that.

The basics of masking in this context are having images where opaque pixels of the mask show the content they are masking whereas transparent pixels of the mask hide the content they are masking.

We use the Twitter logo as a mask, and having it mask two layers; the solid white layer, and the app layer.

To reveal the app, we scale the mask up until it is larger than the entire screen.

While the mask is scaling up, we fade in the opacity of the app layer, showing the app and hiding the solid white layer behind it. To finish the effect, we start the app layer at a scale > 1, and scale it down to 1 as the animation is ending. We then hide the non-app layers as they will never be seen again.

They say a picture is worth 1,000 words. How many words is an interactive visualization worth? Click through the animation with the “Next Step” button. Showing the layers gives you a side view perspective. The grid is there to help visualize the transparent layers.

<iframe
  height="750"
  scrolling="no"
  title="Loading Screen Animation Steps"
  src="//codepen.io/TheSavior/embed/NXNoJM/?height=265&theme-id=0&default-tab=result&embed-version=2"
  frameborder="no"
  allowFullScreen={true}
  className="codepen">
  See the Pen{' '}
  <a href="https://codepen.io/TheSavior/pen/NXNoJM/">
    Loading Screen Animation Steps
  </a>
  {' '}by Eli White (
  <a href="https://codepen.io/TheSavior">@TheSavior</a>) on{' '}
  <a href="https://codepen.io">CodePen</a>.
</iframe>

## Now, for the React Native

Alrighty. Now that we know what we are building and how the animation works, we can get down to the code — the reason you are really here.

這個效果的關鍵在於 [MaskedViewIOS](https://reactnative.dev/docs/0.63/maskedviewios)，這是 React Native 的核心元件之一。

```jsx
import {MaskedViewIOS} from 'react-native';

<MaskedViewIOS maskElement={<Text>Basic Mask</Text>}>
  <View style={{backgroundColor: 'blue'}} />
</MaskedViewIOS>;
```

`MaskedViewIOS` 接受 `maskElement` 和 `children` 這兩個 props。`children` 會被 `maskElement` 遮罩。注意遮罩不必是圖片，它可以是任何視圖元件。上述範例的行為會渲染藍色視圖，但只會在 `maskElement` 的「Basic Mask」文字區域顯示。我們剛剛製作了複雜的藍色文字效果。

我們要做的是先渲染藍色圖層，然後在上面渲染被 Twitter 標誌遮罩的應用程式和白色圖層。

```jsx
{
  fullScreenBlueLayer;
}
<MaskedViewIOS
  style={{flex: 1}}
  maskElement={
    <View style={styles.centeredFullScreen}>
      <Image source={twitterLogo} />
    </View>
  }>
  {fullScreenWhiteLayer}
  <View style={{flex: 1}}>
    <MyApp />
  </View>
</MaskedViewIOS>;
```

這樣我們就能得到下方顯示的圖層結構。

<img src="/blog/assets/loading-screen-04.png" style={{marginLeft: 'auto', marginRight: 'auto', display: 'block'}} />

## 現在進入動畫部分

我們已具備所有必要元件，下一步是讓它們動起來。為了讓動畫效果流暢，我們將使用 React Native 的 [Animated](/docs/animated) API。

Animated 讓我們能以宣告式的方式在 JavaScript 中定義動畫。預設情況下，這些動畫會在 JavaScript 中執行，並逐幀通知原生層需要進行的變更。儘管 JavaScript 會嘗試每幀更新動畫，但很可能無法足夠快速地完成，導致掉幀（卡頓）發生。這可不是我們想要的！

Animated 提供特殊行為來避免這種卡頓。它有一個名為 `useNativeDriver` 的標誌，能在動畫開始時將動畫定義從 JavaScript 傳送到原生端，讓原生端處理動畫更新，而無需每幀與 JavaScript 來回溝通。`useNativeDriver` 的缺點是只能更新特定屬性集，主要是 `transform` 和 `opacity`。目前還無法用它來動畫化背景色等屬性——不過未來會陸續增加，當然你也可以提交 PR 來為專案添加所需屬性，讓整個社群受益 😀。

由於我們希望動畫流暢，將在這些限制下工作。若想深入了解 `useNativeDriver` 的運作原理，請參閱我們的[發布部落格文章](/blog/2017/02/14/using-native-driver-for-animated)。

## 分解我們的動畫

我們的動畫有 4 個組成部分：

1. 放大鳥標誌，逐漸顯示應用程式和純白圖層
1. 淡入應用程式
1. 縮小應用程式
1. 完成後隱藏白色和藍色圖層

使用 Animated 時，有兩種主要方式定義動畫。第一種是使用 `Animated.timing`，可精確設定動畫持續時間和緩動曲線來平滑運動。另一種是使用基於物理的 API 如 `Animated.spring`，透過設定摩擦力和張力等參數，讓物理引擎驅動動畫。

我們需要同時運行多個密切相關的動畫。例如，希望在遮罩展開到一半時開始淡入應用程式。由於這些動畫密切相關，我們將使用單一 `Animated.Value` 配合 `Animated.timing` 來實現。

`Animated.Value` 是對原生值的包裝，Animated 用它來追蹤動畫狀態。通常一個完整動畫只需一個這樣的實例。多數使用 Animated 的元件會將這個值儲存在狀態中。

由於我將此動畫視為在完整動畫時間軸上不同節點發生的步驟，我們會將 `Animated.Value` 初始設為 0（代表 0% 完成），結束值設為 100（代表 100% 完成）。

我們的元件初始狀態如下。

```jsx
state = {
  loadingProgress: new Animated.Value(0),
};
```

當我們準備開始動畫時，我們會告訴 Animated 將這個值動畫化到 100。

```jsx
Animated.timing(this.state.loadingProgress, {
  toValue: 100,
  duration: 1000,
  useNativeDriver: true, // This is important!
}).start();
```

接著我嘗試估算動畫的不同部分，以及它們在整體動畫進程中不同階段應該具有的值。以下是一個表格，顯示了動畫的不同部分，以及我認為它們在時間進展過程中應該具有的值。

![](/blog/assets/loading-screen-05.png)

Twitter 鳥形遮罩應該從縮放比例 1 開始，在放大之前會先變小。因此在動畫進行到 10% 時，它的縮放值應該是 0.8，然後在結束時放大到 70。選擇 70 其實相當隨意，它需要足夠大以確保鳥形完全揭示畫面，而 60 還不夠大 😀。有趣的是，這個數字越大，它看起來增長得越快，因為它必須在相同的時間內達到目標。這個數字經過了一些嘗試和錯誤，才讓它與這個標誌看起來效果良好。不同大小的標誌或設備將需要不同的結束縮放比例，以確保整個畫面被完全揭示。

應用程式應該保持不透明一段時間，至少在 Twitter 標誌變小的過程中。根據官方動畫，我希望在鳥形放大到一半時開始顯示它，並在整體動畫進行到 30% 時完全顯示。

應用程式的縮放從 1.1 開始，並在動畫結束時縮小到正常比例。

## 現在，用代碼實現。

我們上面所做的本質上是將動畫進度百分比的值映射到各個部分的值。我們使用 Animated 的 `.interpolate` 方法來實現這一點。我們創建了三個不同的樣式對象，每個對象對應動畫的一部分，使用基於 `this.state.loadingProgress` 的插值。

```jsx
const loadingProgress = this.state.loadingProgress;

const opacityClearToVisible = {
  opacity: loadingProgress.interpolate({
    inputRange: [0, 15, 30],
    outputRange: [0, 0, 1],
    extrapolate: 'clamp',
    // clamp means when the input is 30-100, output should stay at 1
  }),
};

const imageScale = {
  transform: [
    {
      scale: loadingProgress.interpolate({
        inputRange: [0, 10, 100],
        outputRange: [1, 0.8, 70],
      }),
    },
  ],
};

const appScale = {
  transform: [
    {
      scale: loadingProgress.interpolate({
        inputRange: [0, 100],
        outputRange: [1.1, 1],
      }),
    },
  ],
};
```

現在我們有了這些樣式對象，可以在渲染文章前面提到的視圖片段時使用它們。注意，只有 `Animated.View`、`Animated.Text` 和 `Animated.Image` 能夠使用包含 `Animated.Value` 的樣式對象。

```jsx
const fullScreenBlueLayer = (
  <View style={styles.fullScreenBlueLayer} />
);
const fullScreenWhiteLayer = (
  <View style={styles.fullScreenWhiteLayer} />
);

return (
  <View style={styles.fullScreen}>
    {fullScreenBlueLayer}
    <MaskedViewIOS
      style={{flex: 1}}
      maskElement={
        <View style={styles.centeredFullScreen}>
          <Animated.Image
            style={[styles.maskImageStyle, imageScale]}
            source={twitterLogo}
          />
        </View>
      }>
      {fullScreenWhiteLayer}
      <Animated.View
        style={[opacityClearToVisible, appScale, {flex: 1}]}>
        {this.props.children}
      </Animated.View>
    </MaskedViewIOS>
  </View>
);
```

<img src="/blog/assets/loading-screen-06.gif" style={{float: 'left', paddingRight: 80, paddingBottom: 20}} />

太好了！現在我們的動畫部分看起來符合預期。現在我們只需要清理那些永遠不會再看到的藍色和白色圖層。

要知道何時可以清理它們，我們需要知道動畫何時完成。幸運的是，當我們調用 `Animated.timing` 時，`.start` 方法接受一個可選的回調函數，該函數會在動畫完成時運行。

```jsx
Animated.timing(this.state.loadingProgress, {
  toValue: 100,
  duration: 1000,
  useNativeDriver: true,
}).start(() => {
  this.setState({
    animationDone: true,
  });
});
```

現在我們有了一個 `state` 中的值來判斷動畫是否完成，我們可以修改藍色和白色圖層來使用這個值。

```jsx
const fullScreenBlueLayer = this.state.animationDone ? null : (
  <View style={[styles.fullScreenBlueLayer]} />
);
const fullScreenWhiteLayer = this.state.animationDone ? null : (
  <View style={[styles.fullScreenWhiteLayer]} />
);
```

瞧！我們的動畫現在可以正常運作，並且在動畫完成後清理了未使用的圖層。我們已經成功構建了 Twitter 應用程式的載入動畫！

## 但是等等，我的動畫不起作用！

別擔心，親愛的讀者。我也討厭那些只提供代碼片段而不給出完整源碼的指南。

這個組件已經發布到 npm，並且在 GitHub 上名為 [react-native-mask-loader](https://github.com/TheSavior/react-native-mask-loader)。如果你想在手機上試試看，可以在 [Expo](https://expo.io/@eliwhite/react-native-mask-loader-example) 上找到它：

<img src="/blog/assets/loading-screen-07.png" style={{marginLeft: 'auto', marginRight: 'auto', display: 'block'}} />

## 更多閱讀 / 額外練習

1. [這本 gitbook](https://browniefed.com/react-native-animation-book/) 是閱讀完 React Native 文件後，深入學習 Animated 的絕佳資源。
1. 實際的 Twitter 動畫在接近尾聲時似乎會加速遮罩的顯現。嘗試修改加載器，使用不同的緩動函數（或彈簧效果！）以更貼近該行為。
1. 目前遮罩的最終縮放比例是硬編碼的，在平板上可能無法完整顯現整個應用程式。根據螢幕尺寸和圖片大小計算最終縮放比例，將會是一個很棒的 Pull Request。