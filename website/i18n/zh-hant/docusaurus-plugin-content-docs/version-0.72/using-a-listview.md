---
id: using-a-listview
title: Using List Views
---

React Native 提供了一系列用於展示數據列表的組件。通常情況下，你會希望使用 [FlatList](flatlist.md) 或 [SectionList](sectionlist.md)。

`FlatList` 組件用於顯示結構相似但內容可變的滾動列表數據。`FlatList` 特別適合處理可能隨時間變化的長列表數據。與通用的 [`ScrollView`](using-a-scrollview.md) 不同，`FlatList` 只會渲染當前顯示在屏幕上的元素，而非一次性渲染所有元素。

`FlatList` 組件需要兩個必要屬性：`data` 和 `renderItem`。`data` 是列表的數據來源，而 `renderItem` 會從數據源中提取單個項目並返回格式化後的組件進行渲染。

這個範例創建了一個基於硬編碼數據的基本 `FlatList`。`data` 屬性中的每個項目都會被渲染為一個 `Text` 組件。`FlatListBasics` 組件則負責渲染 `FlatList` 和所有 `Text` 組件。

```SnackPlayer name=FlatList%20Basics
import React from 'react';
import {FlatList, StyleSheet, Text, View} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 22,
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
});

const FlatListBasics = () => {
  return (
    <View style={styles.container}>
      <FlatList
        data={[
          {key: 'Devin'},
          {key: 'Dan'},
          {key: 'Dominic'},
          {key: 'Jackson'},
          {key: 'James'},
          {key: 'Joel'},
          {key: 'John'},
          {key: 'Jillian'},
          {key: 'Jimmy'},
          {key: 'Julie'},
        ]}
        renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
      />
    </View>
  );
};

export default FlatListBasics;
```

如果你需要渲染分為邏輯區塊的數據集（可能包含區段標題，類似 iOS 的 `UITableView`），那麼 [SectionList](sectionlist.md) 會是更合適的選擇。

```SnackPlayer name=SectionList%20Basics
import React from 'react';
import {SectionList, StyleSheet, Text, View} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 22,
  },
  sectionHeader: {
    paddingTop: 2,
    paddingLeft: 10,
    paddingRight: 10,
    paddingBottom: 2,
    fontSize: 14,
    fontWeight: 'bold',
    backgroundColor: 'rgba(247,247,247,1.0)',
  },
  item: {
    padding: 10,
    fontSize: 18,
    height: 44,
  },
});

const SectionListBasics = () => {
  return (
    <View style={styles.container}>
      <SectionList
        sections={[
          {title: 'D', data: ['Devin', 'Dan', 'Dominic']},
          {
            title: 'J',
            data: [
              'Jackson',
              'James',
              'Jillian',
              'Jimmy',
              'Joel',
              'John',
              'Julie',
            ],
          },
        ]}
        renderItem={({item}) => <Text style={styles.item}>{item}</Text>}
        renderSectionHeader={({section}) => (
          <Text style={styles.sectionHeader}>{section.title}</Text>
        )}
        keyExtractor={item => `basicListEntry-${item}`}
      />
    </View>
  );
};

export default SectionListBasics;
```

列表視圖最常見的用途之一就是顯示從服務器獲取的數據。要實現這一點，你需要先 [學習 React Native 中的網絡請求](network.md)。