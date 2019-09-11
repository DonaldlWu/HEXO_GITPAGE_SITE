---
title: Swift-隨手紀錄Day3-Button控制
date: 2017-06-24 19:18:38
tags: [Swift]
---

防止偷懶日記Day3

今天就來實作換圖用的button

來修改一下昨天實作的兩個button

<!--more-->

但在這之前，由於我希望這兩個button的作用是換圖

因此我需要知道我要換到哪一張圖

所以先在viewController內定義一個變數來代表我們的圖片


```swift
	var imgIndex = 0
```

再來就開始幫button加上一些新動作拉


```swift
    let nextButton: UIButton = {
        let button = UIButton()
        button.backgroundColor = .gray
        button.setTitle("next>", for: .normal)
        button.translatesAutoresizingMaskIntoConstraints = false
        button.addTarget(self, action: #selector(btnNextPressed), for: .touchUpInside)
        return button
    }()
    
    let prevButton: UIButton = {
        let button = UIButton()
        button.backgroundColor = .gray
        button.setTitle("<prev", for: .normal)
        button.translatesAutoresizingMaskIntoConstraints = false
        button.addTarget(self, action: #selector(btnPrevPressed), for: .touchUpInside)
        return button
    }()
```

在6與15行加上了偵測點擊的動作

而當觀察到button被點擊時會執行#selector()當中的function

再來就來定義兩個function的內容

```swift
    func btnNextPressed() {
        if imgIndex < 5 {
            imgIndex += 1
        }
        
        let strImage = "image\(imgIndex)"
        self.imageView.image = UIImage(named: strImage)
        
    }
    
    func btnPrevPressed() {
        if imgIndex > 0 {
            imgIndex -= 1
        }
        
        let strImage = "image\(imgIndex)"
        self.imageView.image = UIImage(named: strImage)
        
    }
```

裡面我們用一個if敘述來判斷現在我的imgIndex是多少

並用這個數字來代表我要顯示哪張圖在imageView上

然後就在Assets裡面加入命名為image0~image5的圖片

今天就先到這裡

明天再來繼續做．


