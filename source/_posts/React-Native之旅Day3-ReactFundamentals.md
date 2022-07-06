---
title: React-Native之旅Day3-React Fundamentals
date: 2022-07-06 21:26:31
tags: [React-Native]
---

接下來繼續跟著文件來認識 React Fundamentals

React-Native 是基於 React 發展的技術

因此深入瞭解 React 如何運作也是學習 React-Native 的好方法

<!--more-->

首先我們會來了解 React 的幾個核心觀念

- components

- JSX

- props

- state

[React's official documentation](https://reactjs.org/docs/getting-started.html)

## First component

接下來我們就跟著文件來寫第一個 component 

### Function Component

```JSX
// 1
import React from 'react';
import { Text } from 'react-native'

// 2
const Cat = () => {
  // 4
  return (
    <Text>Hellow, I am your cat!</Text>
  );
};

// 3
export default Cat;
```
1. import

要使用 Function Component 的方式定義一個 `Cat` Component

首先會需要 import React 以及原生 Text Component

2. 此 Component 從一個 fuction 開始

我們可以將 components 當作一個藍圖，而 function 會 return 一個 React element

這個 React element 即用來渲染你的畫面

<br>

在這裡 return 一個 Text element 表示我們會在畫面上渲染一個 Text 元件

3. 為了讓其他地方可以引用這個 Component，這裡使用了 `export default`

> [導出元件整理](https://www.samanthaming.com/tidbits/79-module-cheatsheet/)

4. 最後我們來看看 return 語句

`<Text>Hellow, I am your cat!</Text>`

這是種簡化 React 元素的寫法，我們稱為 JSX

<br>

### Class Component

> Class components 寫起來稍微比 Function Component 繁複一點

```JSX
// 1
import React, { Component } from 'react';
import { Text } from 'react-native';

// 2
class Cat extends Component {
  // 3
  render() {
    // 5
    return (
      <Text>Hellow, I am your cat!</Text>
    );
  }
}

// 4
export default Cat;
```

1. 首先我們要從 React import `Component`

2. 這個 Component 從一個 class extending 開始

3. Class 會有一個 `render()` 函數，裡面也是會 return 一個 React element

4. 如同 Function component，這裡也需要將元件 export 出來

5. JSX

## JSX

JSX 語法讓我們可以在 JavaScript 內建構元件

[A comporehensice guide to JSX](https://reactjs.org/docs/jsx-in-depth.html)

而且 JSX 其實也是 JavaScript，我們可以在語法裡使用變數

```JSX
import React from 'react';
import { Text } from 'react-native';

const Cat = () => {
  const name = "Maru";
  return (
    <Text>Hello, I am {name}!</Text>
  );
}

export default Cat;
```

如上範例，我們可以用括號來引入變數

而括號內也可以使用 JavaScript 表達式

```JSX
import React from 'react';
import { Text } from 'react-native';

const getFullName = (firstName, secondName, thirdName) => {
  return firstName + " " + secondName + " " + thirdName;
}

const Cat = () => {
  return (
    <Text>
      Hello, I am {getFullName("Rum", "Tum", "Tugger")}!
    </Text>
  );
}

export default Cat;
```

> JSX 語法需要引入 `react`

## Custom Component

我們也可以將原生組件組合再一起做出我們的客製化元件

```JSX
import React from 'react';
import { Text, TextInput, View } from 'react-native';

const Cat = () => {
  return (
    <View>
      <Text>Hello, I am...</Text>
      <TextInput
        style={{
          height: 40,
          borderColor: 'gray',
          borderWidth: 1
        }}
        defaultValue="Name me!"
      />
    </View>
  );
}

export default Cat;
```

也可以將做好的元件包入其他元件

```JSX
import React from 'react';
import { Text, View } from 'react-native';

const Cat = () => {
  return (
    <View>
      <Text>I am also a cat!</Text>
    </View>
  );
}

const Cafe = () => {
  return (
    <View>
      <Text>Welcome!</Text>
      <Cat />
      <Cat />
      <Cat />
    </View>
  );
}

export default Cafe;
```

如果有個元件包含這其他元件，則此元件被稱為父元件

被包含的則稱為子元件

在這裡每次呼叫 <Cat> 渲染都是不同元件

意味者我們可以使用不同的 props 來客製化他們

## Props

就像給貓取名字，我們可以給元件定義 props

```JSX
import React from 'react';
import { Text, View } from 'react-native';

const Cat = (props) => {
  return (
    <View>
      <Text>Hello, I am {props.name}!</Text>
    </View>
  );
}

const Cafe = () => {
  return (
    <View>
      <Cat name="Maru" />
      <Cat name="Jellylorum" />
      <Cat name="Spot" />
    </View>
  );
}

export default Cafe;
```

大多數的 React 核心元件都可以通過 props 來完成客製化

例如 Image 可以從訂製圖片來源

```JSX
import React from 'react';
import { Text, View, Image } from 'react-native';

const CatApp = () => {
  return (
    <View>
      <Image
        source={{uri: "https://reactnative.dev/docs/assets/p_cat1.png"}}
        style={{width: 200, height: 200}}
      />
      <Text>Hello, I am your cat!</Text>
    </View>
  );
}

export default CatApp;
```

核心元件通常有多種不同的 props

通常由 property-value pair 的方式設計

> 雙括號用來在 JSX 中傳遞 JS Object

## State 

如果 props 理解成參數，那 State 就是使用紀錄了

通常用來處理用戶交互的變化

也就是 props 用來進行初次的元件畫面渲染

然後利用 state 紀錄可能隨時間變化的資料

### Function Component

```JSX
// 1
import React, { useState } from "react";
import { Button, Text, View } from "react-native";

const Cat = (props) => {
  // 2
  const [isHungry, setIsHungry] = useState(true);

  return (
    <View>
      <Text>
        I am {props.name}, and I am {isHungry ? "hungry" : "full"}!
      </Text>
      // 3
      <Button
        onPress={() => {
          setIsHungry(false);
        }}
        // 4
        disabled={!isHungry}
        title={isHungry ? "Pour me some milk, please!" : "Thank you!"}
      />
    </View>
  );
}

// 5
const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
}

export default Cafe;
```

可以利用 Hook 為 component 新增 state

[useState 相關文件](https://reactjs.org/docs/hooks-state.html)

Hook 是種 function 讓我們可以連結至 React 的功能中

例如 `useState` 就是個可以加入 component 的 Hook

[各種 Hooks in React](https://reactjs.org/docs/hooks-intro.html)

1. 首先還是從 react 中 import useState

2. 然後我們就可以利用在 Function 中調用 useState 來幫組建創建變數

> 可以用 useState 紀錄 strings, numbers, Booleans, arrays, objects.

在這邊創建了一個狀態變數 `isHungry` 並給一個初始值 `true`

並創建一個用於改變狀態變量的值 `setIsHugry`

> [<getter>, <setter>] = useState(<initalValue>)

3. 接下來設定了一個 Button 並帶了一個 `onPress` 的初始值

當 `onPress` 被觸發，就會呼叫到 `setIsHungry(false)` 改變到 `isHungry` 的值改變為 false

4. 而當 `isHungry` 為 false，Button 的 `disabled` prop 就會變為 true 並改變 title

> `isHungry` 是使用了 const 關鍵字依然看起來可以被修改的原因是當我們調用 `setIsHungry` 時，所在的 component 將會重新渲染(整個 Cat 會從頭執行，此時 `useState` 會返回新值)

5. 將不同 props 的 `Cat` 放入 `Cafe 元件`

> 這裡`<></>` 是JSX 的 [Fragments](https://zh-hans.reactjs.org/docs/fragments.html)，可以簡化多餘宣告 View 的麻煩

### Class Components

```
import React, { Component } from "react";
import { Button, Text, View } from "react-native";

class Cat extends Component {
  // 1
  state = { isHungry: true };

  render() {
    return (
      <View>
        <Text>
          I am {this.props.name}, and I am
          {this.state.isHungry ? " hungry" : " full"}!
        </Text>
        <Button
          onPress={() => {
            // 3
            this.setState({ isHungry: false });
          }}
          disabled={!this.state.isHungry}
          title={
            this.state.isHungry ? "Pour me some milk, please!" : "Thank you!"
          }
        />
      </View>
    );
  }
}

class Cafe extends Component {
  render() {
    return (
      <>
        <Cat name="Munkustrap" />
        <Cat name="Spot" />
      </>
    );
  }
}

export default  Cafe;
```

1. 與 Function 方式不同的是 state 以 object 方式存放

2. 元件中獲取 state 是通過 `this.state`

3. 修改狀態僅需 `this.setState()` 傳入要修改的 key-value 物件即可

> 不要給 state 直接賦值，使用 `this.setState` 才能讓 React 知道狀態的變化且觸發渲染。

接下來就與 Function 類似拉

<br>

下篇繼續來學習更多核心元件用法
