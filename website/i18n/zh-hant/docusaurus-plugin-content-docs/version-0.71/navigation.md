---
id: navigation
title: Navigating Between Screens
---

行動應用程式很少由單一畫面組成。管理多個畫面的呈現與轉場通常由導航器(navigator)處理。

本指南涵蓋React Native中提供的各種導航元件。若您剛開始接觸導航功能，建議使用[React Navigation](navigation.md#react-navigation)。該函式庫提供直觀的導航解決方案，能在Android和iOS平台上實現常見的堆疊導航(stack navigation)與分頁導航(tabbed navigation)模式。

若您需將React Native整合至已具備原生導航功能的應用程式，或尋求React Navigation的替代方案，以下函式庫提供跨平台原生導航支援：[react-native-navigation](https://github.com/wix/react-native-navigation)。

## React Navigation

此社群解決方案是獨立函式庫，開發者僅需幾行程式碼即可設定應用程式的畫面結構。

### 安裝與設定

首先需在專案中安裝：

```shell
npm install @react-navigation/native @react-navigation/native-stack
```

接著安裝必要的peer dependencies。根據專案類型（Expo託管專案或裸React Native專案）需執行不同指令：

- Expo託管專案請使用`expo`安裝依賴項：

  ```shell
  npx expo install react-native-screens react-native-safe-area-context
  ```

- 裸React Native專案請使用`npm`安裝：

  ```shell
  npm install react-native-screens react-native-safe-area-context
  ```

  若為iOS裸專案，請確保已安裝[CocoaPods](https://cocoapods.org/)。接著執行pod安裝完成設定：

  ```shell
  cd ios
  pod install
  cd ..
  ```

:::note
安裝後可能會出現peer dependencies相關警告，通常由某些套件指定的版本範圍不正確導致。只要應用程式能正常建構，大多數警告可安全忽略。
:::

現在需將整個應用程式包裹在`NavigationContainer`中。通常會在入口檔案（如`index.js`或`App.js`）進行此設定：

```tsx
import * as React from 'react';
import {NavigationContainer} from '@react-navigation/native';

const App = () => {
  return (
    <NavigationContainer>
      {/* Rest of your app code */}
    </NavigationContainer>
  );
};

export default App;
```

至此您已準備好在裝置/模擬器上建構並執行應用程式。

### 使用方式

現在可建立包含首頁畫面與個人檔案畫面的應用程式：

```tsx
import * as React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import {createNativeStackNavigator} from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

const MyStack = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{title: 'Welcome'}}
        />
        <Stack.Screen name="Profile" component={ProfileScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
};
```

此範例使用`Stack.Screen`元件定義了兩個畫面（`Home`和`Profile`）。您可依需求定義任意數量畫面。

可透過`Stack.Screen`的`options`屬性設定各畫面選項，例如畫面標題。

每個畫面接收的`component`屬性為React元件，該元件會獲取名為`navigation`的prop，其中包含連結至其他畫面的各種方法。例如使用`navigation.navigate`跳轉至`Profile`畫面：

```tsx
const HomeScreen = ({navigation}) => {
  return (
    <Button
      title="Go to Jane's profile"
      onPress={() =>
        navigation.navigate('Profile', {name: 'Jane'})
      }
    />
  );
};
const ProfileScreen = ({navigation, route}) => {
  return <Text>This is {route.params.name}'s profile</Text>;
};
```

此`native-stack`導航器使用原生API：iOS的`UINavigationController`與Android的`Fragment`，因此透過`createNativeStackNavigator`建立的導航行為將與基於這些API原生開發的應用程式具有相同特性與效能表現。

React Navigation還提供不同類型的導航器套件（如分頁與抽屜式導航），可用於實作多種畫面模式。

完整入門教學請參閱[React Navigation入門指南](https://reactnavigation.org/docs/getting-started)。