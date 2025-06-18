---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在構建應用程式時，安全性往往被忽視。雖然確實無法打造完全無懈可擊的軟體（畢竟銀行金庫仍會被攻破），但遭受惡意攻擊或暴露安全漏洞的機率，與你投入應用程式防護的努力成反比。就像普通掛鎖雖可被撬開，但突破難度仍遠高於櫃門掛鉤！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及強化應用安全的工具。這不是預檢清單，而是選項目錄——每項選擇都能為你的應用和用戶提供更深層保護。

## 敏感資訊儲存

切勿在應用程式碼中儲存敏感API金鑰。任何包含在程式碼中的內容，都可能被檢查應用套件的人以明文形式存取。雖然像[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv)和[react-native-config](https://github.com/luggit/react-native-config/)這類工具適合用於添加環境變數（如API端點），但它們與伺服器端環境變數不同，後者常包含密鑰和API金鑰。

若必須在應用中使用API金鑰或密鑰存取資源，最安全的做法是在應用與資源間建立協調層。這可以是無伺服器函數（如AWS Lambda或Google Cloud Functions），用於轉發帶有所需API金鑰的請求。伺服器端程式碼中的密鑰無法像應用程式碼中的密鑰那樣被API使用者存取。

**針對持久化用戶數據，請根據敏感度選擇合適的儲存類型。** 隨著應用使用，你會經常需要將數據儲存在設備上——無論是為了支援離線使用、減少網路請求，還是保存用戶的存取權杖以避免每次重新驗證。

> **持久化 vs 非持久化** — 持久化數據會寫入設備磁碟，讓應用在多次啟動間能讀取數據，無需重新網路請求或用戶輸入。但這也使數據更易被攻擊者存取。非持久化數據永不寫入磁碟——自然無數據可竊！

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage)是React Native社群維護的模組，提供非同步、未加密的鍵值儲存。Async Storage不跨應用共享：每個應用都有獨立的沙盒環境，無法存取其他應用的數據。

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

React Native原生未內建敏感數據儲存方案，但Android和iOS平台已有現成解決方案。

#### iOS - 鑰匙圈服務

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services)可安全儲存用戶的小型敏感數據，是存放憑證、權杖、密碼等不應置於Async Storage之敏感資訊的理想位置。

#### Android - 加密Shared Preferences

[Shared Preferences](https://developer.android.com/reference/android/content/SharedPreferences)是Android的持久化鍵值儲存方案。**預設情況下Shared Preferences的數據未加密**，但[加密版Shared Preferences](https://developer.android.com/topic/security/data)封裝了該類別，會自動加密鍵與值。

#### Android - Keystore

[Android Keystore](https://developer.android.com/training/articles/keystore) 系統可讓您將加密金鑰儲存在容器中，使其更難從裝置中提取。

若要使用 iOS Keychain 服務或 Android Secure Shared Preferences，您可以自行編寫橋接程式碼，或使用封裝這些功能的函式庫（需自行承擔風險），這些函式庫會提供統一的 API。以下是一些可考慮的函式庫：

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-encrypted-storage](https://github.com/emeraldsanto/react-native-encrypted-storage) - 在 iOS 上使用 Keychain，在 Android 上使用 EncryptedSharedPreferences。
- [react-native-keychain](https://github.com/oblador/react-native-keychain)
- [react-native-sensitive-info](https://github.com/mCodex/react-native-sensitive-info) - 在 iOS 上是安全的，但在 Android 上使用 Shared Preferences（預設不安全）。不過有一個[分支](https://github.com/mCodex/react-native-sensitive-info/tree/keystore)使用 Android Keystore。
  - [redux-persist-sensitive-storage](https://github.com/CodingZeal/redux-persist-sensitive-storage) - 封裝 react-native-sensitive-info 以用於 Redux。

> **注意不要無意中儲存或暴露敏感資訊。** 這種情況可能意外發生，例如將敏感表單資料儲存在 redux state 中，並將整個 state 樹持久化到 Async Storage。或將用戶令牌和個人資訊發送到應用監控服務（如 Sentry 或 Crashlytics）。

## 認證與深度連結

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

移動應用有一個在網頁中不存在的獨特漏洞：**深度連結**。深度連結是一種從外部來源直接向原生應用發送數據的方式。深度連結看起來像 `app://`，其中 `app` 是您的應用方案，`//` 之後的任何內容可用於內部處理請求。

例如，如果您正在構建一個電子商務應用，可以使用 `app://products/1` 來深度連結到您的應用，並打開 id 為 1 的產品詳細頁面。您可以將這些視為類似於網頁上的 URL，但有一個關鍵區別：

深度連結不安全，您不應在其中發送任何敏感資訊。

深度連結不安全的原因是沒有集中註冊 URL 方案的方法。作為應用開發者，您可以通過[在 Xcode 中配置](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app)（iOS）或[在 Android 上添加 intent](https://developer.android.com/training/app-links/deep-linking) 來使用幾乎任何 URL 方案。

惡意應用可以通過註冊相同的方案來劫持您的深度連結，從而獲取連結中包含的數據。發送像 `app://products/1` 這樣的內容無害，但發送令牌則是一個安全問題。

當操作系統在打開連結時有兩個或更多應用可選時，Android 會向用戶顯示[歧義對話框](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog)，並詢問用戶選擇使用哪個應用打開連結。然而在 iOS 上，操作系統會為您做出選擇，因此用戶可能完全不知情。Apple 在後續的 iOS 版本（iOS 11）中採取了措施來解決此問題，實施了先到先得的原則，儘管此漏洞仍可能以其他方式被利用，您可以[在此](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)閱讀更多相關資訊。使用[通用連結](https://developer.apple.com/ios/universal-links/)可以在 iOS 中安全地連結到應用內的內容。

### OAuth2 與重定向

OAuth2 認證協議在當今極為流行，被譽為最完善且安全的協議。OpenID Connect 協議也基於此。在 OAuth2 中，用戶需透過第三方進行身份驗證。成功完成後，該第三方會重定向回請求應用程式，並附帶一個可兌換為 JWT（[JSON Web Token](https://jwt.io/introduction/)）的驗證碼。JWT 是一種在網絡上安全傳輸資訊的開放標準。

在網絡上，此重定向步驟是安全的，因為網址具有唯一性保證。但對應用程式而言並非如此，如前所述，並無集中式方法來註冊網址方案！為解決此安全隱患，必須額外加入 PKCE 檢查機制。

[PKCE](https://oauth.net/2/pkce/)（讀作「Pixy」）全稱為 Proof of Key Code Exchange，是 OAuth 2 規範的擴展。它透過增加一層安全驗證，確保認證與令牌交換請求來自同一客戶端。PKCE 採用 [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 加密雜湊演算法，該演算法能為任意大小的文字或檔案產生獨特「簽章」，且具有以下特性：

- 無論輸入檔案大小，輸出長度固定
- 相同輸入必產生相同結果
- 單向性（無法逆向推導原始輸入）

此時您將持有兩個數值：

- **code_verifier**：由客戶端生成的大型隨機字串
- **code_challenge**：code_verifier 的 SHA 256 雜湊值

在初始 `/authorize` 請求中，客戶端會同時發送記憶體中的 code_verifier 所對應的 code_challenge。當授權請求成功返回後，客戶端還需發送用於生成 code_challenge 的原始 code_verifier。身份提供者（IDP）將重新計算 code_challenge，核對是否與初始 `/authorize` 請求設定的值相符，僅在匹配時發送存取令牌。

此機制確保只有觸發初始授權流程的應用程式能成功兌換驗證碼為 JWT。即使惡意應用取得驗證碼，單獨持有亦無效用。實作範例可參考[此示範](https://aaronparecki.com/oauth-2-simplified/#mobile-apps)。

適用於原生 OAuth 的推薦函式庫為 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth)。該 SDK 封裝了原生 [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) 與 [AppAuth-Android](https://github.com/openid/AppAuth-Android) 庫，能與 OAuth2 供應商通訊並支援 PKCE。

> 注意：react-native-app-auth 僅在您的身份供應商支援時方可啟用 PKCE 功能。

![OAuth2 搭配 PKCE 流程圖](/docs/assets/diagram_pkce.svg)

## 網絡安全

您的 API 應始終採用 [SSL 加密](https://www.ssl.com/faqs/faq-what-is-ssl/)。此加密能防止資料在伺服器與客戶端傳輸過程中被明文竊取。安全端點可透過 `https://` 開頭（而非 `http://`）識別。

### SSL 憑證綁定

即使使用 https 端點，資料仍可能遭攔截。在 https 架構下，客戶端僅信任由預裝可信憑證機構（CA）簽署的有效憑證。攻擊者可透過在用戶設備安裝惡意根 CA 憑證，使客戶端信任攻擊者簽署的所有憑證。因此，單純依賴憑證驗證仍可能使您暴露於[中間人攻擊](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)風險中。

**SSL 憑證固定**是一種可在客戶端使用的技術，能有效防範此類攻擊。其原理是在開發階段將一組受信任的憑證清單嵌入（或固定）至客戶端，使得只有由這些受信任憑證簽署的請求才會被接受，任何自簽憑證都將被拒絕。

> 使用 SSL 憑證固定時，需特別注意憑證有效期問題。憑證通常每 1-2 年就會過期，屆時不僅伺服器端需要更新憑證，應用程式內嵌的憑證也必須同步更新。一旦伺服器憑證更新後，仍使用舊憑證的應用程式將立即無法運作。

## 總結

雖然不存在萬無一失的安全方案，但透過有意識的努力與嚴謹措施，能大幅降低應用程式遭受安全攻擊的可能性。請根據應用程式儲存資料的敏感性、用戶規模及駭客入侵可能造成的損害程度，投入相應等級的安全防護資源。切記：那些從未被請求的數據，往往最難被竊取。