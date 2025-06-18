---
id: pixelratio
title: PixelRatio
---

`PixelRatio` 提供裝置像素密度與字體縮放比例的存取功能。

## 獲取正確尺寸的圖片

若使用高像素密度裝置，應獲取更高解析度的圖片。實務上可將圖片顯示尺寸乘以像素比例作為簡易準則。

```tsx
const image = getImage({
  width: PixelRatio.getPixelSizeForLayoutSize(200),
  height: PixelRatio.getPixelSizeForLayoutSize(100),
});
<Image source={image} style={{width: 200, height: 100}} />;
```

## 像素網格對齊

在 iOS 中，您能以任意精度指定元素位置與尺寸（例如 29.674825）。但實體顯示器僅具固定像素數量，如 iPhone SE（第一代）的 640×1136 或 iPhone 11 的 828×1792。iOS 透過將原始像素擴展為多個子像素來模擬視覺效果，但此技術會導致元素邊緣模糊化。

實務中發現開發者通常需手動四捨五入來避免模糊問題。React Native 已自動處理所有像素捨入。

執行捨入時需格外謹慎。切勿同時操作已捨入與未捨入數值，否則會累積捨入誤差。即使單一像素誤差也可能導致邊框消失或加倍顯示。

React Native 中，JavaScript 與佈局引擎皆使用任意精度數值運算。僅在原生主線程設定元素位置與尺寸時執行捨入，且捨入基準為根元素而非父元素，以避免誤差累積。

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

# 參考資料

## 方法

### `get()`

```tsx
static get(): number;
```

回傳裝置像素密度。常見範例：

- `PixelRatio.get() === 1`
  - [mdpi Android 裝置](https://material.io/tools/devices/)
- `PixelRatio.get() === 1.5`
  - [hdpi Android 裝置](https://material.io/tools/devices/)
- `PixelRatio.get() === 2`
  - iPhone SE、6S、7、8
  - iPhone XR
  - iPhone 11
  - [xhdpi Android 裝置](https://material.io/tools/devices/)
- `PixelRatio.get() === 3`
  - iPhone 6S Plus、7 Plus、8 Plus
  - iPhone X、XS、XS Max
  - iPhone 11 Pro、11 Pro Max
  - Pixel、Pixel 2
  - [xxhdpi Android 裝置](https://material.io/tools/devices/)
- `PixelRatio.get() === 3.5`
  - Nexus 6
  - Pixel XL、Pixel 2 XL
  - [xxxhdpi Android 裝置](https://material.io/tools/devices/)

---

### `getFontScale()`

```tsx
static getFontScale(): number;
```

回傳字體尺寸縮放係數。此比例值用於計算絕對字體大小，相關元件應據此進行運算。

- Android 數值反映「設定 > 顯示 > 字型大小」的使用者偏好
- iOS 數值反映「設定 > 顯示與亮度 > 文字大小」設定，亦可透過「設定 > 輔助使用 > 顯示與文字大小 > 較大文字」調整

若未設定字體縮放比例，則回傳裝置像素密度。

---

### `getPixelSizeForLayoutSize()`

```tsx
static getPixelSizeForLayoutSize(layoutSize: number): number;
```

將佈局尺寸（dp）轉換為像素尺寸（px）。

保證回傳整數值。

---

### `roundToNearestPixel()`

```tsx
static roundToNearestPixel(layoutSize: number): number;
```

將佈局尺寸(dp)四捨五入至最接近的整數像素對應值。例如在PixelRatio為3的裝置上，`PixelRatio.roundToNearestPixel(8.4) = 8.33`，這正好對應到(8.33 * 3) = 25像素。