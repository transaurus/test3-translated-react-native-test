---
title: Preparing Your App for iOS 15 and Android 12
authors: [SamuelSusla]
tags: [engineering]
---

大家好！

隨著新版行動作業系統將於今年稍晚上市，我們建議您預先準備好 React Native 應用程式，以避免正式版本發佈時出現功能倒退的情況。

<!--truncate-->

## iOS 15

iOS 15 的發佈日期尚未公佈，但根據過往 iOS 版本的發佈時程，很可能會落在 9 月 16 日左右。若您的應用程式需要調整以相容 iOS 15，請務必預留 App Store 審核所需的時間。

### 需注意事項

#### 快速輸入欄 (QuickType Bar)

在 _[TextInput](/docs/textinput)_ 中停用 _QuickType_ 欄位的方式已變更。_QuickType_ 欄位是鍵盤上方顯示三個建議詞彙的橫條。若您的 UI 需要隱藏此欄位，在 iOS 15 中僅設定 [autoCorrect](/docs/textinput#autocorrect) 為 `false` 將無法像舊版一樣停用 _QuickType_ 欄位。要隱藏 _QuickType_ 欄位，您必須同時將 [spellCheck](/docs/textinput#spellcheck-ios) 設為 `false`。這會停用 _TextInput_ 中的拼字檢查功能（紅色底線）。在啟用拼字檢查的情況下停用 QuickType 欄位已不再可行。

<figure>
  <img src="/blog/assets/ios-15-quicktype-bar.png" alt="Screenshot of QuickType bar" />
  <figcaption>
    QuickType bar with three suggested words
  </figcaption>
</figure>

若要在 iOS 15 停用 QuickType 欄位，請將屬性 [spellCheck](/docs/textinput#spellcheck-ios) 和 [autoCorrect](/docs/textinput#autocorrect) 設為 `false`。

```jsx
<TextInput
  placeholder="something"
  autoCorrect={false}
  spellCheck={false}
/>
```

#### 透明導覽列

iOS 15 變更了導覽列的預設行為。與 iOS 14 不同，當內容完全向上滾動時，導覽列會變為透明。請注意此變化可能導致內容難以閱讀。相關解決技巧可參考[此討論串](https://developer.apple.com/forums/thread/682420)。

![iOS 14 與 iOS 15 導覽列對照截圖](/blog/assets/ios-15-navigation-bar.jpg)

### 如何安裝 iOS 15

#### 實體裝置

若您有備用裝置，可加入[測試版計畫](https://beta.apple.com/sp/betaprogram/)安裝 iOS 15。目前測試版已趨於穩定，但請注意**升級至 iOS 15 後將無法降版**。

#### 模擬器

若要在 iOS 15 模擬器測試應用程式，您需下載 Xcode 13。可於[此處](https://developer.apple.com/xcode/)取得 Xcode 13。

## Android 12

Android 12 將於今年秋季發佈，其中包含可能影響應用體驗的變更。按照慣例，Google Play 會要求應用程式的目標 SDK 在隔年 11 月前完成升級（參見[舊版要求](https://developer.android.com/distribute/best-practices/develop/target-sdk)）。

### 需注意事項

#### 過捲動效果

Android 12 新增了影響所有滾動容器的[過捲動效果](https://developer.android.com/about/versions/12/overscroll)。由於 React Native 的滾動視圖基於原生視圖，建議檢查您的可滾動容器以確保效果正確套用。可透過設定 [`overScrollMode`](/docs/scrollview#overscrollmode-android) 屬性為 `never` 來停用此效果。

#### 權限更新

當您請求 **`ACCESS_FINE_LOCATION`** 權限時，Android 12 允許使用者僅提供近似位置存取權限。詳情請參閱[此說明](https://developer.android.com/about/versions/12/approximate-location)。

查看 Google 提供的 [Android 12 所有應用行為變更詳情](https://developer.android.com/about/versions/12/behavior-changes-all)。

### 如何安裝 Android 12

#### 實體裝置

若您有備用的 Android 裝置，請參閱[此處說明](https://developer.android.com/about/versions/12/get)確認是否能安裝 Android 12 Beta 版。

#### 模擬器

若無可用裝置，可依照[此處指南](https://developer.android.com/about/versions/12/get#on_emulator)設定模擬器進行測試。