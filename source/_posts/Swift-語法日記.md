---
title: Swift 語法日記 -  記憶體管理
date: 2017-06-08 10:35:02
tags: [ Swift ]
---

## Swift記憶體管理 ##

Swift當中，引用類型(class)儲存在(棧)stack上，而值類型(enum, struct)儲存在(堆)裡面，而這兩個的定義為

### stack ###

系統自動分配之記憶體，空間較小但運行較快，遵循LIFO原則，透過移動stack尾端的指針去實現pop與push操作

在執行一個方法需要在stack上為此方法開闢新的記憶體空間，stack的尾針指向stack底端移動

當執行完畢之後再將指針移回stack頂端釋放掉這些空間．
<!--more-->

### heap ### 

相較stack有較大的空間，但運行也相對的較慢，優點在於可以動態分配，不像stack中調用完直接回收

系統會在heap上尋找不再需要的記憶體(Swift中使用了ARC來幫我們處理)，再多線程環境下，多線程會共享heap上的記憶體

為了確保線程安全，必須在heap上進行加鎖操作，但這動作相對浪費效能．

以一個範例來做說明

首先是我們定義一個值類型的struct並實現它

```swift
struct ValueType {
	var a, b: Double
}

let value1 = ValueType(a: 3, b: 4)
var value2 = value1

```

`value1`與`value2`會在stack上各分配到一塊記憶體，這兩者的儲存是獨立的，意思是你不必擔心兩者會互相影響

例如下面我們修改了`value2`的值，然後再去印出`value1`的值來觀察

```swift
value2.a = 100
print(value1.a)
```

可以發現印出來的依然是`value1`原來的值3.0

再來我們用class再來定義一次試試

```swift
class ValueType {
    var a, b: Double
    init(a: Double, b: Double) {
        self.a = a
        self.b = b
    }
    
}
let value1 = ValueType(a: 3, b: 4)
```
由於這次`ValueType`是class因此並不會直接將資料存在stack上，而是在stack上開闢兩個指針

由這兩個指針負責去尋找被儲存在heap上的資料，而當我們指定

```swift
var value2 = value1
```

`value2`的指針就會跟`value1`的指針指向同一位置，因此我們可以知道，在引用類型的賦值並不會發生拷貝的動作

所以當你修改`value2`的內容時也會導致指向同一位置的`value1`一同變動．

p.s.在這裡我們使用了`let`關鍵字來定義`value1`，限制它為一不可變動的值，但在引用類型中，不可變動的只有

裡面所儲存的指針位置保證不會發生變化，並不代表所指向的數值不會發生變動．

