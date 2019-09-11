---
title: 後台使用者註冊&登錄系統Demo
date: 2017-08-11 21:33:36
tags: [BackEnd]
---

這段時間除了持續在swift的練習之外

也終於開始下手往後台的功能開始摸索

雖然有看了一些vapor的資料跟練習了一下

但想想剛開始還是用已經比較多人用過的方式來寫

先把基礎打好之後要換應該也不難吧ＸＤ

問題是所以我說那個基礎勒ＱＱ

<!--more-->

第一個Demo先試著使用XMAPP幫我整合Apache、MySQL跟PHP然後跑在我的電腦上

然後利用postfix做認證信的傳送

目前先做好了假的Twitter註冊跟登錄系統

大致上的流程就是使用者輸入帳號密碼跟姓名與電子郵件進行註冊

Server這邊收到之後把使用者資料存到Database的users這個table內

並且在emailTokens這個Table裡面存入這名使用者的id與註冊用的token

然後發送認證郵件給使用者，使用者接收到這個有token的email後就可以進行驗證

驗證成功就把database的emailTokens裡面的token刪除

並把user的emailConfirmed這個欄位從0改成1

實際操作如下

先在iOS端輸入註冊資訊，傳輸成功iOS端會收到回傳的JSON資訊

{% asset_img BackEndDemoPart1.gif %}

在後端會把輸入的資訊放到users裡面，也會在emailTokens裡放入此id的token

{% asset_img BackEndDemoPart2.gif %}

最後在使用者接受到認證信並按下確認之後會將後台的token刪除

並更改emailConfirmed確認這名使用者完成註冊

{% asset_img BackEndDemoPart3.gif %}



