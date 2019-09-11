---
title: Swift 隨手紀錄
date: 2017-06-22 16:25:19
tags: [ Swift ]
---

每天寫一點點iOS開發的路程，不然會偷懶ＱＱ

最一開始就從捨棄Storyboard之旅開始好了

對剛開始開發iOS的我來說，Storyboard確實帶給我不少方便

也減少了不少我開發的時間

但當project越來越大，Controller越來越多

課金多少的差異在每次要開啟龐大的Storyboard的時候就非常有感覺了！！？？

	謎之聲：這裡有新的mac好便宜啊～買吧～買吧

所以為了我的錢包著想(X)裝逼(O)

就來學學怎麼用程式碼來刻自己要的畫面吧

<!--more-->

首先，必須將 TARGETS -> General -> Deployment Info內 Main interface給清空(預設是main)






{% asset_img TARGETS.png %}





接下來就移動到AppDelegate.swift當中來指定我們要從哪裡開始吧

在AppDelegate.swift檔案中我們可以找到`didFinishLaunchingWithOptions`這個Function，在裡面我們可以照這樣去設定

```swift
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        
        window = UIWindow(frame: UIScreen.main.bounds)
        window?.makeKeyAndVisible()
        window?.rootViewController = ViewController()
        
        return true
    }
```
我們手動去增加新的UIWindow Object來幫我們處理ＵＩ的行為(內略n行說明請自行Option左鍵UIWindow)，但光只有UIWindow並不夠

我們需要去指定這個window的`rootViewController`

在這裡我們將專案開啟時就會幫我們建好的`ViewController`指定為`rootViewController`

這樣就完成拉

command + R 給他催落去

恩？ 全黑一片？ 

{% asset_img hanjomain.jpeg %}

別急，這很正常

因為`viewController`還啥都沒有啊

來～給他變個色確認一下

回到`viewController`在viewdidLoad裡面加上

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .purple
    }
```

再次啟動就會發現畫面如我所想的變成全部一片紫色摟



最後

如果需要用到navigation bar在你的controller中

只需要將指定的rootViewcontroller那行稍微修改一下

```swift
window?.rootViewController = UINavigationController(rootViewController: ViewController())
```

當然，`ViewController`也能換成其他自己新增的Controller

下次再來談談怎麼用程式碼加上其他元件吧
