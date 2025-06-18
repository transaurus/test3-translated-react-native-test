---
id: testing-overview
title: Testing
author: Vojtech Novak
authorURL: 'https://twitter.com/vonovak'
description: This guide introduces React Native developers to the key concepts behind testing, how to write good tests, and what kinds of tests you can incorporate into your workflow.
---

隨著程式碼庫的擴展，未預期的小錯誤和邊緣案例可能引發更大的故障。這些錯誤會導致糟糕的使用者體驗，最終造成業務損失。預防脆弱程式設計的方法之一，就是在將程式碼發布前進行測試。

本指南將涵蓋多種自動化方法，從靜態分析到端到端測試，確保您的應用程式如預期運作。

<img src="/docs/assets/diagram_testing.svg" alt="Testing is a cycle of fixing, testing, and either passing to release or failing back into testing." />

## 為何需要測試

人類難免犯錯。測試的重要性在於幫助發現這些錯誤並驗證程式碼是否正常運作。更重要的是，測試能確保未來在新增功能、重構現有程式碼或升級專案主要依賴套件時，程式碼仍能持續運作。

測試的價值超乎想像。修正程式錯誤的最佳方法之一，就是撰寫一個能暴露該錯誤的失敗測試。當您修正錯誤後重新執行測試，若測試通過，代表錯誤已修復且不會再次引入程式碼庫。

測試也能作為新團隊成員的技術文件。對於初次接觸程式碼庫的人來說，閱讀測試能幫助他們理解現有程式碼的運作方式。

最後但同樣重要的是，更多自動化測試意味著減少手動<abbr title="Quality Assurance">QA</abbr>的時間，釋放寶貴資源。

## 靜態分析

提升程式碼品質的第一步是使用靜態分析工具。靜態分析會在您撰寫程式碼時檢查錯誤，但無需實際執行這些程式碼。

- **Linters** 會分析程式碼以捕捉常見錯誤（如未使用的程式碼），幫助避免陷阱，並標記違反風格指南的行為（例如使用製表符而非空格，或反之，取決於您的配置）。
- **型別檢查** 確保傳遞給函式的結構符合函式設計預期，例如防止將字串傳遞給預期接收數字的計數函式。

React Native 預設配置了兩種工具：[ESLint](https://eslint.org/) 用於程式碼檢查，[TypeScript](typescript) 用於型別檢查。

## 撰寫可測試的程式碼

要開始測試，首先需要撰寫可測試的程式碼。以飛機製造過程為例——在模型首次起飛展示所有複雜系統協同運作前，會先測試個別零件以確保其安全性和功能。例如：機翼需承受極端負載彎曲測試、引擎零件需通過耐久性測試、擋風玻璃需模擬鳥擊測試。

軟體開發亦同。與其將整個程式寫在一個龐大檔案中，不如將程式碼拆分為多個小模組進行更徹底的測試。因此，撰寫可測試程式碼與撰寫乾淨、模組化的程式碼息息相關。

要提升應用程式的可測試性，首先應將視圖部分（React 元件）與業務邏輯和應用狀態分離（無論您使用 Redux、MobX 或其他解決方案）。如此一來，業務邏輯測試（不應依賴 React 元件）可獨立於主要負責渲染 UI 的元件之外！

理論上，您甚至可以將所有邏輯和資料獲取移出元件，使元件專注於渲染。應用狀態將完全獨立於元件，業務邏輯能在完全不使用 React 元件的情況下運作！

> 我們鼓勵您透過其他學習資源進一步探索可測試程式碼的主題。

## 撰寫測試

完成可測試程式碼後，接下來就是撰寫實際測試！React Native 的預設模板內建 [Jest](https://jestjs.io) 測試框架，包含針對此環境量身打造的預設配置，讓您無需立即調整設定和模擬（mock）就能開始工作——稍後將[詳細說明模擬](#mocking)。您可以使用 Jest 撰寫本指南提到的所有測試類型。

> 如果你採用測試驅動開發（TDD），實際上你會先寫測試！這樣一來，程式碼的可測試性自然就具備了。

### 測試結構設計

測試案例應該簡短，且理想情況下只測試單一功能。以下是一個用 Jest 撰寫的單元測試範例：

```js
it('given a date in the past, colorForDueDate() returns red', () => {
  expect(colorForDueDate('2000-10-20')).toBe('red');
});
```

測試描述由傳遞給 [`it`](https://jestjs.io/docs/en/api#testname-fn-timeout) 函式的字串定義。請仔細撰寫描述以明確說明測試內容，建議涵蓋以下要素：

1. **Given** - 前置條件
2. **When** - 被測函式執行的動作
3. **Then** - 預期結果

此模式亦稱為 AAA 模式（Arrange, Act, Assert）。

Jest 提供 [`describe`](https://jestjs.io/docs/en/api#describename-fn) 函式來組織測試。用於將同一功能的測試案例分組，必要時可嵌套使用。其他常用函式如 [`beforeEach`](https://jestjs.io/docs/en/api#beforeeachfn-timeout) 或 [`beforeAll`](https://jestjs.io/docs/en/api#beforeallfn-timeout) 可用於設置測試對象，詳見 [Jest API 文件](https://jestjs.io/docs/en/api)。

若測試步驟過多或包含多個驗證點，建議拆分成多個小測試。同時確保測試案例完全獨立——每個測試都應能單獨執行，且測試間不互相影響（例如前一個測試不會改變後續測試的執行結果）。

最後要記得：對開發者而言程式碼正常運作是好事，但測試案例恰好相反。請將測試失敗視為「發現問題的契機」——這代表你能在問題影響用戶前及時修復。

## 單元測試

單元測試針對程式碼的最小可測單元，例如獨立函式或類別。

當被測對象存在依賴項時，通常需要進行模擬（Mock），詳見下節說明。

單元測試的優勢在於快速撰寫與執行，能即時反饋程式碼狀態。Jest 還提供 [Watch 模式](https://jestjs.io/docs/en/cli#watch)，可持續運行與修改程式碼相關的測試。

<img src="/docs/assets/p_tests-unit.svg" alt=" " />

### 模擬（Mocking）

當被測程式碼有外部依賴時，可能需要進行「模擬」——即用自訂實現替換原有依賴。

> 原則上，測試中使用真實對象優於模擬對象，但某些情境無法避免。例如：當 JS 單元測試依賴 Java/Objective-C 寫的原生模組時。

假設你正在開發天氣應用，依賴外部服務獲取天氣數據。若服務回報下雨，應用需顯示雨雲圖示。測試時不應直接呼叫真實服務，因為：

- 網路請求會導致測試變慢且不穩定
- 服務每次可能返回不同數據
- 第三方服務可能在測試時離線！

此時可建立服務的模擬實現，用幾行程式碼替代數千行真實程式與聯網溫度計！

> Jest 內建完整的[模擬支援](https://jestjs.io/docs/en/mock-functions#mocking-modules)，從函式級到模組級皆可模擬。

## 整合測試

在開發大型軟體系統時，各個組件需要相互協作。在單元測試中，若測試單元依賴其他組件，往往需要透過模擬(mock)來替換這些依賴項。

整合測試則是將實際的獨立單元組合（如同在應用程式中）並共同測試，以確保它們的協作符合預期。這並非意味著完全不需要模擬——例如仍需模擬天氣服務的通訊——但使用頻率會遠低於單元測試。

> 請注意「整合測試」的術語定義並非絕對一致，且單元測試與整合測試的界線有時也較模糊。本指南中，符合以下任一條件即歸類為整合測試：
>
> - 組合多個應用模組
> - 使用外部系統
> - 進行網路呼叫（如天氣服務API）
> - 執行任何檔案或資料庫<abbr title="輸入/輸出">I/O</abbr>操作

<img src="/docs/assets/p_tests-integration.svg" alt=" " />

## 元件測試

React元件直接負責渲染應用介面並與用戶互動。即使業務邏輯測試覆蓋率高且正確，若缺乏元件測試仍可能交付有缺陷的UI。元件測試可歸類為單元或整合測試，但由於其在React Native中的核心地位，我們獨立說明。

React元件測試主要關注兩個面向：

- 互動行為：確保元件響應用戶操作正確（如按鈕點擊）
- 渲染輸出：確保React使用的渲染結果正確（如按鈕外觀與UI位置）

例如測試具備`onPress`監聽的按鈕時，需同時驗證按鈕顯示正確性與點擊事件處理邏輯。

常用測試工具庫包括：

- React官方[Test Renderer](https://reactjs.org/docs/test-renderer.html)：可在無DOM或原生環境下，將元件渲染為純JavaScript物件
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)：基於Test Renderer，提供`fireEvent`與`query`API

> 元件測試僅在Node.js環境執行JavaScript測試，不涉及iOS/Android等平台底層程式碼。因此無法100%保證用戶端運作正常，若原生代碼存在缺陷，此類測試無法偵測。

<img src="/docs/assets/p_tests-component.svg" alt=" " />

### 測試用戶互動

除渲染UI外，元件還需處理如`TextInput`的`onChangeText`或`Button`的`onPress`等事件。測試時應從用戶視角出發：

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

驗證互動時，聚焦於用戶可見可聞的元素：
- 透過渲染文字或[無障礙輔助工具](https://reactnative.dev/docs/accessibility#accessibility-properties)進行斷言

應避免以下做法：
- 對元件props或state進行斷言
- 使用testID查詢

- make assertions using rendered text or [accessibility helpers](https://reactnative.dev/docs/accessibility#accessibility-properties)

Conversely, you should avoid:

- making assertions on component props or state
- testID queries

避免測試實作細節（如props/state），這類測試易因重構（例如改用Hooks改寫class元件）而失效，且偏離真實用戶互動情境。

> React 類別元件特別容易測試其實作細節，例如內部狀態、props 或事件處理器。為避免測試實作細節，建議優先使用搭配 Hooks 的函式元件，這會讓依賴元件內部邏輯變得更加困難。

諸如 [React Native Testing Library](https://callstack.github.io/react-native-testing-library/) 這類元件測試函式庫，透過精心設計的 API 來協助撰寫以使用者為中心的測試。以下範例使用 `fireEvent` 的 `changeText` 和 `press` 方法模擬使用者與元件的互動，並透過查詢函式 `getAllByText` 在渲染輸出中尋找匹配的 `Text` 節點。

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

[快照測試](https://jestjs.io/docs/en/snapshot-testing)是 Jest 提供的一種進階測試方式。由於這是極強大且底層的工具，使用時需格外謹慎。

「元件快照」是由 Jest 內建的自訂 React 序列化器產生的類 JSX 字串。此序列化器讓 Jest 能將 React 元件樹轉換為人類可讀的字串。換言之：元件快照是測試執行期間_生成_的元件渲染輸出的文字表示，可能如下所示：

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

進行快照測試時，通常會先實作元件再執行快照測試。測試會建立快照並將其作為參考快照存入程式庫中的檔案。**該檔案需提交並在程式碼審查時檢查**。未來任何元件渲染輸出的變更都會導致快照變化，使測試失敗。此時需更新儲存的參考快照才能使測試通過，且該變更同樣需提交並審查。

快照存在幾項弱點：

- 對開發者或審查者而言，難以判斷快照變更屬預期修改或錯誤證據。龐大的快照尤其容易變得難以理解，其附加價值隨之降低。
- 快照生成當下即被視為正確版本——即便實際渲染輸出存在錯誤。
- 當快照失敗時，開發者容易直接使用 Jest 的 `--updateSnapshot` 選項更新，而未妥善確認變更是否合理。因此需要相當的開發紀律。

快照本身無法保證元件渲染邏輯正確，其主要價值在於防範意外變更，以及驗證測試中 React 元件樹是否接收預期的 props（樣式等）。

建議僅使用小型快照（參見 [`no-large-snapshots` 規則](https://github.com/jest-community/eslint-plugin-jest/blob/master/docs/rules/no-large-snapshots.md)）。若需測試兩個 React 元件狀態間的_差異_，可使用 [`snapshot-diff`](https://github.com/jest-community/snapshot-diff)。若有疑慮，建議優先採用前文所述的明確斷言方式。

<img src="/docs/assets/p_tests-snapshot.svg" alt=" " />

## 端對端測試

在端對端（E2E）測試中，您需驗證應用程式在裝置（或模擬器）上是否如預期般運作，測試角度完全模擬使用者行為。

此測試需建置發布版本的應用程式並對其執行測試。E2E 測試不再考量 React 元件、React Native API、Redux store 或任何業務邏輯——這些並非 E2E 測試目的，且在測試期間也無法存取。

反之，E2E 測試函式庫讓您能尋找並控制應用畫面上的元素：例如，您可以_實際_點擊按鈕或在 `TextInput` 中輸入文字，完全模擬真實使用者操作。接著可針對畫面上元素是否存在、是否可見、包含何種文字等進行斷言。

E2E 測試能提供最高程度的信心，確認應用程式的某部分運作正常。其代價包括：

- 撰寫這類測試比其他類型的測試更耗時
- 執行速度較慢
- 更容易出現不穩定性（「不穩定」測試是指隨機通過或失敗，而程式碼並未變更的測試）

建議針對應用程式的關鍵部分進行端到端測試：驗證流程、核心功能、支付等。對於非關鍵部分，則使用更快速的 JavaScript 測試。測試案例越多，信心水準越高，但維護和執行測試的時間也會增加。請權衡利弊，選擇最適合您的方式。

目前有幾種端到端測試工具可供選擇：在 React Native 社群中，[Detox](https://github.com/wix/detox/) 是一個熱門框架，因為它專為 React Native 應用程式設計。在 iOS 和 Android 應用程式領域，另一個流行的庫是 [Appium](https://appium.io/) 或 [Maestro](https://maestro.mobile.dev/)。

<img src="/docs/assets/p_tests-e2e.svg" alt=" " />

## 總結

希望您喜歡閱讀本指南並有所收穫。測試應用程式的方法有很多種，一開始可能難以決定使用哪種方式。但我們相信，當您開始為出色的 React Native 應用程式添加測試時，一切將會變得清晰。還在等什麼？趕緊提升您的測試覆蓋率吧！

### 相關連結

- [React 測試概述](https://reactjs.org/docs/testing.html)
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)
- [Jest 文件](https://jestjs.io/docs/en/tutorial-react-native)
- [Detox](https://github.com/wix/detox/)
- [Appium](https://appium.io/)
- [Maestro](https://maestro.mobile.dev/)

---

_本指南最初由 [Vojtech Novak](https://twitter.com/vonovak) 撰寫並貢獻完整內容。_