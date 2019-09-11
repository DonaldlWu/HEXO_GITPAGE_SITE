---
title: 無聊實驗室：用vim寫swift
date: 2018-01-01 22:05:27
tags: [vim]
---

目前想要開發Swift項目，Xcode絕對是第一個會被拿出來的選項

但不用Xcode能不能用來寫Swif呢？

喂了喂估狗大神，看到了一些蠻有趣的結果

本篇就來配置一下用vim編輯.swift文檔，並編譯出簡單的結果吧

<!--more-->

首先，要編譯當然還是需要Xcode裡面的編譯命令拉，這樣到底意義在哪裡拉ＸＤ （謎之音：潮啊)

打開終端機，輸入

```
$ sudo xcode-select -switch /Applications/Xcode.app
```

其中的路徑請根據您的Xcode.app所在路徑做調整

設置完成後輸入

```
xcrun swift
```

這樣就可以使用xcrun幫我們編譯摟

{% asset_img swift.png %}

在這模式下可以輸入`:help`查看幫助，要離開則可以輸入`:exit`

如果只是簡單的.swift檔案想看編譯結果可以直接執行

```
xcrun swift example.swift
```

但是這當然不是我們最終的目標，我想看到的是視窗上半部我可以開著vim編輯文件

視窗下半部則是隨時顯示編譯的結果，但這樣就需要一個幫手在我儲存文件時去執行swift的編譯

每次都要再打一次`xcrun swift `實在是不科學

[這邊的Guard就可以幫我們解決這個問題]( https://github.com/guard/guard )

用gem安裝一下，終端機輸入

```
gem install guard-shell
```

安裝完之後來試用一下吧，將終端機位置移動到想要編譯的.swift文檔目錄下

執行

```
guard init shell
```

這樣便會產生一個Guardfile文件，貼入以下內容

```
guard :shell do
  watch(/(.*).swift/) do |m|
    puts "-" * 10
    puts "Start Building"
    puts "-" * 10
    puts `xcrun swift #{m[0]}`
    puts "-" * 10
    puts "Build Ended"
    puts "-" * 10
  end
end
```

主要就是在觀測到.swift檔案編輯時印出分隔線並執行`xcrun swift`

要啟動就在終端機目錄下輸入`guard`

Guard就會開始依照Guardfile裡面的設定監看所在目錄下的變動了

實際使用就看看下面的gif吧

{% asset_img guard.gif %}

不過到這裡其實最最最麻煩的問題其實是

現在沒有autocomplete要寫code太困難了啊!!!!

為了解決這個問題，繼續餵狗的結果我幫我的vim裝了以下兩個插件

[ YouCompleteMe ]( https://github.com/Valloric/YouCompleteMe )

[ vim-swift ]( https://github.com/toyamarinyon/vim-swift )

試用的結果...離實際能拿來開發還有極度遙遠的距離，慢慢研究吧ＱＱ

2018第一篇廢文就到此結束，找資料跟實際安裝的過程中也有不少新的發現

期許今年的自己能夠產出更多的廢文，有更多的發現．


