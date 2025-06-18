---
id: navigation
title: Navigating Between Screens
---

行動應用程式很少僅由單一畫面組成。管理多個畫面的呈現與轉場，通常由所謂的「導航器」來處理。

本指南涵蓋 React Native 中提供的各種導航元件。如果你是導航功能的新手，可能會想使用 [React Navigation](navigation.md#react-navigation)。React Navigation 提供直觀的導航解決方案，能夠在 Android 和 iOS 上呈現常見的堆疊導航和分頁導航模式。

如果你正在將 React Native 整合到一個已經使用原生方式管理導航的應用程式中，或是在尋找 React Navigation 的替代方案，以下函式庫可在兩個平台上提供原生導航功能：[react-native-navigation](https://github.com/wix/react-native-navigation)。

## React Navigation

這個由社群開發的導航解決方案是一個獨立的函式庫，開發者只需幾行程式碼就能設定應用程式的畫面。

### 安裝與設定

首先，你需要在專案中安裝它們：

```shell
npm install @react-navigation/native @react-navigation/native-stack
```

接著，安裝必要的相依套件。根據你的專案是 Expo 託管專案還是純 React Native 專案，你需要執行不同的指令。

- 如果是 Expo 託管專案，請使用 `expo` 安裝相依套件：

  ```shell
  npx expo install react-native-screens react-native-safe-area-context
  ```

- 如果是純 React Native 專案，請使用 `npm` 安裝相依套件：

  ```shell
  npm install react-native-screens react-native-safe-area-context
  ```

  對於純 React Native 專案的 iOS 部分，請確保已安裝 [CocoaPods](https://cocoapods.org/)。然後安裝 Pods 以完成安裝：

  ```shell
  cd ios
  pod install
  cd ..
  ```

:::note
安裝後可能會看到與相依套件版本相關的警告。這些警告通常是由某些套件中指定的版本範圍不正確所導致。只要你的應用程式能正常建置，大多數警告可以安全忽略。
:::

現在，你需要將整個應用程式包裹在 `NavigationContainer` 中。通常這會在入口檔案中進行，例如 `index.js` 或 `App.js`：

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

現在你已經準備好在裝置或模擬器上建置並執行你的應用程式。

### 使用方式

現在你可以建立一個包含主畫面和使用者畫面的應用程式：

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

在這個範例中，使用 `Stack.Screen` 元件定義了兩個畫面（`Home` 和 `Profile`）。同樣地，你可以定義任意數量的畫面。

你可以透過 `Stack.Screen` 的 `options` 屬性為每個畫面設定選項，例如畫面標題。

每個畫面接受一個 `component` 屬性，該屬性是一個 React 元件。這些元件會接收到一個名為 `navigation` 的屬性，其中包含連結到其他畫面的各種方法。例如，你可以使用 `navigation.navigate` 前往 `Profile` 畫面：

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

這個 `native-stack` 導航器使用原生 API：iOS 上的 `UINavigationController` 和 Android 上的 `Fragment`，因此使用 `createNativeStackNavigator` 建置的導航行為將與基於這些 API 原生建置的應用程式具有相同的表現和效能特性。

React Navigation 還提供適用於不同類型導航器的套件，例如分頁和抽屜式導航。你可以使用它們在應用程式中實作各種模式。

如需完整的 React Navigation 入門指南，請參閱 [React Navigation 入門指南](https://reactnavigation.org/docs/getting-started)。