---
id: gesture-responder-system
title: Gesture Responder System
---

手勢響應系統負責管理應用中手勢的生命週期。當應用判斷用戶意圖時，一次觸碰可能會經歷多個階段。例如，應用需要判斷觸碰是滾動、滑動元件還是點擊。甚至在觸碰持續期間，這個判斷也可能改變。同時還可能存在多個並發的觸碰。

觸碰響應系統的設計目的，是讓元件能在無需了解父元件或子元件的情況下，協調這些觸碰交互。

### 最佳實踐

要讓應用體驗良好，每個操作都應具備以下特性：

- 反饋/高亮效果 - 向用戶展示當前處理觸碰的元件，以及手勢釋放後會發生的動作
- 可取消性 - 執行操作時，用戶應能通過拖動手指中斷正在進行的觸碰動作

這些特性讓用戶使用應用時更安心，因為允許人們在無需擔心操作錯誤的情況下進行嘗試和交互。

### TouchableHighlight 與 Touchable* 元件

響應系統可能較複雜。因此我們為需要「可點擊」的場景提供了抽象的 `Touchable` 實現。該實現利用響應系統，並允許您以聲明式配置點擊交互。任何在網頁上使用按鈕或連結的地方，都可以使用 `TouchableHighlight`。

## 響應者生命週期

視圖可通過實現正確的協商方法成為觸碰響應者。有兩個方法用於詢問視圖是否願意成為響應者：

- `View.props.onStartShouldSetResponder: evt => true,` - 該視圖是否希望在觸碰開始時成為響應者？
- `View.props.onMoveShouldSetResponder: evt => true,` - 當視圖非響應者時，每次觸碰移動都會調用：該視圖是否想「聲明」觸碰響應權？

若視圖返回 true 並嘗試成為響應者，將發生以下情況之一：

- `View.props.onResponderGrant: evt => {}` - 該視圖現正響應觸碰事件。此時應顯示高亮效果向用戶展示正在發生的操作
- `View.props.onResponderReject: evt => {}` - 當前已有其他元件作為響應者且不願釋放權限

若視圖正在響應，可能會調用以下處理程序：

- `View.props.onResponderMove: evt => {}` - 用戶正在移動手指
- `View.props.onResponderRelease: evt => {}` - 觸碰結束時觸發，即「touchUp」
- `View.props.onResponderTerminationRequest: evt => true` - 其他元件請求成為響應者。該視圖是否應釋放響應權？返回 true 表示允許釋放
- `View.props.onResponderTerminate: evt => {}` - 響應權已被從該視圖剝奪。可能因 `onResponderTerminationRequest` 調用後被其他視圖獲取，也可能被系統未經詢問直接剝奪（iOS 上的控制中心/通知中心會觸發此情況）

`evt` 是合成觸碰事件，結構如下：

- `nativeEvent`
  - `changedTouches` - 自上次事件後所有發生變化的觸碰事件陣列
  - `identifier` - 觸碰的唯一識別碼
  - `locationX` - 觸碰相對於元件的 X 座標
  - `locationY` - 觸碰相對於元件的 Y 座標
  - `pageX` - 觸碰相對於根元件的 X 座標
  - `pageY` - 觸碰相對於根元件的 Y 座標
  - `target` - 接收觸碰事件的元件節點 ID
  - `timestamp` - 觸碰的時間標識符，用於計算速度
  - `touches` - 當前螢幕上所有觸碰的陣列

### 捕獲 ShouldSet 處理程序

`onStartShouldSetResponder` 和 `onMoveShouldSetResponder` 會以冒泡模式呼叫，最深的節點會優先被呼叫。這意味著當多個 View 對 `*ShouldSetResponder` 處理程序返回 true 時，最深的元件將成為響應者。這在大多數情況下是理想的，因為它能確保所有控制項和按鈕都可使用。

然而，有時父元件會希望確保自己成為響應者。這可以通過使用捕獲階段來處理。在響應系統從最深元件冒泡之前，它會先執行捕獲階段，觸發 `on*ShouldSetResponderCapture`。因此，如果父 View 希望阻止子元件在觸摸開始時成為響應者，它應該有一個返回 true 的 `onStartShouldSetResponderCapture` 處理程序。

- `View.props.onStartShouldSetResponderCapture: evt => true,`
- `View.props.onMoveShouldSetResponderCapture: evt => true,`

### PanResponder

如需更高層次的手勢解譯，請參閱 [PanResponder](panresponder.md)。