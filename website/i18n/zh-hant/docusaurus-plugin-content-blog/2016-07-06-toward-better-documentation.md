---
title: Toward Better Documentation
author: Kevin Lacker
authorTitle: Engineering Manager at Facebook
authorURL: 'https://twitter.com/lacker'
authorImageURL: 'https://www.gravatar.com/avatar/9b790592be15d4f55a5ed7abb5103304?s=128'
authorTwitter: lacker
tags: [announcement]
---

優質的開發者體驗部分來自於完善的技術文件。撰寫優秀文件需要投入大量心力——理想的文件應當簡潔、實用、準確、完整且令人愉悅。近期我們根據您的反饋持續改進文件，在此分享部分優化成果。

## 內嵌範例

當您學習新函式庫、程式語言或框架時，最美好的時刻莫過於首次寫入代碼並驗證其運作——當它成功運行時，您創造了真實存在的東西。我們希望將這種直觀體驗直接融入文件中，就像這樣：

```ReactNativeWebPlayer
import React, { Component } from 'react';
import { AppRegistry, Text, View } from 'react-native';

class ScratchPad extends Component {
  render() {
    return (
      <View style={{flex: 1}}>
        <Text style={{fontSize: 30, flex: 1, textAlign: 'center'}}>
          Isn't this cool?
        </Text>
        <Text style={{fontSize: 100, flex: 1, textAlign: 'center'}}>
          👍
        </Text>
      </View>
    );
  }
}

AppRegistry.registerComponent('ScratchPad', () => ScratchPad);
```

這些內嵌範例採用[`react-native-web-player`](https://github.com/dabbott/react-native-web-player)模組（由[Devin Abbott](https://twitter.com/devinaabbott)協助開發），是學習React Native基礎的絕佳方式。我們已更新[React Native新手教學](/docs/tutorial)，盡可能採用此形式。試試看吧——若您曾好奇修改示例代碼會發生什麼，這將是絕佳的實驗方式。此外，若您正在開發工具並希望展示即時React Native範例，[`react-native-web-player`](https://github.com/dabbott/react-native-web-player)能簡化此流程。

核心模擬引擎由[Nicolas Gallagher](https://twitter.com/necolas)的[`react-native-web`](https://github.com/necolas/react-native-web)專案提供，該專案實現了在網頁端顯示如`Text`和`View`等React Native組件。若您有興趣建構共享代碼庫的行動與網頁應用，請參閱[`react-native-web`](https://github.com/necolas/react-native-web)。

## 強化指南

React Native某些功能存在多種實現方式，我們收到反饋建議需要更明確的指引。

新版[導航指南](/docs/navigation)比較了不同方案（如`Navigator`、`NavigatorIOS`、`NavigationExperimental`）並提供選用建議。中期規劃中，我們正致力於改進並整合這些介面；短期內，更完善的指南希望能簡化您的工作流程。

我們還新增了[觸控處理指南](/docs/handling-touches)，講解按鈕式介面的基礎實作，並簡述不同觸控事件處理方式。

另一項重點是Flexbox佈局，包含[Flexbox佈局教學](/docs/flexbox)、[組件尺寸控制](/docs/height-and-width)，以及雖不炫目但實用的[React Native佈局屬性全集](/docs/layout-props)。

## 入門指引

在本地環境建置React Native開發環境時，需安裝並配置多項工具。雖然難以讓安裝過程充滿樂趣，但我們至少能使其快速且無痛。

我們設計了[新版入門流程](/docs/next/getting-started)，讓您預先選擇開發作業系統與行動作業系統，集中提供所有設定說明。我們也實際測試安裝流程，確保每個決策點都有明確建議。經過同事實測後，我們確信這項改進卓有成效。

同時優化了[現有應用整合指南](/docs/integration-with-existing-apps)。許多大型應用（如Facebook主應用）實際採用混合開發模式，部分功能使用React Native實現。我們希望此指南能降低此類開發模式的門檻。

## 需要您的協助

您的反饋讓我們知道應該優先處理哪些事項。我知道有些人讀完這篇部落格文章後會想：「更好的文件？哼！X 的文件還是很糟糕！」這很好——我們需要這種動力。根據反饋類型的不同，提供反饋的最佳方式也有所不同。

如果您在文件中發現錯誤，例如描述不準確或程式碼無法運行，請[提交問題](https://github.com/facebook/react-native/issues)。標記為「Documentation」，以便更容易將其路由給正確的人員。

如果沒有具體錯誤，但文件中的某些內容根本令人困惑，這不太適合 GitHub 問題。相反，請在 [Canny](https://react-native.canny.io/feature-requests) 上發布有關需要幫助的文件區域的資訊。這有助於我們在進行更一般性的工作（如指南撰寫）時確定優先順序。

感謝您閱讀到這裡，也感謝您使用 React Native！