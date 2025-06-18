---
title: Introducing Button, Faster Installs with Yarn, and a Public Roadmap
authors: [hectorramos]
tags: [announcement]
---

我們收到許多反饋表示，由於React Native的開發進展迅速，很難掌握最新動態。為幫助社群了解當前工作重點，我們正式發佈了[React Native發展藍圖](https://github.com/facebook/react-native/wiki/Roadmap)。整體規劃可分為三大優先方向：

- **核心函式庫**：擴充高使用率元件與API的功能性
- **穩定性**：強化基礎架構以減少錯誤並提升程式碼品質
- **開發者體驗**：幫助React Native開發者提升效率

若您有希望納入藍圖的功能建議，歡迎透過[Canny平台](https://react-native.canny.io/feature-requests)提交新功能提案或參與現有討論。

## React Native最新動態

今日發佈的[React Native 0.37版本](https://github.com/facebook/react-native/releases/tag/v0.37.0)新增核心元件，讓開發者能輕鬆為應用程式加入觸控按鈕。同時我們也支援全新的[Yarn](https://yarnpkg.com/)套件管理工具，可大幅加速應用程式依賴項的更新流程。

## 全新Button元件登場

我們推出基礎的`<Button />`元件，能在各平台呈現完美原生樣式。這解決了最常見的用戶反饋：React Native是少數未內建即用按鈕的行動開發工具包之一。

![Android與iOS平台按鈕樣式](/blog/assets/button-android-ios.png)

```
<Button
  onPress={onPressMe}
  title="Press Me"
  accessibilityLabel="Learn more about this Simple Button"
/>
```

資深React Native開發者都了解如何自訂按鈕：在iOS使用TouchableOpacity實現預設樣式，在Android採用TouchableNativeFeedback產生漣漪效果，再套用些許樣式。雖然自訂按鈕不難實作，但我們致力於讓React Native的學習曲線極度平緩。透過將基礎按鈕納入核心，新手開發者首日就能打造出色功能，而非耗時調整按鈕格式或學習觸控元件細節。

Button元件設計目標是在各平台完美運作並呈現原生風格，因此不會支援自訂按鈕的所有進階功能。它是最佳起點，但並非用於取代現有按鈕。查閱[新版Button文件](/docs/button)了解更多，內含可執行範例！

## 使用Yarn加速react-native init

現在您可透過JavaScript新套件管理工具[Yarn](https://yarnpkg.com/)大幅加速`react-native init`流程。請先[安裝yarn](https://yarnpkg.com/en/docs/install)並將`react-native-cli`升級至1.2.0版以體驗效能提升：

```sh
$ npm install -g react-native-cli
```

建立新應用程式時將顯示「Using yarn」提示：

![使用yarn](/blog/assets/yarn-rncli.png)

簡易本地測試顯示，在良好網路環境下**約1分鐘即可完成react-native init**（相較於npm 3.10.8需時約3分鐘）。Yarn安裝為選用項目但強烈建議採用。

## 致謝

感謝所有參與此版本貢獻的開發者。完整[發佈說明](https://github.com/facebook/react-native/releases/tag/v0.37.0)已公佈於GitHub。透過二十餘項錯誤修正與新功能，React Native因您的付出持續進化。