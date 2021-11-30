---
title: nvim + shell script 漂浮視窗快速筆記
date: 2021-11-30 11:10:16
tags: [nvim, BASH, lspsaga]
---

雖然 mac 本身的 notes 就相當不錯

甚至在更新到 Monterey 之後也新增了*隨處可用* 的功能

第三方也有很多好用的軟體 Notion, hackmd 等等

但身為一名懶惰到不想把手移動到觸控板上的人來說

每次有些想法想快速紀錄一點想法

按下 `Option + Space` 叫出 Alfred 快速搜尋叫出 notes 或其他軟體之後的操作總覺得就是差了一點點

因此就決定自己來調整看看

<!--more-->

所以首先要確定目標

*Target: 鍵盤可以獨自完成從叫出筆記 -> 寫筆記 -> 關閉筆記這一連串動作，並繼續原本的作業*

再來確認一下現況

1. 目前切換 app 都是使用 Alfred 搜索進行切換
2. notes 或 Notion 、hackmd 看起來後續操作都不太方便

依照以上整理，切換看起來不會是大問題，問題是目前我用過的筆記軟體純用鍵盤的操作我都不太習慣

既然這樣，那就換一款我熟的吧

nvim + [lspsaga](https://github.com/glepnir/lspsaga.nvim)

看來是個不錯的選項

nvim 跟 tmux 的 terminal 可以提供我熟悉的文檔操作

而 lspsaga 可以提供漂亮的漂浮視窗並呼叫出 terminal

可以叫出 terminal 也就代表我們可以執行自訂的 script 來做事

---

那這個 script 我希望可以做到幾件事

    1. 用日期為檔名建立一個新的 .md 檔案(如果檔案已存在就直接執行 2)
    2. 用 nvim 打開檔案，
    3. 並新增一行時間標題
    4. 移動至下一行並置中
    5. 開啟 insert mode


OK，那接下來就是要實作的細節

1. lspsaga 叫出漂浮視窗叫出 teminal 並執行 script

2. 新增 notetaker.script 執行 *新增/編輯* 筆記文檔

根據 lspsaga 的文件，我們可以在設定檔內加上

`nnoremap <silent> nt <cmd>lua require('lspsaga.floaterm').open_float_terminal('notetaker.sh')<CR>`

這樣只要在 nvim 的 normal mode 依序按下 nt 就會跳出 terminal 並執行 `notaker.sh` 這個命令了

然後就是關鍵的 Script 內容

```script
#/bin/bash

noteFilename="/Users/wuderen/Desktop/Note/note-$(date +%Y-%m-%d).md"

if [ ! -f $noteFilename ]; then
    echo "# Notes for $date"
fi

nvim -c "norm Go" \
    -c "norm Go## $(date +%H:%M)" \
    -c "norm G2o" \
    -c "norm zz" \
    -c "startinsert" $noteFilename
```

---

然後來執行吧

相信一定會很順利的...失敗

首先要先把 `notetaker.sh` 放到 `/usr/local/bin` 

讓這個 script 可以順利在全域都可以執行

然後調整一下檔案的權限，不然也是沒辦法在跳出來的終端上執行的 

`chmod u+x notetaker.sh`

完成之後就可以來試試看拉

按下 tr 打開檔案 -> 編輯文檔 -> `Option + Q` 離開

<img src=https://dl.dropboxusercontent.com/s/5g22qc0gll4phxu/notetaker.gif>

用起來還不錯，但感覺編輯文檔 q 之後可以直接把整個漂浮窗關乾淨會更好

有空再來進一步研究

<br>

參考資料

1. [Calin Leafshade: Note Taking in vim with markdown and pandoc and bears, oh my!](https://www.youtube.com/watch?v=zB_3FIGRWRU&t=274s)
