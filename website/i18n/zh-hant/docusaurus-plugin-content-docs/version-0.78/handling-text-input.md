---
id: handling-text-input
title: Handling Text Input
---

[`TextInput`](textinput#content) 是一個[核心組件](intro-react-native-components)，允許用戶輸入文本。它具有 `onChangeText` 屬性，該屬性接受一個在文本每次變化時調用的函數，以及一個 `onSubmitEditing` 屬性，該屬性接受一個在文本提交時調用的函數。

例如，假設當用戶輸入時，您將他們的單詞翻譯成另一種語言。在這個新語言中，每個單詞都以相同的方式書寫：🍕。因此，句子「Hello there Bob」將被翻譯為「🍕 🍕 🍕」。

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

在這個例子中，我們將 `text` 存儲在狀態中，因為它會隨時間變化。

您可能還想對文本輸入做更多的事情。例如，您可以在用戶輸入時驗證文本內容。更詳細的例子，請參閱 [React 文檔中的受控組件部分](https://react.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)，或 [TextInput 的參考文檔](textinput.md)。

文本輸入是用戶與應用交互的方式之一。接下來，我們將看看另一種類型的輸入，並[學習如何處理觸摸](handling-touches.md)。