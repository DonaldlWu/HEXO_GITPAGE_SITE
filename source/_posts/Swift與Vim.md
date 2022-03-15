---
title: Swift 與 Vim
date: 2022-03-15 14:38:02
tags: [Vim]
---

在 Apple 官方推出 [sourcekit-lsp](https://github.com/apple/sourcekit-lsp) 之後

我就一直在尋找一個使用 Vim 來完成一些日常 Swift 使用的流程

雖然說在使用 UIKit 的自動補完上目前還是悲劇

但拿來取代個 Playground 應該是綽綽有餘

目前配置上是使用 nvim + [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig) 配合 sourcekit-lsp

加上 [lspsaga.nvim](https://github.com/glepnir/lspsaga.nvim) 的漂浮視窗來做輸出驗證

<!--more-->

使用上可以簡單的打開一個 .swift 檔案

直接呼叫 Terminal 執行

```
Swift {filename}
```

<imgsrc=https://dl.dropboxusercontent.com/s/71n0wvay4vsoj6i/2022-03-15%2014.56.57.gif>


或是開一個新的 Swift Package

```
cd {SomeFolder}
swift package init
```

就可以順道利用裡面的 XCTest 模組來利用測試產出想要的程式碼

寫完一樣叫出 Terminal 然後執行

```
Swift test
```

就可以看到結果拉

<imgsrc=https://dl.dropboxusercontent.com/s/g5ej6wc1ipe9yfr/2022-03-15%2015.04.15.gif>

