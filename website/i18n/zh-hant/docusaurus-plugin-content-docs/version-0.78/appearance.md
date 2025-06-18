---
id: appearance
title: Appearance
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

```tsx
import {Appearance} from 'react-native';
```

`Appearance` 模組提供了使用者外觀偏好的相關資訊，例如他們偏好的色彩方案（淺色或深色）。

#### 開發者注意事項

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["android", "ios", "web"])}>

<TabItem value="web">

> The `Appearance` API is inspired by the [Media Queries draft](https://drafts.csswg.org/mediaqueries-5/) from the W3C. The color scheme preference is modeled after the [`prefers-color-scheme` CSS media feature](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme).

</TabItem>
<TabItem value="android">

> The color scheme preference will map to the user's Light or [Dark theme](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme) preference on Android 10 (API level 29) devices and higher.

</TabItem>
<TabItem value="ios">

> The color scheme preference will map to the user's Light or [Dark Mode](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/) preference on iOS 13 devices and higher.

> Note: When taking a screenshot, by default, the color scheme may flicker between light and dark mode. It happens because the iOS takes snapshots on both color schemes and updating the user interface with color scheme is asynchronous.

</TabItem>
</Tabs>

## 範例

您可以使用 `Appearance` 模組來判斷使用者是否偏好深色色彩方案：

```tsx
const colorScheme = Appearance.getColorScheme();
if (colorScheme === 'dark') {
  // Use dark color scheme
}
```

雖然色彩方案可立即取得，但這可能會改變（例如在日出或日落時預定的色彩方案變更）。任何依賴使用者偏好色彩方案的渲染邏輯或樣式，應在每次渲染時呼叫此函式，而非快取值。例如，您可以使用 [`useColorScheme`](usecolorscheme) React 鉤子，因為它提供並訂閱色彩方案的更新，或者您可以使用內聯樣式而非在 `StyleSheet` 中設定值。

---

# 參考

## 方法

### `getColorScheme()`

```tsx
static getColorScheme(): 'light' | 'dark' | null;
```

表示當前使用者偏好的色彩方案。此值可能會在之後更新，無論是透過直接的使用者操作（例如在裝置設定中選擇主題或透過 `setColorScheme` 設定應用程式層級的使用者介面風格）或根據排程（例如遵循日夜週期的淺色和深色主題）。

支援的色彩方案：

- `light`：使用者偏好淺色主題。
- `dark`：使用者偏好深色主題。
- null：使用者未指明偏好的色彩主題。

另請參閱：`useColorScheme` 鉤子。

> 注意：在使用 Chrome 進行偵錯時，`getColorScheme()` 將始終返回 `light`。

---

### `setColorScheme()`

```tsx
static setColorScheme('light' | 'dark' | null): void;
```

強制應用程式始終採用淺色或深色介面風格。預設值為 `null`，這會使應用程式繼承系統的介面風格。如果您指定了不同的值，新的風格將應用於應用程式及其內部的所有原生元素（如警示、選擇器等）。

支援的色彩方案：

- `light`：套用淺色使用者介面風格。
- `dark`：套用深色使用者介面風格。
- null：遵循系統的介面風格。

> 注意：此變更不會影響系統選擇的介面風格或其他應用程式中的任何風格設定。

---

### `addChangeListener()`

```tsx
static addChangeListener(
  listener: (preferences: {colorScheme: 'light' | 'dark' | null}) => void,
): NativeEventSubscription;
```

新增一個事件處理器，當外觀偏好變更時觸發。