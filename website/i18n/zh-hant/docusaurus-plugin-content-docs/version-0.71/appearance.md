---
id: appearance
title: Appearance
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

```tsx
import {Appearance} from 'react-native';
```

`Appearance` 模組提供了使用者外觀偏好的相關資訊，例如他們偏好的配色方案（淺色或深色）。

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

您可以使用 `Appearance` 模組來判斷使用者是否偏好深色配色方案：

```tsx
const colorScheme = Appearance.getColorScheme();
if (colorScheme === 'dark') {
  // Use dark color scheme
}
```

雖然配色方案可立即取得，但這可能會改變（例如在日出或日落時預定的配色方案變更）。任何依賴使用者偏好配色方案的渲染邏輯或樣式，應嘗試在每次渲染時調用此函數，而不是緩存該值。例如，您可以使用 [`useColorScheme`](usecolorscheme) React 鉤子，因為它提供並訂閱配色方案的更新，或者您可以使用內聯樣式而不是在 `StyleSheet` 中設置值。

---

# 參考

## 方法

### `getColorScheme()`

```tsx
static getColorScheme(): 'light' | 'dark' | null;
```

表示當前使用者偏好的配色方案。該值可能會在之後更新，無論是通過直接的使用者操作（例如在設備設置中選擇主題）還是按照計劃（例如跟隨日夜循環的淺色和深色主題）。

支援的配色方案：

- `light`：使用者偏好淺色主題。
- `dark`：使用者偏好深色主題。
- null：使用者未指定偏好的配色方案。

另請參閱：`useColorScheme` 鉤子。

> 注意：在使用 Chrome 進行調試時，`getColorScheme()` 將始終返回 `light`。

---

### `addChangeListener()`

```tsx
static addChangeListener(
  listener: (preferences: {colorScheme: 'light' | 'dark' | null}) => void,
): NativeEventSubscription;
```

添加一個事件處理程序，當外觀偏好發生變化時觸發。