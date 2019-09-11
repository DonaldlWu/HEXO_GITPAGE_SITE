---
title: Swift-隨手紀錄Day4 CollectionView Crash
date: 2017-06-25 21:05:57
tags: [Swift]
---

防止偷懶Day4

決定開始製作之前構想很久的一些Project

但在開始之前，先挖一些之前遇過的問題紀錄下解法

<!--more-->

首先是新專案我打算用CollectionView組成我的主要畫面

所以我新開了一個專案

一樣不使用storyboard的情況去設定完(請參照前兩天的文章)

然後將ViewController.swift重新命名為RecordController.swift並改成下面這樣

```swift
import UIKit

class RecordController: UICollectionViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        collectionView?.backgroundColor = .green
        
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }

}
```

再進入AppDelegate.swift內的didFinishLaunchingWithOptions修改一下變成

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        
        window = UIWindow(frame: UIScreen.main.bounds)
        window?.makeKeyAndVisible()
        window?.rootViewController = UINavigationController(rootViewController: RecordController())
        
        return true
    }
```

然後執行下去

很好

喜聞樂見的.....Crash了

錯誤訊息如下

'UICollectionView must be initialized with a non-nil layout parameter'

上stackOverflow看了看

簡單來說，我指定了我的RecordController作為UICollectionViewController的subClass，但是裡面collectionView的property其實就是UICollectionView本身

所以才會噴出這樣的錯誤．

所以需要修改一下內容如下

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        
        window = UIWindow(frame: UIScreen.main.bounds)
        window?.makeKeyAndVisible()
        let recordController = RecordController(collectionViewLayout: UICollectionViewFlowLayout())
        window?.rootViewController = UINavigationController(rootViewController: recordController)
        
        return true
    }
```

再次執行

這次就順利編譯成功

並且如預期的顯示了我要的綠色背景．

專案內容都放在Github上

[ HBCRecord Github ]( https://github.com/DonaldlWu/HBCRecord )
