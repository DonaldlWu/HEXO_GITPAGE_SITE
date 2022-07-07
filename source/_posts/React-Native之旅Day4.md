---
title: React-Native之旅Day4 Text Input
date: 2022-07-08 00:30:47
tags: [React-Native]
---

今天就來看看 Reac-Native 如何利用[核心元件](https://reactnative.dev/docs/intro-react-native-components)來處理文字輸入

[TextInput](https://reactnative.dev/docs/textinput#content) 帶有一個 `onChangeText` 的 prop

這個 prop 接受一個 Funciton 可在文字變化時被呼叫

以及在文字送出時呼叫的 `onSubmitEditing`

<!--more-->

這裡的範例會將用戶輸入的文字用空格格開並將文字轉換成 🍕

```JSX
// 1
import React, { useState } from 'react';
import { Text, TextInput, View } from 'react-native';

const PizzaTranslator = () => {
  // 2
  const [text, setText] = useState('');
  return (
    <View style={{padding: 10}}>
      <TextInput
        style={{height: 40}}
        placeholder="Type here to translate!"
        // 3
        onChangeText={newText => setText(newText)}
        defaultValue={text}
      />
      <Text style={{padding: 10, fontSize: 42}}>
        {text.split(' ').map((word) => word && '🍕').join(' ')}
      </Text>
    </View>
  );
}

export default PizzaTranslator;
```

1. Import Text

2. 因為 text 是會改變的，因此這裡使用 state 儲存

3. 在元件內將觀察輸入的文字並更改 state

4. 在 Text 元件內根據 text 顯示轉換完成的資訊

延伸閱讀

[Controlled Component](https://reactjs.org/docs/forms.html#controlled-components)

[TextInput](https://reactnative.dev/docs/textinput)

[Handle touches](https://reactnative.dev/docs/handling-touches)

下篇待續 ...
