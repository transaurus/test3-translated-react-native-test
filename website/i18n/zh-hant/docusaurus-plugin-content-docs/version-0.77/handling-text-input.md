---
id: handling-text-input
title: Handling Text Input
---

[`TextInput`](textinput#content) 是一個[核心元件](intro-react-native-components)，允許使用者輸入文字。它具有 `onChangeText` 屬性，接收一個在文字每次變更時呼叫的函式，以及 `onSubmitEditing` 屬性，接收一個在文字提交時呼叫的函式。

舉例來說，假設當使用者輸入時，您要將他們的文字翻譯成另一種語言。在這個新語言中，每個單字都以相同方式書寫：🍕。因此句子「Hello there Bob」會被翻譯為「🍕 🍕 🍕」。

```SnackPlayer name=Handling%20Text%20Input
import React, {useState} from 'react';
import {Text, TextInput, View} from 'react-native';

const PizzaTranslator = () => {
  const [text, setText] = useState('');
  return (
    <View style={{padding: 10}}>
      <TextInput
        style={{height: 40, padding: 5}}
        placeholder="Type here to translate!"
        onChangeText={newText => setText(newText)}
        defaultValue={text}
      />
      <Text style={{padding: 10, fontSize: 42}}>
        {text
          .split(' ')
          .map(word => word && '🍕')
          .join(' ')}
      </Text>
    </View>
  );
};

export default PizzaTranslator;
```

在此範例中，我們將 `text` 儲存在狀態中，因為它會隨時間變化。

您可能還想對文字輸入進行更多操作。例如，可以在使用者輸入時驗證文字內容。更詳細的範例請參閱[React 關於受控元件的文件](https://react.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)，或[TextInput 的參考文件](textinput.md)。

文字輸入是使用者與應用程式互動的方式之一。接下來，我們將了解另一種輸入類型，並[學習如何處理觸控操作](handling-touches.md)。