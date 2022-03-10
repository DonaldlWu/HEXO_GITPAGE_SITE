---
title: 關於App Store上架圖，你可能想知道的那些事
date: 2022-03-09 21:53:16
tags: [上架圖]
---

在公司，要上架的時候通常都有 Design Team 提供設計好的上架圖。

但當你作為一名獨立開發者，這些事就都得自己處理拉。

抑或是你家設計初來乍到對 App Store 上架圖的規則不太了解，你得跟他解釋一下需求。

這邊就來統整一下過去我碰過的問題，以及一些好用的工具。

<!--more-->

### 1. 我需要哪些尺寸的上架圖？ 
---

請看 Apple 提供的 [Screenshot specifications](https://help.apple.com/app-store-connect/#/devd274dd925)

細看 Screenshot specifications 裡提供的資訊，

我們可以依據 Requirement 以及 Screenshot source 這兩個欄位裡是不是必要，是不是有預設值來決定我們要提供哪些尺寸的截圖。

依照目前蘋果的要求，如果我想上架一款 iOS app，那我會需要提供 

|6.5 inch | 
|---------|
| 1284 x 2778 pixels (portrait)<br>2778 x 1284 pixels (landscape)<br>1242 x 2688 pixels (portrait)<br>2688 x 1242 pixels (landscape) 

<br>

| 5.5 inch |
|----------|
|1242 x 2208 pixels (portrait)<br>2208 x 1242 pixels (landscape) |

<br>

如果 iPad 也可以用的話，加上

|12.9 inch | 
|----------|
|  2048 x 2732 pixels (portrait)<br>2732 x 2048 pixels (landscape)|

<br>

### 2. 調整圖片大小的工具網站
---

[iloveimg](https://www.iloveimg.com/zh-tw)

這網站把圖丟進去就可以自己調整需要的大小拉。

這方法簡單粗暴，可謂獨立開發者的好朋友。

<br>

### 3. iPhone 外殼？
---

[App Store Screenshot Generator](https://www.appstorescreenshot.com)

這網站可以讓你選擇一些裝置外殼，加上背景以及一些廣告詞。

而 [MockUPhone](https://mockuphone.com) 可以幫你的圖片單純的加上手機殼，並產生直立跟橫躺兩種圖片。

<br>

### 4. 我的 Side Project 想自己想畫圖？
---

作為一款免費工具我想 [Figma](https://www.figma.com/?fuid=) 是個不錯的選擇。

畫完還能利用他提供的 Export 功能把圖輸出。

<br>

### 5. 圖像不能包含alpha通道或透明度？
---

簡單處理可以用 preview 打開圖片 file -> export 把 alpha 的勾選清掉

<br>

### 6. 因為圖片被退件？

我遇過的是外框沒有依照尺寸給，我統一都用 6.5 inch 瀏海家族的 iPhoneX 外殼放在 5.5 inch 圖上然後就被退了🤪

