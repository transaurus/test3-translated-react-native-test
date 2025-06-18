---
id: xplat-implementation
title: Cross Platform Implementation
---

import FabricWarning from './\_fabric-warning.mdx';

<FabricWarning />

#### React Native 渲染器採用核心渲染實現以跨平台共享

在 React Native 先前的渲染系統中，**[React 陰影樹](architecture-glossary.md#react-shadow-tree-and-react-shadow-node)**、佈局邏輯與 **[視圖扁平化](view-flattening.md)** 演算法需針對每個平台各自實作。現行渲染器設計為跨平台解決方案，透過共享 C++ 核心實現達成此目標。

React Native 團隊計劃將動畫系統整合至渲染系統，並將渲染系統擴展至 Windows 等新平台，以及遊戲主機、電視等作業系統。

採用 C++ 作為核心渲染系統具備多重優勢：單一實現降低開發與維護成本；由於最小化 [Yoga](architecture-glossary.md#yoga-tree-and-yoga-node) 與渲染器的整合開銷（如 Android 端不再需要 [JNI](architecture-glossary.md#java-native-interface-jni)），可提升 React 陰影樹創建與佈局計算效能；此外，C++ 分配的 React 陰影節點記憶體佔用也低於 Kotlin 或 Swift 實現。

團隊同時運用 C++ 的不可變性特性，確保共享資源的並行存取不會引發安全問題。

需特別注意的是，Android 端的渲染器用例仍存在兩項主要 [JNI](architecture-glossary.md#java-native-interface-jni) 成本：

- 複雜視圖（如 `Text`、`TextInput` 等）的佈局計算需透過 JNI 傳遞屬性
- 掛載階段需透過 JNI 傳送變異操作

團隊正研究以 `ByteBuffer` 序列化機制取代 `ReadableMap`，目標將 JNI 開銷降低 35–50%。

渲染器提供兩類 C++ API 介面：

- **(i)** 與 React 通訊
- **(ii)** 與宿主平台通訊

針對 **(i)**，React 透過渲染器進行 **渲染** React 樹，並「監聽」**事件**（如 `onLayout`、`onKeyPress`、觸控等）。

針對 **(ii)**，React Native 渲染器與宿主平台通訊以掛載螢幕上的宿主視圖（創建、插入、更新或刪除宿主視圖），並監聽用戶在宿主平台觸發的 **事件**。

![跨平台實現示意圖](/docs/assets/Architecture/xplat-implementation/xplat-implementation-diagram.png)