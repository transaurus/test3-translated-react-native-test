---
id: security
title: Security
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import constants from '@site/core/TabsConstants';

在構建應用程式時，安全性往往被忽視。雖然確實無法打造完全無懈可擊的軟體（畢竟銀行金庫也仍會被攻破），但遭受惡意攻擊或暴露安全漏洞的機率，與你投入保護應用程式的努力成反比。即使普通掛鎖能被撬開，它仍比櫥櫃掛鉤難突破得多！

<img src="/docs/assets/d_security_chart.svg" width={283} alt=" " style={{float: 'right'}} />

本指南將介紹儲存敏感資訊、身份驗證、網路安全的最佳實踐，以及強化應用安全的工具。這不是一份預檢清單，而是選項目錄——每個選項都能為你的應用和用戶提供更深層的保護。

## 儲存敏感資訊

切勿在應用程式碼中儲存敏感的API金鑰。任何包含在程式碼中的內容，都可能被檢查應用套件的人以純文字形式存取。雖然像[react-native-dotenv](https://github.com/goatandsheep/react-native-dotenv)和[react-native-config](https://github.com/luggit/react-native-config/)這類工具適合用於添加環境變數（如API端點），但它們與伺服器端環境變數不同——後者常包含密鑰和API金鑰。

若必須在應用中使用API金鑰或密鑰存取資源，最安全的做法是在應用與資源間建立協調層。這可以是無伺服器函數（如使用AWS Lambda或Google Cloud Functions），它能轉發請求並附加所需API金鑰。伺服器端程式碼中的密鑰不會像應用程式碼中的密鑰那樣被API消費者存取。

**針對需持久化的用戶數據，請根據敏感度選擇合適的儲存類型。** 隨著應用使用，你會發現需要將數據儲存在設備上——無論是為了支援離線使用、減少網路請求，或是保存用戶的存取權杖以避免每次重新驗證。

> **持久化 vs 非持久化** —— 持久化數據會寫入設備磁碟，讓應用在多次啟動間能讀取數據，無需重新網路請求或用戶輸入。但這也使數據更易被攻擊者存取。非持久化數據永不寫入磁碟——自然無數據可竊！

### Async Storage

[Async Storage](https://github.com/react-native-async-storage/async-storage)是React Native的社群維護模組，提供非同步、未加密的鍵值儲存。Async Storage不跨應用共享：每個應用都有獨立的沙箱環境，無法存取其他應用的數據。

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

React Native未內建敏感數據儲存方案，但Android和iOS平台已有現成解決方案。

#### iOS - 鑰匙圈服務

[鑰匙圈服務](https://developer.apple.com/documentation/security/keychain_services)可安全儲存用戶的小型敏感資訊，是存放憑證、權杖、密碼等不適合Async Storage數據的理想場所。

#### Android - 加密共享偏好設定

[共享偏好設定](https://developer.android.com/reference/android/content/SharedPreferences)是Android的持久化鍵值儲存方案。**預設情況下數據未加密**，但[加密共享偏好設定](https://developer.android.com/topic/security/data)封裝了該類別，會自動加密鍵與值。

#### Android - 金鑰庫

The [Android Keystore](https://developer.android.com/training/articles/keystore) system lets you store cryptographic keys in a container to make it more difficult to extract from the device.

In order to use iOS Keychain services or Android Secure Shared Preferences, you can either write a bridge yourself or use a library which wraps them for you and provides a unified API at your own risk. Some libraries to consider:

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/)
- [react-native-keychain](https://github.com/oblador/react-native-keychain)

> **Be mindful of unintentionally storing or exposing sensitive info.** This could happen accidentally, for example saving sensitive form data in redux state and persisting the whole state tree in Async Storage. Or sending user tokens and personal info to an application monitoring service such as Sentry or Crashlytics.

## Authentication and Deep Linking

<img src="/docs/assets/d_security_deep-linking.svg" width={225} alt=" " style={{float: 'right', margin: '0 0 1em 1em'}} />

Mobile apps have a unique vulnerability that is non-existent in the web: **deep linking**. Deep linking is a way of sending data directly to a native application from an outside source. A deep link looks like `app://` where `app` is your app scheme and anything following the // could be used internally to handle the request.

For example, if you were building an ecommerce app, you could use `app://products/1` to deep link to your app and open the product detail page for a product with id 1. You can think of these kind of like URLs on the web, but with one crucial distinction:

Deep links are not secure and you should never send any sensitive information in them.

The reason deep links are not secure is because there is no centralized method of registering URL schemes. As an application developer, you can use almost any url scheme you choose by [configuring it in Xcode](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content/defining_a_custom_url_scheme_for_your_app) for iOS or [adding an intent on Android](https://developer.android.com/training/app-links/deep-linking).

There is nothing stopping a malicious application from hijacking your deep link by also registering to the same scheme and then obtaining access to the data your link contains. Sending something like `app://products/1` is not harmful, but sending tokens is a security concern.

When the operating system has two or more applications to choose from when opening a link, Android will show the user a [Disambiguation dialog](https://developer.android.com/training/basics/intents/sending#disambiguation-dialog) and ask them to choose which application to use to open the link. On iOS however, the operating system will make the choice for you, so the user will be blissfully unaware. Apple has made steps to address this issue in later iOS versions (iOS 11) where they instituted a first-come-first-served principle, although this vulnerability could still be exploited in different ways which you can read more about [here](https://thehackernews.com/2019/07/ios-custom-url-scheme.html). Using [universal links](https://developer.apple.com/ios/universal-links/) will allow linking to content within your app securely in iOS.

### OAuth2 and Redirects

The OAuth2 authentication protocol is incredibly popular nowadays, prided as the most complete and secure protocol around. The OpenID Connect protocol is also based on this. In OAuth2, the user is asked to authenticate via a third party. On successful completion, this third party redirects back to the requesting application with a verification code which can be exchanged for a JWT — a [JSON Web Token](https://jwt.io/introduction/). JWT is an open standard for securely transmitting information between parties on the web.

On the web, this redirect step is secure, because URLs on the web are guaranteed to be unique. This is not true for apps because, as mentioned earlier, there is no centralized method of registering URL schemes! In order to address this security concern, an additional check must be added in the form of PKCE.

[PKCE](https://oauth.net/2/pkce/), pronounced “Pixy” stands for Proof of Key Code Exchange, and is an extension to the OAuth 2 spec. This involves adding an additional layer of security which verifies that the authentication and token exchange requests come from the same client. PKCE uses the [SHA 256](https://www.movable-type.co.uk/scripts/sha256.html) Cryptographic Hash Algorithm. SHA 256 creates a unique “signature” for a text or file of any size, but it is:

- Always the same length regardless of the input file
- Guaranteed to always produce the same result for the same input
- One way (that is, you can’t reverse engineer it to reveal the original input)

Now you have two values:

- **code_verifier** - a large random string generated by the client
- **code_challenge** - the SHA 256 of the code_verifier

During the initial `/authorize` request, the client also sends the `code_challenge` for the `code_verifier` it keeps in memory. After the authorize request has returned correctly, the client also sends the `code_verifier` that was used to generate the `code_challenge`. The IDP will then calculate the `code_challenge`, see if it matches what was set on the very first `/authorize` request, and only send the access token if the values match.

This guarantees that only the application that triggered the initial authorization flow would be able to successfully exchange the verification code for a JWT. So even if a malicious application gets access to the verification code, it will be useless on its own. To see this in action, check out [this example](https://aaronparecki.com/oauth-2-simplified/#mobile-apps).

A library to consider for native OAuth is [react-native-app-auth](https://github.com/FormidableLabs/react-native-app-auth). React-native-app-auth is an SDK for communicating with OAuth2 providers. It wraps the native [AppAuth-iOS](https://github.com/openid/AppAuth-iOS) and [AppAuth-Android](https://github.com/openid/AppAuth-Android) libraries and can support PKCE.

> React-native-app-auth can support PKCE only if your Identity Provider supports it.

![OAuth2 with PKCE](/docs/assets/diagram_pkce.svg)

## Network Security

Your APIs should always use [SSL encryption](https://www.ssl.com/faqs/faq-what-is-ssl/). SSL encryption protects against the requested data being read in plain text between when it leaves the server and before it reaches the client. You’ll know the endpoint is secure, because it starts with `https://` instead of `http://`.

### SSL Pinning

Using https endpoints could still leave your data vulnerable to interception. With https, the client will only trust the server if it can provide a valid certificate that is signed by a trusted Certificate Authority that is pre-installed on the client. An attacker could take advantage of this by installing a malicious root CA certificate to the user’s device, so the client would trust all certificates that are signed by the attacker. Thus, relying on certificates alone could still leave you vulnerable to a [man-in-the-middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

**SSL pinning** is a technique that can be used on the client side to avoid this attack. It works by embedding (or pinning) a list of trusted certificates to the client during development, so that only the requests signed with one of the trusted certificates will be accepted, and any self-signed certificates will not be.

> When using SSL pinning, you should be mindful of certificate expiry. Certificates expire every 1-2 years and when one does, it’ll need to be updated in the app as well as on the server. As soon as the certificate on the server has been updated, any apps with the old certificate embedded in them will cease to work.

## Summary

雖然沒有萬無一失的安全處理方式，但透過有意識的努力與謹慎，仍能大幅降低應用程式遭受安全漏洞的風險。請根據應用程式儲存資料的敏感性、用戶數量，以及駭客入侵可能造成的損害程度，投入相應比例的安全防護資源。切記：最安全的資料就是那些從未被請求過的資料。