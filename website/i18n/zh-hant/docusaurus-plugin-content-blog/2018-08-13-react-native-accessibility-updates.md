---
title: Accessibility API Updates
author: Ziqi Chen
authorTitle: Student at UC Berkeley
authorURL: 'https://ziqichen.com/'
authorImageURL: 'https://avatars2.githubusercontent.com/u/13990087?s=400&u=5841da1b6064341d52ecab70a586b6701d9f6978&v=4'
tags: [engineering]
---

## 動機

隨著科技進步，行動應用程式在日常生活中日益重要，開發無障礙應用程式的必要性也隨之提升。

React Native 有限的無障礙 API 一直是開發者的痛點，因此我們對無障礙 API 進行了多項更新，讓開發者能更輕鬆地建立包容性行動應用程式。

## 現有 API 的問題

### 問題一：兩個功能相似卻完全不同的屬性 - accessibilityComponentType (Android) 與 accessibilityTraits (iOS)

`accessibilityComponentType` 和 `accessibilityTraits` 是用於告知 Android 的 TalkBack 和 iOS 的 VoiceOver 使用者正在互動的 UI 元素類型。這兩個屬性最大的問題在於：

1. **它們是兩個不同屬性，使用方式各異，卻有相同目的**。在舊版 API 中，這兩個獨立屬性（各平台一個）不僅不便，也讓許多開發者感到困惑。iOS 的 `accessibilityTraits` 允許 17 種不同值，而 Android 的 `accessibilityComponentType` 僅允許 4 種值。此外，這些值大多沒有重疊。甚至這兩個屬性的輸入類型也不同：`accessibilityTraits` 允許傳入特徵陣列或單一特徵，而 `accessibilityComponentType` 僅允許單一值。
2. **Android 功能極為有限**。舊屬性下，Talkback 僅能識別「button」、「radiobutton_checked」和「radiobutton_unchecked」三種 UI 元素。

### 問題二：缺乏無障礙提示功能

無障礙提示能幫助使用 TalkBack 或 VoiceOver 的使用者理解，當他們對無障礙元素執行操作時，會發生什麼無法僅透過無障礙標籤顯現的結果。這些提示可在設定面板中開啟或關閉。先前 React Native 的 API 完全不支援無障礙提示。

### 問題三：忽略反轉色彩設定

部分視力受損使用者會在手機上使用反轉色彩功能來增強螢幕對比度。Apple 為 iOS 提供的 API 允許開發者忽略特定視圖，如此當使用者開啟反轉色彩設定時，圖片和影片不會失真。React Native 目前不支援此 API。

## 新 API 的設計

### 解決方案一：合併 accessibilityComponentType (Android) 與 accessibilityTraits (iOS)

為了解決 `accessibilityComponentType` 和 `accessibilityTraits` 之間的混淆，我們決定將它們合併為單一屬性。這很合理，因為它們技術上具有相同的預期功能，且合併後開發者在建立無障礙功能時，無需再擔心平台特定細節。

**背景說明**

在 iOS 上，`UIAccessibilityTraits` 是可設定於任何 NSObject 的屬性。透過 JavaScript 屬性傳遞至原生端的 17 種特徵，都會映射到 Objective-C 的 `UIAccessibilityTraits` 元素。每個特徵由一個長整數表示，所有設定的特徵會進行 OR 運算組合。

而在 Android 上，`AccessibilityComponentType` 是 React Native 自創的概念，並不直接對應 Android 的任何屬性。無障礙功能由無障礙委派處理。每個視圖都有預設的無障礙委派。若要自訂任何無障礙操作，必須建立新的無障礙委派，覆寫你想自訂的特定方法，然後將處理中的視圖的無障礙委派設為與新委派關聯。當開發者設定 `AccessibilityComponentType` 時，原生代碼會根據傳入的元件建立新委派，並將視圖設為使用該無障礙委派。

**實施的變更**

針對新屬性，我們希望建立兩個屬性的超集。我們決定讓新屬性主要沿用現有屬性 `accessibilityTraits` 的模型，因為 `accessibilityTraits` 的值明顯更多。這些特徵在 Android 上的功能將透過修改無障礙委派來實現相容。

iOS的`accessibilityTraits`可以設置17種UIAccessibilityTraits值，但我們並未將所有值納入新屬性。這是因為某些特性的實際效果尚不明確，且許多值幾乎從未被使用過。

UIAccessibilityTraits的值通常有兩種用途：描述UI元素的角色或狀態。我們觀察到大多數情況下，開發者會使用一個代表角色的值，並結合「state selected」、「state disabled」或兩者。因此，我們決定創建兩個新的無障礙屬性：`accessibilityRole`和`accessibilityState`。

**`accessibilityRole`**

新屬性`accessibilityRole`用於告知Talkback或Voiceover某個UI元素的角色。該屬性可接受以下值之一：

- `none`
- `button`
- `link`
- `search`
- `image`
- `keyboardkey`
- `text`
- `adjustable`
- `header`
- `summary`
- `imagebutton`

此屬性僅允許傳入一個值，因為UI元素通常不會同時具有多種角色。例外情況是image和button，因此我們新增了一個結合兩者的角色`imagebutton`。

**`accessibilityStates`**

新屬性`accessibilityStates`用於告知Talkback或Voiceover某個UI元素的狀態。該屬性接受一個包含以下一個或多個值的陣列：

- `selected`
- `disabled`

### 解決方案二：新增無障礙提示

為此我們新增了屬性`accessibilityHint`。設置此屬性將允許Talkback或Voiceover向用戶朗讀提示。

**`accessibilityHint`**

此屬性接受一個字串形式的無障礙提示內容。

在iOS上，設置此屬性會同步設定視圖的原生AccessibilityHint屬性。若iPhone中啟用了無障礙提示，Voiceover將朗讀此提示。

在Android上，設置此屬性會將提示內容附加到無障礙標籤末尾。這種實現的優點是模擬了iOS的提示行為，但缺點是這些提示無法像iOS那樣在系統設置中關閉。

我們在Android上採用此設計的原因是，無障礙提示通常對應特定操作（如點擊），我們希望保持跨平台行為一致性。

### 問題三的解決方案

**`accessibilityIgnoresInvertColors`**

我們將Apple的AccessibilityIgnoresInvertColors API暴露給JavaScript。現在當您有不希望顏色反轉的視圖（例如圖片）時，可將此屬性設為true來避免反轉。

## 新用法

這些新屬性將在React Native 0.57版本中提供。

### 升級指南

若您當前使用`accessibilityComponentType`和`accessibilityTraits`，可按照以下步驟升級至新屬性。

#### 1. 使用jscodeshift

最簡單的用例可通過運行jscodeshift腳本來替換。

這個[腳本](https://gist.github.com/ziqichen6/246e5778617224d2b4aff198dab0305d)會替換以下實例：

```
accessibilityTraits=“trait”
accessibilityTraits={[“trait”]}
```

替換為

```
accessibilityRole= “trait”
```

此腳本同時會移除所有 `AccessibilityComponentType` 的實例（假設所有設定 `AccessibilityComponentType` 的地方都會同時設定 `AccessibilityTraits`）。

#### 2. 使用手動代碼轉換工具

對於那些使用沒有對應 `AccessibilityRole` 值的 `AccessibilityTraits` 的情況，以及傳入多個特性到 `AccessibilityTraits` 的情況，需要進行手動代碼轉換。

一般來說，

```tsx
accessibilityTraits= {[“button”, “selected”]}
```

需手動替換為

```tsx
accessibilityRole=“button”
accessibilityStates={[“selected”]}
```

這些屬性已在 Facebook 的代碼庫中使用。Facebook 的代碼轉換過程出乎意料地簡單。jscodeshift 腳本解決了大約一半的實例，另一半則手動修正。整個過程總共花費不到幾個小時。

希望您會發現更新後的 API 很有用！並請繼續讓應用程式保持無障礙！#包容性