---
id: gesture-responder-system
title: Gesture Responder System
---

手勢響應系統負責管理應用程式中的手勢生命週期。一個觸控操作可能會經歷多個階段，因為應用程式需要判斷使用者的意圖。例如，應用程式需要判斷該觸控是滾動、滑動某個元件，還是點擊。甚至在觸控持續期間，這個判斷也可能改變。同時也可能存在多個並行的觸控操作。

觸控響應系統的存在，讓元件能在不需要了解其父元件或子元件的情況下，協調這些觸控互動。

### 最佳實踐

為了讓應用程式體驗更佳，每個操作都應具備以下特性：

- 反饋/高亮效果 - 向使用者展示當前正在處理他們的觸控操作，以及當他們釋放手勢時會發生什麼
- 可取消性 - 當進行某個操作時，使用者應該能夠在觸控過程中通過將手指拖離來中止該操作

這些特性讓使用者在操作應用程式時更加舒適，因為它允許人們在不必擔心犯錯的情況下進行嘗試和互動。

### TouchableHighlight 與 Touchable* 元件

響應系統可能較為複雜。因此我們提供了一個抽象的 `Touchable` 實現，用於那些應該「可點擊」的元素。這利用了響應系統，並允許您以宣告式的方式配置點擊互動。在任何您會在網頁上使用按鈕或連結的地方，都可以使用 `TouchableHighlight`。

## 響應者生命週期

一個視圖可以通過實現正確的協商方法來成為觸控響應者。有兩種方法可以詢問視圖是否想要成為響應者：

- `View.props.onStartShouldSetResponder: evt => true,` - 這個視圖是否希望在觸控開始時成為響應者？
- `View.props.onMoveShouldSetResponder: evt => true,` - 當視圖不是響應者時，每次觸控移動都會呼叫此方法：這個視圖是否想要「聲明」觸控響應權？

如果視圖返回 true 並嘗試成為響應者，將會發生以下情況之一：

- `View.props.onResponderGrant: evt => {}` - 視圖現在負責處理觸控事件。此時應該高亮並向使用者展示正在發生的情況
- `View.props.onResponderReject: evt => {}` - 目前有其他元件是響應者且不願意釋放響應權

如果視圖正在響應，可能會呼叫以下處理程序：

- `View.props.onResponderMove: evt => {}` - 使用者正在移動手指
- `View.props.onResponderRelease: evt => {}` - 在觸控結束時觸發，即「touchUp」
- `View.props.onResponderTerminationRequest: evt => true` - 其他元件想要成為響應者。這個視圖是否應該釋放響應權？返回 true 表示允許釋放
- `View.props.onResponderTerminate: evt => {}` - 響應權已從視圖中取走。可能是在呼叫 `onResponderTerminationRequest` 後被其他視圖取走，也可能是被作業系統未經詢問直接取走（iOS 上的控制中心/通知中心會發生這種情況）

`evt` 是一個合成觸控事件，具有以下結構：

- `nativeEvent`
  - `changedTouches` - 自上次事件以來所有已變更的觸控事件陣列
  - `identifier` - 觸控操作的 ID
  - `locationX` - 觸控位置的 X 座標，相對於元素
  - `locationY` - 觸控位置的 Y 座標，相對於元素
  - `pageX` - 觸控位置的 X 座標，相對於根元素
  - `pageY` - 觸控位置的 Y 座標，相對於根元素
  - `target` - 接收觸控事件的元素的節點 ID
  - `timestamp` - 觸控操作的時間標識，用於速度計算
  - `touches` - 當前螢幕上所有觸控操作的陣列

### 捕獲 ShouldSet 處理程序

`onStartShouldSetResponder` 和 `onMoveShouldSetResponder` 是以冒泡模式呼叫的，最深的節點會最先被呼叫。這意味著當多個視圖對 `*ShouldSetResponder` 處理程序返回 true 時，最深的組件將成為響應者。在大多數情況下這是理想的，因為它能確保所有控件和按鈕都可使用。

然而，有時父組件會希望確保自己成為響應者。這可以通過使用捕獲階段來處理。在響應系統從最深組件冒泡之前，它會先執行一個捕獲階段，觸發 `on*ShouldSetResponderCapture`。因此，如果父視圖想要阻止子組件在觸摸開始時成為響應者，它應該有一個返回 true 的 `onStartShouldSetResponderCapture` 處理程序。

- `View.props.onStartShouldSetResponderCapture: evt => true,`
- `View.props.onMoveShouldSetResponderCapture: evt => true,`

### PanResponder

對於更高層次的手勢解譯，請參閱 [PanResponder](panresponder.md)。