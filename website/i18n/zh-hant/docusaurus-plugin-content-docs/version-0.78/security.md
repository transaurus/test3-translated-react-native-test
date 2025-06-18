---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在開發應用程式時，安全性經常被忽視。雖然確實無法打造完全無懈可擊的軟體（畢竟銀行金庫仍可能被攻破），但遭受惡意攻擊或暴露於安全漏洞的風險，與你願意投入的防護努力成反比。即使普通掛鎖能被撬開，它仍比櫥櫃掛鉤難突破得多！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及強化應用程式安全的工具。這不是一份檢查清單，而是各種選項的目錄，每項都能為你的應用程式和用戶提供更深層的保護。

## 儲存敏感資訊

切勿在應用程式碼中儲存敏感的API金鑰。任何包含在程式碼中的內容，都可能被檢查應用套件的人以明文形式存取。雖然像[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv)和[react-native-config](https://github.com/luggit/react-native-config/)這類工具適合用於添加環境變數（如API端點），但它們與伺服器端的環境變數不同，後者常包含機密資訊和API金鑰。

若必須在應用中使用API金鑰或密鑰存取資源，最安全的做法是在應用程式與資源間建立協調層。這可以是無伺服器函數（如使用AWS Lambda或Google Cloud Functions），它能轉發帶有所需API金鑰或密鑰的請求。伺服器端程式碼中的密鑰無法像應用程式碼中的密鑰那樣被API使用者存取。

**針對需持久化的用戶數據，請根據敏感程度選擇合適的儲存類型。** 隨著應用程式的使用，你常需要將數據儲存在裝置上，無論是為了支援離線使用、減少網路請求，或是保存用戶的存取權杖以避免每次使用時重新驗證。

> **持久化 vs 非持久化** — 持久化數據會被寫入裝置的磁碟，讓應用程式能在不同啟動間讀取數據，無需再次網路請求或用戶重新輸入。但這也使得數據更容易被攻擊者存取。非持久化數據則永遠不會寫入磁碟，因此不存在被存取的風險！

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage)是React Native的社群維護模組，提供非同步、未加密的鍵值儲存。Async Storage不跨應用共享：每個應用都有自己的沙箱環境，無法存取其他應用的數據。

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

React Native並未內建儲存敏感數據的方法，但Android和iOS平台已有現成解決方案。

#### iOS - 鑰匙圈服務

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services)可安全儲存用戶的小型敏感資訊，是存放憑證、權杖、密碼等不適合放在Async Storage之數據的理想位置。

#### Android - 加密Shared Preferences

[Shared Preferences](https://developer.android.com/reference/android/content/SharedPreferences)是Android的持久化鍵值儲存方案。**預設情況下，Shared Preferences中的數據未加密**，但[加密型Shared Preferences](https://developer.android.com/topic/security/data)會自動加密鍵與值。

#### Android - 金鑰庫

[Android Keystore](https://developer.android.com/training/articles/keystore) 系統可讓您將加密金鑰儲存在容器中，使其更難從裝置中提取。

若要使用 iOS Keychain 服務或 Android 安全共享偏好設定，您可以自行編寫橋接程式，或使用封裝這些功能的函式庫並提供統一 API（風險自負）。以下是一些可考慮的函式庫：

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-keychain](https://github.com/oblador/react-native-keychain)

> **請注意無意間儲存或暴露敏感資訊的情況。** 這可能意外發生，例如將敏感表單資料儲存在 redux 狀態中，並將整個狀態樹持久化到 Async Storage。或將使用者令牌和個人資訊傳送到應用程式監控服務（如 Sentry 或 Crashlytics）。

## 認證與深度連結

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

行動應用程式有一個網頁應用程式不存在的獨特漏洞：**深度連結**。深度連結是一種從外部來源直接將資料傳送到原生應用程式的方式。深度連結看起來像 `app://`，其中 `app` 是您的應用程式方案，而 `//` 後面的內容可用於內部處理請求。

例如，如果您正在構建一個電子商務應用程式，可以使用 `app://products/1` 來深度連結到您的應用程式，並開啟 id 為 1 的產品詳細資訊頁面。您可以將這些連結視為網頁上的 URL，但有一個關鍵區別：

深度連結並不安全，您絕不應在其中傳送任何敏感資訊。

深度連結不安全的原因是沒有集中式的 URL 方案註冊方法。作為應用程式開發人員，您可以透過 [在 Xcode 中配置](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app)（iOS）或 [在 Android 上新增意圖](https://developer.android.com/training/app-links/deep-linking) 來使用幾乎任何 URL 方案。

惡意應用程式可以透過註冊相同的方案來劫持您的深度連結，從而取得連結中包含的資料。傳送像 `app://products/1` 這樣的內容並無害處，但傳送令牌則存在安全隱患。

當作業系統在開啟連結時有兩個或更多應用程式可選擇時，Android 會向使用者顯示 [歧義對話框](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog)，並詢問他們選擇使用哪個應用程式來開啟連結。然而在 iOS 上，作業系統會為您做出選擇，因此使用者將毫不知情。Apple 在後續的 iOS 版本（iOS 11）中已採取措施解決此問題，實施了先到先得的原則，儘管此漏洞仍可能以其他方式被利用，您可以在[此處](https://thehackernews.com/2019/07/ios-custom-url-scheme.html)閱讀更多相關資訊。使用[通用連結](https://developer.apple.com/ios/universal-links/)可在 iOS 中安全地連結到您的應用程式內容。

### OAuth2 與重新導向

OAuth2 認證協定現今極為流行，被譽為最完整且安全的協定。OpenID Connect 協定也基於此。在 OAuth2 中，使用者被要求透過第三方進行認證。成功完成後，此第三方會重新導向回請求應用程式，並帶有一個可兌換為 JWT（[JSON Web Token](https://jwt.io/introduction/)）的驗證碼。JWT 是一種在網路上安全傳輸資訊的開放標準。

在網頁上，此重新導向步驟是安全的，因為網頁上的 URL 保證是唯一的。但這對應用程式並不成立，因為如前所述，沒有集中式的 URL 方案註冊方法！為了解決此安全問題，必須以 PKCE 的形式新增額外的檢查。

[PKCE](https://oauth.net/2/pkce/)，發音為「Pixy」，全名為「Proof of Key Code Exchange」，是 OAuth 2 規範的擴充功能。它透過增加一層安全驗證機制，確保認證請求與令牌交換請求來自同一個客戶端。PKCE 採用 [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) 加密雜湊演算法，該演算法能為任何大小的文字或檔案產生獨特的「簽章」，且具有以下特性：

- 無論輸入檔案大小，輸出長度固定
- 相同輸入必定產生相同結果
- 單向不可逆（無法從簽章反推原始輸入）

此時您會持有兩個數值：

- **code_verifier** - 由客戶端生成的隨機長字串
- **code_challenge** - code_verifier 的 SHA 256 雜湊值

在初始的 `/authorize` 請求中，客戶端會同時發送記憶體中的 `code_verifier` 所對應的 `code_challenge`。當授權請求成功返回後，客戶端需再發送當初用於生成 `code_challenge` 的原始 `code_verifier`。身份提供者（IDP）將重新計算 `code_challenge`，核對是否與初始請求中的數值匹配，僅在驗證通過時才會發送存取令牌。

此機制確保只有發起初始授權流程的應用程式能成功兌換驗證碼取得 JWT。即使惡意程式截獲驗證碼，單獨持有也毫無用處。實作範例可參考[此教學](https://aaronparecki.com/oauth-2-simplified/#mobile-apps)。

針對原生 OAuth 實作，可考慮使用 [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth) 函式庫。該 SDK 封裝了原生 [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) 與 [AppAuth-Android](https://github.com/openid/AppAuth-Android) 庫，能與 OAuth2 供應商對接並支援 PKCE 機制。

> 注意：react-native-app-auth 的 PKCE 支援需視您的身份供應商是否實作此功能而定。

![OAuth2 搭配 PKCE 流程圖](/docs/assets/diagram_pkce.svg)

## 網路安全

API 必須全程使用 [SSL 加密](https://www.ssl.com/faqs/faq-what-is-ssl/)。SSL 能防止資料在伺服器與客戶端傳輸過程中被以明文竊取。安全端點的特徵是網址開頭為 `https://` 而非 `http://`。

### SSL 憑證綁定

僅使用 https 仍可能使資料遭攔截。在 https 架構下，客戶端僅信任由預裝可信憑證機構（CA）簽署的憑證。攻擊者可能透過在用戶裝置安裝惡意根憑證，讓客戶端誤認攻擊者簽發的憑證。此漏洞可能導致[中間人攻擊](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)。

**SSL 憑證綁定**技術可防範此類攻擊。其原理是在開發階段將受信任的憑證清單嵌入（綁定）至客戶端，使應用程式僅接受特定憑證簽署的請求，拒絕自簽憑證。

> 實作 SSL 綁定時需注意憑證有效期。憑證通常每 1-2 年失效，屆時需同步更新伺服器與應用程式內嵌的憑證。若伺服器更新憑證後，用戶端仍使用舊版應用程式，連線將立即中斷。

## 總結

雖然沒有萬無一失的安全處理方式，但透過有意識的努力和謹慎，仍能大幅降低應用程式遭受安全漏洞的可能性。請根據應用程式中儲存資料的敏感度、用戶數量，以及駭客入侵帳戶可能造成的損害程度，投入相應比例的安全防護資源。切記：從一開始就未請求的資訊，其被存取難度會顯著提高。