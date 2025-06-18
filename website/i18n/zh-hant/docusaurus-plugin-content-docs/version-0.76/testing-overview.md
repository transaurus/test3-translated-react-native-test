---
id: testing-overview
title: Testing
author: Vojtech Novak
authorURL: 'https://twitter.com/vonovak'
description: This guide introduces React Native developers to the key concepts behind testing, how to write good tests, and what kinds of tests you can incorporate into your workflow.
---

As your codebase expands, small errors and edge cases you don’t expect can cascade into larger failures. Bugs lead to bad user experience and ultimately, business losses. One way to prevent fragile programming is to test your code before releasing it into the wild.

In this guide, we will cover different, automated ways to ensure your app works as expected, ranging from static analysis to end-to-end tests.

<img src="/docs/assets/diagram_testing.svg" alt="Testing is a cycle of fixing, testing, and either passing to release or failing back into testing." />

## Why Test

We're humans, and humans make mistakes. Testing is important because it helps you uncover these mistakes and verifies that your code is working. Perhaps even more importantly, testing ensures that your code continues to work in the future as you add new features, refactor the existing ones, or upgrade major dependencies of your project.

There is more value in testing than you might realize. One of the best ways to fix a bug in your code is to write a failing test that exposes it. Then when you fix the bug and re-run the test, if it passes it means the bug is fixed, never reintroduced into the code base.

Tests can also serve as documentation for new people joining your team. For people who have never seen a codebase before, reading tests can help them understand how the existing code works.

Last but not least, more automated testing means less time spent with manual <abbr title="Quality Assurance">QA</abbr>, freeing up valuable time.

## Static Analysis

The first step to improve your code quality is to start using static analysis tools. Static analysis checks your code for errors as you write it, but without running any of that code.

- **Linters** analyze code to catch common errors such as unused code and to help avoid pitfalls, to flag style guide no-nos like using tabs instead of spaces (or vice versa, depending on your configuration).
- **Type checking** ensures that the construct you’re passing to a function matches what the function was designed to accept, preventing passing a string to a counting function that expects a number, for instance.

React Native comes with two such tools configured out of the box: [ESLint](https://eslint.org/) for linting and [TypeScript](typescript) for type checking.

## Writing Testable Code

To start with tests, you first need to write code that is testable. Consider an aircraft manufacturing process - before any model first takes off to show that all of its complex systems work well together, individual parts are tested to guarantee they are safe and function correctly. For example, wings are tested by bending them under extreme load; engine parts are tested for their durability; the windshield is tested against simulated bird impact.

Software is similar. Instead of writing your entire program in one huge file with many lines of code, you write your code in multiple small modules that you can test more thoroughly than if you tested the assembled whole. In this way, writing testable code is intertwined with writing clean, modular code.

To make your app more testable, start by separating the view part of your app—your React components—from your business logic and app state (regardless of whether you use Redux, MobX or other solutions). This way, you can keep your business logic testing—which shouldn’t rely on your React components—independent of the components themselves, whose job is primarily rendering your app’s UI!

Theoretically, you could go so far as to move all logic and data fetching out of your components. This way your components would be solely dedicated to rendering. Your state would be entirely independent of your components. Your app’s logic would work without any React components at all!

> We encourage you to further explore the topic of testable code in other learning resources.

## Writing Tests

After writing testable code, it’s time to write some actual tests! The default template of React Native ships with [Jest](https://jestjs.io) testing framework. It includes a preset that's tailored to this environment so you can get productive without tweaking the configuration and mocks straight away—[more on mocks](#mocking) shortly. You can use Jest to write all types of tests featured in this guide.

> If you do test-driven development, you actually write tests first! That way, testability of your code is given.

### Structuring Tests

Your tests should be short and ideally test only one thing. Let's start with an example unit test written with Jest:

```js
it('given a date in the past, colorForDueDate() returns red', () => {
  expect(colorForDueDate('2000-10-20')).toBe('red');
});
```

The test is described by the string passed to the [`it`](https://jestjs.io/docs/en/api#testname-fn-timeout) function. Take good care writing the description so that it’s clear what is being tested. Do your best to cover the following:

1. **Given** - some precondition
2. **When** - some action executed by the function that you’re testing
3. **Then** - the expected outcome

This is also known as AAA (Arrange, Act, Assert).

Jest offers [`describe`](https://jestjs.io/docs/en/api#describename-fn) function to help structure your tests. Use `describe` to group together all tests that belong to one functionality. Describes can be nested, if you need that. Other functions you'll commonly use are [`beforeEach`](https://jestjs.io/docs/en/api#beforeeachfn-timeout) or [`beforeAll`](https://jestjs.io/docs/en/api#beforeallfn-timeout) that you can use for setting up the objects you're testing. Read more in the [Jest api reference](https://jestjs.io/docs/en/api).

If your test has many steps or many expectations, you probably want to split it into multiple smaller ones. Also, ensure that your tests are completely independent of one another. Each test in your suite must be executable on its own without first running some other test. Conversely, if you run all your tests together, the first test must not influence the output of the second one.

Lastly, as developers we like when our code works great and doesn't crash. With tests, this is often the opposite. Think of a failed test as of a _good thing!_ When a test fails, it often means something is not right. This gives you an opportunity to fix the problem before it impacts the users.

## Unit Tests

Unit tests cover the smallest parts of code, like individual functions or classes.

When the object being tested has any dependencies, you’ll often need to mock them out, as described in the next paragraph.

The great thing about unit tests is that they are quick to write and run. Therefore, as you work, you get fast feedback about whether your tests are passing. Jest even has an option to continuously run tests that are related to code you’re editing: [Watch mode](https://jestjs.io/docs/en/cli#watch).

<img src="/docs/assets/p_tests-unit.svg" alt=" " />

### Mocking

Sometimes, when your tested objects have external dependencies, you’ll want to “mock them out.” “Mocking” is when you replace some dependency of your code with your own implementation.

> Generally, using real objects in your tests is better than using mocks but there are situations where this is not possible. For example: when your JS unit test relies on a native module written in Java or Objective-C.

Imagine you’re writing an app that shows the current weather in your city and you’re using some external service or other dependency that provides you with the weather information. If the service tells you that it’s raining, you want to show an image with a rainy cloud. You don’t want to call that service in your tests, because:

- It could make the tests slow and unstable (because of the network requests involved)
- The service may return different data every time you run the test
- Third party services can go offline when you really need to run tests!

Therefore, you can provide a mock implementation of the service, effectively replacing thousands of lines of code and some internet-connected thermometers!

> Jest comes with [support for mocking](https://jestjs.io/docs/en/mock-functions#mocking-modules) from function level all the way to module level mocking.

## Integration Tests

在開發大型軟體系統時，各個組件需要相互協作。在單元測試中，若被測試單元依賴其他組件，通常會使用模擬(mock)方式替換這些依賴項。

整合測試則是將實際的獨立單元組合（如同在應用程式中一樣）並共同測試，以確保它們的協作符合預期。這並非意味著完全不需要模擬——例如仍需模擬與天氣服務的通訊——但相較單元測試，所需模擬的場景會少很多。

> 請注意，關於整合測試的術語定義並非總是保持一致。單元測試與整合測試的界線有時也較為模糊。在本指南中，符合以下條件的測試即歸類為「整合測試」：
>
> - 組合了應用程式的多個模組
> - 使用外部系統
> - 進行網路呼叫（如天氣服務API）
> - 執行任何形式的檔案或資料庫<abbr title="輸入/輸出">I/O</abbr>操作

<img src="/docs/assets/p_tests-integration.svg" alt=" " />

## 元件測試

React元件負責渲染應用界面，使用者將直接與其輸出互動。即使業務邏輯測試覆蓋率高且正確，若缺乏元件測試，仍可能向使用者交付有缺陷的UI。元件測試可歸類為單元測試或整合測試，但由於其在React Native中的核心地位，我們將單獨討論。

測試React元件時，主要有兩個方向：

- 互動測試：確保元件在使用者操作時行為正確（例如點擊按鈕）
- 渲染測試：確保React使用的元件渲染輸出正確（例如按鈕外觀與UI位置）

舉例來說，若按鈕設有`onPress`監聽器，需同時測試按鈕顯示是否正確，以及點擊事件是否被元件正確處理。

以下套件可協助進行這類測試：

- React官方的[Test Renderer](https://reactjs.org/docs/test-renderer.html)，能將元件渲染為純JavaScript物件，無需依賴DOM或原生環境
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)基於React測試渲染器，增加了`fireEvent`和`query`API

> 元件測試僅是在Node.js環境中運行的JavaScript測試，不涉及iOS、Android等平台的底層程式碼。因此無法100%保證使用者體驗的完整性——若原生代碼存在錯誤，這些測試將無法發現。

<img src="/docs/assets/p_tests-component.svg" alt=" " />

### 測試使用者互動

除了渲染UI外，元件還需處理如`TextInput`的`onChangeText`或`Button`的`onPress`等事件。測試使用者互動時，應從使用者角度出發——頁面顯示什麼？互動後會如何變化？

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

測試使用者互動時，請始終從使用者視角出發：頁面上有哪些元素？互動後會產生什麼變化？

基本原則：優先使用使用者能感知的元素進行驗證：

- 透過渲染文字或[無障礙輔助功能](https://reactnative.dev/docs/accessibility#accessibility-properties)進行斷言

反之，應避免：

- 對元件props或state進行斷言
- 使用testID查詢元素

避免測試實作細節（如props或state）。這類測試雖然有效，但不符合使用者互動模式，且在重構時（例如改用Hooks改寫class元件）容易失效。

> React 類別元件特別容易測試其實作細節，例如內部狀態、props 或事件處理器。為避免測試實作細節，建議優先使用搭配 Hooks 的函式元件，這會讓依賴元件內部邏輯變得更加困難。

諸如 [React Native Testing Library](https://callstack.github.io/react-native-testing-library/) 這類元件測試函式庫，透過精心設計的 API 選擇，能協助撰寫以使用者為核心的測試。以下範例使用 `fireEvent` 方法的 `changeText` 和 `press` 來模擬使用者與元件的互動，並透過查詢函式 `getAllByText` 在渲染輸出中尋找匹配的 `Text` 節點。

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

「元件快照」是由 Jest 內建的自訂 React 序列化器產生的類 JSX 字串。該序列化器讓 Jest 能將 React 元件樹轉換為人類可讀的字串。換言之：元件快照是測試執行期間_生成_的元件渲染輸出的文字表徵，其內容可能如下：

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

進行快照測試時，通常會先實作元件再執行快照測試。測試會建立快照並將其作為參考快照存入代碼庫中的檔案。**該檔案需提交並在代碼審查時檢查**。後續任何元件渲染輸出的變更都會導致快照變化，從而使測試失敗。此時需更新儲存的參考快照才能使測試通過，而該變更同樣需提交並審查。

快照存在幾項弱點：

- 對開發者或審查者而言，難以判斷快照變更是預期行為還是錯誤證據。龐大的快照尤其容易變得難以理解，其附加價值隨之降低。
- 快照建立當下即被視為正確——即使實際渲染輸出存在錯誤。
- 當快照失敗時，開發者容易直接使用 `--updateSnapshot` 選項更新快照，而未仔細檢查變更是否合理。因此需要一定的開發紀律。

快照本身無法保證元件渲染邏輯的正確性，其主要作用在於防範意外變更，並驗證測試中 React 樹下的元件是否接收預期的 props（樣式等）。

建議僅使用小型快照（參見 [`no-large-snapshots` 規則](https://github.com/jest-community/eslint-plugin-jest/blob/master/docs/rules/no-large-snapshots.md)）。若需測試兩個 React 元件狀態間的_差異_，可使用 [`snapshot-diff`](https://github.com/jest-community/snapshot-diff)。如有疑慮，建議優先採用前文所述的明確斷言方式。

<img src="/docs/assets/p_tests-snapshot.svg" alt=" " />

## 端對端測試

在端對端（E2E）測試中，您需驗證應用程式在裝置（或模擬器/仿真器）上是否如預期般運作，測試角度完全模擬使用者情境。

此測試需以發布配置建置應用程式，並對其執行測試。E2E 測試不再考量 React 元件、React Native API、Redux 儲存或任何業務邏輯——這些並非 E2E 測試的目的，且在測試過程中甚至無法存取。

反之，E2E 測試函式庫讓您能尋找並控制應用畫面中的元素：例如，您可以_實際_點擊按鈕或在 `TextInput` 中輸入文字，完全模擬真實使用者操作。接著可對畫面元素進行斷言，例如判斷特定元素是否存在、是否可見、包含何種文字等。

E2E 測試能為應用功能提供最高級別的信心保證，但需權衡以下代價：

- 撰寫這類測試比其他類型的測試更耗時
- 執行速度較慢
- 更容易出現不穩定性（「不穩定」測試是指隨機通過或失敗，而程式碼並未變更的測試）

請盡量為應用程式的關鍵部分進行端對端測試：身份驗證流程、核心功能、支付等。對於非關鍵部分，則使用更快速的 JavaScript 測試。添加的測試越多，信心就越高，但維護和執行它們的時間也會增加。請權衡利弊，選擇最適合您的方式。

有幾種可用的端對端測試工具：在 React Native 社群中，[Detox](https://github.com/wix/detox/) 是一個受歡迎的框架，因為它是專為 React Native 應用程式設計的。在 iOS 和 Android 應用程式領域，另一個受歡迎的庫是 [Appium](https://appium.io/) 或 [Maestro](https://maestro.mobile.dev/)。

<img src="/docs/assets/p_tests-e2e.svg" alt=" " />

## 總結

希望您喜歡閱讀並從本指南中學到一些東西。測試應用程式的方法有很多種，一開始可能很難決定使用哪種方法。然而，我們相信一旦您開始為您出色的 React Native 應用程式添加測試，一切都會變得清晰。那麼還在等什麼呢？提高您的測試覆蓋率吧！

### 相關連結

- [React 測試概述](https://reactjs.org/docs/testing.html)
- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)
- [Jest 文檔](https://jestjs.io/docs/en/tutorial-react-native)
- [Detox](https://github.com/wix/detox/)
- [Appium](https://appium.io/)
- [Maestro](https://maestro.mobile.dev/)

---

_本指南最初由 [Vojtech Novak](https://twitter.com/vonovak) 撰寫並貢獻完整內容。_