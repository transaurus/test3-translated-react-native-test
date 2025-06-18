---
id: linking-libraries-ios
title: Linking Libraries
---

並非每個應用程式都會使用所有原生功能，若包含支援所有功能的程式碼會影響二進位檔大小...但我們仍希望在您需要時能支援添加這些功能。

基於此考量，我們將許多這類功能以獨立靜態函式庫的形式提供。

大多數函式庫只需拖曳兩個檔案即可完成連結，有時需要第三步驟，但不會超過這個範圍。

:::note
所有隨 React Native 發佈的函式庫都存放在儲存庫根目錄的 `Libraries` 資料夾中。部分為純 JavaScript，您只需透過 `require` 引用即可。
其他函式庫則依賴原生程式碼，此時您必須將這些檔案加入應用程式，否則當您嘗試使用該函式庫時，應用程式會立即拋出錯誤。
:::

## 以下是連結含原生程式碼函式庫的簡要步驟

### 自動連結

安裝含原生依賴的函式庫：

```shell
npm install <library-with-native-dependencies> --save
```

:::info
`--save` 或 `--save-dev` 標記在此步驟極為重要。React Native 會根據您 `package.json` 檔案中的 `dependencies` 和 `devDependencies` 來連結函式庫。
:::

完成！下次建置應用程式時，原生程式碼將透過[自動連結](https://github.com/react-native-community/cli/blob/main/docs/autolinking.md)機制完成連結。

### 手動連結

#### 步驟 1

若函式庫包含原生程式碼，其資料夾內會有 `.xcodeproj` 檔案。將此檔案拖曳至 Xcode 專案中（通常在 Xcode 的 `Libraries` 群組下）；

![](/docs/assets/AddToLibraries.png)

#### 步驟 2

點擊您的主專案檔案（代表 `.xcodeproj` 的那個），選擇 `Build Phases`，然後從您要匯入的函式庫內的 `Products` 資料夾中，將靜態函式庫拖曳至 `Link Binary With Libraries`

![](/docs/assets/AddToBuildPhases.png)

#### 步驟 3

並非所有函式庫都需要此步驟，您需考量的是：

_我是否需要在編譯時知曉函式庫的內容？_

這意味著，您是在原生端還是僅在 JavaScript 中使用此函式庫？若僅在 JavaScript 中使用，您已準備就緒！

若需從原生端呼叫，則我們需知曉函式庫的標頭檔。為此，您需前往專案檔案，選擇 `Build Settings` 並搜尋 `Header Search Paths`。在此處加入函式庫的路徑。（此文件曾建議使用 `recursive`，但現已不再推薦，因其可能導致細微的建置失敗，特別是與 CocoaPods 共用時。）

![](/docs/assets/AddToSearchPaths.png)