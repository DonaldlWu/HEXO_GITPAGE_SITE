---
title: 用 Swift 做個推播發送器並在 Homebrew 上 Release 
date: 2018-11-17 20:47:51
tags: [Swift, SPM, Push Notification, Homebrew]
---

上個月的 [iPlayground](https://medium.com/iplayground/iplayground-2018-slides-11587ebdddc5) 真的收穫良多

今天就來記錄一下如何將寫好的 swift 程式變成可執行檔並上傳至 homebrew 供人下載 

~~隔了一個月!!?? 謎之音：沒關係拉，Blog 都能從一月拖到十一月一篇都生不出來了~~

[GitHub 位置](https://github.com/DonaldlWu/DRWApns)

<!--more-->

程式本身並不困難，僅僅是接收使用者在 Terminal 輸入的參數取得發送推播需要的資訊

並拉入現成的 Package [Perfect-Notifications](https://github.com/PerfectlySoft/Perfect-Notifications.git)

幫忙處理發送推播的實作，下次再來詳細討論關於發推播這件事

今天主要來記錄一下將東西放到 homebrew 上這件事

在完成程式的開發後主要的 Release 流程有以下幾點

1. 撰寫 Makefile

2. 將程式 push 至 Github 並 Release

3. 用 brew create 製作 formula file

5. 將 formula push 到一個管理用的 Github Repo

6. 在 terminal 驗證是否能成功下載安裝與執行

### 撰寫 Makefile

首先在專案下新建個 Makefile 檔案

內容如下

```applescript
PREFIX?=/usr/local
PROD_NAME=DRWApns
PROD_NAME_HOMEBREW=DRWApns

build:
  swift build --disable-sandbox -c release -Xswiftc -static-stdlib
build-for-linux:
  swift build --disable-sandbox -c release
install: build
  mkdir -p "$(PREFIX)/bin"
  cp -f ".build/release/DRWApns" "$(PREFIX)/bin/DRWAPns"
run:
.build/release/$(PROD_NAME)
```

接下來就是 Push 到 Github

### Push Repo & Release

````applescript
git push origin master
````

在 Repo 的首頁找到 Releases 的按鈕

進去之後點擊右上新增一個 new release 

![newdraft](https://user-images.githubusercontent.com/28559915/48698417-fe572500-ec21-11e8-9ed0-3ab1edbd44a8.gif)

接下來將游標移到 Source code(tar.gz) 右鍵複製一下鏈結

![copylink](https://user-images.githubusercontent.com/28559915/48698728-ca303400-ec22-11e8-8055-bda6047cbc6a.gif)

###  用 brew create 製作 formula file

接下來打開 terminal 輸入

```applescript
brew create 剛剛複製的鏈結
```

homebrew 就會幫你做好一個 formula.rb 檔案

附上最後我們調整過後需要的部分

```applescript
# Documentation: https://docs.brew.sh/Formula-Cookbook
#                https://www.rubydoc.info/github/Homebrew/brew/master/Formula
# PLEASE REMOVE ALL GENERATED COMMENTS BEFORE SUBMITTING YOUR PULL REQUEST!
class Drwapns < Formula
    desc "A tool for sending push notification to iOS devices"
    homepage ""
    url "https://github.com/DonaldlWu/DRWApns/archive/1.0.5.tar.gz"
    sha256 "ad5b27f1b5ca929311ec5873b4ecc61d2ca7aaa496531b1b9024f9a714c2020a"
  def install
    system "make", "install", "PREFIX=#{prefix}" # if this fails, try separate make/make install steps
  end

  depends_on :xcode => ["9.2", :build]
end
```

接下來在 Github 上創一個負責集中管理 .rb 檔案的 Repo

並將改好的 .rb 檔 push 上去

### 驗證下載

一樣來到 Terminal 輸入

```applescript
brew install {.rb 檔 repo 的名稱}/{.rb 檔名}
```

以我的 Repo 當範例就是

```applescript
brew install DonaldlWu/homebrew-taps/drwapns
```

<img width="1038" alt="2018-11-18 9 20 16" src="https://user-images.githubusercontent.com/28559915/48673004-06f32100-eb78-11e8-8e15-c14f072ed3dc.png">

最後移動到 ``/usr/local/Cellar/{rb檔名}/{release version}/bin``

並執行編譯好的的執行檔 

Done!!!



