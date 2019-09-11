---
title: iOS Jenkins之旅Part1
date: 2017-11-06 21:48:24
tags: [Jenkins]
---

雖然老早就想開始寫測試，學習持續整合(Continuous Integration)的東西

但無奈人懶沒藥醫

{% asset_img 我也很絕望.jpeg %}

拖了超久才總算踏出這第一步

雖然做這個好像也只是為了讓自己能少更多事ＸＤＤ

<!--more-->

Jenkins的各種功能我就不贅述了，只介紹我有用到的部分

其他請自行求教估狗大神(其實是我也沒懂多少LuL)

這邊主要會分成三個部分來記錄我Jenkins的使(ㄕㄨㄚˇ)用(ㄅㄣˋ)經過，順便加深記憶

今天的第一部份是Jenkins的安裝

第二部分則是插件的安奘以及最重(ㄎㄥ)要(ㄖㄣˊ)的設定

最後是簡單的Shell指令完成編譯、打包、上傳app store的動作

途中會順便介紹一些踩過的坑

其實到最後也是拼拼湊湊達到需要的功能

有錯跪求路過好心大神指點ＱＱ


首先就來安裝吧

Jenkins需要在java環境下運行，沒裝的童鞋記得去載個JDK裝一下

我是用homebrew進行安裝

[沒homebrew ＬＵＬ](https://brew.sh/index_zh-tw.html)

其實也有懶人安裝包可以使用，沒差多少，有興趣可以找一下

打開終端機，輸入

```
brew install jenkins
```

安裝成功後用瀏覽器開啟

```
http://localhost:8080/
```

如果沒有東西可以嘗試以下指令開啟服務

```
sudo launchctl load /Library/LaunchDaemons/org.jenkins-ci.plist
```

停用

```
sudo launchctl unload /Library/LaunchDaemons/org.jenkins-ci.plist
```

之後需要開啟Jenkins只要打開終端機輸入Jenkins即可

反之關掉終端機即停止服務



進入Jenkins我以為我應該會看到類似這樣子

{% asset_img Jenkins.png %}

結果竟然是這樣子

{% asset_img UnlockJenkins.png %}

{% asset_img 嚇到吃手手.jpg %}

{% asset_img 嚇到吃手手.jpg %}

{% asset_img 嚇到吃手手.jpg %}

太驚慌了害我都忘記截圖，只好再跟估神借幾張圖來擋擋

冷靜下來吃個手手壓壓驚，然後喂個狗

原來是安裝完的訊息被我忽略掉的樣子

{% asset_img 假的眼睛.jpeg %}

理論上如果是mac他會把初始的密碼存在/Users/“yourUserName”/.jenkins/secrets/initialAdminPassword裡面

因此再用終端機輸入(“yourUserName”請改成自己mac的使用者名稱)

```
cat /Users/“yourUserName”/.jenkins/secrets/initialAdminPassword
```

開始的密碼就會印出來拉，而Jenkins的預設使用者是admin

有了資訊就可以登入準備開始使用Jenkins拉～

欲知後事如何，且聽下回分解>.*
