---
title: 紀錄APP功能回顧
date: 2017-08-24 08:53:09
tags: [Swift]
---

因為中間偷懶沒寫文章，有不少是加上去但前面沒提到的

所以稍微來回顧一下目前這個棒球紀錄APP時做了哪些功能

<!--more-->

首先是註冊

可以讓使用者選擇照片

以及填入帳號資訊連接到Firebase後台進行註冊(或登入)

在註冊頁面當使用者點擊註冊紐時會先判斷使用者是否有輸入資訊

如果有沒輸入資訊的欄位會把placeholder變成紅色提醒使用者

如果都有輸入就會連接Firebase確認email格式

如果都沒有問題就完成註冊將使用者送入首頁

{% asset_img RegisterDemo.gif %}

登入介面一樣會檢查空欄位

都有資訊就連接Firebase確認是否為註冊過的用戶

以及密碼是否正確

{% asset_img LoginDemo.gif %}

在首頁可以進行新增球隊的功能

會先跳出一個視窗讓使用者輸入新隊名

輸入完會讓使用者可以選擇隊伍照片

按下START會將隊伍資訊傳送到Firebase

然後把使用者帶回首頁，在首頁會去Fetch Firebase的隊伍資料

所以在首頁就會看到剛剛設定的隊徽跟隊名

{% asset_img AddTeamDemo.gif %}

接下來點擊新增好的隊伍欄位，會將使用者帶到隊伍編輯頁面

按下右上角ADD可以進行新增球員的動作

在新增球員頁面可以讓使用者選擇球員的照片、守備位置、背號、姓名

一樣在按下新增鈕之後會將資料送到Firebase的資料庫

而在隊伍編輯頁面一樣會去Firebase尋找這支隊伍的球員資料

{% asset_img AddMemberDemo.gif %}

接下來在新增完球隊的成員們之後

可以看到右邊有顯示的守備位置的按鈕，點擊可以進行守備位置的變更

將球員的欄位向左滑動可以看到兩個按鈕

一個是LineUp，可以指定準備要開始的比賽中這位球員的棒次

另一個是Cancel可以取消指定的棒次

最後將整個表單向下拉，會跳出選項來排序目前球員排列的方式

目前提供依照背號數字從小排到大以及從大排到小與依照棒次三種方式

{% asset_img MemberControllerDemo.gif %}

當選擇完至少九棒的球員之後就可以按下下方變色的開始按鈕

並選擇是先攻或者先守準備開始一場新的比賽紀錄

進入紀錄頁面可以看到剛才設定的球員、棒次以及守備位置，還會先生成好敵對的欄位

可以再下方TabBar選擇第二欄來記錄對手打席紀錄

按下UPDATE按鈕，就會跳出選項來記錄球員打席的結果

{% asset_img RecordControllerDemo.gif %}

再來是一場棒球比賽時間很長，不太可能長時間只開著這款APP

因此就需要將資訊即時儲存起來

這裡使用了CoreData來儲存兩隊的資料

並使用UserDefault來儲存是否正在比賽之中的狀態

使用者打開APP到首頁就會自動檢查是否有未完成的紀錄

如果有就會從CoreData裡拿出資料並進入紀錄頁面

{% asset_img CoreDataDemo.gif %}

最後是比賽狀態頁面

先用一個UIView上面疊上CollectionView來做出計分板的效果

不過資料目前還沒接上

{% asset_img ScoreControllerDemo.gif %}

現在的進度就到這邊為止

[ GitHub ]( https://github.com/DonaldlWu/HBCRecord )

