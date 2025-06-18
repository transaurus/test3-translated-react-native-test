---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在構建應用程式時，安全性往往容易被忽視。雖然要打造完全無懈可擊的軟體確實不可能（畢竟就連銀行金庫也還是會被攻破），但遭受惡意攻擊或暴露安全漏洞的機率，與你願意投入多少心力來保護應用程式成反比。即使普通掛鎖能被撬開，但它仍比櫥櫃掛鉤難突破得多！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及協助強化應用程式安全的工具。這不是一份起飛前檢查清單，而是一系列選項的目錄，每個選項都能為你的應用程式和用戶提供更進一步的保護。

## 儲存敏感資訊

切勿將敏感API金鑰儲存在應用程式代碼中。任何包含在代碼裡的內容都可能被檢查應用套件的人以純文字形式存取。雖然像[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv)和[react-native-config](https://github.com/luggit/react-native-config/)這類工具很適合用來添加環境變數（如API端點），但它們不應與伺服器端的環境變數混淆，後者常包含密鑰和API金鑰。

若必須在應用程式中使用API金鑰或密鑰來存取資源，最安全的做法是在應用程式與資源之間建立協調層。這可以是無伺服器函數（例如使用AWS Lambda或Google Cloud Functions），它能轉發請求並附上必要的API金鑰或密鑰。伺服器端代碼中的密鑰無法像應用程式代碼中的密鑰那樣被API使用者存取。

**對於需持久化的用戶數據，請根據其敏感程度選擇合適的儲存方式。** 隨著應用程式的使用，你會經常需要將數據儲存在裝置上，無論是為了支援離線使用、減少網路請求，或是保存用戶的存取權杖以避免每次使用時重新驗證。

> **持久化 vs 非持久化** — 持久化數據會被寫入裝置的磁碟，讓應用程式能在多次啟動間讀取數據，無需再次發送網路請求或要求用戶重新輸入。但這也使得數據更容易被攻擊者存取。非持久化數據則永遠不會寫入磁碟——因此根本沒有數據可供存取！

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage)是React Native社群維護的模組，提供非同步、未加密的鍵值儲存。Async Storage不會在應用程式間共享：每個應用程式都有自己的沙箱環境，無法存取其他應用程式的數據。

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

React Native本身並未內建儲存敏感數據的方法，但Android和iOS平台已有現成解決方案。

#### iOS - 鑰匙圈服務

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services)允許你安全地儲存用戶的小型敏感資訊。這是儲存證書、權杖、密碼等不應放在Async Storage中的敏感數據的理想場所。

#### Android - 加密Shared Preferences

[Shared Preferences](https://developer.android.com/reference/android/content/SharedPreferences)是Android平台上等效的持久化鍵值數據儲存。**預設情況下，Shared Preferences中的數據並未加密**，但[加密型Shared Preferences](https://developer.android.com/topic/security/data)封裝了Shared Preferences類別，會自動加密鍵與值。

#### Android - Keystore

[Android Keystore](https://developer.android.com/training/articles/keystore) 系統可讓您將加密金鑰儲存在容器中，使其更難從裝置中提取。

若要使用 iOS Keychain 服務或 Android Secure Shared Preferences，您可以自行編寫橋接程式，或使用封裝這些功能的函式庫並提供統一 API（風險自負）。以下是一些可考慮的函式庫：

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-encrypted-storage](https://github.com/emeraldsanto/react-native-encrypted-storage) - iOS 使用 Keychain，Android 使用 EncryptedSharedPreferences。
- [react-native-keychain](https://github.com/oblador/react-native-keychain)
- [react-native-sensitive-info](https://github.com/mCodex/react-native-sensitive-info) - iOS 安全，但 Android 預設使用 Shared Preferences（不安全）。不過有一個[分支](https://github.com/mCodex/react-native-sensitive-info/tree/keystore)使用 Android Keystore。
  - [redux-persist-sensitive-storage](https://github.com/CodingZeal/redux-persist-sensitive-storage) - 為 Redux 封裝 react-native-sensitive-info。

> **注意無意中儲存或暴露敏感資訊。** 這種情況可能意外發生，例如將敏感表單資料儲存在 redux state 中，並將整個 state 樹持久化到 Async Storage。或將用戶令牌和個人資訊發送到應用監控服務（如 Sentry 或 Crashlytics）。

## 身份驗證與深度連結

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

行動應用有一個網頁應用不存在的獨特漏洞：**深度連結**。深度連結是一種從外部來源直接向原生應用發送資料的方式。深度連結看起來像 `app://`，其中 `app` 是您的應用方案，`//` 後面的內容可用於內部處理請求。

例如，如果您正在構建一個電子商務應用，可以使用 `app://products/1` 深度連結到您的應用，並打開 id 為 1 的產品詳細頁面。您可以將這些視為網頁上的 URL，但有一個關鍵區別：

深度連結不安全，切勿在其中發送任何敏感資訊。

深度連結不安全的原因是沒有集中註冊 URL 方案的方法。作為應用開發者，您可以通過[在 Xcode 中配置](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app)（iOS）或[在 Android 中添加 intent](https://developer.android.com/training/app-links/deep-linking) 來使用幾乎任何 URL 方案。

惡意應用可以通過註冊相同的方案來劫持您的深度連結，從而獲取連結中包含的資料。發送像 `app://products/1` 這樣的內容無害，但發送令牌則是一個安全問題。

當操作系統在打開連結時有兩個或更多應用可選時，Android 會顯示[歧義對話框](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog)讓用戶選擇使用哪個應用打開連結。然而在 iOS 上，操作系統會為您做出選擇，用戶可能完全不知情。Apple 在後續 iOS 版本（iOS 11）中採取了措施解決此問題，實行了先到先得的原則，儘管此漏洞仍可能以其他方式被利用，您可以[在此](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)閱讀更多內容。使用[通用連結](https://developer.apple.com/ios/universal-links/)可以在 iOS 中安全地連結到應用內的內容。

### OAuth2 與重定向

OAuth2 認證協議在當今極為流行，被譽為最完善且安全的協議。OpenID Connect 協議也基於此。在 OAuth2 中，用戶需透過第三方進行認證。成功完成後，該第三方會重定向回請求應用程式，並附帶一個可兌換為 JWT（[JSON Web Token](https://jwt.io/introduction/)）的驗證碼。JWT 是一種在網絡上安全傳輸資訊的開放標準。

在網絡上，此重定向步驟是安全的，因為網址具有唯一性保證。但對應用程式而言並非如此，如前所述，並無集中式方法來註冊網址方案！為解決此安全隱患，必須透過 PKCE 加入額外檢查。

[PKCE](https://oauth.net/2/pkce/)（讀作「Pixy」）全稱為「Proof of Key Code Exchange」，是 OAuth 2 規範的擴展。它透過增加一層安全驗證，確保認證與令牌交換請求來自同一客戶端。PKCE 使用 [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 加密雜湊演算法。SHA 256 能為任何大小的文字或檔案生成獨特「簽章」，其特性如下：

- 無論輸入檔案大小，輸出長度固定
- 相同輸入必產生相同結果
- 單向不可逆（無法逆向推導原始輸入）

此時您將持有兩個值：

- **code_verifier**：由客戶端生成的大型隨機字串
- **code_challenge**：code_verifier 的 SHA 256 雜湊值

在初始 `/authorize` 請求中，客戶端會同時發送記憶體中的 code_verifier 所對應的 code_challenge。當授權請求成功返回後，客戶端還需發送用於生成 code_challenge 的 code_verifier。身份提供者（IDP）將計算 code_challenge，核對是否與初始 `/authorize` 請求設定的值相符，僅在匹配時才會發送存取令牌。

此機制確保只有觸發初始授權流程的應用程式能成功用驗證碼兌換 JWT。即使惡意應用取得驗證碼，單獨持有也毫無用處。實作範例可參考[此處](https://aaronparecki.com/oauth-2-simplified/#mobile-apps)。

若需原生 OAuth 方案，可考慮使用 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth)。此 SDK 封裝了原生 [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) 與 [AppAuth-Android](https://github.com/openid/AppAuth-Android) 庫，能與 OAuth2 供應商交互並支援 PKCE。

> 注意：react-native-app-auth 的 PKCE 支援取決於您的身份供應商是否支援此功能。

![OAuth2 搭配 PKCE 流程圖](/docs/assets/diagram_pkce.svg)

## 網絡安全

您的 API 應始終使用 [SSL 加密](https://www.ssl.com/faqs/faq-what-is-ssl/)。SSL 加密能防止資料在伺服器與客戶端傳輸過程中被明文竊取。安全端點可透過 `https://` 開頭（而非 `http://`）識別。

### SSL 憑證綁定

即使使用 https 端點，資料仍可能遭攔截。在 https 中，客戶端僅信任能提供有效憑證的伺服器，且該憑證須由客戶端預裝的可信憑證機構簽署。攻擊者可能透過在使用者裝置安裝惡意根憑證，使客戶端信任攻擊者簽署的所有憑證。因此，僅依賴憑證仍可能使您暴露於[中間人攻擊](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)風險中。

**SSL 憑證固定**是一種可在客戶端使用的技術，能有效防範此類攻擊。其原理是在開發階段將一組受信任的憑證清單嵌入（或固定）至客戶端，使得只有由這些受信任憑證簽署的請求才會被接受，任何自簽憑證都將被拒絕。

> 使用 SSL 憑證固定時，需特別注意憑證的有效期限。憑證通常每 1-2 年會過期，屆時不僅伺服器端需要更新憑證，應用程式內嵌的憑證也必須同步更新。一旦伺服器端的憑證完成更新，任何仍內嵌舊版憑證的應用程式將立即無法運作。

## 總結

沒有任何方法能絕對保證安全性，但透過有意識的努力與嚴謹措施，可大幅降低應用程式遭受安全漏洞攻擊的可能性。請根據應用程式儲存資料的敏感性、用戶數量，以及駭客入侵可能造成的損害程度，投入相應比例的安全防護資源。切記：最安全的資料，往往是那些從未被請求過的資料。