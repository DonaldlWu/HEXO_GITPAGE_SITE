---
title: Swift-隨手紀錄Day5 紀錄APP START
date: 2017-06-26 21:51:15
tags: [Swift]
---

防止偷懶Day5

準備做一個棒球紀錄用的APP

今天準備將主幹的紀錄頁面做出雛形

接續昨天完成的collectionView繼續將需要的元件放上去

<!--more-->

目標大概長這樣

{% asset_img RecordController.png %}

先用9個cell做出9棒各自的頭像(image),成績內容(label)，發送按鈕(button)

預計用陣列做成績的儲存，應該之後會將label換成textView

但今天先大致將畫面刻出來

首先，來客製化我們的collectionViewCell

新增一個RecordCell.swift檔案，順便將檔案分類一下

目前的目錄長下面這樣

{% asset_img Index.png %}

在RecordCell.swift的裡面來增加我們需要的view

```swift
import UIKit

class RecordCell: UICollectionViewCell {
    override init(frame: CGRect) {
        super.init(frame: frame)
        setupView()
    }
    
    let profileImage: UIImageView = {
        let image = UIImageView()
        image.translatesAutoresizingMaskIntoConstraints = false
        image.image = UIImage(named: "image")
        image.contentMode = .scaleAspectFill
        image.layer.cornerRadius = 5
        image.layer.masksToBounds = true
        return image
    }()
    
    let wordLabel: UILabel = {
        let label = UILabel()
        label.text = "Test Test Test"
        label.backgroundColor = .yellow
        label.translatesAutoresizingMaskIntoConstraints = false
        return label
    }()
    
    let sentButton: UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.backgroundColor = .purple
        button.layer.cornerRadius = 5
        button.layer.borderColor = UIColor.blue.cgColor
        button.layer.borderWidth = 2
        button.setTitle("Sent", for: .normal)
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
        return button
    }()
    
    func setupView() {
        backgroundColor = .yellow
        addSubview(profileImage)
        addSubview(wordLabel)
        addSubview(sentButton)
        
        profileImage.centerYAnchor.constraint(equalTo: self.centerYAnchor).isActive = true
        profileImage.leftAnchor.constraint(equalTo: self.leftAnchor, constant: 12).isActive = true
        profileImage.heightAnchor.constraint(equalToConstant: 48).isActive = true
        profileImage.widthAnchor.constraint(equalToConstant: 48).isActive = true
        
        wordLabel.topAnchor.constraint(equalTo: self.topAnchor).isActive = true
        wordLabel.leftAnchor.constraint(equalTo: profileImage.rightAnchor, constant: 12).isActive = true
        wordLabel.bottomAnchor.constraint(equalTo: self.bottomAnchor).isActive = true
        wordLabel.rightAnchor.constraint(equalTo: self.rightAnchor, constant: 12).isActive = true
        
        sentButton.centerYAnchor.constraint(equalTo: self.centerYAnchor).isActive = true
        sentButton.rightAnchor.constraint(equalTo: self.rightAnchor, constant: -12).isActive = true
        sentButton.widthAnchor.constraint(equalToConstant: 60).isActive = true
        sentButton.heightAnchor.constraint(equalToConstant: 32).isActive = true
    }
    

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

先分別實現ImageView、Label、Button並且設定好在cell內的layout

目前只是先大致定位，之後再根據需求新增跟調整

再來新增RecordController的內容

```swift
import UIKit

class RecordController: UICollectionViewController, UICollectionViewDelegateFlowLayout {
    
    let cellId = "CellId"
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        collectionView?.backgroundColor = .green
        collectionView?.register(RecordCell.self, forCellWithReuseIdentifier: cellId)
    }
    
    override func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 9
    }
    
    override func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellId, for: indexPath)
        cell.backgroundColor = .blue
        
        return cell
    }
    
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
        return CGSize(width: view.frame.width, height: 80)
    }
    
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }

}
```

首先是讓RecordController繼承UICollectionViewDelegateFlowLayout

再來是定義cellId方便註冊並在viewDidLoad裡面註冊RecordCell

然後設定numberOfItemsInSection回傳需要的cell數量，棒球有9個棒次，暫時先設9

之後有其他需要應該會用header跟footer去處理

在把之前註冊的RecordCell拿來用在cellForItemAt的function中

最後是cell的大小設定由layout collectionViewLayout處理

會回傳一個CGSize，我在這裡寬度設定與畫面等寬，高度80

這樣子執行看看就會看到目標的畫面

之後會花一些時間整理需要的功能以及畫面的定案，希望可以速度想好

不然我要沒梗寫拉（￣ c￣）y▂ξ



