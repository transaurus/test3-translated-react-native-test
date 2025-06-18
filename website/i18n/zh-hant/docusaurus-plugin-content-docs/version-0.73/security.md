---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在構建應用程式時，安全性往往被忽視。雖然確實無法打造完全無懈可擊的軟體（畢竟就連銀行金庫也可能被攻破），但遭受惡意攻擊或暴露安全漏洞的機率，與你願意投入多少心力來保護應用程式成反比。普通掛鎖雖能被撬開，但比起櫥櫃掛鉤仍難突破得多！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及協助強化應用程式安全的工具。這並非起飛前檢查清單，而是各項能進一步保護應用程式與使用者的選項目錄。

## 儲存敏感資訊

切勿在應用程式程式碼中儲存敏感的 API 金鑰。任何包含在程式碼中的內容，都可能被檢查應用套件的人以純文字形式存取。雖然 [react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv) 和 [react-native-config](https://github.com/luggit/react-native-config/) 等工具適合用於添加環境特定變數（如 API 端點），但它們不應與常包含密鑰和 API 金鑰的伺服器端環境變數混淆。

若必須在應用程式中使用 API 金鑰或密鑰來存取資源，最安全的做法是在應用程式與資源間建立協調層。這可以是無伺服器函數（例如使用 AWS Lambda 或 Google Cloud Functions），能轉發帶有所需 API 金鑰或密鑰的請求。伺服器端程式碼中的密鑰無法像應用程式程式碼中的密鑰那樣被 API 使用者存取。

**針對需持久化的使用者資料，請根據其敏感性選擇適當的儲存類型。** 隨著應用程式的使用，你常會需要將資料儲存在裝置上，無論是為了支援離線使用、減少網路請求，或是儲存使用者的存取權杖以便無需每次重新驗證。

> **持久化 vs 非持久化** — 持久化資料會寫入裝置磁碟，讓應用程式能在不同啟動間讀取，無需再次網路請求或要求使用者重新輸入。但這也使得資料更容易被攻擊者存取。非持久化資料則永不寫入磁碟——因此無資料可存取！

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage) 是 React Native 的社群維護模組，提供非同步、未加密的鍵值儲存。Async Storage 不在應用程式間共享：每個應用程式都有獨立的沙箱環境，無法存取其他應用程式的資料。

| **Do** use async storage when...              | **Don't** use async storage for... |
| --------------------------------------------- | ---------------------------------- |
| Persisting non-sensitive data across app runs | Token storage                      |
| Persisting Redux state                        | Secrets                            |
| Persisting GraphQL state                      |                                    |
| Storing global app-wide variables             |                                    |

#### 開發者注意事項

<Tabs groupId="guide" queryString defaultValue="web" values={constants.getDevNotesTabs(["web"])}>

<TabItem value="web">

> Async Storage is the React Native equivalent of Local Storage from the web

</TabItem>
</Tabs>

### 安全儲存

React Native 並未內建儲存敏感資料的方式，但 Android 和 iOS 平台已有現成解決方案。

#### iOS - 鑰匙圈服務

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services) 可安全儲存使用者的少量敏感資訊，是存放憑證、權杖、密碼等不應置於 Async Storage 之敏感資料的理想場所。

#### Android - 安全共享偏好設定

[共享偏好設定](https://developer.android.com/reference/android/content/SharedPreferences) 是 Android 上等效的持久化鍵值資料儲存。**預設情況下，共享偏好設定中的資料未經加密**，但 [加密共享偏好設定](https://developer.android.com/topic/security/data) 封裝了共享偏好設定類別，會自動加密鍵與值。

#### Android - 金鑰庫

[Android Keystore](https://developer.android.com/training/articles/keystore) 系統可讓您將加密金鑰儲存在容器中，使其更難從裝置中提取。

若要使用 iOS Keychain 服務或 Android 安全共享偏好設定，您可以自行編寫橋接程式，或使用封裝這些功能的函式庫並提供統一 API（但需自行承擔風險）。以下是一些可考慮的函式庫：

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-encrypted-storage](https://github.com/emeraldsanto/react-native-encrypted-storage) - 在 iOS 上使用 Keychain，在 Android 上使用 EncryptedSharedPreferences。
- [react-native-keychain](https://github.com/oblador/react-native-keychain)
- [react-native-sensitive-info](https://github.com/mCodex/react-native-sensitive-info) - 在 iOS 上是安全的，但在 Android 上使用 Shared Preferences（預設不安全）。不過有一個[分支](https://github.com/mCodex/react-native-sensitive-info/tree/keystore)使用 Android Keystore。
  - [redux-persist-sensitive-storage](https://github.com/CodingZeal/redux-persist-sensitive-storage) - 為 Redux 封裝 react-native-sensitive-info。

> **注意不要無意中儲存或暴露敏感資訊。** 這種情況可能意外發生，例如將敏感表單資料儲存在 redux 狀態中，並將整個狀態樹持久化到 Async Storage 中。或將使用者令牌和個人資訊發送到應用程式監控服務（如 Sentry 或 Crashlytics）。

## 認證與深度連結

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

行動應用程式有一個在網頁上不存在的獨特漏洞：**深度連結**。深度連結是一種從外部來源直接將資料發送到原生應用程式的方式。深度連結看起來像 `app://`，其中 `app` 是您的應用程式方案，而 `//` 之後的任何內容可用於內部處理請求。

例如，如果您正在構建一個電子商務應用程式，可以使用 `app://products/1` 來深度連結到您的應用程式，並打開 id 為 1 的產品詳細頁面。您可以將這些視為類似於網頁上的 URL，但有一個關鍵區別：

深度連結不安全，您不應在其中發送任何敏感資訊。

深度連結不安全的原因是沒有集中註冊 URL 方案的方法。作為應用程式開發人員，您可以通過[在 Xcode 中配置](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app)（iOS）或[在 Android 上添加意圖](https://developer.android.com/training/app-links/deep-linking)來使用幾乎任何 URL 方案。

沒有什麼能阻止惡意應用程式通過註冊相同的方案來劫持您的深度連結，從而獲取連結中包含的資料。發送像 `app://products/1` 這樣的內容無害，但發送令牌則是一個安全問題。

當操作系統在打開連結時有兩個或更多應用程式可供選擇時，Android 會向使用者顯示[歧義對話框](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog)，並詢問他們選擇使用哪個應用程式來打開連結。然而在 iOS 上，操作系統會為您做出選擇，因此使用者將毫不知情。Apple 在後續的 iOS 版本（iOS 11）中已採取措施解決此問題，實施了先到先得的原則，儘管此漏洞仍可能以其他方式被利用，您可以在此[閱讀更多](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)。使用[通用連結](https://developer.apple.com/ios/universal-links/)將允許在 iOS 中安全地連結到您的應用程式內容。

### OAuth2 與重定向

OAuth2 認證協議在當今極為流行，被譽為最完善且安全的協議。OpenID Connect 協議也是基於此。在 OAuth2 中，用戶需透過第三方進行身份驗證。成功完成後，該第三方會重定向回請求應用程式，並附帶一個驗證碼，此碼可兌換為 JWT——[JSON Web Token](https://jwt.io/introduction/)。JWT 是一種在網絡上安全傳輸資訊的開放標準。

在網絡上，此重定向步驟是安全的，因為網址具有唯一性。但對應用程式而言並非如此，如前所述，註冊網址方案並無集中管理機制！為解決此安全隱患，必須額外加入 PKCE 檢查機制。

[PKCE](https://oauth.net/2/pkce/) 全稱為 Proof of Key Code Exchange（發音為「Pixy」），是 OAuth 2 規範的擴展。它透過新增一層安全驗證，確保認證與令牌交換請求來自同一客戶端。PKCE 採用 [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 加密雜湊演算法。SHA 256 能為任何大小的文字或檔案產生獨特「簽章」，其特性如下：

- 無論輸入檔案大小，輸出長度固定
- 相同輸入必定產生相同結果
- 單向不可逆（無法逆向推導原始輸入）

此時您將持有兩個數值：

- **code_verifier**：由客戶端生成的大型隨機字串
- **code_challenge**：code_verifier 的 SHA 256 雜湊值

在初始 `/authorize` 請求階段，客戶端會同時發送記憶體中的 `code_verifier` 所對應之 `code_challenge`。當授權請求成功返回後，客戶端需再發送用於生成 `code_challenge` 的原始 `code_verifier`。身份提供者（IDP）將重新計算 `code_challenge`，核對是否與初始 `/authorize` 請求設定的值相符，僅在匹配時發放存取令牌。

此機制確保只有觸發初始授權流程的應用程式能成功用驗證碼兌換 JWT。即使惡意應用取得驗證碼，單獨持有亦無效用。實作範例可參考[此示範](https://aaronparecki.com/oauth-2-simplified/#mobile-apps)。

適用於原生 OAuth 的推薦函式庫為 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth)。該 SDK 封裝了原生 [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) 與 [AppAuth-Android](https://github.com/openid/AppAuth-Android) 函式庫，可支援 PKCE 機制。

> 注意：react-native-app-auth 僅在您的身份提供者支援時方可啟用 PKCE 功能。

![OAuth2 搭配 PKCE 流程圖](/docs/assets/diagram_pkce.svg)

## 網絡安全

您的 API 應始終採用 [SSL 加密](https://www.ssl.com/faqs/faq-what-is-ssl/)。SSL 加密能防止資料在伺服器與客戶端傳輸過程中被明文竊取。安全端點的識別特徵為其以 `https://` 開頭（而非 `http://`）。

### SSL 憑證綁定

即使使用 https 端點，資料仍可能遭攔截。在 https 架構下，客戶端僅信任由預裝可信憑證機構（CA）簽署的有效憑證。攻擊者可透過在用戶設備安裝惡意根 CA 憑證，使客戶端信任攻擊者簽發的所有憑證。因此，單純依賴憑證驗證仍可能使您暴露於[中間人攻擊](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)風險中。

**SSL 憑證固定**是一種可在客戶端使用的技術，用於避免此類攻擊。其原理是在開發階段將一組受信任的憑證清單嵌入（或固定）至客戶端，使得只有使用這些受信任憑證簽署的請求才會被接受，任何自簽憑證都將被拒絕。

> 使用 SSL 憑證固定時，需注意憑證有效期問題。憑證每 1-2 年會過期，屆時不僅伺服器需要更新憑證，應用程式內嵌的憑證也需同步更新。一旦伺服器憑證更新後，仍使用舊憑證的應用程式將立即失效。

## 總結

雖然不存在萬無一失的安全方案，但透過審慎規劃與嚴格執行，能大幅降低應用程式的資安風險。請根據應用程式儲存資料的敏感性、用戶規模及駭客入侵可能造成的損害程度，投入相應等級的安全防護資源。切記：最安全的資料，永遠是那些從未被請求過的資料。