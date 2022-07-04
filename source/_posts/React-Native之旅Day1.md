---
title: 從文件開始React-Native之旅Day1
date: 2022-07-04 21:32:03
tags: [React-Native]
---

因為工作需要，開始要跟 React-Native 打交道拉

那要學習一項技術，[官方文件](https://reactnative.dev/docs/getting-started)總是要面對的

但總覺得年紀大了過目就忘

自己有時做筆記還是太精簡了

那不如就寫成 Blog 順便拯救我那可悲的更新頻率🤪

<!--more-->

### 設定環境

首先就進入導覽首頁看看摟

看沒兩句果然還是需要先去[設置一下環境](https://reactnative.dev/docs/environment-setup)

文件已經蠻清楚了，m1 的 mac 也沒遇到太大問題

安裝 node, Xcode, cocoapods

然後創建一個新的初始 App

```
npx react-native init AwesomeProject
```

然後先啟動 `Metro` 看來是幫我們搞定 `javaScript` 一些 bunding 設定的工具

> If you're familiar with web development, Metro is a lot like webpack—for React Native apps. Unlike Swift or Objective-C, JavaScript isn't compiled—and neither is React Native. Bundling isn't the same as compiling, but it can help improve startup performance and translate some platform-specific JavaScript into more widely supported JavaScript.

```
npx react-native start
```

然後就可以把 App 跑起來看看

```
npx react-native run-ios
```
<br>

接下來文檔提到了該如何使用這份文件，可以線性的看下去

又或者對 React 有了解的可以跳過一些部分

但對我來說就是看下去拉

<br>

前置作業，文檔告訴我們至少要懂些 javaScript 

<br> 

玩一下慣例的 [Hello World 範例](https://snack.expo.dev/) ~~預設不是 Hello World 差評~~

<br>

### Function Components and Class Components

原本只有 class components 有 state，在 0.59 有了 Hooks API 之後 function components 也可以有 state 了

因此後面範例官方也會附上兩種方式的寫法

以前面的 Hello World 為例

Function Component
```javaScript
import React from 'react';
import { Text, View } from 'react-native';

const HelloWorldApp = () => {
  return (
    <View style={{
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center'
    }}>
      <Text>Hello, world!</Text>
    </View>
  );
}

export default HelloWorldApp;
```

Class Component
```JavaScript
import React, { Component } from 'react';
import { Text, View } from 'react-native';

class HelloWorldApp extends Component {
  render() {
    return (
      <View style={{
          flex: 1,
          justifyContent: "center",
          alignItems: "center"
        }}>
        <Text>Hello, world!</Text>
      </View>
    );
  }
}

export default HelloWorldApp;
```

後面就是提醒你在文章裡會有不同領域開發者熟悉的想法出現在文檔中可以查看

跟選單路徑會用粗體呈現

Introduction 就到這邊拉

下篇待續...


