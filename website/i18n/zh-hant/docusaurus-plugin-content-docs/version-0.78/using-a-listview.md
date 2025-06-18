---
id: using-a-listview
title: Using List Views
---

React Native 提供了一系列元件用於展示列表數據。通常情況下，您會希望使用 [FlatList](flatlist.md) 或 [SectionList](sectionlist.md)。

`FlatList` 元件用於顯示可滾動的、結構相似但內容變動的數據列表。`FlatList` 非常適合處理長列表數據，尤其是當項目數量可能隨時間變化的情況。與通用的 [`ScrollView`](using-a-scrollview.md) 不同，`FlatList` 僅渲染當前顯示在螢幕上的元素，而非一次性渲染所有元素。

`FlatList` 元件需要兩個屬性：`data` 和 `renderItem`。`data` 是列表的數據來源，而 `renderItem` 則從數據源中提取單個項目並返回格式化後的元件以供渲染。

此範例創建了一個基於硬編碼數據的基本 `FlatList`。`data` 屬性中的每個項目均被渲染為 `Text` 元件。`FlatListBasics` 元件隨後渲染 `FlatList` 及所有 `Text` 元件。

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

若您需要渲染按邏輯分段的數據集（可能包含分段標題，類似 iOS 的 `UITableView`），則 [SectionList](sectionlist.md) 是更合適的選擇。

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

列表視圖最常見的用途之一是顯示從伺服器獲取的數據。為此，您需要 [學習 React Native 中的網路請求處理](network.md)。