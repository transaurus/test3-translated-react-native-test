# 附錄

## I. 術語

- **Spec** - 用於描述 Turbo Native 模組或 Fabric Native 元件的 API 的 TypeScript 或 Flow 程式碼。由 **Codegen** 用來生成樣板程式碼。

- **Native Modules** - 沒有使用者介面(UI)的原生函式庫。例如持久儲存、通知、網路事件等。這些可透過函式和物件讓您的 JavaScript 應用程式程式碼存取。
- **Native Component** - 可透過 React 元件讓您的應用程式 JavaScript 程式碼使用的原生平台視圖。

- **Legacy Native Components** - 運行在舊版 React Native 架構上的元件。
- **Legacy Native Modules** - 運行在舊版 React Native 架構上的模組。

## II. Codegen 類型

您可參考以下表格，了解支援的類型及其在各平台的對應關係：

| Flow                                                                       | TypeScript                                          | Flow Nullable Support                                   | TypeScript Nullable Support                          | Android (Java)                       | iOS (ObjC)                                                     |
| -------------------------------------------------------------------------- | --------------------------------------------------- | ------------------------------------------------------- | ---------------------------------------------------- | ------------------------------------ | -------------------------------------------------------------- |
| `string`                                                                   | `string`                                            | `?string`                                               | <code>string &#124; null</code>                      | `string`                             | `NSString`                                                     |
| `boolean`                                                                  | `boolean`                                           | `?boolean`                                              | <code>boolean &#124; null</code>                     | `Boolean`                            | `NSNumber`                                                     |
| Object Literal<br /><code>&#123;&#124; foo: string, ...&#124;&#125;</code> | <code>&#123; foo: string, ...&#125; as const</code> | <code>?&#123;&#124; foo: string, ...&#124;&#125;</code> | <code>?&#123; foo: string, ...&#125; as const</code> | \-                                   | \-                                                             |
| Object [[1](#notes)]                                                       | Object [[1](#notes)]                                | `?Object`                                               | <code>Object &#124; null</code>                      | `ReadableMap`                        | `@` (untyped dictionary)                                       |
| <code>Array&lt;T&gt;</code>                                                | <code>Array&lt;T&gt;</code>                         | <code>?Array&lt;T&gt;</code>                            | <code>Array&lt;T&gt; &#124; null</code>              | `ReadableArray`                      | `NSArray` (or `RCTConvertVecToArray` when used inside objects) |
| `Function`                                                                 | `Function`                                          | `?Function`                                             | <code>Function &#124; null</code>                    | \-                                   | \-                                                             |
| <code>Promise&lt;T&gt;</code>                                              | <code>Promise&lt;T&gt;</code>                       | <code>?Promise&lt;T&gt;</code>                          | <code>Promise&lt;T&gt; &#124; null</code>            | `com.facebook.react.bridge.Promise`  | `RCTPromiseResolve` and `RCTPromiseRejectBlock`                |
| Type Unions<br /><code>'SUCCESS'&#124;'FAIL'</code>                        | Type Unions<br /><code>'SUCCESS'&#124;'FAIL'</code> | Only as callbacks                                       |                                                      | \-                                   | \-                                                             |
| Callbacks<br />`() =>`                                                     | Callbacks<br />`() =>`                              | Yes                                                     |                                                      | `com.facebook.react.bridge.Callback` | `RCTResponseSenderBlock`                                       |
| `number`                                                                   | `number`                                            | No                                                      |                                                      | `double`                             | `NSNumber`                                                     |

### 注意事項：

<b>[1]</b> 我們強烈建議使用物件字面量(Object literals)而非物件(Objects)。

:::info
您可能也會發現參考 React Native 核心模組的 JavaScript 規格很有幫助。這些位於 React Native 儲存庫中的 `Libraries/` 目錄內。
:::