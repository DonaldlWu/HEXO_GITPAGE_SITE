---
title: Swift隨手紀錄Day7-Pull to refresh
date: 2017-06-28 16:43:50
tags: [Swift]
---

防止偷懶日記Day7

目前在實現collectionView之後對cell內資料的reload方式遇到了一點瓶頸

正在思考有沒有更好的解決方式

<!--more-->

現在的做法首先會新增一個Player.swift檔案來做為選手資料的模型

需要的內容有照片、姓名、守備位置、背號以及用來儲存成績的文字陣列

```swift
import UIKit

struct Player {
    let name: String
    let order: String
    let position: String
    let recordArray: Array<String>
    let profileImage: UIImage
}
```

有了模型就可以在RecordController裡面先實作一些假資料來放看看

先在class外作出用來存資料的陣列

```swift
var recordArray0 = ["一安", "三振", "保送", "二安"]
var recordArray1 = ["一安", "三振", "保送", "二安"]
var recordArray2 = ["一安", "三振", "保送", "二安"]
var recordArray3 = ["一安", "三振", "保送", "二安"]
var recordArray4 = ["一安", "三振", "保送", "二安"]
var recordArray5 = ["一安", "三振", "保送", "二安"]
var recordArray6 = ["一安", "三振", "保送", "二安"]
var recordArray7 = ["一安", "三振", "保送", "二安"]
var recordArray8 = ["一安", "三振", "保送", "二安"]
```

在RecordController裡實作新的選手物件

```swift
    let players: [Player] = {
        let player1 = Player(name: "Yohoho", order: "一棒", position: "中外", recordArray: recordArray0, profileImage: UIImage(named: strImage))
        let player2 = Player(name: "Yohoho", order: "二棒", position: "二壘", recordArray: recordArray1, profileImage: UIImage(named: strImage))
        let player3 = Player(name: "Yohoho", order: "三棒", position: "游擊", recordArray: recordArray2, profileImage: UIImage(named: strImage))
        let player4 = Player(name: "Yohoho", order: "四棒", position: "一壘", recordArray: recordArray3, profileImage: UIImage(named: strImage))
        let player5 = Player(name: "Yohoho", order: "五棒", position: "三壘", recordArray: recordArray4, profileImage: UIImage(named: strImage))
        let player6 = Player(name: "Yohoho", order: "六棒", position: "捕手", recordArray: recordArray5, profileImage: UIImage(named: strImage))
        let player7 = Player(name: "Yohoho", order: "七棒", position: "DH", recordArray: recordArray6, profileImage: UIImage(named: strImage))
        let player8 = Player(name: "Yohoho", order: "八棒", position: "右外", recordArray: recordArray7, profileImage: UIImage(named: strImage))
        let player9 = Player(name: "Yohoho", order: "九棒", position: "左外", recordArray: recordArray8, profileImage: UIImage(named: strImage))
        return [player1, player2, player3, player4, player5, player6, player7, player8, player9]
    }()
```

接下來把昨天在RecordCell.swift內實現的button.addTarget先改放到RecordController.swift內

改在cellForItemAt這個function內來處理

順便將上面實作的假資料放進cell裡面

```swift
    override func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellId, for: indexPath) as? RecordCell
        cell?.backgroundColor = .blue
        cell?.sentButton.tag = indexPath.item
        let player = players[indexPath.item]
        cell?.sentButton.addTarget(self, action: #selector(sentRecord(sender:)), for: .touchUpInside)
        cell?.profileImage.image = player.profileImage
        cell?.orderLabel.text = player.order + " - " + player.position
        cell?.recordText.text = String(describing: player.recordArray)
        return cell!
    }
```

接著把sentRecord的function也拿到這裡來修改一下

```swift
    func sentRecord(sender: UIButton) {
        
        print("Hit Button\(sender.tag)")
        switch sender.tag {
        case 0:
            recordArray0.append("New Record1")
        case 1:
            recordArray1.append("New Record1")
        case 2:
            recordArray2.append("New Record2")
        case 3:
            recordArray3.append("New Record3")
        case 4:
            recordArray4.append("New Record4")
        case 5:
            recordArray5.append("New Record5")
        case 6:
            recordArray6.append("New Record6")
        case 7:
            recordArray7.append("New Record7")
        case 8:
            recordArray8.append("New Record8")
        default:
            return
        }
        
        DispatchQueue.main.async {
            self.collectionView?.reloadData()
        }
        
    }
```

目前雖然點擊button可以順利顯示是哪一個button以及更新array內的資料

但是畫面並沒有跟著更新，讓我好好研究一下到底是為什麼...

不過在找資料途中順手把下拉更新給放上去了(雖然也不會更新（￣ c￣）y▂ξ)

就當作經驗玩玩看摟，大概長這樣

```swift
    var refreshControl:UIRefreshControl!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        collectionView?.backgroundColor = .green
        collectionView?.register(RecordCell.self, forCellWithReuseIdentifier: cellId)
        self.setNeedsStatusBarAppearanceUpdate()
        
        self.refreshControl = UIRefreshControl()
        self.refreshControl.attributedTitle = NSAttributedString(string: "Reloading")
        self.refreshControl.addTarget(self, action: #selector(refresh), for: .valueChanged)
        collectionView!.addSubview(refreshControl)
    }
    
    func refresh(sender:AnyObject)
    {
        DispatchQueue.main.async {
            self.collectionView?.reloadData()
        }
        self.refreshControl.endRefreshing()
    }
```

實際大概長這樣

{% asset_img Reloading.png %}



