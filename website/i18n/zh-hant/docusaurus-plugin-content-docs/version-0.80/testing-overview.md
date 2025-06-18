---
id: testing-overview
title: Testing
author: Vojtech Novak
authorURL: 'https://twitter.com/vonovak'
description: This guide introduces React Native developers to the key concepts behind testing, how to write good tests, and what kinds of tests you can incorporate into your workflow.
---

隨著程式碼庫擴展，未預期的小錯誤和邊緣案例可能引發連鎖反應導致重大故障。程式錯誤會造成糟糕的使用者體驗，最終導致業務損失。預防脆弱程式設計的方法之一，就是在釋出前對程式碼進行測試。

本指南將涵蓋多種自動化測試方法，從靜態分析到端對端測試，確保您的應用程式如預期運作。

<img src="/docs/assets/diagram_testing.svg" alt="Testing is a cycle of fixing, testing, and either passing to release or failing back into testing." />

## 為何需要測試

人非聖賢，孰能無過。測試的重要性在於幫助發現這些錯誤並驗證程式碼是否正常運作。更重要的是，測試能確保未來新增功能、重構現有程式碼或升級專案主要依賴項時，程式碼仍能持續運作。

測試的價值超乎想像。修復程式錯誤的最佳方法之一，就是撰寫能暴露該錯誤的失敗測試。當您修復錯誤後重新執行測試，若通過則表示錯誤已修復且不會再次引入程式碼庫。

測試還能作為新團隊成員的技術文件。對於初次接觸程式碼庫的人，閱讀測試能幫助理解現有程式碼的運作方式。

最後但同樣重要的是，自動化測試越多，就能節省更多寶貴的手動<abbr title="Quality Assurance">QA</abbr>時間。

## 靜態分析

提升程式碼品質的第一步是使用靜態分析工具。靜態分析會在撰寫程式碼時檢查錯誤，但無需實際執行該程式碼。

- **Linter**會分析程式碼，捕捉未使用程式碼等常見錯誤，幫助避免陷阱，並標記違反風格指南的行為（例如使用製表符而非空格，或反之，取決於您的配置）。
- **型別檢查**確保傳遞給函式的結構符合設計預期，例如防止將字串傳遞給預期接收數字的計數函式。

React Native 預設配置了兩種工具：[ESLint](https://eslint.org/) 用於程式碼檢查，[TypeScript](typescript) 用於型別檢查。

## 撰寫可測試的程式碼

要開始測試，首先需要撰寫可測試的程式碼。以飛機製造過程為例——在首飛展示所有複雜系統協同運作前，會先測試各部件以確保安全性和功能性。例如機翼需承受極端負載彎曲測試，引擎零件需通過耐久性測試，擋風玻璃需模擬鳥擊測試。

軟體開發同理。與其將整個程式寫在一個龐大檔案中，不如將程式碼拆分為多個小型模組，這樣能比測試組裝後的整體進行更徹底的測試。因此，撰寫可測試程式碼與撰寫乾淨、模組化的程式碼息息相關。

要提高應用程式的可測試性，首先需將應用的視圖部分（React 元件）與業務邏輯和應用狀態分離（無論您使用 Redux、MobX 或其他解決方案）。如此一來，業務邏輯測試（不應依賴 React 元件）就能獨立於主要負責渲染應用程式 UI 的元件！

理論上，您甚至可以將所有邏輯和資料獲取移出元件。這樣元件將專注於渲染，狀態完全獨立於元件，應用程式邏輯甚至能在沒有任何 React 元件的情況下運作！

> 我們鼓勵您透過其他學習資源進一步探索可測試程式碼的主題。

## 撰寫測試

完成可測試程式碼後，接下來就是撰寫實際測試！React Native 的預設模板內建 [Jest](https://jestjs.io) 測試框架，包含針對此環境量身打造的預設配置，讓您無需立即調整設定和模擬（稍後將詳細說明[模擬技巧](#mocking)）。您可以使用 Jest 撰寫本指南提到的所有測試類型。

> 如果你採用測試驅動開發（TDD），實際上你會先寫測試！這樣一來，程式碼的可測試性自然就具備了。

### 測試結構設計

你的測試應該簡短且理想上只測試單一功能。讓我們從一個用 Jest 撰寫的單元測試範例開始：

```js
it('given a date in the past, colorForDueDate() returns red', () => {
  expect(colorForDueDate('2000-10-20')).toBe('red');
});
```

測試的描述由傳遞給 [`it`](https://jestjs.io/docs/en/api#testname-fn-timeout) 函式的字串定義。請仔細撰寫描述以清楚說明測試內容。盡量涵蓋以下要素：

1. **Given** - 某些前置條件
2. **When** - 被測試函式執行的某個動作
3. **Then** - 預期的結果

這也被稱為 AAA 模式（Arrange, Act, Assert）。

Jest 提供 [`describe`](https://jestjs.io/docs/en/api#describename-fn) 函式來協助組織測試結構。使用 `describe` 將屬於同一功能的所有測試分組。如有需要，可以嵌套使用 `describe`。其他常用的函式包括 [`beforeEach`](https://jestjs.io/docs/en/api#beforeeachfn-timeout) 或 [`beforeAll`](https://jestjs.io/docs/en/api#beforeallfn-timeout)，可用於設置測試對象。詳情請參閱 [Jest API 參考文檔](https://jestjs.io/docs/en/api)。

如果你的測試步驟繁多或有多個預期結果，建議將其拆分為多個較小的測試。同時，確保測試之間完全獨立。測試套件中的每個測試都必須能單獨執行，而不需先執行其他測試。反之，若同時執行所有測試，第一個測試的結果不應影響第二個測試的輸出。

最後，作為開發者，我們樂見程式碼運行順暢且不崩潰。但在測試中，情況往往相反。請將測試失敗視為一件「好事」！當測試失敗時，通常意味著某些地方出了問題。這讓你有機會在問題影響用戶之前將其修復。

## 單元測試

單元測試涵蓋程式碼的最小部分，例如個別函式或類別。

當被測試對象具有依賴項時，通常需要將其模擬（mock）出來，如下一段所述。

單元測試的優點在於它們撰寫和運行速度快。因此，在開發過程中，你能快速獲得測試是否通過的反饋。Jest 甚至提供持續運行與編輯程式碼相關測試的選項：[監視模式](https://jestjs.io/docs/en/cli#watch)。

<img src="/docs/assets/p_tests-unit.svg" alt=" " />

### 模擬（Mocking）

當被測試對象具有外部依賴項時，你可能需要「模擬」這些依賴。「模擬」是指用你自己的實現替換程式碼的某些依賴項。

> 一般來說，在測試中使用真實對象比使用模擬更好，但在某些情況下這不可行。例如：當你的 JS 單元測試依賴於用 Java 或 Objective-C 編寫的原生模組時。

假設你正在開發一個顯示當前城市天氣的應用程式，並使用某個外部服務或其他依賴來獲取天氣資訊。如果服務告訴你正在下雨，你希望顯示一個雨雲圖像。你不希望在測試中調用該服務，因為：

- 這可能使測試變慢且不穩定（由於涉及網路請求）
- 服務每次運行測試時可能返回不同的數據
- 第三方服務可能在你急需運行測試時離線！

因此，你可以提供該服務的模擬實現，有效取代數千行程式碼和某些連網的溫度計！

> Jest 內建[模擬支援](https://jestjs.io/docs/en/mock-functions#mocking-modules)，從函式層級到模組層級的模擬都能實現。

## 整合測試

在開發大型軟體系統時，各個組件需要相互協作。在單元測試中，若測試單元依賴其他組件，通常會透過模擬(mock)方式替換依賴項，使用虛擬對象進行測試。

整合測試則是將實際單元組件（如同在應用程式中）組合起來共同測試，以驗證其協作是否符合預期。這並非意味完全不需要模擬——例如仍需模擬天氣服務等外部通訊，但使用頻率遠低於單元測試。

> 請注意「整合測試」的定義在業界並不完全一致，且單元測試與整合測試的界線有時模糊。本指南中，符合以下任一條件即歸類為整合測試：
>
> - 組合多個應用模組進行測試
> - 使用外部系統
> - 進行網路呼叫（如天氣服務API）
> - 涉及檔案或資料庫<abbr title="輸入/輸出">I/O</abbr>操作

<img src="/docs/assets/p_tests-integration.svg" alt=" " />

## 組件測試

React組件直接負責渲染介面並與用戶互動。即使業務邏輯測試覆蓋率高且正確，若缺乏組件測試仍可能交付有缺陷的UI。組件測試可歸類為單元或整合測試，但因其在React Native中的核心地位，我們獨立章節說明。

React組件測試主要涵蓋兩個面向：

- **互動行為**：驗證用戶操作時組件反應是否正確（如按鈕點擊）
- **渲染輸出**：確保React使用的渲染結果正確（如按鈕外觀與UI位置）

例如，若按鈕設有`onPress`監聽器，需同時測試其視覺呈現與點擊事件處理邏輯。

常用測試工具庫包括：

- React官方[Test Renderer](https://reactjs.org/docs/test-renderer.html)，可將組件渲染為純JavaScript對象，無需DOM或原生環境
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)基於Test Renderer，擴增了後文將說明的`fireEvent`與`query`API

> 組件測試僅在Node.js環境執行JavaScript，不涉及iOS/Android等平台底層程式碼。因此無法100%保證用戶端完全正常，若原生代碼存在缺陷，此類測試無法偵測。

<img src="/docs/assets/p_tests-component.svg" alt=" " />

### 測試用戶互動

除了UI渲染，組件還需處理如`TextInput`的`onChangeText`或`Button`的`onPress`等事件。參考以下範例：

```tsx
function GroceryShoppingList() {
  const [groceryItem, setGroceryItem] = useState('');
  const [items, setItems] = useState<string[]>([]);

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

測試用戶互動時，應從用戶視角出發——頁面顯示什麼？互動後如何變化？

基本原則：優先基於用戶可感知元素進行驗證：

- 使用渲染文字或[無障礙輔助功能](https://reactnative.dev/docs/accessibility#accessibility-properties)進行斷言

反之應避免：

- 對組件props或state進行斷言
- 使用testID查詢元素

避免測試實作細節（如props或state），這類測試雖可行但偏離用戶真實互動模式，且重構時易失效（例如改用hooks改寫class組件時）。

> React 類別元件特別容易測試其實作細節，例如內部狀態、props 或事件處理器。為避免測試實作細節，建議優先使用搭配 Hooks 的函式元件，這會讓依賴元件內部邏輯變得更加困難。

諸如 [React Native Testing Library](https://callstack.github.io/react-native-testing-library/) 這類元件測試函式庫，透過精心設計的 API 選擇，能協助撰寫以使用者為中心的測試。以下範例使用 `fireEvent` 方法的 `changeText` 和 `press` 來模擬使用者與元件的互動，並透過查詢函式 `getAllByText` 在渲染輸出中尋找匹配的 `Text` 節點。

```tsx
test('given empty GroceryShoppingList, user can add an item to it', () => {
  const {getByPlaceholderText, getByText, getAllByText} = render(
    <GroceryShoppingList />,
  );

  fireEvent.changeText(
    getByPlaceholderText('Enter grocery item'),
    'banana',
  );
  fireEvent.press(getByText('Add the item to list'));

  const bananaElements = getAllByText('banana');
  expect(bananaElements).toHaveLength(1); // expect 'banana' to be on the list
});
```

此範例並非測試呼叫函式時狀態如何變化，而是測試當使用者在 `TextInput` 中變更文字並按下 `Button` 時會發生什麼！

### 測試渲染輸出

[快照測試](https://jestjs.io/docs/en/snapshot-testing)是 Jest 提供的一種進階測試方式。這是一個非常強大且底層的工具，因此使用時需格外謹慎。

「元件快照」是由 Jest 內建的自訂 React 序列化器產生的類 JSX 字串。該序列化器讓 Jest 能將 React 元件樹轉換為人類可讀的字串。換言之：元件快照是測試執行期間生成的元件渲染輸出的文字表徵，其內容可能如下：

```tsx
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

進行快照測試時，通常會先實作元件再執行快照測試。測試會建立快照並將其作為參考快照存入程式庫中的檔案。**該檔案需提交並在程式碼審查時檢查**。後續任何元件渲染輸出的變更都會導致快照變化，從而使測試失敗。此時需更新儲存的參考快照才能使測試通過，而該變更同樣需提交並審查。

快照測試有以下弱點：

- 對開發者或審查者而言，難以判斷快照變更是否為預期行為或錯誤證據。大型快照尤其容易變得難以理解，其附加價值隨之降低。
- 快照建立當下即被視為正確——即使實際渲染輸出存在錯誤。
- 當快照失敗時，開發者容易直接使用 Jest 的 `--updateSnapshot` 選項更新快照，而未仔細檢查變更是否合理。因此需要一定的開發紀律。

快照本身無法確保元件渲染邏輯正確，其主要用途在於防範意外變更，以及驗證測試中 React 樹下的元件是否接收預期的 props（樣式等）。

建議僅使用小型快照（參見 [`no-large-snapshots` 規則](https://github.com/jest-community/eslint-plugin-jest/blob/master/docs/rules/no-large-snapshots.md)）。若需測試兩個 React 元件狀態間的差異，可使用 [`snapshot-diff`](https://github.com/jest-community/snapshot-diff)。如有疑慮，建議優先採用前文所述的明確斷言方式。

<img src="/docs/assets/p_tests-snapshot.svg" alt=" " />

## 端對端測試

在端對端（E2E）測試中，您需驗證應用程式在裝置（或模擬器）上是否如預期般運作，測試角度完全模擬使用者視角。

此測試需建置發布版本的應用程式並對其執行測試。E2E 測試不再考量 React 元件、React Native API、Redux store 或任何業務邏輯——這些並非 E2E 測試目的，且在測試過程中也不可存取。

相反地，E2E 測試函式庫讓您能尋找並控制應用畫面中的元素：例如，您可以像真實使用者一樣實際點擊按鈕或在 `TextInput` 中輸入文字。接著可對畫面元素進行斷言，例如特定元素是否存在、是否可見、包含何種文字等。

E2E 測試能提供最高程度的信心，確保應用程式的某部分正常運作。其代價包括：

- 撰寫這類測試比其他類型的測試更耗時
- 執行速度較慢
- 更容易出現不穩定情況（「不穩定」測試是指未經代碼修改卻隨機通過或失敗的測試）

請盡量為應用程式的關鍵部分進行端到端測試：身份驗證流程、核心功能、支付等。對於非關鍵部分，可使用執行速度更快的 JavaScript 測試。測試覆蓋率越高，信心程度就越高，但維護和執行測試所需的時間也會隨之增加。請權衡利弊，選擇最適合您的方式。

現有數種端到端測試工具可供選擇：在 React Native 社群中，[Detox](https://github.com/wix/detox/) 是專為 React Native 應用程式設計的熱門框架。在 iOS 和 Android 應用程式領域，[Appium](https://appium.io/) 或 [Maestro](https://maestro.mobile.dev/) 也是廣受歡迎的函式庫。

<img src="/docs/assets/p_tests-e2e.svg" alt=" " />

## 總結

希望您喜歡閱讀本指南並有所收穫。測試應用程式的方法有很多種，一開始可能難以決定使用哪種方法。不過，我們相信一旦您開始為出色的 React Native 應用程式添加測試，一切就會變得清晰。還在等什麼？趕緊提高測試覆蓋率吧！

### 相關連結

- [React 測試概述](https://reactjs.org/docs/testing.html)
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)
- [Jest 文件](https://jestjs.io/docs/en/tutorial-react-native)
- [Detox](https://github.com/wix/detox/)
- [Appium](https://appium.io/)
- [Maestro](https://maestro.mobile.dev/)

---

_本指南最初由 [Vojtech Novak](https://twitter.com/vonovak) 撰寫並貢獻完整內容。_