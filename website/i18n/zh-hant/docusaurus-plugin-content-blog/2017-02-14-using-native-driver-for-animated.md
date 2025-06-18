---
title: Using Native Driver for Animated
author: Janic Duplessis
authorTitle: Software Engineer at App & Flow
authorURL: 'https://twitter.com/janicduplessis'
authorImageURL: 'https://secure.gravatar.com/avatar/8d6b6c0f5b228b0a8566a69de448b9dd?s=128'
authorTwitter: janicduplessis
tags: [engineering]
---

過去一年來，我們持續優化使用 Animated 函式庫的動畫效能。動畫對於打造流暢的使用者體驗至關重要，但實作難度往往較高。我們的目標是讓開發者能輕鬆創建高效能動畫，無需擔心程式碼導致的卡頓問題。

## 這是什麼？

Animated API 的設計核心在於「可序列化」的關鍵約束。這意味著我們能在動畫開始前就將所有參數傳送至原生端，讓原生程式碼直接在 UI 執行緒執行動畫，無需每幀都透過橋接層溝通。此機制特別實用，因為即使動畫開始後 JS 執行緒被阻塞，動畫仍能保持流暢。實務上這種情況很常見，因為使用者程式碼與 React 渲染都可能長時間佔用 JS 執行緒。

## 歷史回顧...

此專案始於一年前，當時 Expo 為 Android 版 li.st 應用進行開發。[Krzysztof Magiera](https://twitter.com/kzzzf) 受託建置 Android 端的初始實現，成果斐然使 li.st 成為首個採用 Animated 原生驅動動畫的應用。數月後，[Brandon Withrow](https://github.com/buba447) 完成 iOS 端實現。隨後 [Ryan Gomba](https://twitter.com/ryangomba) 與我陸續加入，補齊如 `Animated.event` 支援等缺失功能，並修復生產環境中發現的問題。這真正是社群協力的成果，我要感謝所有參與者以及贊助大部分開發工作的 Expo。該技術現已用於 React Native 的 `Touchable` 元件，以及新發布的 [React Navigation](https://github.com/react-community/react-navigation) 函式庫中的導航動畫。

## 運作原理

首先了解現行 JS 驅動模式下 Animated 的運作方式。使用 Animated 時，您需宣告代表動畫效果的節點圖，再透過驅動程式依預定義曲線更新 Animated 值。也可透過 `Animated.event` 將 Animated 值連接至 `View` 的事件來更新數值。

![](/blog/assets/animated-diagram.png)

以下是動畫執行步驟與發生位置的分解說明：

- JS: 動畫驅動程式使用 `requestAnimationFrame` 每幀執行，根據動畫曲線計算新值並更新驅動的數值。
- JS: 計算中介值並傳遞至附加於 `View` 的屬性節點。
- JS: 透過 `setNativeProps` 更新 `View`。
- JS 至原生橋接層。
- 原生端: 更新 `UIView` 或 `android.View`。

由此可見，多數運算發生在 JS 執行緒。若該執行緒阻塞將導致動畫掉幀，且每幀都需透過 JS-Native 橋接層更新原生視圖。

原生驅動程式的改良在於將所有步驟移至原生端。由於 Animated 會產生動畫節點圖，這些節點可在動畫開始時序列化並一次性傳送至原生端，消除回調 JS 執行緒的需求；原生程式碼能直接於 UI 執行緒每幀更新視圖。

以下示範如何序列化動畫值與插值節點（非實際實作，僅為範例）：

建立原生數值節點（即待動畫化的數值）：

```
NativeAnimatedModule.createNode({
  id: 1,
  type: 'value',
  initialValue: 0,
});
```

建立原生插值節點（告知原生驅動程式如何插值）：

```
NativeAnimatedModule.createNode({
  id: 2,
  type: 'interpolation',
  inputRange: [0, 10],
  outputRange: [10, 0],
  extrapolate: 'clamp',
});
```

建立原生屬性節點（標明其附加於視圖的哪個屬性）：

```
NativeAnimatedModule.createNode({
  id: 3,
  type: 'props',
  properties: ['style.opacity'],
});
```

連接節點：

```
NativeAnimatedModule.connectNodes(1, 2);
NativeAnimatedModule.connectNodes(2, 3);
```

將屬性節點連接至視圖：

```
NativeAnimatedModule.connectToView(3, ReactNative.findNodeHandle(viewRef));
```

如此一來，原生動畫模組便擁有直接更新原生視圖所需的所有資訊，無需再透過 JavaScript 計算任何數值。

剩下的工作就是實際啟動動畫，指定我們想要的動畫曲線類型以及要更新的動畫數值。定時動畫也可以透過在 JavaScript 中預先計算動畫的每一幀來簡化，使原生實作更精簡。

```
NativeAnimatedModule.startAnimation({
  type: 'timing',
  frames: [0, 0.1, 0.2, 0.4, 0.65, ...],
  animatedValueId: 1,
});
```

以下是動畫運行時的具體流程：

- 原生端：原生動畫驅動器使用 `CADisplayLink` 或 `android.view.Choreographer` 在每一幀執行，並根據動畫曲線計算的新數值更新驅動的數值。
- 原生端：計算中間值並傳遞給附加到原生視圖的屬性節點。
- 原生端：更新 `UIView` 或 `android.View`。

如你所見，不再需要 JavaScript 執行緒和橋接層，這意味著更流暢的動畫！🎉🎉

## 如何在應用程式中使用此功能？

對於一般動畫，答案很簡單：在啟動動畫時，只需在動畫配置中添加 `useNativeDriver: true`。

之前：

```
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
}).start();
```

之後：

```
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // <-- Add this
}).start();
```

動畫數值僅與一種驅動器相容，因此如果你在啟動某個數值的動畫時使用了原生驅動器，請確保該數值的所有動畫都使用原生驅動器。

此功能也適用於 `Animated.event`，這對於必須跟隨滾動位置的動畫非常有用，因為如果沒有原生驅動器，由於 React Native 的非同步特性，動畫總是會比手勢慢一幀。

之前：

```
<ScrollView
  scrollEventThrottle={16}
  onScroll={Animated.event(
    [{ nativeEvent: { contentOffset: { y: this.state.animatedValue } } }]
  )}
>
  {content}
</ScrollView>
```

之後：

```
<Animated.ScrollView // <-- Use the Animated ScrollView wrapper
  scrollEventThrottle={1} // <-- Use 1 here to make sure no events are ever missed
  onScroll={Animated.event(
    [{ nativeEvent: { contentOffset: { y: this.state.animatedValue } } }],
    { useNativeDriver: true } // <-- Add this
  )}
>
  {content}
</Animated.ScrollView>
```

## 注意事項

並非所有 Animated 的功能目前都支援原生動畫。主要的限制是你只能動畫非佈局屬性，例如 `transform` 和 `opacity` 可以正常工作，但 Flexbox 和位置屬性則不行。另一個限制是 `Animated.event` 僅適用於直接事件，不適用於冒泡事件。這意味著它不適用於 `PanResponder`，但適用於 `ScrollView#onScroll` 等事件。

原生動畫功能雖然已經在 React Native 中存在一段時間，但由於被視為實驗性功能，一直未被正式記錄。因此，如果你想使用此功能，請確保你使用的是較新版本（0.40+）的 React Native。

## 資源

如需更多關於動畫的資訊，我推薦觀看 [Christopher Chedeau](https://twitter.com/Vjeux) 的[這場演講](https://www.youtube.com/watch?v=xtqUJVqpKNo)。

如果你想深入了解動畫以及如何透過將其卸載到原生端來提升使用者體驗，也可以觀看 [Krzysztof Magiera](https://twitter.com/kzzzf) 的[這場演講](https://www.youtube.com/watch?v=qgSMjYWqBk4)。