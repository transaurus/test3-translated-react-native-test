---
title: React Native's Many Platform Vision
authors: [abernathyca, Eli White, lunaleaps, yungsters]
tags: [announcement]
---

React Native 在提升移動開發標準方面取得了巨大成功，無論是在 Facebook 內部還是在整個行業中。隨著我們以新的方式與計算機互動以及新設備的發明，我們希望 React Native 能夠為所有人服務。儘管 React Native 最初是為構建移動應用而創建的，但我們相信，專注於多平台並根據每個平台的優勢和限制進行開發具有共生效應。我們已經看到了將這項技術擴展到桌面和虛擬現實所帶來的巨大好處，並且我們很高興分享這對 React Native 的未來意味著什麼。

<!--truncate-->

## 尊重平台特性

我們的第一指導原則是[符合人們對每個平台的期望](https://reactnative.dev/blog/2020/07/17/react-native-principles#native-experience)。Android 用戶期望使用 TalkBack 的可訪問應用。導航應以其他 Android 應用中的方式工作。按鈕的外觀和感覺應與 Android 上的按鈕一致，而不是像 iOS 按鈕那樣。儘管我們尋求提供一致的跨平台開發體驗，但我們抵制犧牲用戶期望的誘惑。

我們相信，React Native 使開發人員能夠滿足用戶的期望，同時也能獲得更好的開發體驗的好處。在本節中，我們分享以下內容：

1. 通過擁抱平台限制，我們實際上改善了其他平台上的體驗。
2. 我們可以從機構知識中學習，以構建更高層次的跨平台抽象。
3. 每個平台上的其他參與者激勵我們構建更好的開發者和用戶體驗。

### 擁抱平台限制

<!-- alex ignore easy -->

特定的設備硬件或用戶期望會帶來獨特的限制和要求。例如，Android 和 VR 頭戴設備的內存通常比 iOS、macOS 和 Windows 更受限。另一個例子是，用戶期望移動應用幾乎立即啟動，但對桌面應用啟動時間較長則不太沮喪。**我們發現，通過使用 React Native 解決這些問題，我們可以更容易地借鑒一個平台的經驗和代碼，並將其應用於其他平台。**

<figure>
  <img src="/blog/assets/many-platform-vision-facebook-dating.png" alt="Screenshot of Facebook Dating on mobile" />
  <figcaption>
    React Native and Relay power over 1000 Facebook surfaces on Android and iOS.
  </figcaption>
</figure>

例如，React Native 依賴於一種稱為「視圖扁平化」的優化，這對於減少 Android 上的內存使用至關重要。我們從未為 iOS 構建這種優化，因為它沒有相同的內存限制。在過去幾年中，當我們構建新的跨平台渲染器時，我們不得不重新實現「視圖扁平化」。但這次，它是用 C++ 而不是平台特定的 Java 編寫的。在 iOS 上嘗試這種優化現在只需切換一個開關。在 Facebook 的生產應用中，我們觀察到這提高了 iOS 上的性能！我們可能永遠不會為 iOS 構建這個功能，但我們在 Android 上的投資能夠惠及 iOS。

### 從機構知識中學習

React Native 最初創建的原因之一是減少工程孤島。Android 工程師往往與從事同一產品的 iOS 工程師隔離。Android 工程師為 Android 工程師審查代碼並參加 Android 會議和聚會。iOS 工程師為 iOS 工程師審查代碼並參加 iOS 會議和聚會。從事不同平台的工程師帶來了關於如何構建優秀產品體驗的獨特領域和機構知識。

像 React Native 這樣的跨平台框架的最佳成果之一是如何將具有完全不同領域專業知識的工程師聚集在一起。**我們相信，通過針對更多平台，我們可以加速平台專家之間機構知識的交叉傳播。**

例如，React Native 中的可訪問性抽象受到 Android、iOS 和網頁各自以不同方式處理可訪問性的影響。這使我們能夠構建一個通用接口，改進移動平台上可訪問性提示的處理方式。

另一個例子是，我們對網頁上用戶速度感知的研究導致了像 Suspense 這樣的並發功能。在過去一年中，這些功能由新的 [Facebook.com](https://facebook.com/) 網站進行了驗證。現在，隨著我們的新渲染器，這些功能正在進入 React Native，並影響我們如何設計事件調度和優先級。React 團隊在改善網頁體驗方面的投資正在惠及原生平台的 React Native。

### 競爭驅動創新

除了領域特定的工程師、聚會和會議外，每個平台還有其他獨特的參與者在解決相似的問題。在網頁端，React（直接驅動React Native的框架）經常從其他開源網頁框架如[Vue](https://vuejs.org/)、[Preact](https://preactjs.com/)和[Svelte](https://svelte.dev/)中汲取靈感。在移動端，React Native也受到其他開源移動框架的啟發，同時我們也從Facebook內部開發的其他移動框架中學習。

<!-- alex ignore special -->

**我們相信，競爭最終會為所有人帶來更好的結果。**通過研究每個平台上其他優秀參與者的成功之處，我們可以學到可能適用於其他平台的經驗。例如，簡化複雜網站的競賽影響了React的發展，並讓React Native在為移動應用提供聲明式框架方面佔得先機。對更快迭代周期和構建時間的需求也促成了Fast Refresh的開發，這極大地惠及了React Native。同樣地，我們內部移動框架中的性能優化——特別是在數據獲取和並行化方面——促使我們改進React Native，這些改進在我們構建新的[Facebook.com](https://facebook.com/)網站時也影響了React。

<figure>
  <img src="/blog/assets/many-platform-vision-facebook-website.png" alt="Screenshot of the Facebook.com website" />
  <figcaption>
    React and Relay powers the <a href="https://facebook.com/">Facebook.com</a> website.
  </figcaption>
</figure>

## 擴展至新平台

React和React Native正處於一個轉折點。React已經[開始了React 18的發布之路](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html)，而[新的React Native渲染器現在已全面支持Facebook移動應用](https://twitter.com/reactnative/status/1415099806507167745)。今年我們的團隊大幅擴張，以支持Facebook內部日益增長的採用率。其他平台的開發團隊注意到了這一趨勢，並看到了他們也能從React Native中受益的機會。

**過去一年，我們與微軟和Messenger團隊合作，在Windows和macOS上打造真正原生的視頻通話體驗。**由於我們對移動應用的啟動時間有極高的要求，我們使用React Native實現的桌面視頻通話初始版本完全超越了它所取代的Electron實現的性能。在構建這一體驗的頭幾周，我們錄製了調整窗口大小並同時進行多個實時視頻通話的視頻，並對流暢的幀率感到驚嘆。

為桌面平台開發對我們來說非常令人興奮。我們將在移動平台上的經驗應用到桌面平台，並保持開放的態度。我們通過多個子窗口、菜單欄、系統托盤等擴展了視野。隨著我們繼續合作開發新的桌面Messenger功能，我們預計會發現影響我們在網頁和移動平台上構建方式的機會。如果你想了解最新動態，我們的桌面合作項目正在[GitHub](https://github.com/microsoft/react-native-windows)上進行。

<figure>
  <img src="/blog/assets/many-platform-vision-messenger-desktop.png" alt="Screenshot of the Messenger app on macOS" />
  <figcaption>
    React Native powers Video Calling in Messenger for Windows and macOS.
  </figcaption>
</figure>

**我們也與[Facebook Reality Labs](https://tech.fb.com/ar-vr/)更緊密地合作，以了解React如何獨特地定位於在Oculus上提供虛擬現實體驗。**使用React Native構建VR體驗將特別有趣，因為內存限制更嚴格且用戶對交互延遲更敏感。

與我們在移動平台上處理React Native的方式類似，我們將在Facebook的規模上驗證我們的技術，然後再公開分享任何內容。許多事情仍在變化，我們仍有許多問題。我們希望在與社區分享之前，對技術的生產就緒性和可靠性有足夠的信心。

儘管VR的大部分開發仍將在內部進行，但我們希望盡快分享更多信息。我們也預計，針對VR用例的React Native改進將在開源中體現。例如，我們預計減少VR用例內存使用的項目也將減少React Native在移動和桌面體驗中的內存使用。

<figure>
  <img src="/blog/assets/many-platform-vision-oculus-home.png" alt="Screenshot of Oculus Home in virtual reality" />
  <figcaption>
    React and Relay power the Oculus Home and many other virtual reality experiences.
  </figcaption>
</figure>

當我們回顧行業如何採用React時，社區中一直存在對React在更多平台上運行的渴望。甚至在我們向社區宣布React Native之前，Netflix就已經在打造Gibbon，這是他們用React構建電視體驗的自定義渲染器。在Facebook開始構建桌面版Messenger之前，[微軟已經在使用React為Office和Windows 10構建原生桌面體驗](https://www.youtube.com/watch?v=IUMWFExtDSg&t=382s)。

## 總結

總而言之，我們的願景是將 React Native 的應用範圍擴展至行動裝置以外的平台，並已開始與 Facebook 的桌面和 VR 團隊展開合作。我們深知，當我們擁抱每個平台的限制、學習機構知識，並從其他參與者中汲取靈感時，整個生態系統中的每個平台都將受益。最重要的是，在此過程中，我們始終堅守[我們的指導原則](https://reactnative.dev/blog/2020/07/17/react-native-principles)。

對於未來，我們充滿期待，將持續探索多平台為 React Native 帶來的可能性。歡迎與我們聯繫 ([@reactnative](https://twitter.com/reactnative)) 以獲取更多更新並分享您的想法！