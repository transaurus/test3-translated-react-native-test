---
title: The GAAD Pledge - March Accessibility Issues Update
authors: [alexmarlette]
tags: [announcement]
---

自從我們向 GitHub 社群發布經過徹底審查的無障礙功能缺口分析與改進清單以來，已過了四週。在 React Native 社群協助下，我們在改進無障礙功能方面已取得顯著進展。社群成員持續協助貢獻者、審查測試，並關注先前的無障礙議題。自3月8日起，社群已關閉6個議題，合併4個拉取請求，另有7個拉取請求正在審查流程中。

這項工作持續進行的同時，Facebook 的 React Native 與無障礙團隊正在評估在此計劃前提交的無障礙錯誤與議題，以確認是否已被現有缺口分析涵蓋，或是否有其他議題需納入專案。目前已發現1個新議題並移入專案，4個直接對應現有議題，另有2個預計透過解決根本原因的現有議題來關閉。

感謝所有參與的社群成員！你們確實在推動 React Native 變得更具無障礙性的進程中發揮關鍵作用！

<!--truncate-->

## 已關閉的拉取請求 🎉

- [為按鈕無障礙功能新增 TalkBack 支援：disabled 屬性 #31001](https://github.com/facebook/react-native/pull/31001) - 由 [@huzaifaaak ](https://twitter.com/huzaifaaak) 關閉

- [功能：當 TouchableHighlight 禁用時設置無障礙狀態為 disabled #31135](https://github.com/facebook/react-native/pull/31135) 由 [@natural_clar](https://twitter.com/natural_clar) 關閉

- [[Android] 選中狀態未在 TextInput 組件選中時播報 #31144](https://github.com/facebook/react-native/pull/31144) 由 [fabriziobertoglio1987](https://fabriziobertoglio.xyz/) 關閉

- [為 TouchableNativeFeedback 無障礙功能新增 TalkBack 支援：disabled 屬性 #31224](https://github.com/facebook/react-native/pull/31224) 由 [@kyamashiro73](https://twitter.com/kyamashiro73) 關閉

- [無障礙/按鈕測試 #31189](https://github.com/facebook/react-native/pull/31189) 由 [@huzaifaaak ](https://twitter.com/huzaifaaak) 關閉

  - 新增按鈕 accessibilityState 的測試

## 修復項目

- `Button` 組件（由 [#31001](https://github.com/facebook/react-native/pull/31001) 修復）：

  - 現在會播報禁用狀態

  - 當按鈕禁用時，對螢幕閱讀器禁用點擊功能

  - 播報按鈕的選中狀態

- `TextInput` 組件（由 [#31144](https://github.com/facebook/react-native/pull/31144) 修復）：

  - 當 accessibilityState 的 "selected" 設為 true 且元素獲焦時，會播報「已選中」

- `TouchableHighlight` 組件（由 [#31135](https://github.com/facebook/react-native/pull/31135) 修復）：

  - 當組件禁用時，對螢幕閱讀器禁用點擊功能

- `TouchableNativeFeedback` 組件（由 [#31224](https://github.com/facebook/react-native/pull/31224) 修復）：

  - 當組件禁用時，對螢幕閱讀器禁用點擊功能

## 其他進展

| Status                                  | Number of Issues |
| --------------------------------------- | :--------------: |
| Issues To Do                            |        53        |
| In Progress Issues by the Community     |        8         |
| In Progress Issues by React Native Team |        5         |
| Pull Request in Progress                |        3         |
| Pull Request in Reviews                 |        4         |

## 參與貢獻！

- New contributors should read the [contribution guide](https://github.com/facebook/react-native/blob/master/CONTRIBUTING.md) and browse the list of 37 [good first issues](https://github.com/facebook/react-native/issues?q=is%3Aopen+is%3Aissue+label%3A%22Good+first+issue%22+label%3AAccessibility) in the React Native GitHub.

- Contributors interested in issues requiring a bit more effort should visit [the project page for Improved React Native Accessibility](https://github.com/facebook/react-native/projects/15) to see the GitHub issues that need their knowledge of React Native.

- Technical writers interested in updating React Native's documentation to reflect the accessibility gaps being closed should visit the [React Native Docs](https://github.com/facebook/react-native-website#-overview).

- Share this initiative with anyone who may be able to help!

- Follow the GAAD Pledge Open Source Accessibility Community Manager for React Native on [Twitter](https://twitter.com/alexmarlette) or [Facebook](https://www.facebook.com/React-Native-Open-Source-Accessibility-Community-Manager-102732258549941) to keep up to date on progress.