---
title: Releasing React Native 0.59
author: Ryan Turner
authorTitle: Core Maintainer & React Native Developer
authorURL: 'https://twitter.com/turnrye'
authorImageURL: 'https://avatars0.githubusercontent.com/u/701035?s=460&v=4'
authorTwitter: turnrye
tags: [announcement, release]
---

歡迎來到 React Native 0.59 版本！這是一個包含 88 位貢獻者提交的 644 個 commits 的重大版本。貢獻也以其他形式呈現，因此 _感謝您_ 維護問題、培育社群以及向人們傳授 React Native 知識。本月帶來了一些備受期待的變更，希望您會喜歡。

## 🎣 Hooks 登場

React Hooks 是此版本的一部分，它讓您可以在組件之間重用狀態邏輯。關於 hooks 有很多討論，但如果您還沒聽說過，請看看以下一些精彩的資源：

> - [介紹 Hooks](https://reactjs.org/docs/hooks-intro.html) 解釋了為什麼我們要在 React 中添加 Hooks。
> - [Hooks 概覽](https://reactjs.org/docs/hooks-overview.html) 是對內置 Hooks 的快速概述。
> - [構建您自己的 Hooks](https://reactjs.org/docs/hooks-custom.html) 展示了如何使用自定義 Hooks 重用代碼。
> - [理解 React Hooks](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889) 探討了 Hooks 解鎖的新可能性。
> - [useHooks.com](https://usehooks.com/) 展示了社區維護的 Hooks 配方和演示。

請務必在您的應用中嘗試這些功能。我們希望您能像我們一樣對這種重用感到興奮。

## 📱 更新的 JSC 帶來性能提升和 Android 上的 64 位支持

React Native 使用 JSC ([JavaScriptCore](https://webkit.org/)) 來驅動您的應用程序。Android 上的 JSC 已經有幾年的歷史，這意味著許多現代 JavaScript 功能不受支持。更糟糕的是，與 iOS 的現代 JSC 相比，它的性能較差。隨著此版本的發布，這一切都將改變。

感謝 [@DanielZlotin](https://github.com/danielzlotin)、[@dulmandakh](https://github.com/dulmandakh)、[@gengjiawen](https://github.com/gengjiawen)、[@kmagiera](https://github.com/kmagiera) 和 [@kudo](https://github.com/kudo) 的出色工作，JSC 已經趕上了過去幾年的發展。這帶來了 64 位支持、現代 JavaScript 支持以及[巨大的性能改進](https://github.com/react-native-community/jsc-android-buildscripts/tree/master/measure)。同時也感謝他們使這一過程變得可維護，以便我們可以在未來輕鬆利用 WebKit 的改進，而無需太多繁瑣的工作。感謝 Software Mansion 和 Expo 使這項工作成為可能。

## 💨 通過內聯 requires 加快應用啟動速度

我們希望幫助人們默認擁有高性能的 React Native 應用，並正在努力將 Facebook 的優化帶給社區。應用程序按需加載資源，而不是減慢啟動速度。此功能稱為「內聯 requires」，因為它讓 Metro 識別需要懶加載的組件。具有深度和多樣化組件架構的應用將看到最大的改進。

![0.59 模板中的 `metro.config.js` 文件源代碼，展示了如何啟用 `inlineRequires`](/blog/assets/inline-requires.png)

我們需要社區在我們默認啟用此功能之前告訴我們它的效果如何。當您升級到 0.59 時，將會有一個新的 `metro.config.js` 文件；將選項切換為 true 並給我們[您的反饋](https://twitter.com/hashtag/inline-requires)！閱讀更多關於內聯 requires 的內容[在性能文檔中](/docs/performance#inline-requires)以對您的應用進行基準測試。

## 🚅 Lean core 正在進行中

React Native 是一個龐大而複雜的項目，擁有複雜的代碼庫。這使得代碼庫對貢獻者不太友好，難以測試，並且作為開發依賴項顯得臃腫。[Lean Core](https://github.com/react-native-community/discussions-and-proposals/issues/6) 是我們通過將代碼遷移到單獨的庫中以更好地管理這些問題的努力。過去的幾個版本已經看到了這方面的第一步，但[讓我們認真對待](https://www.youtube.com/watch?v=FMLKb4or8yg)。

您可能會注意到，現在有額外的元件已被正式棄用。這是個好消息，因為這些功能現在有維護者積極維護它們。請注意警告訊息並遷移到這些功能的新函式庫，因為它們將在未來的版本中被移除。下方表格列出了元件、其狀態以及您可以遷移到的替代方案。

| Component            | Deprecated? | New home                                                                                                                                                 |
| -------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AsyncStorage**     | 0.59        | [@react-native-community/react-native-async-storage](https://github.com/react-native-community/react-native-async-storage)                               |
| **ImageStore**       | 0.59        | [expo-file-system](https://github.com/expo/expo/tree/master/packages/expo-file-system) or [react-native-fs](https://github.com/itinance/react-native-fs) |
| **MaskedViewIOS**    | 0.59        | [@react-native-community/react-native-masked-view](https://github.com/react-native-community/react-native-masked-view)                                   |
| **NetInfo**          | 0.59        | [@react-native-community/react-native-netinfo](https://github.com/react-native-community/react-native-netinfo)                                           |
| **Slider**           | 0.59        | [@react-native-community/react-native-slider](https://github.com/react-native-community/react-native-slider)                                             |
| **ViewPagerAndroid** | 0.59        | [@react-native-community/react-native-viewpager](https://github.com/react-native-community/react-native-viewpager)                                       |

在接下來的幾個月裡，將有更多元件遵循這條精簡核心的路徑。我們需要協助來完成這項工作 — 請前往 [lean core umbrella](https://github.com/facebook/react-native/issues/23313) 參與貢獻。

## 👩🏽‍💻 CLI 改進

React Native 的命令行工具是開發者進入生態系統的入口，但它們長期存在問題且缺乏官方支援。CLI 工具已移至 [新的儲存庫](https://github.com/react-native-community/react-native-cli)，並且由 [專職的維護者團隊](https://blog.callstack.io/the-react-native-cli-has-a-new-home-79b63838f0e6) 進行了一些令人興奮的改進。

現在日誌的格式更加美觀。命令的執行速度幾乎是即時的 — 您會立即注意到差異：

![0.58 版本的 CLI 啟動緩慢](/blog/assets/0.58-cli-speed.png)![0.59 版本的 CLI 幾乎是即時的](/blog/assets/0.59-cli-speed.png)

## 🚀 升級至 0.59

我們聽取了您對 [React Native 升級流程](https://github.com/react-native-community/discussions-and-proposals/issues/68) 的反饋，並正在採取措施在 [未來的版本](https://github.com/react-native-community/discussions-and-proposals/issues/64#issuecomment-444775432) 中改善體驗。要升級至 0.59，我們建議使用 [`rn-diff-purge`](https://github.com/react-native-community/rn-diff-purge) 來確定您當前的 React Native 版本與 0.59 之間的變更，然後手動應用這些變更。一旦您將專案升級至 0.59，您將能夠使用新改進的 `react-native upgrade` 命令（基於 `rn-diff-purge`！）來升級至 0.60 及更高版本。

## 🔨 重大變更

0.59 中的 Android 支援已根據 Google 的最新建議進行了清理，這可能會導致現有應用程式出現問題。此問題可能表現為運行時崩潰並顯示訊息「You need to use a Theme.AppCompat theme (or descendant) with this activity」。我們建議更新您專案的 `AndroidManifest.xml` 文件，確保 `android:theme` 的值是 `AppCompat` 主題（例如 `@style/Theme.AppCompat.Light.NoActionBar`）。

`react-native-git-upgrade` 命令已在 0.59 中被移除，取而代之的是新改進的 `react-native upgrade` 命令。

## 🤗 感謝

許多新貢獻者協助 [啟用從 Flow 類型生成原生程式碼](https://github.com/facebook/react-native/issues/22990) 和 [解決 Xcode 警告](https://github.com/facebook/react-native/issues/22609) — 這些是了解 React Native 如何運作並為公共利益做出貢獻的好方法。謝謝！請留意未來的類似問題。

雖然這些是我們注意到的亮點，但還有許多其他令人興奮的更新。要查看所有更新，請參閱 [變更日誌](https://github.com/react-native-community/react-native-releases/blob/master/CHANGELOG.md)。0.59 是一個重大版本 — 我們迫不及待想讓您試用。

我們在今年還會有更多改進。敬請期待！

[Ryan](https://github.com/turnrye) 和整個 [React Native 核心團隊](https://twitter.com/reactnative)