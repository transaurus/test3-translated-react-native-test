---
id: using-a-listview
title: Using List Views
---

React Native 提供了一系列元件來呈現列表資料。通常，你會想使用 [FlatList](flatlist.md) 或 [SectionList](sectionlist.md)。

`FlatList` 元件會顯示一個可滾動的列表，其中的資料會變動但結構相似。`FlatList` 適合用於長列表資料，且項目數量可能隨時間變化的情況。與較通用的 [`ScrollView`](using-a-scrollview.md) 不同，`FlatList` 只會渲染當前顯示在螢幕上的元素，而非一次渲染所有元素。

`FlatList` 元件需要兩個 props：`data` 和 `renderItem`。`data` 是列表的資料來源，而 `renderItem` 會從來源中取出一項資料並回傳一個格式化後的元件來渲染。

這個範例建立了一個基本的 `FlatList`，使用硬編碼的資料。`data` props 中的每一項都會被渲染為一個 `Text` 元件。`FlatListBasics` 元件接著會渲染 `FlatList` 和所有的 `Text` 元件。

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

如果你想渲染一組被分為邏輯區段的資料，可能還包含區段標題（類似 iOS 的 `UITableView`），那麼 [SectionList](sectionlist.md) 會是你的選擇。

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

列表視圖最常見的用途之一是顯示從伺服器取得的資料。要做到這點，你需要先 [學習 React Native 中的網路功能](network.md)。