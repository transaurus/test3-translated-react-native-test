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

本指南將教您如何設定開發環境，以便使用 Android Studio 和 Xcode 運行專案。這將使您能夠使用 Android 模擬器和 iOS 模擬器進行開發、在本機建置應用程式等。

:::note
本指南需要安裝 Android Studio 或 Xcode。若您已安裝其中任一程式，應能在幾分鐘內完成設定。若尚未安裝，預計需花費約一小時進行安裝與配置。

<details>
<summary>是否必須設定開發環境？</summary>

若您使用 [框架](/architecture/glossary#react-native-framework)，則無需設定開發環境。React Native 框架會自動為您建置原生應用程式，因此不需要安裝 Android Studio 或 Xcode。

若因限制無法使用框架，或您想自行開發框架，則必須設定本機開發環境。完成環境設定後，請參閱 [無框架入門指南](getting-started-without-a-framework) 開始開發。

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