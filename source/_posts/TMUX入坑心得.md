---
title: Tmux入坑心得
date: 2021-09-08 18:31:09
tags: [tmux]
---

自從拿到新的 M1 Mac，免不了是要設定一下環境啦

在過去的機器上我的終端是使用 iterm2 

換機器順便摸摸新東西

主要是覺得切換作業區跟分類的方式是我比較喜歡的

這篇主要就是紀錄一下我個人常用的功能

安裝和介紹 google 非常多，這裡就不多重複了

<!--more-->

首先是 key 鍵，我把預設使用的 `Ctrl + c` 改成了 `Ctrl + a`

然後調整了一下複製貼上的設定

`Ctrl + a + [` 進入複製模式，然後跟 vim 一樣按下 v 開始選取需要複製的範圍(加上 `Ctrl + v` 開啟垂直選取)  

按下 y 確定複製，同時複製到系統剪貼簿

而且只要在 Tmux 底下都可以用，開著 vim 也可以輕鬆的共用，相當方便．

<img src=https://dl.dropboxusercontent.com/s/lbges95s9ad53eb/tmuxcopy.gif>



然後就是切換 Window 與 Session 了

`Ctrl + a + w` 個人覺得這樣非常清楚知道整體狀況非常舒服

<img src=https://dl.dropboxusercontent.com/s//oeldggnr94oqwin/tmuxSplit.png>


螢幕切割其實我並沒有很常用，我通常喜歡直接開全新 Window 或 Session

但偶爾需要又忘記的時候就按下 `Ctrl + a + ?` 直接找


最後先附個 tmux.conf 裡的設定

```
#Send prefix
set-option -g prefix C-a
unbind-key C-a
bind-key C-a send-prefix

# Shift arrow to switch windows
bind -n S-Left previous-window
bind -n S-Right next-window

# Mouse mode
set -g mouse on

setw -g mode-keys vi
set-option -g default-command "reattach-to-user-namespace -l zsh"
bind-key -T copy-mode-vi 'v' send-keys -X begin-selection
bind-key -T copy-mode-vi 'y' send -X copy-pipe-and-cancel "reattach-to-user-namespace pbcopy"
bind-key -T copy-mode-vi 'r' send-keys -X rectangle-toggle

# Set easier window split keys
bind-key v split-window -h
bind-key h split-window -v

# Easy config reload
bind-key r source-file ~/.tmux.conf \; display-message "tmux.conf reloaded"
```

再來預計也將 vim 換成 nvim 

原因是看到一些我覺得很好玩的東西

當初把自動產生預設文章格式的 Script 寫好生成這篇之後...

這篇就原樣躺在電腦裡兩個月以上有了，下一篇不知道... 呵呵

