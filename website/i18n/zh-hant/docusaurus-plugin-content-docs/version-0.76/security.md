---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在構建應用程式時，安全性往往被忽視。雖然確實無法打造完全無法攻破的軟體（畢竟銀行金庫也仍會被破解），但遭受惡意攻擊或暴露安全漏洞的機率，與你願意投入多少心力來保護應用程式成反比。普通掛鎖雖可被撬開，但總比櫥櫃掛鉤難突破得多！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及協助強化應用安全的工具。這並非起飛前檢查清單——而是一系列選項的目錄，每項都能為你的應用和用戶提供更進一步的保護。

## 儲存敏感資訊

切勿將敏感API金鑰儲存在應用程式碼中。任何包含在程式碼中的內容，都可能被檢查應用套件的人以純文字形式存取。雖然像[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv)和[react-native-config](https://github.com/luggit/react-native-config/)這類工具很適合用於添加環境變數（如API端點），但它們不應與伺服器端的環境變數混淆，後者常包含密鑰和API金鑰。

若必須透過應用存取某項資源的API金鑰或密鑰，最安全的處理方式是建立應用與資源間的協調層。這可以是無伺服器函數（例如使用AWS Lambda或Google Cloud Functions），它能轉發請求並附上所需的API金鑰或密鑰。伺服器端程式碼中的密鑰，無法像應用程式碼中的密鑰那樣被API使用者存取。

**針對需持久化的用戶數據，根據其敏感性選擇合適的儲存類型。**隨著應用程式的使用，你會經常需要將數據儲存在裝置上——無論是為了支援離線使用、減少網路請求，或是保存用戶的存取權杖以避免每次使用時重新驗證。

> **持久化 vs 非持久化**——持久化數據會被寫入裝置的磁碟，讓應用程式能在不同啟動間讀取數據，無需再次透過網路請求獲取或要求用戶重新輸入。但這也使得數據更容易被攻擊者存取。非持久化數據則從不會寫入磁碟——因此根本沒有數據可供存取！

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage)是React Native的社群維護模組，提供非同步、未加密的鍵值儲存。Async Storage不會在應用間共享：每個應用都有自己的沙箱環境，無法存取其他應用的數據。

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

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services)允許你安全地儲存用戶的小型敏感資訊。這是儲存憑證、權杖、密碼等不應放入Async Storage之敏感數據的理想場所。

#### Android - 加密Shared Preferences

[Shared Preferences](https://developer.android.com/reference/android/content/SharedPreferences)是Android上等效的持久化鍵值數據儲存。**預設情況下，Shared Preferences中的數據並未加密**，但[加密型Shared Preferences](https://developer.android.com/topic/security/data)封裝了Android的Shared Preferences類別，會自動加密鍵與值。

#### Android - Keystore

[Android Keystore](https://developer.android.com/training/articles/keystore) 系統可讓您將加密金鑰儲存在容器中，使其更難從裝置中提取。

若要使用 iOS Keychain 服務或 Android Secure Shared Preferences，您可以自行編寫橋接程式碼，或使用封裝這些功能的函式庫（需自行承擔風險），這些函式庫會提供統一的 API。以下是一些可考慮的函式庫：

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-keychain](https://github.com/oblador/react-native-keychain)

> **注意避免無意中儲存或暴露敏感資訊。** 這種情況可能意外發生，例如將敏感表單資料儲存在 redux 狀態中，並將整個狀態樹持久化到 Async Storage。或將使用者令牌和個人資訊傳送到應用程式監控服務（如 Sentry 或 Crashlytics）。

## 認證與深度連結

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

行動應用程式有一個網頁應用程式所沒有的獨特漏洞：**深度連結**。深度連結是一種從外部來源直接將資料傳送到原生應用程式的方式。深度連結的格式類似 `app://`，其中 `app` 是您的應用程式方案，而 `//` 之後的任何內容都可用於內部處理請求。

例如，如果您正在開發一個電子商務應用程式，您可以使用 `app://products/1` 來深度連結到您的應用程式，並開啟 id 為 1 的產品詳細頁面。您可以將這些連結視為網頁上的 URL，但有一個關鍵區別：

深度連結並不安全，您絕不應該在其中傳送任何敏感資訊。

深度連結不安全的原因是沒有統一的方法來註冊 URL 方案。作為應用程式開發人員，您可以透過在 iOS 的 [Xcode 中配置](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app) 或在 Android 的 [新增意圖](https://developer.android.com/training/app-links/deep-linking) 來使用幾乎任何 URL 方案。

惡意應用程式可以透過註冊相同的方案來劫持您的深度連結，從而取得連結中包含的資料。傳送像 `app://products/1` 這樣的內容並無害處，但傳送令牌則是一個安全問題。

當作業系統在開啟連結時有兩個或更多應用程式可選擇時，Android 會向使用者顯示 [歧義對話框](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog)，並詢問他們要使用哪個應用程式來開啟連結。然而，在 iOS 上，作業系統會為您做出選擇，因此使用者將毫無察覺。Apple 在後續的 iOS 版本（iOS 11）中已採取措施解決此問題，實施了先到先得的原則，儘管此漏洞仍可能以其他方式被利用，您可以在[這裡](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)閱讀更多相關資訊。使用 [通用連結](https://developer.apple.com/ios/universal-links/) 可以在 iOS 中安全地連結到您的應用程式內容。

### OAuth2 與重新導向

OAuth2 認證協議現今非常流行，被譽為最完整且安全的協議。OpenID Connect 協議也基於此。在 OAuth2 中，使用者會被要求透過第三方進行認證。成功完成後，此第三方會重新導向回請求的應用程式，並帶有一個驗證碼，該驗證碼可兌換為 JWT — 一個 [JSON Web Token](https://jwt.io/introduction/)。JWT 是一種在網路上安全傳輸資訊的開放標準。

在網頁上，這個重新導向步驟是安全的，因為網頁上的 URL 保證是唯一的。但這對應用程式來說並不成立，因為如前所述，沒有統一的方法來註冊 URL 方案！為了解決這個安全問題，必須以 PKCE 的形式新增額外的檢查。

[PKCE](https://oauth.net/2/pkce/)（發音為「Pixy」）全稱為「Proof of Key Code Exchange」，是 OAuth 2 規範的擴充功能。它透過新增一層安全驗證機制，確保授權請求與令牌交換請求來自同一個客戶端。PKCE 採用 [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 加密雜湊演算法，該演算法能為任意長度的文字或檔案產生獨特的「數位指紋」，並具有以下特性：

- 無論輸入檔案大小，輸出長度固定不變
- 相同輸入必定產生相同結果
- 單向不可逆（無法從雜湊值反推原始輸入）

流程中會產生兩個關鍵值：

- **code_verifier**：由客戶端生成的隨機長字串
- **code_challenge**：code_verifier 經過 SHA 256 運算後的值

在初始的 `/authorize` 請求階段，客戶端除了將 code_verifier 暫存於記憶體，還會同步傳送其對應的 code_challenge。當授權請求成功返回後，客戶端需再次提交當初用於生成 code_challenge 的 code_verifier。此時身份提供者（IDP）會重新計算 code_challenge，並與初始請求中的數值比對，只有完全匹配時才會發放存取令牌。

此機制確保僅有發起初始授權流程的應用程式能成功兌換驗證碼取得 JWT。即使惡意程式截獲驗證碼，單獨持有也毫無用處。實際運作範例可參考[此示範](https://aaronparecki.com/oauth-2-simplified/#mobile-apps)。

針對原生 OAuth 實作，可考慮使用 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth) 函式庫。該 SDK 封裝了原生 [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) 與 [AppAuth-Android](https://github.com/openid/AppAuth-Android) 元件，能與 OAuth2 供應商對接並支援 PKCE 機制。

> 注意：react-native-app-auth 的 PKCE 支援與否取決於您的身份供應商是否實作該功能。

![OAuth2 搭配 PKCE 流程圖](/docs/assets/diagram_pkce.svg)

## 網路安全

API 必須全程採用 [SSL 加密](https://www.ssl.com/faqs/faq-what-is-ssl/)。SSL 能防止資料在伺服器與客戶端傳輸過程中被以明文竊取，安全端點可透過 `https://` 開頭（而非 `http://`）的網址識別。

### SSL 憑證綁定

僅使用 https 仍可能存在資料攔截風險。在標準 https 流程中，客戶端僅信任由預裝可信憑證機構（CA）簽署的伺服器憑證。攻擊者可能透過在用戶設備安裝惡意根憑證，使客戶端誤認攻擊者簽發的憑證為可信來源，導致仍可能遭受[中間人攻擊](https://zh.wikipedia.org/wiki/%E4%B8%AD%E9%97%B4%E4%BA%BA%E6%94%BB%E5%87%BB)。

**SSL 憑證綁定**技術可防範此類攻擊，其原理是在開發階段將受信任的憑證清單直接嵌入（綁定）至客戶端。此後應用程式只接受這些預存憑證的簽章請求，任何自簽憑證都會被拒絕。

> 實作 SSL 綁定時需注意憑證有效期問題。伺服器憑證通常每 1-2 年會過期更新，屆時必須同步更新應用內嵌的憑證。若伺服器更新憑證後未及時更新客戶端，持有舊憑證的應用將完全無法連線。

## 總結

雖然沒有萬無一失的安全處理方式，但透過有意識的努力與嚴謹態度，仍能大幅降低應用程式遭受安全漏洞的風險。請根據應用程式儲存資料的敏感程度、用戶數量，以及駭客入侵可能造成的損害，投入相應的安全防護資源。切記：最安全的資料就是那些從未被請求過的資料。