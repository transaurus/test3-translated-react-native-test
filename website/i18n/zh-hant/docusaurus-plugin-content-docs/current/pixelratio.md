---
id: pixelratio
title: PixelRatio
---

`PixelRatio` 提供對設備像素密度和字體縮放比例的存取功能。

## 獲取正確尺寸的圖片

若您處於高像素密度的設備上，應獲取更高解析度的圖片。一個實用的經驗法則是將顯示的圖片尺寸乘以像素比例。

```tsx
const image = getImage({
  width: PixelRatio.getPixelSizeForLayoutSize(200),
  height: PixelRatio.getPixelSizeForLayoutSize(100),
});
<Image source={image} style={{width: 200, height: 100}} />;
```

## 像素網格對齊

在 iOS 中，您可以為元素指定任意精度的位置和尺寸，例如 29.674825。但最終物理顯示器僅有固定數量的像素，例如 iPhone SE（第一代）的 640×1136 或 iPhone 11 的 828×1792。iOS 會透過將原始像素分散成多個像素來盡可能忠實呈現用戶設定的數值，以達到視覺上的欺騙效果。此技術的缺點是會導致最終元素看起來模糊。

實務上，我們發現開發者並不希望有此功能，因此必須手動進行四捨五入來避免元素模糊。在 React Native 中，我們會自動對所有像素進行四捨五入。

進行此四捨五入時必須謹慎。您絕不希望在處理四捨五入值與未四捨五入值時同時進行，否則會累積捨入誤差。即使僅有一個捨入誤差也可能造成致命影響，例如一像素邊框可能消失或變成兩倍寬。

在 React Native 中，JavaScript 和佈局引擎內的所有運算皆使用任意精度的數字。僅當在主執行緒上設定原生元素的位置和尺寸時才會進行四捨五入。此外，捨入是相對於根元素而非父元素進行，以避免累積捨入誤差。

## 範例

```SnackPlayer name=PixelRatio%20Example
import React from 'react';
import {
  Image,
  PixelRatio,
  ScrollView,
  StyleSheet,
  Text,
  View,
} from 'react-native';
import {SafeAreaView, SafeAreaProvider} from 'react-native-safe-area-context';

const size = 50;
const cat = {
  uri: 'https://reactnative.dev/docs/assets/p_cat1.png',
  width: size,
  height: size,
};

const App = () => (
  <SafeAreaProvider>
    <SafeAreaView style={styles.container}>
      <ScrollView style={styles.scrollContainer}>
        <View style={styles.container}>
          <Text>Current Pixel Ratio is:</Text>
          <Text style={styles.value}>{PixelRatio.get()}</Text>
        </View>
        <View style={styles.container}>
          <Text>Current Font Scale is:</Text>
          <Text style={styles.value}>{PixelRatio.getFontScale()}</Text>
        </View>
        <View style={styles.container}>
          <Text>On this device images with a layout width of</Text>
          <Text style={styles.value}>{size} px</Text>
          <Image source={cat} />
        </View>
        <View style={styles.container}>
          <Text>require images with a pixel width of</Text>
          <Text style={styles.value}>
            {PixelRatio.getPixelSizeForLayoutSize(size)} px
          </Text>
          <Image
            source={cat}
            style={{
              width: PixelRatio.getPixelSizeForLayoutSize(size),
              height: PixelRatio.getPixelSizeForLayoutSize(size),
            }}
          />
        </View>
      </ScrollView>
    </SafeAreaView>
  </SafeAreaProvider>
);

const styles = StyleSheet.create({
  scrollContainer: {
    flex: 1,
  },
  container: {
    justifyContent: 'center',
    alignItems: 'center',
  },
  value: {
    fontSize: 24,
    marginBottom: 12,
    marginTop: 4,
  },
});

export default App;
```

---

# 參考文獻

## 方法

### `get()`

```tsx
static get(): number;
```

返回設備像素密度。範例如下：

- `PixelRatio.get() === 1`
  - [mdpi Android 設備](https://material.io/tools/devices/)
- `PixelRatio.get() === 1.5`
  - [hdpi Android 設備](https://material.io/tools/devices/)
- `PixelRatio.get() === 2`
  - iPhone SE、6S、7、8
  - iPhone XR
  - iPhone 11
  - [xhdpi Android 設備](https://material.io/tools/devices/)
- `PixelRatio.get() === 3`
  - iPhone 6S Plus、7 Plus、8 Plus
  - iPhone X、XS、XS Max
  - iPhone 11 Pro、11 Pro Max
  - Pixel、Pixel 2
  - [xxhdpi Android 設備](https://material.io/tools/devices/)
- `PixelRatio.get() === 3.5`
  - Nexus 6
  - Pixel XL、Pixel 2 XL
  - [xxxhdpi Android 設備](https://material.io/tools/devices/)

---

### `getFontScale()`

```tsx
static getFontScale(): number;
```

返回字體大小的縮放比例。此比例用於計算絕對字體大小，因此任何高度依賴此值的元素應使用此方法進行計算。

- 在 Android 上，此值反映使用者在**設定 > 顯示 > 字型大小**中的偏好設定
- 在 iOS 上，此值反映使用者在**設定 > 顯示與亮度 > 文字大小**中的偏好設定，亦可透過**設定 > 輔助使用 > 顯示與文字大小 > 較大文字**更新此值

若未設定字體縮放比例，則返回設備像素比例。

---

### `getPixelSizeForLayoutSize()`

```tsx
static getPixelSizeForLayoutSize(layoutSize: number): number;
```

將佈局尺寸（dp）轉換為像素尺寸（px）。

保證返回整數值。

---

### `roundToNearestPixel()`

```tsx
static roundToNearestPixel(layoutSize: number): number;
```

Rounds a layout size (dp) to the nearest layout size that corresponds to an integer number of pixels. For example, on a device with a PixelRatio of 3, `PixelRatio.roundToNearestPixel(8.4) = 8.33`, which corresponds to exactly (8.33 \* 3) = 25 pixels.