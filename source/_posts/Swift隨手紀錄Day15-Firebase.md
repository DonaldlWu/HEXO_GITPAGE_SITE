---
title: Swift隨手紀錄Day15-Firebase
date: 2017-07-06 22:00:41
tags:
---

防止偷懶Day15

為了儲存之後的資料

今天就來設定Firebase的套件

<!--more-->

[ 首先來到Firebase官網的文件 ]( https://firebase.google.com/docs/ios/setup )

來跟著上面的步驟一步一步的來設定

首先用google帳號登入來到console點擊新增專案

{% asset_img Day15NEW.png %}

填入專案名稱跟地點

建立完成就可以看到下面這個畫面

{% asset_img Day15Add.png %}

點選加入iOS

{% asset_img Day15Add2.png %}

接下來打開Xcode找到Target下面的Bundle Identifier

{% asset_img Day15Bundle Identifier.gif %}

填入iOS繫結ID之後點註冊應用程式

{% asset_img Day15Add3.png %}

接下來就把GoogleService-info.plist下載下來並放到Xcode裡面

{% asset_img Day15AddGSInfo.gif %}

接下來就邊看接下來該做的步驟邊按完成到結束吧

{% asset_img Day15Add4.gif %}

CocoaPods的安裝這裡就先跳過（偷懶，跪謝谷歌大神）

直接用CocoaPods安裝Firebase的套件

先打開終端機移動到Project的目錄下輸入

```terminal
pod init
```

{% asset_img Day15Podinit.gif %}

然後修改產生出來的podfile內容

這邊範例我用vim進行修改，用xcode或其他文字編輯器也可以

直接在終端機Project的目錄下輸入

```terminal
vim podfile
```

打開檔案之後按下i進入INSERT MODE

輸入

```terminal
pod 'Firebase/Database'
pod 'Firebase/Auth'
pod 'Firebase/Storage'
```

輸入完成按下esc離開INSERT MODE

按下Shift + ;輸入wq就會儲存檔案並回到終端機的畫面

{% asset_img Day15Vim.gif %}

修改完podfile就可以來進行pod的安裝拉(如果這時xcode有開可以先關掉)

一樣在終端機Project的目錄下輸入pod install

等他跑完就會發現目錄下多了一個projectName.xcworkspace檔案

點兩下這檔案打開xcode就會發現多了個pod的資料夾

{% asset_img Day15Posinstall.gif %}

裡面就包含了剛剛安裝的pod

最後再回到AppDelegate.swift修改一下

先import Firebase的套件

然後在didFinishLaunchingWithOptions這個function內加上

```swift
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        
        FIRApp.configure()
        
        window = UIWindow(frame: UIScreen.main.bounds)
        window?.makeKeyAndVisible()
        window?.rootViewController = UINavigationController(rootViewController: HomeController())
        
        return true
    }
```

接下來就可以準備使用有安裝的Firebase套件了．