---
id: gesture-responder-system
title: Gesture Responder System
---

手勢響應系統負責管理應用程式中的手勢生命週期。當應用程式判斷使用者意圖時，觸控操作可能會經歷多個階段。例如，應用程式需要判斷觸控是滾動、滑動小工具還是點擊。這些判斷甚至可能在觸控持續期間發生變化，同時也可能存在多個並發觸控操作。

觸控響應系統的設計目的，是讓元件能在無需了解父元件或子元件的情況下，協調這些觸控互動行為。

### 最佳實踐

要讓應用程式體驗更佳，每個操作都應具備以下特性：

- 反饋/高亮效果 - 向使用者顯示當前處理觸控的元件，以及釋放手勢後將發生的動作
- 可取消性 - 執行操作時，使用者應能通過拖移手指中途中止該操作

這些特性讓使用者能更自在地操作應用程式，因為它允許人們在無需擔心操作失誤的情況下進行嘗試和互動。

### TouchableHighlight 與 Touchable* 元件

響應系統的使用可能較為複雜。因此我們為需要實現「可點擊」功能的元件提供了抽象的 `Touchable` 實現方案。該方案利用響應系統，並允許您以宣告式配置點擊互動。在任何需要按鈕或連結的場景中（類似網頁），都可使用 `TouchableHighlight`。

## 響應者生命週期

視圖可通過實現正確的協商方法成為觸控響應者。有兩種方法用於詢問視圖是否希望成為響應者：

- `View.props.onStartShouldSetResponder: (evt) => true,` - 該視圖是否希望在觸控開始時成為響應者？
- `View.props.onMoveShouldSetResponder: (evt) => true,` - 當視圖非響應者時，每次觸控移動都會觸發此方法：該視圖是否想「聲明」觸控響應權？

若視圖返回 true 並嘗試成為響應者，將發生以下情況之一：

- `View.props.onResponderGrant: (evt) => {}` - 該視圖現已取得觸控事件響應權。此時應顯示高亮效果，向使用者展示當前狀態
- `View.props.onResponderReject: (evt) => {}` - 當前已有其他元件佔用響應權且不願釋放

若視圖正在響應，則可能調用以下處理程序：

- `View.props.onResponderMove: (evt) => {}` - 使用者正在移動手指
- `View.props.onResponderRelease: (evt) => {}` - 觸控結束時觸發（即 "touchUp"）
- `View.props.onResponderTerminationRequest: (evt) => true` - 其他元件請求取得響應權。該視圖是否應釋放響應權？返回 true 表示允許釋放
- `View.props.onResponderTerminate: (evt) => {}` - 響應權已從該視圖剝離。可能因 `onResponderTerminationRequest` 調用後被其他視圖取得，也可能被作業系統未經詢問直接剝離（iOS 系統中控制中心/通知中心的行為）

`evt` 為合成觸控事件，其結構如下：

- `nativeEvent`
  - `changedTouches` - 自上次事件以來所有發生變化的觸控事件陣列
  - `identifier` - 觸控操作 ID
  - `locationX` - 觸控點相對於元素的 X 座標
  - `locationY` - 觸控點相對於元素的 Y 座標
  - `pageX` - 觸控點相對於根元素的 X 座標
  - `pageY` - 觸控點相對於根元素的 Y 座標
  - `target` - 接收觸控事件的元素節點 ID
  - `timestamp` - 觸控操作的時間標識符，可用於計算速度
  - `touches` - 當前螢幕上所有觸控點的陣列

### 捕獲 ShouldSet 處理程序

`onStartShouldSetResponder` and `onMoveShouldSetResponder` are called with a bubbling pattern, where the deepest node is called first. That means that the deepest component will become responder when multiple Views return true for `*ShouldSetResponder` handlers. This is desirable in most cases, because it makes sure all controls and buttons are usable.

However, sometimes a parent will want to make sure that it becomes responder. This can be handled by using the capture phase. Before the responder system bubbles up from the deepest component, it will do a capture phase, firing `on*ShouldSetResponderCapture`. So if a parent View wants to prevent the child from becoming responder on a touch start, it should have a `onStartShouldSetResponderCapture` handler which returns true.

- `View.props.onStartShouldSetResponderCapture: (evt) => true,`
- `View.props.onMoveShouldSetResponderCapture: (evt) => true,`

### PanResponder

For higher-level gesture interpretation, check out [PanResponder](panresponder.md).