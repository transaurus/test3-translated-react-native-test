---
id: testing-overview
title: Testing
author: Vojtech Novak
authorURL: 'https://twitter.com/vonovak'
description: This guide introduces React Native developers to the key concepts behind testing, how to write good tests, and what kinds of tests you can incorporate into your workflow.
---

隨著程式碼庫的擴展，未預期的小錯誤和邊緣案例可能引發更大的故障。這些錯誤會導致糟糕的使用者體驗，最終造成業務損失。防止脆弱程式設計的方法之一，就是在將程式碼發布前進行測試。

本指南將涵蓋多種自動化方法，從靜態分析到端對端測試，確保您的應用程式如預期運作。

<img src="/docs/assets/diagram_testing.svg" alt="Testing is a cycle of fixing, testing, and either passing to release or failing back into testing." />

## 為何需要測試

人非聖賢，孰能無過。測試的重要性在於它能幫助您發現這些錯誤並驗證程式碼是否正常運作。更重要的是，測試能確保在未來新增功能、重構現有程式碼或升級專案主要依賴項時，程式碼仍能持續運作。

測試的價值超乎您的想像。修正程式錯誤的最佳方法之一，就是撰寫一個能暴露該錯誤的失敗測試。當您修正錯誤並重新執行測試時，若測試通過，代表錯誤已修復且不會再次引入程式碼庫。

測試也能作為新團隊成員的技術文件。對於初次接觸程式碼庫的人來說，閱讀測試能幫助他們理解現有程式碼的運作方式。

最後但同樣重要的是，更多的自動化測試意味著減少手動<abbr title="Quality Assurance">QA</abbr>的時間，釋放寶貴資源。

## 靜態分析

提升程式碼品質的第一步是使用靜態分析工具。靜態分析會在您撰寫程式碼時檢查錯誤，但無需實際執行這些程式碼。

- **Linters** 會分析程式碼以捕捉常見錯誤（如未使用的程式碼），幫助避免陷阱，並標記違反風格指南的行為（例如使用製表符而非空格，或反之，取決於您的配置）。
- **類型檢查** 確保傳遞給函式的結構符合函式設計預期，例如防止將字串傳遞給預期接收數字的計數函式。

React Native 預設配置了兩種工具：[ESLint](https://eslint.org/) 用於程式碼檢查，[TypeScript](typescript) 用於類型檢查。

## 撰寫可測試的程式碼

要開始測試，首先需要撰寫可測試的程式碼。以飛機製造過程為例——在模型首次起飛展示所有複雜系統協同運作前，會先測試各個零件以確保其安全性和功能。例如，機翼會在極端負載下進行彎曲測試；引擎零件會測試耐用性；擋風玻璃會模擬鳥擊測試。

軟體開發亦是如此。與其將整個程式寫在一個龐大的檔案中，不如將程式碼拆分為多個小型模組，這樣能比測試組裝後的整體進行更徹底的測試。因此，撰寫可測試的程式碼與撰寫乾淨、模組化的程式碼息息相關。

要讓應用程式更具可測試性，首先應將應用的視圖部分（React 元件）與業務邏輯和應用狀態分離（無論您使用 Redux、MobX 或其他解決方案）。這樣一來，您可以將業務邏輯測試（不應依賴 React 元件）與元件本身的測試分開，後者的主要職責是渲染應用程式的 UI！

理論上，您可以將所有邏輯和資料獲取完全移出元件。如此一來，元件將專注於渲染，狀態將完全獨立於元件，應用程式的邏輯甚至可以在沒有任何 React 元件的情況下運作！

> 我們鼓勵您透過其他學習資源進一步探索可測試程式碼的主題。

## 撰寫測試

撰寫完可測試的程式碼後，是時候撰寫實際的測試了！React Native 的預設模板附帶 [Jest](https://jestjs.io) 測試框架。它包含一個針對此環境量身打造的預設配置，讓您無需立即調整配置和模擬（mock）就能開始工作——稍後將[詳細介紹模擬](#mocking)。您可以使用 Jest 來撰寫本指南中提到的所有類型的測試。

> 如果你採用測試驅動開發（TDD），實際上你會先寫測試！這樣一來，程式碼的可測試性自然就具備了。

### 測試結構設計

測試案例應該簡潔且理想情況下只驗證單一功能。以下是一個用 Jest 撰寫的單元測試範例：

```js
it('given a date in the past, colorForDueDate() returns red', () => {
  expect(colorForDueDate('2000-10-20')).toBe('red');
});
```

測試描述透過傳遞給 [`it`](https://jestjs.io/docs/en/api#testname-fn-timeout) 函式的字串定義。請仔細撰寫描述以明確說明測試內容，並盡量涵蓋以下要素：

1. **Given** - 前置條件
2. **When** - 被測函式執行的操作  
3. **Then** - 預期結果

這也被稱為 AAA 模式（Arrange, Act, Assert）。

Jest 提供 [`describe`](https://jestjs.io/docs/en/api#describename-fn) 函式來組織測試。用 `describe` 將相同功能的測試案例分組，必要時可嵌套使用。其他常用函式如 [`beforeEach`](https://jestjs.io/docs/en/api#beforeeachfn-timeout) 或 [`beforeAll`](https://jestjs.io/docs/en/api#beforeallfn-timeout) 可用於設置測試物件，詳見 [Jest API 文件](https://jestjs.io/docs/en/api)。

若測試步驟繁多或有多個驗證點，建議拆分成多個小測試。同時確保測試案例完全獨立——每個測試都應能單獨執行，且測試間不會相互影響結果。

最後要記得：對開發者而言，程式正常運作是好事，但測試失敗反而是「好現象」！測試失敗通常意味著發現潛在問題，讓你有機會在影響用戶前修復它。

## 單元測試

單元測試針對程式最小可測單元，例如獨立函式或類別。

當被測物件有依賴項時，常需使用「模擬」（Mock）技術，詳見下節說明。

單元測試的優勢在於快速撰寫與執行，能即時反饋測試結果。Jest 還提供 [Watch 模式](https://jestjs.io/docs/en/cli#watch)，可持續運行與修改程式碼相關的測試。

<img src="/docs/assets/p_tests-unit.svg" alt=" " />

### 模擬技術

當被測程式有外部依賴時，你可能需要「模擬」這些依賴——即用自訂實現替換原始依賴項。

> 原則上，測試中使用真實物件優於模擬物件，但某些情境無法避免。例如：當 JS 單元測試依賴 Java/Objective-C 寫的原生模組時。

假設你開發的天氣 App 需呼叫外部服務獲取數據。測試時不應實際呼叫該服務，因為：

- 網路請求會導致測試變慢且不穩定  
- 服務每次可能返回不同數據  
- 第三方服務可能在測試時離線！

此時可建立該服務的模擬實現，用幾行程式碼取代實際的網路連線與感測器！

> Jest 內建從函式級到模組級的[模擬支援](https://jestjs.io/docs/en/mock-functions#mocking-modules)。

## 整合測試

在開發大型軟體系統時，各個組件需要相互協作。在單元測試中，若被測單元依賴其他組件，往往需要透過模擬(mock)來替換這些依賴項。

整合測試會將實際的獨立單元組合（如同在應用程式中那樣）進行聯合測試，以確保它們的協作符合預期。這並非意味著完全不需要模擬——例如仍需模擬天氣服務的通訊——但使用頻率會遠低於單元測試。

> 請注意「整合測試」的定義在業界並不完全統一。單元測試與整合測試的界線有時也較模糊。本指南中，符合以下任一條件即歸類為整合測試：
>
> - 組合多個應用模組進行測試
> - 使用外部系統
> - 發起網路請求（如呼叫天氣服務API）
> - 涉及檔案或資料庫<abbr title="輸入/輸出">I/O</abbr>操作

<img src="/docs/assets/p_tests-integration.svg" alt=" " />

## 元件測試

React元件直接負責渲染應用界面並與用戶互動。即使業務邏輯測試覆蓋率高且正確，若缺乏元件測試仍可能交付有缺陷的UI。元件測試可歸類為單元或整合測試，但由於其在React Native中的核心地位，我們單獨討論。

React元件測試主要關注兩個方面：

- 互動行為：確保元件響應用戶操作時行為正確（例如按鈕點擊）
- 渲染輸出：確保React使用的渲染結果正確（例如按鈕外觀與UI位置）

例如，對於帶有`onPress`監聽的按鈕，需同時測試其視覺呈現與點擊響應邏輯。

常用測試工具包括：

- React官方[Test Renderer](https://reactjs.org/docs/test-renderer.html)：可在不依賴DOM或原生環境的情況下，將元件渲染為純JavaScript對象
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)：基於Test Renderer擴展，提供下文將介紹的`fireEvent`和`query`API

> 元件測試僅在Node.js環境中執行JavaScript測試，不涉及iOS/Android等平台的底層原生代碼。因此無法100%保證用戶端一切正常，若原生代碼存在缺陷，此類測試無法發現。

<img src="/docs/assets/p_tests-component.svg" alt=" " />

### 測試用戶互動

除了渲染UI，元件還需處理如`TextInput`的`onChangeText`或`Button`的`onPress`等事件。參考以下範例：

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

測試用戶互動時，應從用戶視角出發：頁面顯示什麼？互動後如何變化？

基本原則：優先基於用戶可感知的元素進行驗證：

- 使用渲染文本或[無障礙輔助屬性](https://reactnative.dev/docs/accessibility#accessibility-properties)進行斷言

反之應避免：

- 對元件props或state進行斷言
- 使用testID查詢

避免測試實現細節（如props/state），這類測試雖可行，但未聚焦用戶實際互動方式，且容易因重構（例如改用Hooks改寫class元件）而失效。

> React 類別元件特別容易測試其實作細節，例如內部狀態、props 或事件處理器。為避免測試實作細節，建議優先使用搭配 Hooks 的函式元件，這會讓依賴元件內部邏輯變得更加困難。

諸如 [React Native Testing Library](https://callstack.github.io/react-native-testing-library/) 這類元件測試函式庫，透過精心設計的 API 選擇，有助於撰寫以使用者為中心的測試。以下範例使用 `fireEvent` 方法的 `changeText` 和 `press` 來模擬使用者與元件的互動，並透過查詢函式 `getAllByText` 在渲染輸出中尋找匹配的 `Text` 節點。

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

[快照測試](https://jestjs.io/docs/en/snapshot-testing)是 Jest 提供的一種進階測試方式。由於這是極其強大且底層的工具，使用時需格外謹慎。

「元件快照」是由 Jest 內建的自訂 React 序列化器產生的類 JSX 字串。此序列化器讓 Jest 能將 React 元件樹轉換為人類可讀的字串。換言之：元件快照是測試執行期間生成的元件渲染輸出的文字表徵，其內容可能如下：

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

進行快照測試時，通常會先實作元件再執行快照測試。測試會建立快照並將其作為參考快照存入程式庫中的檔案。**該檔案需提交並在程式碼審查時檢查**。後續任何元件渲染輸出的變更都會導致快照變化，從而使測試失敗。此時需更新儲存的參考快照才能使測試通過，且該變更同樣需提交並審查。

快照存在若干弱點：

- 對開發者或審查者而言，難以判斷快照變更屬預期修改或錯誤證據。龐大的快照尤其容易變得難以理解，其附加價值隨之降低。
- 快照建立當下即被視為正確——即便實際渲染輸出存在錯誤。
- 當快照失敗時，開發者容易直接使用 Jest 的 `--updateSnapshot` 選項更新，而未仔細檢查變更是否合理。因此需要一定的開發紀律。

快照本身無法保證元件渲染邏輯的正確性，其主要作用在於防範意外變更，並驗證測試中 React 樹下的元件是否接收預期的 props（樣式等）。

建議僅使用小型快照（參見 [`no-large-snapshots` 規則](https://github.com/jest-community/eslint-plugin-jest/blob/master/docs/rules/no-large-snapshots.md)）。若需測試兩個 React 元件狀態間的差異，請使用 [`snapshot-diff`](https://github.com/jest-community/snapshot-diff)。如有疑慮，優先採用前文所述的明確斷言方式。

<img src="/docs/assets/p_tests-snapshot.svg" alt=" " />

## 端到端測試

在端到端（E2E）測試中，您需驗證應用程式在裝置（或模擬器）上是否如預期般運作，測試角度完全模擬使用者視角。

此測試需以發布配置建置應用程式，並對其執行測試。E2E 測試不再考量 React 元件、React Native API、Redux 儲存或任何業務邏輯——這些並非 E2E 測試目的，且在測試期間也無法觸及。

相反地，E2E 測試函式庫讓您能尋找並控制應用畫面中的元素：例如，您可以像真實使用者一樣實際點擊按鈕或在 `TextInput` 中輸入文字。接著可針對畫面中是否存在特定元素、是否可見、包含何種文字等進行斷言。

E2E 測試能為應用程式功能提供最高程度的信心保證，但需權衡以下代價：

- 撰寫這類測試比其他類型的測試更耗時
- 執行速度較慢
- 更容易出現不穩定性（「不穩定」測試是指隨機通過或失敗，而程式碼並未變更的測試）

請盡量為應用程式的關鍵部分進行端對端測試：驗證流程、核心功能、支付等。對於非關鍵部分，則使用執行速度更快的 JavaScript 測試。測試覆蓋範圍越廣，信心度越高，但維護和執行測試所需的時間也會隨之增加。請權衡利弊，選擇最適合的方案。

目前有數種端對端測試工具可供選擇：在 React Native 社群中，[Detox](https://github.com/wix/detox/) 是熱門框架，因為它專為 React Native 應用程式量身打造。在 iOS 和 Android 應用程式領域，另一個熱門的函式庫是 [Appium](https://appium.io/) 或 [Maestro](https://maestro.mobile.dev/)。

<img src="/docs/assets/p_tests-e2e.svg" alt=" " />

## 總結

希望您喜歡閱讀本指南並有所收穫。測試應用程式的方法有很多種，一開始可能難以抉擇該使用哪種方式。不過，我們相信一旦您開始為出色的 React Native 應用程式添加測試，一切就會變得清晰明瞭。還在等什麼？快來提升測試覆蓋率吧！

### 相關連結

- [React 測試概述](https://reactjs.org/docs/testing.html)
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)
- [Jest 文件](https://jestjs.io/docs/en/tutorial-react-native)
- [Detox](https://github.com/wix/detox/)
- [Appium](https://appium.io/)
- [Maestro](https://maestro.mobile.dev/)

---

_本指南由 [Vojtech Novak](https://twitter.com/vonovak) 原創並完整貢獻。_