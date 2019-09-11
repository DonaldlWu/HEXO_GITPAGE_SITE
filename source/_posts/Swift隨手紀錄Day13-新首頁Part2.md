---
title: Swift隨手紀錄Day13-新首頁Part2
date: 2017-07-04 17:05:43
tags: [Swift]
---

防止偷懶Day13

昨天把首頁跟RecordController接起來

今天要做個navigationItem把畫面送回首頁

一樣先來看看做完的效果

<!--more-->

{% asset_img Day13backHome.gif %}

點開RecordController.swift

稍微修改一下viewDidLoad

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        let backButton = UIBarButtonItem(title: "BACK",
                                            style: .plain, target: self, action: #selector(RecordController.backAction))
        navigationItem.leftBarButtonItem = backButton
        
        collectionView?.backgroundColor = .green
        collectionView?.register(RecordCell.self, forCellWithReuseIdentifier: cellId)
        self.setNeedsStatusBarAppearanceUpdate()
        
        self.refreshControl = UIRefreshControl()
        self.refreshControl.attributedTitle = NSAttributedString(string: "Reloading")
        self.refreshControl.addTarget(self, action: #selector(refresh), for: .valueChanged)
        collectionView!.addSubview(refreshControl)
    }
    
    func backAction() -> Void {
        let controller = HomeController()
        present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
    }
```

實作一個UIBarButtonItem

動作就設定在下面的backAction裡面

然後把這個item放在leftBarButtonItem，完成～
