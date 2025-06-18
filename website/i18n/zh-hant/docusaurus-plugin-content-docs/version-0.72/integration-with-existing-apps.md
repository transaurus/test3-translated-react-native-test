---
id: integration-with-existing-apps
title: Integration with Existing Apps
hide_table_of_contents: true
---

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem';

import IntegrationJava from './\_integration-with-existing-apps-java.md'; import IntegrationObjC from './\_integration-with-existing-apps-objc.md'; import IntegrationSwift from './\_integration-with-existing-apps-swift.md'; import
IntegrationKotlin from './\_integration-with-existing-apps-kotlin.md';

當您從頭開始開發新的行動應用程式時，React Native 是非常理想的選擇。但它同樣適用於在現有的原生應用程式中添加單一視圖或用戶流程。只需幾個步驟，您就能加入基於 React Native 的新功能、畫面、視圖等。

具體步驟會根據您目標平台的不同而有所差異。

<Tabs groupId="language" queryString defaultValue="java" values={[ {label: 'Android (Kotlin)', value: 'kotlin'}, {label: 'Android (Java)', value: 'java'}, {label: 'iOS (Objective-C)', value: 'objc'}, {label: 'iOS (Swift)', value: 'swift'}, ]}>

<TabItem value="kotlin">

<IntegrationKotlin />

</TabItem>
<TabItem value="java">

<IntegrationJava />

</TabItem>
<TabItem value="objc">

<IntegrationObjC />

</TabItem>
<TabItem value="swift">

<IntegrationSwift />

</TabItem>
</Tabs>