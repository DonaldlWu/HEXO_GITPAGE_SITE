---
title: 無聊實驗室：vim + Codi
date: 2018-01-04 23:14:55
tags: [vim]
---

接下來將來繼續介紹目前我在使用vim的插件

第一篇中我們用xcrun跟guard幫我們即時編譯出.swift的執行結果

而本篇的要介紹的插件Codi則可以幫我們運行多種不同語言的執行結果

<!--more-->

[Github上有詳細演示的gif圖](https://github.com/metakirby5/codi.vim)

安裝完之後輸入

```console
:Codi 語言名稱
```

實際支援的語言請見GitHub

以JavaScript為例，就輸入

```console
:Codi javascript
```

但每次都要打這一長串也挺麻煩的

為了貫徹 懶 還要更懶的目標

就打開.vimrc配置一下快捷鍵吧

```shell
nmap <leader>js :Codi javascript<CR>
```

這樣只要在normal  mode下依序按下leader鍵(我的設定是",")js就可以使用了

以上



