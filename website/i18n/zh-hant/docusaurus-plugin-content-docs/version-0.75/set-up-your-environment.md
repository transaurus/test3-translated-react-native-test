---
id: set-up-your-environment
title: Set Up Your Environment
hide_table_of_contents: true
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import constants from '@site/core/TabsConstants';

import GuideLinuxAndroid from './\_getting-started-linux-android.md';
import GuideMacOSAndroid from './\_getting-started-macos-android.md';
import GuideWindowsAndroid from './\_getting-started-windows-android.md';
import GuideMacOSIOS from './\_getting-started-macos-ios.md';

本指南將教你如何設定開發環境，以便使用 Android Studio 和 Xcode 運行專案。這將使你能夠透過 Android 模擬器和 iOS 模擬器進行開發、本地建置應用程式等。

:::note
本指南需要安裝 Android Studio 或 Xcode。若已安裝其中任一程式，通常可在幾分鐘內完成設定。若未安裝，則需預留約一小時進行安裝與配置。

<details>
<summary>是否必須設定開發環境？</summary>

若使用 [框架](/architecture/glossary#react-native-framework)，則無需設定開發環境。React Native 框架會自動為你建置原生應用程式。

若因限制無法使用框架，或需自行開發框架，則必須設定本地環境。完成環境設定後，可學習如何[不使用框架開始開發](getting-started-without-a-framework)。

</details>
:::

#### 開發作業系統

<Tabs groupId="os" queryString defaultValue={constants.defaultOs} values={constants.oses} className="pill-tabs">
<TabItem value="macos">

#### Target OS

<Tabs groupId="platform" queryString defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="android">

[//]: # 'macOS, Android'

<GuideMacOSAndroid/>

</TabItem>
<TabItem value="ios">

[//]: # 'macOS, iOS'

<GuideMacOSIOS/>

</TabItem>
</Tabs>

</TabItem>
<TabItem value="windows">

#### Target OS

<Tabs groupId="platform" queryString defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="android">

[//]: # 'Windows, Android'

<GuideWindowsAndroid/>

</TabItem>
<TabItem value="ios">

[//]: # 'Windows, iOS'

## Unsupported

> A Mac is required to build projects with native code for iOS. You can use [Expo Go](https://expo.dev/go) from [Expo](environment-setup#start-a-new-react-native-project-with-expo) to develop your app on your iOS device.

</TabItem>
</Tabs>

</TabItem>
<TabItem value="linux">

#### Target OS

<Tabs groupId="platform" queryString defaultValue={constants.defaultPlatform} values={constants.platforms} className="pill-tabs">
<TabItem value="android">

[//]: # 'Linux, Android'

<GuideLinuxAndroid/>

</TabItem>
<TabItem value="ios">

[//]: # 'Linux, iOS'

## Unsupported

> A Mac is required to build projects with native code for iOS. You can use [Expo Go](https://expo.dev/go) from [Expo](environment-setup#start-a-new-react-native-project-with-expo) to develop your app on your iOS device.

</TabItem>
</Tabs>

</TabItem>
</Tabs>