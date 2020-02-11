---
title: 測試好幫手-Fabric
date: 2017-11-26 16:06:40
tags: [Fabric]
---

在開發軟體的過程中，測試絕對是相當重要的一環

iOS開發者絕對聽過的TestFlight就是apple提供不錯的工具

當然也有不少其他工具可以滿足各式各樣測試的需求

本篇就來介紹最近開始使用並覺得相當不錯的Fabric

<!--more-->

一樣先從Fabric可以幫我們做到什麼功能開始

首先Fabric可以讓你發佈你的app並發送郵件給你指定的測試人員下載

並且配合Crashlytics可以在發布的app發生Crash時，發送email給你告知你有問題發生了

那麼接下來我們就來一步一步設定Fabric吧



第一步先到apple的開發者網站設定一下

這裡的範例因為是全新的app所以我新建了一個appID以及Provisioning Profiles

如果已經有了就不需要再做了，視情況調整即可

1.建立appID

{% asset_img /1.png %}

2.建立Provisioning Profiles，這裡我只拿來測試因此選擇Development

{% asset_img ~/2.png %}

3. 選擇appID，就選剛剛建立好(要測)的那組

{% asset_img /3.png %}

最後就照著系統提示選擇你的證書、註冊的裝置(之後可以另外加)跟幫這個Provisioning Profile取個名吧



[接下來去官網下載Fabric的mac app並順便申請一個帳號吧](https://get.fabric.io/)

[然後跟著官方教學進行設定](https://fabric.io/kits/ios/crashlytics/install)

登入後教學頁面上的api key就會幫你設定成你的帳號要用的摟，很方便ＸＤ

接下來就進Fabric的app來看看吧

登入之後選擇專案

{% asset_img /4.png %}

選擇第一項Crashlytics install

{% asset_img /5.png %}

因為已經經由cocoapods安裝完了，因此點擊左邊的icon

這兩步都已經先完成了就Next按下去吧

{% asset_img /6.png %}
{% asset_img /7.png %}

再來打開專案，按下command + B編譯

{% asset_img /8.png %}

完成後按下command + R把專案跑起來一次，這時Fabric的app就會有你的專案可以選擇了

{% asset_img /9.png %}

到這邊設定都完成了，之後完成一個版本想要傳送給測試人員要怎麼做呢？

其實只要Arciive你的專案

{% asset_img Archive.gif %}

app自己就會跳出可以上傳的提醒

{% asset_img /10.png %}

接下來可以輸入要傳送給哪些測試人員的email

{% asset_img /11.png %}

還可以寫好給測試人員的訊息

{% asset_img 12.png %}

送出之後就開始上傳拉，上傳完成測試人員就會接收到郵件請測試人員下載app囉

{% asset_img 13.png %}

p.s. 如果archive玩沒有提示(或是提示的版本不是最新版)

可以打開Fabric app裡面的archive的欄位按下發布就可以瞜



最後，如果是第一次加入的測試人員，系統email會提示測試人員回傳他們測試手機的UUID

開發人員只要去apple開發者網站加入那隻手機就ＯＫ了(下圖中Device那邊)

{% asset_img 14.png %}

之後只要有新的Archive就可以用Fabric上傳並且發佈拉～～～




