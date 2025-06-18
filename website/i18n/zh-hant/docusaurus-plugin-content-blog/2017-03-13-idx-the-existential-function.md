---
title: 'idx: The Existential Function'
author: Timothy Yung
authorTitle: Engineering Manager at Facebook
authorURL: 'https://github.com/yungsters'
authorImageURL: 'https://pbs.twimg.com/profile_images/1592444107/image.jpg'
authorTwitter: yungsters
tags: [engineering]
---

在 Facebook，我們經常需要存取透過 GraphQL 取得的資料結構中的深層嵌套值。在存取這些深層嵌套值的過程中，經常會遇到一個或多個中間欄位為可空值的情況。這些中間欄位可能因多種原因而為空，從隱私檢查失敗到單純因為「空值」恰好是表示非致命錯誤最靈活的方式。

不幸的是，目前存取這些深層嵌套值的過程既繁瑣又冗長。

```jsx
props.user &&
  props.user.friends &&
  props.user.friends[0] &&
  props.user.friends[0].friends;
```

目前有一個 [ECMAScript 提案建議引入存在運算子](https://github.com/claudepache/es-optional-chaining)，這將使此操作更加便利。但在該提案最終確定之前，我們需要一個能改善開發體驗、維持現有語言語義，並能透過 Flow 強化型別安全的解決方案。

我們設計了一個稱為 `idx` 的存在性 _函式_。

```jsx
idx(props, _ => _.user.friends[0].friends);
```

此程式碼片段中的調用行為與上述程式碼片段中的布林表達式類似，但重複性大幅降低。`idx` 函式嚴格接受兩個參數：

- 任何值，通常是您想存取嵌套值的物件或陣列。
- 一個接收第一個參數並對其存取嵌套值的函式。

理論上，`idx` 函式會嘗試捕捉因存取 null 或 undefined 屬性而產生的錯誤。若捕捉到此類錯誤，將返回 null 或 undefined。（您可以在 [idx.js](https://github.com/facebookincubator/idx/blob/master/packages/idx/src/idx.js) 中查看實作方式。）

實際上，對每個嵌套屬性存取進行 try-catch 的效率很低，且區分特定類型的 TypeError 並不穩定。為解決這些缺點，我們創建了一個 Babel 插件，將上述 `idx` 調用轉換為以下表達式：

```jsx
props.user == null
  ? props.user
  : props.user.friends == null
    ? props.user.friends
    : props.user.friends[0] == null
      ? props.user.friends[0]
      : props.user.friends[0].friends;
```

最後，我們為 `idx` 添加了自訂的 Flow 型別宣告，允許在第二個參數中的遍歷操作能正確進行型別檢查，同時允許對可空屬性進行嵌套存取。

該函式、Babel 插件和 Flow 宣告現已 [在 GitHub 上提供](https://github.com/facebookincubator/idx)。使用方式為安裝 **idx** 和 **babel-plugin-idx** npm 套件，並將 "idx" 加入您的 `.babelrc` 檔案中的插件列表。