---
id: strict-typescript-api
title: Strict TypeScript API (opt in)
---

嚴格版 TypeScript API 是我們為 React Native 未來穩定版 JavaScript API 提供的預覽功能。

具體而言，這是針對 `react-native` npm 套件的一套全新 TypeScript 型別定義，自 0.80 版本起提供。這些型別能帶來更強健且更具未來性的型別準確度，讓我們能更有信心地將 React Native API 演進至穩定形態。選擇啟用嚴格版 TypeScript API 會帶來一些結構性型別差異，因此屬於一次性破壞性變更。

新型別具備以下特徵：

1. **直接從原始碼生成** — 提升覆蓋率與正確性，您可預期更強的相容性保證。
2. **限縮至 `react-native` 索引檔案** — 更嚴格定義公共 API，意味著進行內部檔案變更時不會破壞 API。

當社群準備就緒時，嚴格版 TypeScript API 將成為我們的預設 API — 並與深層導入移除工作同步實施。

## 啟用方式

我們將這些新型別與現有型別同時發佈，您可自行選擇遷移時機。我們鼓勵早期採用者與新建立的應用程式透過 `tsconfig.json` 檔案啟用此功能。

啟用屬於**破壞性變更**，因為部分新型別具有更新的名稱與結構，不過多數應用程式不會受到影響。您可在下一節了解各項破壞性變更的詳細資訊。

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

此設定會指示 TypeScript 從我們新的 [`types_generated/`](https://www.npmjs.com/package/react-native?activeTab=code) 目錄解析 `react-native` 型別，而非原先手動維護的 [`types/`](https://www.npmjs.com/package/react-native?activeTab=code) 目錄。無需重新啟動 TypeScript 或您的編輯器。

:::

嚴格版 TypeScript API 遵循我們移除 React Native 深層導入的 [RFC](https://github.com/react-native-community/discussions-and-proposals/pull/894) 規範。因此部分 API 不再從根目錄匯出，此為刻意設計以減少 React Native API 的整體表面積。

:::tip[API 意見回饋]

**提交意見**：我們將與社群合作，在至少未來兩個 React Native 版本中共同決定最終匯出的 API 清單。請在我們的[意見討論串](https://github.com/react-native-community/discussions-and-proposals/discussions/893)分享您的想法。

另請參閱我們的[公告部落格文章](/blog/2025/06/12/moving-towards-a-stable-javascript-api)了解動機與時程規劃詳情。

:::

## 遷移指南

### 程式碼生成型別現在應從 `react-native` 套件導入

用於程式碼生成的型別（如 `Int32`、`Double`、`WithDefault` 等）現在統一歸類於 `CodegenTypes` 命名空間下。同樣地，`codegenNativeComponent` 與 `codegenNativeCommands` 現在可直接從 react-native 套件導入，無需使用深層導入路徑。

命名空間化的 `CodegenTypes` 以及 `codegenNativeCommands` 和 `codegenNativeComponent` 在未啟用嚴格版 API 時仍可從 `react-native` 套件取得，以降低第三方函式庫的採用門檻。

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

以下 API 先前以 `*Static` 形式命名並搭配該型別的變數宣告。多數情況下會存在別名使值與型別能以相同識別符號匯出，但部分 API 缺少此機制。

（舉例來說，原本存在 `AlertStatic` 類型、`Alert` 變數（類型為 `AlertStatic`）以及作為 `AlertStatic` 別名的 `Alert` 類型。但在 `PixelRatio` 的案例中，只有 `PixelRatioStatic` 類型和該類型的 `PixelRatio` 變數，缺乏額外的類型別名。）

**受影響的 API**

- `AlertStatic`
- `ActionSheetIOSStatic`
- `ToastAndroidStatic`
- `InteractionManagerStatic`（此案例中沒有對應的 `InteractionManager` 類型別名）
- `UIManagerStatic`
- `PlatformStatic`
- `SectionListStatic`
- `PixelRatioStatic`（此案例中沒有對應的 `PixelRatio` 類型別名）
- `AppStateStatic`
- `AccessibilityInfoStatic`
- `ImageResizeModeStatic`
- `BackHandlerStatic`
- `DevMenuStatic`（此案例中沒有對應的 `DevMenu` 類型別名）
- `ClipboardStatic`
- `PermissionsAndroidStatic`
- `ShareStatic`
- `DeviceEventEmitterStatic`
- `LayoutAnimationStatic`
- `KeyboardStatic`（此案例中沒有對應的 `Keyboard` 類型別名）
- `DevSettingsStatic`（此案例中沒有對應的 `DevSettings` 類型別名）
- `I18nManagerStatic`
- `EasingStatic`
- `PanResponderStatic`
- `NativeModulesStatic`（此案例中沒有對應的 `NativeModules` 類型別名）
- `LogBoxStatic`
- `PushNotificationIOSStatic`
- `SettingsStatic`
- `VibrationStatic`

### 部分核心元件現在改為函式元件而非類別元件

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

由於此變更，存取這些視圖的 ref 類型需改用 `React.ComponentRef<typeof View>` 模式，此模式無論對類別元件或函式元件皆適用，例如：

```ts title=""
const ref = useRef<React.ComponentRef<typeof View>>(null);
```

## 其他重大變更

### Animated 類型的調整

Animated 節點原先會根據插值輸出結果定義為泛型類型，現在改為非泛型類型並搭配泛型 `interpolate` 方法。

`Animated.LegacyRef` 已不可用。

### 統一選擇性屬性的類型

在新類型系統中，所有選擇性屬性都會被定義為 `type | undefined`。

### 移除部分棄用類型

所有列在 [`DeprecatedPropertiesAlias.d.ts`](https://github.com/facebook/react-native/blob/0.80-stable/packages/react-native/types/public/DeprecatedPropertiesAlias.d.ts) 的類型在嚴格 API 下皆不可存取。

### 移除殘留的元件屬性

部分在類型定義中存在但未被元件使用或缺乏定義的屬性已被移除（例如：`Text` 的 `lineBreakMode`、`ScrollView` 的 `scrollWithoutAnimationTo`、定義在 transform 陣列外的 transform 樣式）。

### 原先可存取的私有類型輔助工具可能已被移除

由於舊版類型定義的配置，所有定義的類型都能從 `react-native` 套件存取。這包含未明確導出的類型以及僅限內部使用的輔助類型。

此類型的顯著案例包含與 StyleSheet 相關的類型（如 `RecursiveArray`、`RegisteredStyle` 和 `Falsy`）以及 Animated 相關類型（如 `WithAnimatedArray` 和 `WithAnimatedObject`）。