---
id: strict-typescript-api
title: Strict TypeScript API (opt in)
---

嚴格版 TypeScript API 是我們為 React Native 未來穩定版 JavaScript API 提供的預覽功能。

具體而言，這是針對 `react-native` npm 套件的一套全新 TypeScript 型別定義，自 0.80 版本起提供。這些型別能帶來更強健且更具未來性的型別準確度，讓我們能更有信心地將 React Native API 演進至穩定形態。採用嚴格版 TypeScript API 會帶來一些結構性型別差異，因此屬於一次性重大變更。

新型別具備以下特徵：

1. **直接從原始碼生成** — 提升覆蓋率與正確性，您可預期更強的相容性保證。
2. **限制於 `react-native` 索引檔案** — 更嚴格定義公共 API，意味著進行內部檔案變更時不會破壞 API。

當社群準備就緒時，嚴格版 TypeScript API 將成為我們的預設 API — 並與深層導入移除工作同步實施。

## 啟用方式

我們將這些新型別與現有型別同時發布，您可自行選擇遷移時機。我們鼓勵早期採用者與新建立的應用程式透過 `tsconfig.json` 檔案啟用此功能。

啟用嚴格版屬於**重大變更**，因為部分新型別已更新名稱與結構，不過許多應用程式可能不受影響。您可在下一節了解各項重大變更細節。

```json title="tsconfig.json"
{
  "extends": "@react-native/typescript-config",
  "compilerOptions": {
    ...
    "customConditions": ["react-native-strict-api"]
  }
}
```

:::note[底層機制]

這將指示 TypeScript 從我們新建的 [`types_generated/`](https://www.npmjs.com/package/react-native?activeTab=code) 目錄解析 `react-native` 型別，而非原先手動維護的 [`types/`](https://www.npmjs.com/package/react-native?activeTab=code) 目錄。無需重新啟動 TypeScript 或您的編輯器。

:::

嚴格版 TypeScript API 遵循我們移除 React Native 深層導入的 [RFC](https://github.com/react-native-community/discussions-and-proposals/pull/894) 規範。因此部分 API 不再從根目錄導出，這是刻意為之，旨在減少 React Native API 的整體表面積。

:::tip[API 意見回饋]

**提交意見**：我們將與社群合作，在（至少）未來兩個 React Native 版本中共同決定最終導出的 API 清單。請在我們的[意見討論串](https://github.com/react-native-community/discussions-and-proposals/discussions/893)分享您的想法。

另請參閱我們的[公告部落格文章](/blog/2025/06/12/moving-towards-a-stable-javascript-api)了解我們的動機與時程規劃。

:::

## 遷移指南

### Codegen 型別現在應從 `react-native` 套件導入

用於 codegen 的型別（如 `Int32`、`Double`、`WithDefault` 等）現在統一歸類於 `CodegenTypes` 命名空間下。同樣地，`codegenNativeComponent` 與 `codegenNativeCommands` 現在也改為從 react-native 套件導入，而非使用深層導入路徑。

命名空間化的 `CodegenTypes` 以及 `codegenNativeCommands` 和 `codegenNativeComponent` 在未啟用嚴格版 API 時仍可從 `react-native` 套件取得，以便第三方函式庫更容易進行適配。

**變更前**

```ts title=""
import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';
import type {
  Int32,
  WithDefault,
} from 'react-native/Libraries/Types/CodegenTypes';

interface NativeProps extends ViewProps {
  enabled?: WithDefault<boolean, true>;
  size?: Int32;
}

export default codegenNativeComponent<NativeProps>(
  'RNCustomComponent',
);
```

**變更後**

```ts title=""
import {CodegenTypes, codegenNativeComponent} from 'react-native';

interface NativeProps extends ViewProps {
  enabled?: CodegenTypes.WithDefault<boolean, true>;
  size?: CodegenTypes.Int32;
}

export default codegenNativeComponent<NativeProps>(
  'RNCustomComponent',
);
```

### 移除 `*Static` 型別

**變更前**

```tsx title=""
import {Linking, LinkingStatic} from 'react-native';

function foo(linking: LinkingStatic) {}
foo(Linking);
```

**變更後**

```tsx title=""
import {Linking} from 'react-native';

function foo(linking: Linking) {}
foo(Linking);
```

以下 API 先前採用 `*Static` 命名方式並搭配該型別的變數宣告。多數情況下會存在別名，使值與型別能透過相同識別符號導出，但部分 API 缺少此機制。

（例如過去存在 `AlertStatic` 類型、`Alert` 變數（類型為 `AlertStatic`）以及作為 `AlertStatic` 別名的 `Alert` 類型。但在 `PixelRatio` 案例中，有 `PixelRatioStatic` 類型和該類型的 `PixelRatio` 變數，卻沒有額外的類型別名。）

**受影響的 API**

- `AlertStatic`
- `ActionSheetIOSStatic`
- `ToastAndroidStatic`
- `InteractionManagerStatic`（此案例中無對應的 `InteractionManager` 類型別名）
- `UIManagerStatic`
- `PlatformStatic`
- `SectionListStatic`
- `PixelRatioStatic`（此案例中無對應的 `PixelRatio` 類型別名）
- `AppStateStatic`
- `AccessibilityInfoStatic`
- `ImageResizeModeStatic`
- `BackHandlerStatic`
- `DevMenuStatic`（此案例中無對應的 `DevMenu` 類型別名）
- `ClipboardStatic`
- `PermissionsAndroidStatic`
- `ShareStatic`
- `DeviceEventEmitterStatic`
- `LayoutAnimationStatic`
- `KeyboardStatic`（此案例中無對應的 `Keyboard` 類型別名）
- `DevSettingsStatic`（此案例中無對應的 `DevSettings` 類型別名）
- `I18nManagerStatic`
- `EasingStatic`
- `PanResponderStatic`
- `NativeModulesStatic`（此案例中無對應的 `NativeModules` 類型別名）
- `LogBoxStatic`
- `PushNotificationIOSStatic`
- `SettingsStatic`
- `VibrationStatic`

### 部分核心元件現改為函式元件而非類別元件

- `View`
- `Image`
- `TextInput`
- `Modal`
- `Text`
- `TouchableWithoutFeedback`
- `Switch`
- `ActivityIndicator`
- `ProgressBarAndroid`
- `InputAccessoryView`
- `Button`
- `SafeAreaView`

因此變更，存取這些視圖的 ref 類型需改用 `React.ComponentRef<typeof View>` 模式，此模式對類別元件與函式元件皆適用，例如：

```ts title=""
const ref = useRef<React.ComponentRef<typeof View>>(null);
```

## 其他重大變更

### Animated 類型調整

Animated 節點原先基於插值輸出作為泛型類型，現改為非泛型類型並搭配泛型 `interpolate` 方法。

`Animated.LegacyRef` 已移除。

### 統一可選屬性的類型

新類型系統中，所有可選屬性將統一標記為 `type | undefined`。

### 移除部分棄用類型

嚴格 API 下無法存取 [`DeprecatedPropertiesAlias.d.ts`](https://github.com/facebook/react-native/blob/0.80-stable/packages/react-native/types/public/DeprecatedPropertiesAlias.d.ts) 列出的所有類型。

### 移除殘留元件屬性

刪除了類型定義中存在但元件未使用或缺乏定義的屬性（例如：`Text` 的 `lineBreakMode`、`ScrollView` 的 `scrollWithoutAnimationTo`、transform 陣列外定義的 transform 樣式）。

### 原先可存取的私有類型輔助工具可能已被移除

由於舊版類型定義的配置，所有定義類型皆可從 `react-native` 套件存取，這包含未明確導出的類型與僅限內部使用的輔助類型。

顯著案例包含與 StyleSheet 相關的類型（如 `RecursiveArray`、`RegisteredStyle` 和 `Falsy`）以及 Animated 相關類型（如 `WithAnimatedArray` 和 `WithAnimatedObject`）。