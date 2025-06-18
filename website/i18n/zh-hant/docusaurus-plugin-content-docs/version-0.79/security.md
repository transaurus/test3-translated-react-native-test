---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在構建應用程式時，安全性往往被忽視。雖然確實無法打造完全無法攻破的軟體（畢竟銀行金庫仍會被攻破），但遭受惡意攻擊或暴露安全漏洞的機率，與你願意投入多少心力來保護應用程式成反比。普通掛鎖雖能被撬開，但比起櫥櫃掛鉤仍難突破得多！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及協助強化應用程式安全的工具。這並非起飛前檢查清單，而是選項目錄——每個選項都能為你的應用程式和用戶提供多一層保護。

## 儲存敏感資訊

切勿在應用程式代碼中儲存敏感的API金鑰。任何包含在代碼中的內容，都可能被檢查應用套件的人以純文字形式存取。雖然像[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv)和[react-native-config](https://github.com/luggit/react-native-config/)這類工具很適合用於添加環境變數（如API端點），但它們不應與伺服器端的環境變數混淆——後者常包含機密資訊和API金鑰。

若必須透過應用程式存取需要API金鑰或密鑰的資源，最安全的做法是在應用程式與資源間建立協調層。這可以是無伺服器函數（例如使用AWS Lambda或Google Cloud Functions），它能轉發請求並附上所需的API金鑰或密鑰。伺服器端代碼中的機密資訊，不會像應用程式代碼中的機密那樣被API使用者存取。

**對於需持久化的用戶數據，請根據敏感程度選擇合適的儲存類型。** 隨著應用程式的使用，你會經常需要將數據儲存在裝置上——無論是為了支援離線使用、減少網路請求，或是保存用戶的存取權杖以避免每次使用時重新驗證。

> **持久化 vs 非持久化** — 持久化數據會被寫入裝置的磁碟，讓應用程式能在多次啟動間讀取數據，無需重新發送網路請求或要求用戶再次輸入。但這也使得數據更容易被攻擊者存取。非持久化數據則永遠不會寫入磁碟——因此根本無數據可存取！

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage)是React Native的社群維護模組，提供非同步、未加密的鍵值儲存。Async Storage不跨應用程式共享：每個應用程式都有獨立的沙箱環境，無法存取其他應用程式的數據。

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

React Native本身並未內建儲存敏感數據的方式，但Android和iOS平台已有現成解決方案。

#### iOS - 鑰匙圈服務

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services)可安全儲存用戶的小型敏感數據，是存放憑證、權杖、密碼等不應置於Async Storage之敏感資訊的理想場所。

#### Android - 加密Shared Preferences

[Shared Preferences](https://developer.android.com/reference/android/content/SharedPreferences)是Android的持久化鍵值儲存方案。**預設情況下，Shared Preferences中的數據未經加密**，但[加密型Shared Preferences](https://developer.android.com/topic/security/data)封裝了Shared Preferences類別，會自動加密鍵與值。

#### Android - 金鑰庫

[Android Keystore](https://developer.android.com/training/articles/keystore) 系統可讓您將加密金鑰儲存在容器中，使其更難從裝置中提取。

若要使用 iOS Keychain 服務或 Android Secure Shared Preferences，您可以自行編寫橋接程式碼，或冒險使用封裝這些功能並提供統一 API 的函式庫。以下是一些可考慮的函式庫：

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-keychain](https://github.com/oblador/react-native-keychain)

> **注意無意中儲存或暴露敏感資訊的情況。** 這種情況可能意外發生，例如將敏感表單資料儲存在 redux 狀態中，並將整個狀態樹持久化到 Async Storage。或將使用者令牌和個人資訊發送到應用程式監控服務，如 Sentry 或 Crashlytics。

## 認證與深度連結

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

行動應用程式有一個在網頁上不存在的獨特漏洞：**深度連結**。深度連結是一種從外部來源直接將資料發送到原生應用程式的方式。深度連結看起來像 `app://`，其中 `app` 是您的應用程式方案，而 // 之後的任何內容都可以在內部用於處理請求。

例如，如果您正在構建一個電子商務應用程式，您可以使用 `app://products/1` 來深度連結到您的應用程式，並打開 id 為 1 的產品詳細頁面。您可以將這些視為網頁上的 URL，但有一個關鍵區別：

深度連結並不安全，您絕不應該在其中發送任何敏感資訊。

深度連結不安全的原因是沒有集中註冊 URL 方案的方法。作為應用程式開發人員，您可以通過 [在 Xcode 中配置](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app) 用於 iOS 或 [在 Android 上添加意圖](https://developer.android.com/training/app-links/deep-linking) 來使用幾乎任何 URL 方案。

沒有任何東西可以阻止惡意應用程式通過註冊相同的方案來劫持您的深度連結，從而獲取連結中包含的資料。發送像 `app://products/1` 這樣的內容並無害處，但發送令牌則是一個安全問題。

當操作系統在打開連結時有兩個或更多應用程式可供選擇時，Android 會向用戶顯示 [歧義對話框](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog) 並詢問他們選擇使用哪個應用程式來打開連結。然而，在 iOS 上，操作系統會為您做出選擇，因此用戶將毫不知情。Apple 在後續的 iOS 版本（iOS 11）中已採取措施解決此問題，他們制定了先到先得的原則，儘管這種漏洞仍可以通過不同方式被利用，您可以在此 [閱讀更多](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)。使用 [通用連結](https://developer.apple.com/ios/universal-links/) 將允許在 iOS 中安全地連結到您的應用程式中的內容。

### OAuth2 與重定向

OAuth2 認證協議如今非常流行，被譽為最完整和最安全的協議。OpenID Connect 協議也基於此。在 OAuth2 中，用戶被要求通過第三方進行認證。成功完成後，此第三方將重定向回請求應用程式，並帶有一個可以交換為 JWT（[JSON Web Token](https://jwt.io/introduction/)）的驗證碼。JWT 是一種在網路上安全傳輸資訊的開放標準。

在網頁上，這個重定向步驟是安全的，因為網頁上的 URL 保證是唯一的。這對於應用程式來說並不成立，因為如前所述，沒有集中註冊 URL 方案的方法！為了解決這個安全問題，必須以 PKCE 的形式添加額外的檢查。

[PKCE](https://oauth.net/2/pkce/)（發音為「Pixy」）全稱為「Proof of Key Code Exchange」，是 OAuth 2 規範的擴充功能。它透過增加一層安全驗證機制，確保認證請求與令牌交換請求來自同一客戶端。PKCE 採用 [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 加密雜湊演算法，該演算法能為任意長度的文字或檔案產生獨特的「數位指紋」，其特性如下：

- 無論輸入檔案大小，輸出長度固定
- 相同輸入必定產生相同結果
- 具單向性（無法逆向推導原始輸入）

此時您將持有兩個數值：

- **code_verifier** - 由客戶端生成的隨機長字串
- **code_challenge** - code_verifier 的 SHA 256 雜湊值

在初始的 `/authorize` 請求中，客戶端會同時發送記憶體中保存的 `code_verifier` 所對應的 `code_challenge`。當授權請求成功返回後，客戶端需再次發送用於生成 `code_challenge` 的原始 `code_verifier`。身份提供者（IDP）將重新計算 `code_challenge`，核對是否與初始 `/authorize` 請求設定的值相符，僅在匹配時才會發放存取令牌。

此機制確保僅有發起初始授權流程的應用程式能成功兌換驗證碼取得 JWT。即使惡意應用取得驗證碼，該代碼單獨也毫無用處。實作範例可參考[此示範](https://aaronparecki.com/oauth-2-simplified/#mobile-apps)。

適用於原生 OAuth 的推薦函式庫為 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth)。該 SDK 封裝了原生 [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) 與 [AppAuth-Android](https://github.com/openid/AppAuth-Android) 庫，可實現與 OAuth2 供應商的通訊，並支援 PKCE 機制。

> 注意：react-native-app-auth 的 PKCE 支援需視您的身份供應商是否實作該功能而定。

![OAuth2 搭配 PKCE 流程圖](/docs/assets/diagram_pkce.svg)

## 網路安全

API 必須全程採用 [SSL 加密](https://www.ssl.com/faqs/faq-what-is-ssl/)。SSL 能防止資料在伺服器傳輸至客戶端過程中被以明文竊取，安全端點可透過 `https://` 開頭（而非 `http://`）辨識。

### SSL 憑證綁定

即使使用 https 端點，資料仍有遭攔截風險。在標準 https 流程中，客戶端僅信任由預裝可信憑證機構（CA）簽發的伺服器憑證。攻擊者可能透過在用戶設備安裝惡意根 CA 憑證，使客戶端誤認攻擊者簽發的憑證皆可信，導致單純依賴憑證驗證仍可能遭受[中間人攻擊](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)。

**SSL 憑證綁定**技術可防範此類攻擊，其原理是在開發階段將受信任憑證清單嵌入（綁定）至客戶端，使得僅有採用信任清單內憑證簽署的請求會被接受，自簽憑證將被拒絕。

> 實作 SSL 綁定時需注意憑證有效期問題。憑證通常每 1-2 年會過期，屆時需同步更新伺服器與應用內嵌的憑證。若伺服器更新憑證後，內嵌舊憑證的應用將立即失效。

## 總結

雖然沒有萬無一失的安全處理方式，但透過有意識的努力與謹慎態度，仍能大幅降低應用程式遭受安全漏洞的風險。請根據應用程式儲存資料的敏感性、用戶數量，以及駭客入侵帳戶可能造成的損害程度，投入相應比例的安全防護資源。切記：那些從未被請求的資訊，往往是最難被取得的。