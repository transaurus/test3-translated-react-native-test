---
id: gesture-responder-system
title: Gesture Responder System
---

手勢響應系統負責管理應用中手勢的生命週期。當應用判斷用戶意圖時，一次觸碰可能會經歷多個階段。例如，應用需要判斷觸碰是滾動、滑動控件還是點擊。這些判斷甚至可能在觸碰持續期間發生變化，且可能同時存在多點觸碰。

觸碰響應系統讓組件能在無需了解父組件或子組件的情況下，協調這些觸碰交互行為。

### 最佳實踐

為使應用體驗流暢，每個操作應具備以下特性：

- 反饋/高亮效果 - 向用戶展示當前處理觸碰的元件及手勢釋放後的預期行為
- 可取消性 - 用戶在觸碰過程中可通過拖離手指中止當前操作

這些特性能提升用戶使用應用時的安心感，讓人們能無懼錯誤地自由探索交互。

### TouchableHighlight 與 Touchable* 組件

響應系統可能較複雜，因此我們為"可點擊"元素提供了抽象的「Touchable」實現。該實現基於響應系統，並允許以聲明式配置點擊交互。任何需要網頁按鈕或鏈接功能的場景都可使用「TouchableHighlight」。

## 響應者生命週期

視圖可通過實現正確的協商方法成為觸碰響應者。有兩種方法詢問視圖是否願意成為響應者：

- `View.props.onStartShouldSetResponder: evt => true,` - 該視圖是否希望在觸碰開始時成為響應者？
- `View.props.onMoveShouldSetResponder: evt => true,` - 當視圖非響應者時，每次觸碰移動都會觸發此詢問：該視圖是否想"聲明"觸碰響應權？

若視圖返回true並嘗試成為響應者，將發生以下情況之一：

- `View.props.onResponderGrant: evt => {}` - 視圖開始響應觸碰事件，此時應顯示高亮效果向用戶反饋
- `View.props.onResponderReject: evt => {}` - 當前已有其他響應者且不願釋放權限

當視圖成為響應者後，可能觸發以下處理程序：

- `View.props.onResponderMove: evt => {}` - 用戶手指移動時觸發
- `View.props.onResponderRelease: evt => {}` - 觸碰結束時觸發（即"touchUp"）
- `View.props.onResponderTerminationRequest: evt => true` - 其他元件請求成為響應者，當前視圖是否應釋放權限？返回true表示允許
- `View.props.onResponderTerminate: evt => {}` - 響應權被剝奪，可能因其他視圖調用「onResponderTerminationRequest」所致，也可能被系統無預警接管（如iOS控制中心/通知中心觸發時）

「evt」是合成觸碰事件，結構如下：

- `nativeEvent`
  - `changedTouches` - 自上次事件後所有變化的觸碰事件數組
  - `identifier` - 觸碰點ID
  - `locationX` - 相對於元素的觸碰X座標
  - `locationY` - 相對於元素的觸碰Y座標
  - `pageX` - 相對於根元素的觸碰X座標
  - `pageY` - 相對於根元素的觸碰Y座標
  - `target` - 接收觸碰事件的元素節點ID
  - `timestamp` - 觸碰時間標識符（用於計算速度）
  - `touches` - 當前屏幕上所有觸碰點的數組

### 捕獲型ShouldSet處理器

`onStartShouldSetResponder` 和 `onMoveShouldSetResponder` 會以冒泡模式呼叫，最深的節點會優先被觸發。這意味著當多個視圖對 `*ShouldSetResponder` 處理程序返回 true 時，最深的元件將成為響應者。在大多數情況下這是理想的，因為它能確保所有控制項和按鈕都可使用。

然而，有時父元件會希望確保自己成為響應者。這可以通過使用捕獲階段來處理。在響應系統從最深元件冒泡之前，會先執行捕獲階段，觸發 `on*ShouldSetResponderCapture`。因此，如果父視圖想阻止子元件在觸摸開始時成為響應者，它應該設置一個返回 true 的 `onStartShouldSetResponderCapture` 處理程序。

- `View.props.onStartShouldSetResponderCapture: evt => true,`
- `View.props.onMoveShouldSetResponderCapture: evt => true,`

### PanResponder

如需更高階的手勢解譯，請參閱 [PanResponder](panresponder.md)。