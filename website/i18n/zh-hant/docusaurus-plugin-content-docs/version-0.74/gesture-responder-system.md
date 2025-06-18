---
id: gesture-responder-system
title: Gesture Responder System
---

手勢響應系統負責管理應用程式中的手勢生命週期。當應用程式判斷使用者意圖時，一次觸控可能會經歷多個階段。例如，應用需要判斷觸控是捲動、滑動元件還是點擊。這些判斷甚至可能在觸控持續期間改變，且可能同時存在多點觸控。

觸控響應系統讓元件能在無需了解父元件或子元件的情況下，協調這些觸控互動。

### 最佳實踐

為使應用體驗更佳，每個操作應具備以下特性：

- 反饋/高亮效果 - 向使用者顯示當前處理觸控的元件及手勢釋放後的結果
- 可取消性 - 執行操作時，使用者應能透過拖移手指中途中止觸控

這些特性讓使用者能更放心地操作應用，因為允許人們嘗試互動而不必擔心操作失誤。

### TouchableHighlight 與 Touchable* 元件

響應系統可能較複雜，因此我們提供了抽象的「可觸控」實作。這套實作利用響應系統，讓您能以宣告式配置點擊互動。任何需要「可點擊」的場景（如網頁按鈕或連結）都可使用 TouchableHighlight。

## 響應者生命週期

視圖可透過實作正確的協商方法成為觸控響應者。有兩種方法詢問視圖是否願意成為響應者：

- `View.props.onStartShouldSetResponder: evt => true,` - 此視圖是否要在觸控開始時成為響應者？
- `View.props.onMoveShouldSetResponder: evt => true,` - 當視圖非響應者時，每次觸控移動都會呼叫：此視圖是否要「聲明」觸控響應權？

若視圖返回 true 並嘗試成為響應者，將發生以下情況之一：

- `View.props.onResponderGrant: evt => {}` - 視圖開始響應觸控事件，此時應顯示高亮效果讓使用者了解狀態
- `View.props.onResponderReject: evt => {}` - 當前已有其他響應者且不願釋放權限

若視圖正在響應，可能會呼叫以下處理程序：

- `View.props.onResponderMove: evt => {}` - 使用者正在移動手指
- `View.props.onResponderRelease: evt => {}` - 觸控結束時觸發（即 "touchUp"）
- `View.props.onResponderTerminationRequest: evt => true` - 其他元件要求成為響應者，當前視圖是否應釋放權限？返回 true 表示允許釋放
- `View.props.onResponderTerminate: evt => {}` - 響應權已被剝奪，可能因 onResponderTerminationRequest 呼叫後轉移給其他視圖，也可能被系統直接奪取（如 iOS 控制中心/通知中心觸發時）

`evt` 是合成觸控事件，結構如下：

- `nativeEvent`
  - `changedTouches` - 自上次事件後所有變更的觸控事件陣列
  - `identifier` - 觸控點 ID
  - `locationX` - 觸控點相對於元素的 X 座標
  - `locationY` - 觸控點相對於元素的 Y 座標
  - `pageX` - 觸控點相對於根元素的 X 座標
  - `pageY` - 觸控點相對於根元素的 Y 座標
  - `target` - 接收觸控事件的元素節點 ID
  - `timestamp` - 觸控時間標記（用於計算速度）
  - `touches` - 當前螢幕上所有觸控點的陣列

### 捕獲 ShouldSet 處理程序

`onStartShouldSetResponder` 和 `onMoveShouldSetResponder` 會以冒泡模式被呼叫，最深的節點會優先被觸發。這意味著當多個 View 對 `*ShouldSetResponder` 處理程序返回 true 時，最深的元件將成為響應者。在大多數情況下這是理想的，因為它能確保所有控制項和按鈕都可使用。

然而，有時父元件會希望確保自己成為響應者。這可以通過使用捕獲階段來處理。在響應系統從最深元件冒泡之前，它會先執行捕獲階段，觸發 `on*ShouldSetResponderCapture`。因此，如果父 View 想要阻止子元件在觸摸開始時成為響應者，它應該設置一個返回 true 的 `onStartShouldSetResponderCapture` 處理程序。

- `View.props.onStartShouldSetResponderCapture: evt => true,`
- `View.props.onMoveShouldSetResponderCapture: evt => true,`

### PanResponder

如需更高層級的手勢解譯，請參閱 [PanResponder](panresponder.md)。