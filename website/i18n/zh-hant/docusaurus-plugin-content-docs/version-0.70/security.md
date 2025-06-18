---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在構建應用程式時，安全性往往被忽視。雖然確實無法打造完全無懈可擊的軟體——畢竟我們至今尚未發明完全無法破解的鎖（銀行金庫終究還是會被闖入）。但遭受惡意攻擊或暴露於安全漏洞的機率，與你願意投入多少努力來保護應用程式成反比。普通掛鎖雖可被撬開，但比起櫥櫃掛鉤仍難突破得多！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及有助於強化應用安全的工具。這並非起飛前檢查清單——而是選項目錄，每項都能進一步保護你的應用與使用者。

## 儲存敏感資訊

切勿在應用程式碼中儲存敏感API金鑰。任何包含在程式碼中的內容，都可能被檢查應用套件的人以純文字形式存取。雖然像[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv)和[react-native-config](https://github.com/luggit/react-native-config/)這類工具很適合添加環境變數（如API端點），但它們不應與伺服器端的環境變數混淆，後者常包含密鑰和API金鑰。

若必須透過應用存取資源時使用API金鑰或密鑰，最安全的處理方式是建立應用與資源間的協調層。這可以是無伺服器函數（例如使用AWS Lambda或Google Cloud Functions），能轉發帶有所需API金鑰或密鑰的請求。伺服器端程式碼中的密鑰無法像應用程式碼中的密鑰那樣被API消費者存取。

**針對需持久化的使用者資料，請根據敏感度選擇合適的儲存類型。** 隨著應用使用，你會經常需要將資料儲存在裝置上——無論是為了支援離線使用、減少網路請求，或是儲存使用者的存取權杖以避免每次使用時重新驗證。

> **持久化 vs 非持久化** — 持久化資料會寫入裝置磁碟，讓應用在多次啟動間能讀取資料，無需再次網路請求或要求使用者重新輸入。但這也使得資料更容易被攻擊者存取。非持久化資料永不寫入磁碟——因此根本無資料可存取！

### 非同步儲存(Async Storage)

[非同步儲存](https://github.com/react-native-async-storage/async-storage)是React Native社群維護的模組，提供非同步、未加密的鍵值儲存。各應用程式擁有獨立的沙盒環境，無法存取其他應用的資料。

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

React Native並未內建儲存敏感資料的方式，但Android和iOS平台已有現成解決方案。

#### iOS - 鑰匙圈服務(Keychain Services)

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services)可安全儲存使用者的敏感小數據，是存放憑證、權杖、密碼等不應置於非同步儲存之敏感資訊的理想場所。

#### Android - 加密共享偏好設定(Secure Shared Preferences)

[共享偏好設定](https://developer.android.com/reference/android/content/SharedPreferences)是Android的持久化鍵值資料儲存方案。**預設情況下，共享偏好設定中的資料未經加密**，但[加密共享偏好設定](https://developer.android.com/topic/security/data)封裝了該類別，會自動加密鍵與值。

#### Android - 金鑰庫(Keystore)

[Android Keystore](https://developer.android.com/training/articles/keystore) 系統可讓您將加密金鑰儲存在容器中，使其更難從裝置中提取。

若要使用 iOS Keychain 服務或 Android Secure Shared Preferences，您可以自行編寫橋接程式碼，或選擇使用封裝這些功能的函式庫（需自行承擔風險），這些函式庫會提供統一的 API。以下是一些可考慮的函式庫：

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-encrypted-storage](https://github.com/emeraldsanto/react-native-encrypted-storage) - 在 iOS 上使用 Keychain，在 Android 上使用 EncryptedSharedPreferences。
- [react-native-keychain](https://github.com/oblador/react-native-keychain)
- [react-native-sensitive-info](https://github.com/mCodex/react-native-sensitive-info) - 在 iOS 上是安全的，但在 Android 上使用 Shared Preferences（預設不安全）。不過有一個[分支](https://github.com/mCodex/react-native-sensitive-info/tree/keystore)使用 Android Keystore。
  - [redux-persist-sensitive-storage](https://github.com/CodingZeal/redux-persist-sensitive-storage) - 為 Redux 封裝 react-native-sensitive-info。

> **注意無意中儲存或暴露敏感資訊的情況。** 這種情況可能意外發生，例如將敏感表單資料儲存在 redux state 中，並將整個 state 樹持久化到 Async Storage。或將用戶令牌和個人資訊發送到應用監控服務（如 Sentry 或 Crashlytics）。

## 身份驗證與深度連結

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

移動應用有一個在網頁中不存在的獨特漏洞：**深度連結**。深度連結是一種從外部來源直接將數據發送到原生應用程式的方式。深度連結的形式類似 `app://`，其中 `app` 是您的應用程式方案，而 `//` 之後的內容可用於內部處理請求。

例如，如果您正在構建一個電子商務應用程式，可以使用 `app://products/1` 來深度連結到您的應用程式，並打開 id 為 1 的產品詳情頁面。您可以將這些連結視為網頁上的 URL，但有一個關鍵區別：

深度連結並不安全，您不應在其中發送任何敏感資訊。

深度連結不安全的原因是沒有集中註冊 URL 方案的方法。作為應用程式開發者，您可以通過在 iOS 上[在 Xcode 中配置](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app)，或在 Android 上[添加 intent](https://developer.android.com/training/app-links/deep-linking)，使用幾乎任何 URL 方案。

沒有任何機制可以阻止惡意應用程式通過註冊相同的方案來劫持您的深度連結，從而獲取連結中包含的數據。發送像 `app://products/1` 這樣的連結無害，但發送令牌則是一個安全問題。

當操作系統在打開連結時有兩個或更多應用程式可選擇時，Android 會顯示一個[歧義對話框](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog)，讓用戶選擇使用哪個應用程式打開連結。然而在 iOS 上，操作系統會為您做出選擇，因此用戶可能完全不知情。Apple 在後續的 iOS 版本（iOS 11）中採取了措施來解決這個問題，實施了先到先得的原則，但這個漏洞仍可能以其他方式被利用，您可以[在此](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)閱讀更多相關資訊。使用[通用連結](https://developer.apple.com/ios/universal-links/)可以在 iOS 中安全地連結到應用程式內的內容。

### OAuth2 與重定向

OAuth2 認證協議在當今極為流行，被譽為最完整且安全的協議。OpenID Connect 協議也基於此建構。在 OAuth2 流程中，使用者需透過第三方進行身份驗證。成功完成後，該第三方會重定向回請求應用程式，並附帶一個可兌換為 JWT（[JSON Web Token](https://jwt.io/introduction/)）的驗證碼。JWT 是一種在網路各方之間安全傳輸資訊的開放標準。

在網頁環境中，此重定向步驟是安全的，因為網址具有唯一性保證。但這不適用於應用程式，如前所述，應用程式缺乏集中式的網址方案註冊機制！為解決此安全隱患，必須透過 PKCE 機制增加額外驗證層。

[PKCE](https://oauth.net/2/pkce/)（發音為「Pixy」）全稱為 Proof of Key Code Exchange，是 OAuth 2 規範的擴充功能。它透過新增安全層來驗證認證請求與令牌交換請求是否來自同一客戶端。PKCE 採用 [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 加密雜湊演算法，該演算法能為任何大小的文字或檔案產生獨特「簽章」，且具有以下特性：

- 無論輸入檔案大小，輸出長度固定
- 相同輸入必定產生相同結果
- 單向不可逆（無法反向推導原始輸入）

此時您將持有兩個數值：

- **code_verifier**：由客戶端生成的大型隨機字串
- **code_challenge**：code_verifier 的 SHA 256 雜湊值

在初始的 `/authorize` 請求中，客戶端會同時發送記憶體中保存的 code_verifier 所對應的 code_challenge。當授權請求成功返回後，客戶端需再次發送用於生成 code_challenge 的原始 code_verifier。身份提供者（IDP）將重新計算 code_challenge，核對是否與初始 `/authorize` 請求設定的值相符，僅在匹配時才會發送存取令牌。

此機制確保只有觸發初始授權流程的應用程式才能成功將驗證碼兌換為 JWT。即使惡意應用程式取得驗證碼，單獨持有也毫無用處。實際運作範例可參考[此示範](https://aaronparecki.com/oauth-2-simplified/#mobile-apps)。

針對原生 OAuth 實作，可考慮使用 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth) 函式庫。該 SDK 封裝了原生 [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) 與 [AppAuth-Android](https://github.com/openid/AppAuth-Android) 元件，能與 OAuth2 供應商通訊並支援 PKCE 機制。

> 注意：react-native-app-auth 的 PKCE 支援需仰賴您的身份供應商是否實作此功能。

![OAuth2 搭配 PKCE 流程圖](/docs/assets/diagram_pkce.svg)

## 網路安全

API 應始終採用 [SSL 加密](https://www.ssl.com/faqs/faq-what-is-ssl/)。SSL 加密能防止資料在伺服器發出後至客戶端接收前被以明文形式竊取。安全端點的識別特徵是其以 `https://` 開頭（而非 `http://`）。

### SSL 憑證綁定

即使使用 https 端點，資料仍可能遭攔截。在 https 架構下，客戶端僅信任由預裝可信憑證機構（CA）簽署的有效憑證。攻擊者可能透過在用戶設備安裝惡意根 CA 憑證，使客戶端信任攻擊者簽署的所有憑證。因此，單純依賴憑證驗證仍可能使您暴露於[中間人攻擊](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)風險中。

**SSL 憑證釘選（SSL Pinning）** 是一種可在客戶端使用的技術，用於避免此類攻擊。其原理是在開發階段將一組受信任的憑證清單嵌入（或「釘選」）至客戶端，使得只有由這些受信任憑證簽署的請求才會被接受，任何自簽憑證都將被拒絕。

> 使用 SSL 憑證釘選時，需特別注意憑證的有效期限。憑證通常每 1-2 年會過期，屆時不僅伺服器端需更新憑證，應用程式內嵌的憑證也必須同步更新。一旦伺服器端的憑證更新後，任何仍內嵌舊版憑證的應用程式將立即無法運作。

## 總結

沒有任何方法能絕對保證安全性，但透過有意識的努力與嚴謹措施，可大幅降低應用程式遭受安全漏洞攻擊的可能性。請根據應用程式儲存的資料敏感度、用戶數量及駭客入侵可能造成的損害程度，投入相應的安全防護資源。切記：若從未請求存取某些資訊，駭客要取得這些資料的難度將大幅提高。