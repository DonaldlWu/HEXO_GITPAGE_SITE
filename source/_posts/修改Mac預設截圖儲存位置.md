---
title: 修改Mac預設截圖儲存位置
date: 2022-07-02 18:27:04
tags: [terminal]
---

近期在用 vim 撰寫 markdown 文檔時感受到其他筆記軟體最大的落差

大概就是無法簡單截圖畫面後拖進文檔裡了吧

但轉念一想，既然都要用 vim 了

還要用滑鼠去拖圖片好像也蠻浪費時間的

那我能不能截圖完就知道圖檔位置直接用 markdown 語法寫入文檔中呢？

<!--more-->

那既然都想到了，就試著改良一下吧

首先我預想的流程如下

1. 截圖

2. 截圖同時複製位置跟檔名到剪貼簿

3. 文檔內貼入鏈結

但目前我找到的資訊在截圖時是可以按住 ctrl 複製到剪貼簿

可以貼上其他有支援的筆記軟體

但我還不知道如何取得我要的資訊

<br>

既然這條路卡住了，那就轉個想法

我知道我的截圖預設會存在電腦某個指定位置

可以按下 command + shift + 5 點擊 option 進行儲存位置的修改

那我可以把流程修改一下

1. 截圖存放到已知位置(放到文檔附近就可以直接取用)

2. 文檔內直接用 markdown 語法叫出圖

<br>

那接下來的問題就是改變截圖儲存位置的動作過於繁瑣

光要改變截圖儲存位置的操作就太久了

如果我可以一步就把儲存位置換到現在文件的路徑底下，也可以一步切回原本預設的位置

使用上應該會更加的方便吧

<br>

經過尋找後確定了以下指令可以在 terminal 進行截圖儲存路徑的切換

```
defaults write com.apple.screencapture location {save_path_youwant}
```

但每次都要手動打入路徑肯定是不能接受的

我希望可以利用 script 跳出一個選單給我進行選擇

我目前應該需要兩個選項

1. 儲存到當前目錄

2. 儲存到預設目錄

這樣我就可以簡單選擇了

<br>

最終完成了這個簡易的 helper script

```
#!/bin/bash
echo "=========== select the operation ============"
echo "  1) Default"
echo "  2) PWD"
 
read n
case $n in
  1) 
    defaults write com.apple.screencapture location ~/Screenshot
    echo "SCREENSHOT WILL SAVE TO: Default";;
  2) 
    defaults write com.apple.screencapture location $(pwd)
    echo "SSCREENSHOT WILL SAVE TO: $(pwd)";;
  *) echo "invalid option";;
esac
```

最後再新增個 alias 到 .zshrc 讓呼叫 script 更加方便

```
alias shot='sh Screenshot.sh'
```

使用起來就會是

1. 輸入 script

2. 選擇儲存位置

3. 開心寫作？(`![filename](filename.png)`)

<img src=https://dl.dropboxusercontent.com/s/btwg4ca94wno98g/2022-07-02%2019.46.39.gif>

至於截圖完能不能再更方便一點 ...

像是目前預設檔名是包含時間的一坨，改名也是挺麻煩的

又或是 snippet 讓圖片插入更輕鬆點

下次有空繼續玩摟。

