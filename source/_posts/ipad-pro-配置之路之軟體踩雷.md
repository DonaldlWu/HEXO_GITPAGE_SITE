---
title: ipad-pro-配置之路之軟體不專業推薦
date: 2019-09-19 06:29:13
tags: [IPAD]
---

在尋找目標軟體之前先列出我目前會需要使用的情境

 1. 連線到 VPS 主機上確認後臺狀態
 2. git pull code 到本地進行修改, git push
 3. 文件編輯

<!--more-->

首先是用來連線到遠端機器的軟體

目前有嘗試過的有這兩款 [termius](https://apps.apple.com/tw/app/termius-ssh-client/id549039908) 以及[blink shell](https://apps.apple.com/tw/app/blink-shell-mosh-ssh-client/id1156707581)

就需求上來說都能滿足我的使用需求，ssh 連線，key 的管理等等

也都可以直接在 ipad 的檔案裡面進行操作

雖然從根本來說畢竟還是 iOS 的架構，根目錄還是鎖的死死的

如果哪天能直接在 ipad 上有 macOS 的系統就好了 _(´ཀ`」 ∠)_ 

但目前看起來還有得等(阿不是說這台 pro 已經屌打一堆筆電了嗎QWQ，ipadOS...emmmmm...)

目前使用上是以 blink 為主，搭配 siri shortcut 可以很簡單的做到一鍵遠端連線的功能

</br>

接下來就是 git 以及編輯器拉

目前是使用 [Working copy](https://apps.apple.com/tw/app/working-copy-git-client/id896694807) 搭配 [Textastic](https://apps.apple.com/tw/app/textastic-code-editor-8/id1049254261)

進行使用，Working copy 真的已經很完整，我平常會用到的 git 操作都有了

遠端 pull 下來之後 Textastic 也已經支援直接把 Working copy 的 repo import 進來編輯，相當方便

本文的撰寫就是用這樣的搭配下在 Textastic 下進行編輯，非常順暢的書寫體驗

不過身為一名不務正業又不專業的 iOS 工程師這裡還是得說

這樣的組合要寫 iOS 還是有點不舒服，畢竟本地端無法編譯，寫個網頁 Textastic 還有 preview in safari 的功能

iOS 如果拉回本地還得推回去做編譯，還不是推回去就看得到結果 _(´ཀ`」 ∠)_ 

不得不說這是真的硬傷，目前還是乖乖用 mac 吧

不過基本的 iOS 功能實驗，使用 apple 自己推出的 [playground for ipad](https://apps.apple.com/tw/app/swift-playgrounds/id908519492)
就已經相當好用了

一些臨時想到的小功能在這裡實驗應該是綽綽有餘

編輯器這裡還有個小小的遺珠之憾 [buffer](https://apps.apple.com/tw/app/buffer-editor-code-editor/id502633252)

這個 app 兼具 git 與編輯器功能，編輯器甚至支援一部分基礎 vim 指令

剛開始用的非常順暢，但是可惜的是在我拉下我的其中一個專案進行使用時，出現了無法忽視的嚴重 lag

我目前不確定這是否是我專案的問題，但目前只好暫時割愛

不然就綜合使用體驗來說 buffer editer 真的非常棒

</br>


偶爾需要寫一些小文檔，或是臨時的紀錄時，我會使用的 markdown app [Markdown](https://apps.apple.com/tw/app/markdown/id1472328263)

真的就是個輕量的 markdown 文檔編輯器，簡單的畫面，清楚的左編寫右渲染結果呈現

用起來個人蠻喜歡的，也支援直接輸出 markdown 文檔，PDF，web頁面跟截圖功能

以及最近同事推薦的 hackmd.io(https://hackmd.io) 也是相當好用

[Good notes](https://apps.apple.com/tw/app/goodnotes-5/id1444383602) 配上 apple pancel 在文件上面塗鴉些幹話也變得相當簡單

甚至拿來作簡單的流程圖也沒問題(唯一的問題是那隻貴鬆鬆的筆啊)

</br>

這台 ipad 確實很大程度改變了我目前的寫作習慣(原本沒有應該也可以算改變吧_(´ཀ`」 ∠)_)

我可以不背著我的 mac pro 也能瞎掰寫日記了XD

但是以一個工程師來說最根本的問題還是你依舊需要一台主機負責作為最終需要編譯或部署的主角

對於希望能一台 ipad 解決所有工作的人來說還是一個不小的缺憾啊

蘋果拔拔快給我個能編譯的 ipadOS 啊啊啊啊啊啊啊啊啊啊啊～～～～


### 更新 - Hackmd 心得

在使用了一陣 hackmd 之後，更新這個部落格的工作就可以讓 hackmd 直接同步

在 github 上的專案，進行文章的修改，這應該是目前個人用起來最順手的一個方式


### 更新趴兔 - iSH

[iSH](https://ish.app) 這是在尋找各種使用資料時找到的 iOS app

目前尚在 beta 版本，原理應該使直接運行一個 apline linux 系統

系統本身極小，但是相對的就是需要什麼都得自己裝，少了什麼都得自己找

目前更新網站用的 hexo 需要使用的 npm 因為相容性問題尚無法使用

只能默默按下 github 上面 issue 的關注XD

不過本身執行一些 linux 的基本功能確實是順手很多

期待能在 iPad 上面執行多數工作的那一天能快點到來 (シ_ _)シ


