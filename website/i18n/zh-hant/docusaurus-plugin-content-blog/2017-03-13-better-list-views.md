---
title: Better List Views in React Native
author: Spencer Ahrens
authorTitle: Software Engineer at Facebook
authorURL: 'https://github.com/sahrens'
authorImageURL: 'https://avatars1.githubusercontent.com/u/1509831'
authorTwitter: sahrens2012
tags: [engineering]
---

許多開發者已在我們的[社群群組預告貼文](https://www.facebook.com/groups/react.native.community/permalink/921378591331053)後開始試用新的列表元件，而我們今天正式發佈這些元件！不再需要`ListView`或`DataSource`，不再有陳舊的行項目、被忽略的錯誤或過高的記憶體消耗——透過React Native 2017年3月候選版本(`0.43-rc.1`)，您可以從這套新元件中選擇最符合需求的方案，並獲得開箱即用的卓越效能與功能集：

### [`<FlatList>`](/docs/flatlist)

這是處理簡單高效列表的核心元件。只需提供資料陣列與`renderItem`函數即可運作：

```
<FlatList
  data={[{title: 'Title Text', key: 'item1'}, ...]}
  renderItem={({item}) => <ListItem title={item.title} />}
/>
```

### [`<SectionList>`](/docs/sectionlist)

若需渲染分邏輯區段的資料集（例如通訊錄按字母分區），或需處理異質資料與渲染（如個人檔案頁面含按鈕、編輯區、相片網格、好友網格與動態列表），此元件是最佳選擇。

```
<SectionList
  renderItem={({item}) => <ListItem title={item.title} />}
  renderSectionHeader={({section}) => <H1 title={section.key} />}
  sections={[ // homogeneous rendering between sections
    {data: [...], key: ...},
    {data: [...], key: ...},
    {data: [...], key: ...},
  ]}
/>

<SectionList
  sections={[ // heterogeneous rendering between sections
    {data: [...], key: ..., renderItem: ...},
    {data: [...], key: ..., renderItem: ...},
    {data: [...], key: ..., renderItem: ...},
  ]}
/>
```

### [`<VirtualizedList>`](/docs/virtualizedlist)

底層實作元件，提供更彈性的API。特別適用於非標準陣列資料結構（如不可變列表）。

## 功能特色

列表應用場景多元，因此我們為新元件內建多種功能以滿足多數需求：

- 滾動載入 (`onEndReached`)
- 下拉刷新 (`onRefresh` / `refreshing`)
- 可配置的[可見度](https://github.com/facebook/react-native/blob/master/Libraries/CustomComponents/Lists/ViewabilityHelper.js)回調 (`onViewableItemsChanged` / `viewabilityConfig`)
- 水平模式 (`horizontal`)
- 智能項目與區段分隔線
- 多欄支援 (`numColumns`)
- `scrollToEnd`、`scrollToIndex`與`scrollToItem`
- 更完善的Flow類型檢查

### 注意事項

- 當項目滾出渲染視窗時，其子樹的內部狀態不會保留。請確保所有資料都儲存在項目資料或外部狀態庫（如Flux、Redux或Relay）中。

- 這些元件基於`PureComponent`，意味著當`props`淺層比對相等時不會重新渲染。請確認`renderItem`函數直接依賴的所有prop在更新後都會改變參照（`!==`），否則UI可能不會響應變更。這包含`data`prop與父元件狀態。例如：

  ```jsx
  <FlatList
    data={this.state.data}
    renderItem={({item}) => (
      <MyItem
        item={item}
        onPress={() =>
          this.setState(oldState => ({
            selected: {
              // 新實例打破`===`比對
              ...oldState.selected, // 複製舊資料
              [item.key]: !oldState.selected[item.key], // 切換狀態
            },
          }))
        }
        selected={
          !!this.state.selected[item.key] // renderItem依賴於此狀態
        }
      />
    )}
    selected={
      // 可為任何不與現有prop衝突的屬性
      this.state.selected // selected變更應觸發FlatList重繪
    }
  />
  ```

- 為限制記憶體使用並確保滾動流暢，內容會先在螢幕外非同步渲染。這可能導致快速滾動時暫時看到空白內容。此為可依應用需求調整的取捨方案，我們也持續在底層優化此行為。

- 預設情況下，新列表會尋找每個項目的`key`prop作為React key。您也可透過`keyExtractor`prop自訂此行為。

## 效能表現

除了簡化 API 之外，新的列表元件還具有顯著的效能提升，主要特點是無論有多少行，記憶體使用量幾乎保持恆定。這是通過「虛擬化」渲染視窗外的元素來實現的，即完全從元件層次結構中卸載它們，並回收 React 元件佔用的 JS 記憶體，以及陰影樹和 UI 視圖佔用的原生記憶體。這有一個注意點，即內部元件狀態將不會被保留，因此**請確保您將任何重要狀態追蹤在元件本身之外，例如在 Relay、Redux 或 Flux 儲存中。**

限制渲染視窗還減少了 React 和原生平台需要完成的工作量，例如視圖遍歷。即使您正在渲染一百萬個元素中的最後一個，使用這些新列表也無需遍歷所有元素來進行渲染。您甚至可以使用 `scrollToIndex` 直接跳轉到中間，而無需過度渲染。

我們還對排程進行了一些改進，這應該有助於提升應用程式的響應速度。渲染視窗邊緣的項目會以較低的優先級進行渲染，並且在完成任何手勢、動畫或其他互動後才會進行。

## 進階用法

與 `ListView` 不同，渲染視窗中的所有項目在任何 props 變更時都會重新渲染。通常這沒問題，因為視窗化將項目數量限制為一個常數，但如果您的項目較為複雜，您應該確保遵循 React 的效能最佳實踐，並在元件內部適當使用 `React.PureComponent` 和/或 `shouldComponentUpdate` 來限制遞迴子樹的重新渲染。

如果您可以在不渲染的情況下計算出行的高度，則可以通過提供 `getItemLayout` prop 來改善用戶體驗。這使得使用例如 `scrollToIndex` 滾動到特定項目更加流暢，並且由於內容的高度可以在不渲染的情況下確定，因此會改善滾動指示器 UI。

如果您有替代的資料類型，例如不可變列表，`<VirtualizedList>` 是您的最佳選擇。它接受一個 `getItem` prop，讓您可以返回任何給定索引的項目資料，並且具有更寬鬆的 Flow 類型檢查。

如果您有特殊的使用案例，還可以調整一堆參數。例如，您可以使用 `windowSize` 來權衡記憶體使用量與用戶體驗，使用 `maxToRenderPerBatch` 來調整填充率與響應速度，使用 `onEndReachedThreshold` 來控制滾動加載的時機等等。

## 未來工作

- 遷移現有介面（最終棄用 `ListView`）。
- 根據需求增加更多功能（請告訴我們！）。
- 支援黏性區段標頭。
- 更多效能優化。
- 支援帶有狀態的功能性項目元件。