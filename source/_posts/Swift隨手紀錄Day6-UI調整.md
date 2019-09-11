---
title: Swift隨手紀錄Day6-UI調整
date: 2017-06-27 21:30:13
tags: [Swift]
---

防止偷懶日記Day6

今天來稍微調整一下UI

接續昨天的畫面，今天稍微修改一下

大概長這樣

<!--more-->

{% asset_img RecordUI.png %}

首先是將原來的UILabel換成UITextView

然後是新增一個UILabel來顯示棒次跟守備位置

```swift
    let orderLabel: UILabel = {
        let label = UILabel()
        label.translatesAutoresizingMaskIntoConstraints = false
        label.backgroundColor = .orange
        label.font = UIFont.boldSystemFont(ofSize: 12)
        label.textAlignment = .center
        label.text = "四棒/投手"
        return label
    }()
    
    let recordText: UITextView = {
        let text = UITextView()
        text.textAlignment = .left
        text.text = "一安 / 三振 / 保送 / 二安"
        text.font = UIFont.boldSystemFont(ofSize: 14)
        text.backgroundColor = .yellow
        text.isScrollEnabled = false
        text.translatesAutoresizingMaskIntoConstraints = false
        return text
    }()
```

上面分別是新增的棒次label跟用來取代昨天中間label的textView

再來是修改一下button的設定讓點擊可以作用

```swift
    lazy var sentButton: UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.backgroundColor = .purple
        button.layer.cornerRadius = 5
        button.layer.borderColor = UIColor.blue.cgColor
        button.layer.borderWidth = 2
        button.setTitle("UPDATE", for: .normal)
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 13)
        button.addTarget(self, action: #selector(sentRecord), for: .touchUpInside)
        return button
    }()
```

將let改成lazy var，不這樣改動後面新增的addTarget不會觸發，原因在另外找時間寫一篇來討論

接下來調整一下layout的setupView()

```swift
    func setupView() {
        backgroundColor = .yellow
        addSubview(profileImage)
        addSubview(recordText)
        addSubview(sentButton)
        addSubview(orderLabel)
        
        profileImage.topAnchor.constraint(equalTo: self.topAnchor).isActive = true
        profileImage.leftAnchor.constraint(equalTo: self.leftAnchor, constant: 12).isActive = true
        profileImage.heightAnchor.constraint(equalToConstant: 48).isActive = true
        profileImage.widthAnchor.constraint(equalToConstant: 48).isActive = true
        
        sentButton.centerYAnchor.constraint(equalTo: self.centerYAnchor).isActive = true
        sentButton.rightAnchor.constraint(equalTo: self.rightAnchor, constant: -12).isActive = true
        sentButton.widthAnchor.constraint(equalToConstant: 60).isActive = true
        sentButton.heightAnchor.constraint(equalToConstant: 32).isActive = true
        
        recordText.leftAnchor.constraint(equalTo: profileImage.rightAnchor, constant: 12).isActive = true
        recordText.rightAnchor.constraint(equalTo: sentButton.leftAnchor, constant: -12).isActive = true
        recordText.centerYAnchor.constraint(equalTo: self.centerYAnchor).isActive = true
        recordText.heightAnchor.constraint(equalToConstant: 32).isActive = true
        
        orderLabel.topAnchor.constraint(equalTo: profileImage.bottomAnchor).isActive = true
        orderLabel.bottomAnchor.constraint(equalTo: self.bottomAnchor).isActive = true
        orderLabel.leftAnchor.constraint(equalTo: self.leftAnchor).isActive = true
        orderLabel.rightAnchor.constraint(equalTo: recordText.leftAnchor).isActive = true 
    }
```

最後新增一個function`sentRecord()`來描述button被點擊時的動作，先讓console印出點字串來確認按鈕有作用

```swift
    func sentRecord() {
        print("Hit Button")
    }
```