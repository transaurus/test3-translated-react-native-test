---
id: testing-overview
title: Testing
author: Vojtech Novak
authorURL: 'https://twitter.com/vonovak'
description: This guide introduces React Native developers to the key concepts behind testing, how to write good tests, and what kinds of tests you can incorporate into your workflow.
---

隨著程式碼庫擴展，未預期的小錯誤和邊緣案例可能引發連鎖反應導致重大故障。錯誤會造成糟糕的使用者體驗，最終導致業務損失。預防脆弱程式設計的方法之一，就是在釋出前對程式碼進行測試。

本指南將涵蓋多種自動化測試方法，從靜態分析到端到端測試，確保您的應用程式如預期運作。

<img src="/docs/assets/diagram_testing.svg" alt="Testing is a cycle of fixing, testing, and either passing to release or failing back into testing." />

## 為什麼要測試

人非聖賢，孰能無過。測試的重要性在於它能幫助您發現錯誤並驗證程式碼是否正常運作。更重要的是，測試能確保未來在新增功能、重構現有程式碼或升級專案主要依賴項時，程式碼仍能持續運作。

測試的價值遠超您的想像。修復程式錯誤的最佳方法之一，就是撰寫一個能暴露該錯誤的失敗測試。當您修復錯誤後重新執行測試，若測試通過即表示錯誤已修復，且不會再次出現在程式碼庫中。

測試也能作為新團隊成員的技術文件。對於初次接觸程式碼庫的人來說，閱讀測試能幫助他們理解現有程式碼的運作方式。

最後但同樣重要的是，更多自動化測試意味著更少手動<abbr title="Quality Assurance">QA</abbr>時間，釋放寶貴資源。

## 靜態分析

提升程式碼品質的第一步是使用靜態分析工具。靜態分析會在撰寫程式碼時檢查錯誤，但無需實際執行程式碼。

- **Linter**會分析程式碼以捕捉常見錯誤（如未使用的程式碼），幫助避免陷阱，並標記違反風格指南的行為（例如使用製表符而非空格，或反之，取決於您的配置）。
- **型別檢查**確保傳遞給函式的結構符合函式設計預期，例如防止將字串傳遞給預期接收數字的計數函式。

React Native 預設配置了兩種工具：[ESLint](https://eslint.org/)用於程式碼檢查，[Flow](https://flow.org/en/docs/)用於型別檢查。您也可以使用[TypeScript](typescript)，這是一種編譯為純JavaScript的型別化語言。

## 撰寫可測試的程式碼

要開始測試，首先需要撰寫可測試的程式碼。以飛機製造過程為例——在模型首次起飛展示所有複雜系統協同運作前，會先測試各個零件以確保安全性和功能性。例如：機翼需承受極端負載彎曲測試、引擎零件需通過耐久性測試、擋風玻璃需模擬鳥擊測試。

軟體開發亦如是。與其將整個程式寫在一個龐大檔案中，不如將程式碼拆分為多個小型模組進行更徹底的測試。這種方式下，撰寫可測試程式碼與撰寫乾淨、模組化程式碼息息相關。

要讓應用程式更具可測試性，首先需將視圖部分（React元件）與業務邏輯和應用狀態分離（無論您使用Redux、MobX或其他解決方案）。如此一來，您就能讓業務邏輯測試（不應依賴React元件）獨立於元件本身，而元件的主要職責是渲染應用程式UI！

理論上，您可以將所有邏輯和資料獲取完全移出元件。這樣元件將專注於渲染，狀態完全獨立於元件，應用程式邏輯甚至能在沒有任何React元件的情況下運作！

> 我們鼓勵您透過其他學習資源進一步探索可測試程式碼的主題。

## 撰寫測試

在撰寫可測試的程式碼後，接下來就是實際編寫測試！React Native 的預設模板內建了 [Jest](https://jestjs.io) 測試框架，並包含針對此環境量身打造的預設配置，讓您無需立即調整設定和模擬(mock)就能開始工作——稍後會詳細說明[模擬功能](#mocking)。您可以使用 Jest 來編寫本指南中提到的所有測試類型。

> 若採用測試驅動開發(Test-driven development)，您實際上會先寫測試！這樣就能確保程式碼的可測試性。

### 測試結構

測試應該簡短且理想上只驗證單一功能。以下是用 Jest 編寫的單元測試範例：

```js
it('given a date in the past, colorForDueDate() returns red', () => {
  expect(colorForDueDate('2000-10-20')).toBe('red');
});
```

測試描述由傳入 [`it`](https://jestjs.io/docs/en/api#testname-fn-timeout) 函式的字串定義。請仔細撰寫描述以明確說明測試內容，建議涵蓋以下要素：

1. **Given** - 前置條件
2. **When** - 被測函式執行的動作  
3. **Then** - 預期結果

此模式也稱為 AAA 原則（Arrange, Act, Assert）。

Jest 提供 [`describe`](https://jestjs.io/docs/en/api#describename-fn) 函式來組織測試，可將相同功能的測試歸為一組，並支援巢狀結構。其他常用函式如 [`beforeEach`](https://jestjs.io/docs/en/api#beforeeachfn-timeout) 或 [`beforeAll`](https://jestjs.io/docs/en/api#beforeallfn-timeout) 可用於設置測試對象，詳見 [Jest API 文件](https://jestjs.io/docs/en/api)。

若測試步驟過多或驗證點繁雜，建議拆分成多個小測試。同時確保測試之間完全獨立——每個測試都應能單獨執行，且完整測試套件運行時，前序測試不會影響後續結果。

最後提醒：開發者通常期望程式碼完美運行，但測試恰恰相反。請將測試失敗視為「好事」！這代表發現潛在問題，讓您能在影響用戶前及時修復。

## 單元測試

單元測試針對程式碼的最小單位，例如獨立函式或類別。

當測試對象有依賴項時，通常需要將其模擬(mock)替換，如下段所述。

單元測試的優勢在於編寫和執行速度快，能即時反饋程式碼狀態。Jest 還提供 [Watch 模式](https://jestjs.io/docs/en/cli#watch)，可持續運行與編輯程式碼相關的測試。

<img src="/docs/assets/p_tests-unit.svg" alt=" " />

### 模擬(Mocking)

當測試對象有外部依賴時，您可能需要「模擬替換」這些依賴。「模擬」是指用自訂實作取代程式碼的某些依賴項。

> 原則上，測試中使用真實對象優於模擬，但某些情況無法避免。例如：當 JS 單元測試依賴 Java 或 Objective-C 寫的原生模組時。

假設您正在開發顯示城市天氣的應用程式，並使用外部服務提供天氣數據。若服務回報下雨，您需顯示雨雲圖示。但在測試中不應實際呼叫該服務，因為：

- 這可能導致測試變慢且不穩定（因為涉及網路請求）
- 服務每次運行測試時可能返回不同的數據
- 第三方服務可能在你急需運行測試時離線！

因此，你可以提供該服務的模擬實現，有效取代數千行程式碼和某些連網溫度計！

> Jest 內建[模擬支援功能](https://jestjs.io/docs/en/mock-functions#mocking-modules)，從函數層級到模組層級的模擬都能處理。

## 整合測試

在編寫大型軟體系統時，其各個部分需要相互協作。在單元測試中，如果你的單元依賴於另一個單元，有時最終會模擬該依賴項，用假的替代它。

在整合測試中，真實的單個單元會像在應用程式中一樣組合在一起進行測試，以確保它們的協作符合預期。這並不是說這裡不需要模擬：你仍然需要模擬（例如模擬與天氣服務的通信），但比單元測試中需要的少得多。

> 請注意，關於整合測試的術語並不總是一致的。此外，單元測試和整合測試之間的界限可能並不總是清晰的。在本指南中，如果你的測試符合以下條件，則屬於「整合測試」：
>
> - 如上所述組合了應用程式的多個模組
> - 使用了外部系統
> - 向其他應用程式發起網路呼叫（例如天氣服務API）
> - 進行任何類型的檔案或資料庫<abbr title="輸入/輸出">I/O</abbr>操作

<img src="/docs/assets/p_tests-integration.svg" alt=" " />

## 元件測試

React元件負責渲染你的應用程式，用戶將直接與其輸出互動。即使你的應用業務邏輯測試覆蓋率高且正確，如果沒有元件測試，你可能仍會向用戶交付有問題的UI。元件測試可能同時屬於單元和整合測試，但由於它們是React Native的核心部分，我們將單獨討論。

對於測試React元件，你可能想測試兩件事：

- 互動：確保元件在用戶互動時行為正確（例如用戶按下按鈕時）
- 渲染：確保React使用的元件渲染輸出正確（例如按鈕的外觀和在UI中的位置）

例如，如果你有一個帶有`onPress`監聽器的按鈕，你想測試按鈕是否正確顯示以及點擊按鈕是否被元件正確處理。

有幾個庫可以幫助你測試這些：

- React的[測試渲染器](https://reactjs.org/docs/test-renderer.html)與其核心一起開發，提供了一個React渲染器，可用於將React元件渲染為純JavaScript對象，而不依賴於DOM或原生移動環境。
- [React Native測試庫](https://callstack.github.io/react-native-testing-library/)建立在React的測試渲染器之上，並添加了下一段中描述的`fireEvent`和`query` API。

> 元件測試僅是在Node.js環境中運行的JavaScript測試。它們不考慮任何支持React Native元件的iOS、Android或其他平台代碼。因此，它們無法給你100%的信心保證一切對用戶都正常運作。如果iOS或Android代碼中存在錯誤，它們將無法發現。

<img src="/docs/assets/p_tests-component.svg" alt=" " />

### 測試用戶互動

除了渲染一些UI外，你的元件還處理事件，如`TextInput`的`onChangeText`或`Button`的`onPress`。它們可能還包含其他函數和事件回調。考慮以下示例：

```jsx
function GroceryShoppingList() {
  const [groceryItem, setGroceryItem] = useState('');
  const [items, setItems] = useState([]);

  const addNewItemToShoppingList = useCallback(() => {
    setItems([groceryItem, ...items]);
    setGroceryItem('');
  }, [groceryItem, items]);

  return (
    <>
      <TextInput
        value={groceryItem}
        placeholder="Enter grocery item"
        onChangeText={text => setGroceryItem(text)}
      />
      <Button
        title="Add the item to list"
        onPress={addNewItemToShoppingList}
      />
      {items.map(item => (
        <Text key={item}>{item}</Text>
      ))}
    </>
  );
}
```

在測試用戶互動時，從用戶角度測試元件——頁面上有什麼？互動時會發生什麼變化？

作為經驗法則，優先使用用戶可以看到或聽到的內容：

- 使用渲染的文字或[無障礙輔助功能助手](https://reactnative.dev/docs/accessibility#accessibility-properties)進行斷言

相反地，你應該避免：

- 對元件的 props 或 state 進行斷言
- 使用 testID 查詢

避免測試實作細節如 props 或 state——雖然這類測試可行，但它們並非以使用者與元件互動的方式為導向，且容易在重構時失效（例如當你想重新命名某些內容或將類別元件改寫為使用 hooks 時）。

> React 類別元件尤其容易測試其實作細節，如內部狀態、props 或事件處理器。為避免測試實作細節，建議優先使用搭配 Hooks 的函式元件，這會讓依賴元件內部細節變得更加困難。

如 [React Native Testing Library](https://callstack.github.io/react-native-testing-library/) 這類元件測試函式庫，透過謹慎選擇提供的 API，協助撰寫以使用者為中心的測試。以下範例使用 `fireEvent` 方法的 `changeText` 和 `press` 來模擬使用者與元件的互動，並使用查詢函式 `getAllByText` 在渲染輸出中尋找匹配的 `Text` 節點。

```jsx
test('given empty GroceryShoppingList, user can add an item to it', () => {
  const {getByPlaceholder, getByText, getAllByText} = render(
    <GroceryShoppingList />,
  );

  fireEvent.changeText(
    getByPlaceholder('Enter grocery item'),
    'banana',
  );
  fireEvent.press(getByText('Add the item to list'));

  const bananaElements = getAllByText('banana');
  expect(bananaElements).toHaveLength(1); // expect 'banana' to be on the list
});
```

這個範例並非測試當你呼叫某個函式時狀態如何改變，而是測試當使用者在 `TextInput` 中變更文字並按下 `Button` 時會發生什麼！

### 測試渲染輸出

[快照測試](https://jestjs.io/docs/en/snapshot-testing)是 Jest 提供的一種進階測試方式。它是一個非常強大且底層的工具，因此使用時需格外謹慎。

「元件快照」是由 Jest 內建的自訂 React 序列化器所產生的 JSX 風格字串。這個序列化器讓 Jest 能將 React 元件樹轉換為人類可讀的字串。換句話說：元件快照是在測試執行期間生成的元件渲染輸出的文字表示。它可能看起來像這樣：

```jsx
<Text
  style={
    Object {
      "fontSize": 20,
      "textAlign": "center",
    }
  }>
  Welcome to React Native!
</Text>
```

進行快照測試時，通常你會先實作你的元件，然後執行快照測試。快照測試接著會建立一個快照，並將其作為參考快照儲存到你的代碼庫中的一個檔案裡。**這個檔案會被提交並在代碼審查時檢查**。未來對元件渲染輸出的任何變更都會改變其快照，導致測試失敗。此時你需要更新儲存的參考快照才能使測試通過。這個變更同樣需要被提交和審查。

快照有幾個弱點：

- 對開發者或審查者而言，可能難以判斷快照中的變更是有意為之還是錯誤的證據。特別是大型快照可能很快變得難以理解，其附加價值也會降低。
- 當快照被建立時，它會被視為正確的——即使當時的渲染輸出實際上是錯誤的。
- 當快照失敗時，開發者容易不經仔細調查變更是否預期，就直接使用 `--updateSnapshot` jest 選項更新快照。因此需要一定的開發紀律。

快照本身並不能確保你的元件渲染邏輯正確，它們主要用於防範意外變更，以及檢查測試中的 React 樹下的元件是否接收到預期的 props（樣式等）。

我們建議你只使用小型快照（參見 [`no-large-snapshots` 規則](https://github.com/jest-community/eslint-plugin-jest/blob/master/docs/rules/no-large-snapshots.md)）。如果你想測試兩個 React 元件狀態之間的變更，可使用 [`snapshot-diff`](https://github.com/jest-community/snapshot-diff)。如有疑問，優先選擇前文所述的明確斷言方式。

<img src="/docs/assets/p_tests-snapshot.svg" alt=" " />

## 端對端測試

在端對端（E2E）測試中，你從使用者角度驗證你的應用程式在裝置（或模擬器/仿真器）上是否如預期運作。

這需要將你的應用程式建置為發行版本配置，並針對其執行測試。在端對端測試中，你不再考慮 React 元件、React Native API、Redux 儲存庫或任何業務邏輯。這些並非端對端測試的目的，且在端對端測試期間甚至無法存取這些內容。

相反地，端對端測試函式庫讓你能夠在應用程式畫面中尋找並控制元素：例如，你可以像真實使用者一樣實際點擊按鈕或在 `TextInput` 中輸入文字。接著，你可以對應用程式畫面中是否存在特定元素、是否可見、包含哪些文字等進行斷言。

端對端測試能為你提供應用程式部分功能正常運作的最大信心。其代價包括：

- 撰寫它們比其他類型的測試更耗時
- 執行速度較慢
- 更容易出現不穩定性（「不穩定」測試是指隨機通過或失敗，而程式碼沒有任何變更的測試）

嘗試用端對端測試覆蓋應用程式的關鍵部分：驗證流程、核心功能、支付等。對於非關鍵部分，使用更快的 JavaScript 測試。你添加的測試越多，信心就越高，但維護和執行它們的時間也會越多。請權衡利弊，決定什麼最適合你。

有幾種可用的端對端測試工具：在 React Native 社群中，[Detox](https://github.com/wix/detox/) 是一個流行的框架，因為它是為 React Native 應用程式量身定制的。在 iOS 和 Android 應用程式領域，另一個流行的函式庫是 [Appium](http://appium.io/)。

<img src="/docs/assets/p_tests-e2e.svg" alt=" " />

## 總結

我們希望你在閱讀本指南時有所收穫並學到一些東西。測試應用程式的方法有很多種。一開始可能很難決定使用什麼。然而，我們相信一旦你開始為你優秀的 React Native 應用程式添加測試，一切都會變得有意義。那麼還在等什麼呢？提高你的測試覆蓋率吧！

### 相關連結

- [React 測試概述](https://reactjs.org/docs/testing.html)
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)
- [Jest 文件](https://jestjs.io/docs/en/tutorial-react-native)
- [Detox](https://github.com/wix/detox/)
- [Appium](http://appium.io/)

---

_本指南最初由 [Vojtech Novak](https://twitter.com/vonovak) 撰寫並貢獻完整內容。_