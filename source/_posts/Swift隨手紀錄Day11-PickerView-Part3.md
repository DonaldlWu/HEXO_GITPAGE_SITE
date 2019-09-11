---
title: Swift隨手紀錄Day11-PickerView-Part3
date: 2017-07-02 20:00:19
tags: [Swift]
---

防止偷懶Day11

繼續完成pickView的功能

首先先把之前放在RecordController.swift的紀錄陣列放到Player.swift裡面

所以現在Player.swift長這樣

<!--more-->

```swift
import UIKit

var recordArray0 = ["一安", "三振", "保送", "二安"]
var recordArray1 = ["一安", "三振", "保送", "二安"]
var recordArray2 = ["一安", "三振", "保送", "二安"]
var recordArray3 = ["一安", "三振", "保送", "二安"]
var recordArray4 = ["一安", "三振", "保送", "二安"]
var recordArray5 = ["一安", "三振", "保送", "二安"]
var recordArray6 = ["一安", "三振", "保送", "二安"]
var recordArray7 = ["一安", "三振", "保送", "二安"]
var recordArray8 = ["一安", "三振", "保送", "二安"]

struct Player {
    var name: String
    var order: String
    var position: String
    var recordArray: Array<String>
    var profileImage: UIImage
}
```

第二步來完成昨天在pickerView裡面實作按鈕的點擊動作

```swift
    func sendRecordBack() {
        switch rowInRecordArray {
        case 0:
            recordArray0.append(tempRecord)
        case 1:
            recordArray1.append(tempRecord)
        case 2:
            recordArray2.append(tempRecord)
        case 3:
            recordArray3.append(tempRecord)
        case 4:
            recordArray4.append(tempRecord)
        case 5:
            recordArray5.append(tempRecord)
        case 6:
            recordArray6.append(tempRecord)
        case 7:
            recordArray7.append(tempRecord)
        case 8:
            recordArray8.append(tempRecord)
        default:
            return
        }
        let controller = RecordController(collectionViewLayout: UICollectionViewFlowLayout())
        present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
    }
    
    func sendRecordCancel() {
        let controller = RecordController(collectionViewLayout: UICollectionViewFlowLayout())
        present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
    }
```

因為這邊回資料頁面會重新產生collectionView，所以直接更新陣列內容即可

更新完就回到推出`RecordController`

取消就只要直接回前頁即可

第三步回到RecordCell.swift調整一下UI以及增加一個鍵來取消存在的紀錄

頁面大概長這樣

{% asset_img UI.png %}

就修改一下Anchor跟新增button

```swift
    lazy var undoButton: UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.backgroundColor = .orange
        button.layer.cornerRadius = 5
        button.layer.borderColor = UIColor.blue.cgColor
        button.layer.borderWidth = 2
        button.setTitle("UNDO", for: .normal)
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 13)
        return button
    }()
    
    func setupView() {
        backgroundColor = .yellow
        addSubview(profileImage)
        addSubview(recordText)
        addSubview(sentButton)
        addSubview(undoButton)
        addSubview(orderLabel)
        
        profileImage.topAnchor.constraint(equalTo: self.topAnchor).isActive = true
        profileImage.leftAnchor.constraint(equalTo: self.leftAnchor, constant: 12).isActive = true
        profileImage.heightAnchor.constraint(equalToConstant: 48).isActive = true
        profileImage.widthAnchor.constraint(equalToConstant: 48).isActive = true
        
        sentButton.centerYAnchor.constraint(equalTo: self.centerYAnchor, constant: -18).isActive = true
        sentButton.rightAnchor.constraint(equalTo: self.rightAnchor, constant: -12).isActive = true
        sentButton.widthAnchor.constraint(equalToConstant: 60).isActive = true
        sentButton.heightAnchor.constraint(equalToConstant: 32).isActive = true
        
        undoButton.centerYAnchor.constraint(equalTo: self.centerYAnchor, constant: 18).isActive = true
        undoButton.rightAnchor.constraint(equalTo: self.rightAnchor, constant: -12).isActive = true
        undoButton.widthAnchor.constraint(equalToConstant: 60).isActive = true
        undoButton.heightAnchor.constraint(equalToConstant: 32).isActive = true
        
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

再回到RecordController.swift增加取消的動作undoRecord

如果點擊的那一行有資料(recordArray(row)!= 0)，就把對應對應的players資料的最後一個資料刪除


```swift
    override func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: cellId, for: indexPath) as? RecordCell
        let player = players[indexPath.item]
        cell?.backgroundColor = .blue
        cell?.sentButton.tag = indexPath.item
        cell?.undoButton.tag = indexPath.item
        cell?.sentButton.addTarget(self, action: #selector(sentRecord(sender:)), for: .touchUpInside)
        cell?.undoButton.addTarget(self, action: #selector(uudoRecord(sender:)), for: .touchUpInside)
        cell?.profileImage.image = player.profileImage
        cell?.orderLabel.text = player.order + " - " + player.position
        cell?.recordText.text = String(describing: player.recordArray)
        return cell!
    }
    
    func sentRecord(sender: UIButton) {
        let controller = RecordPickerController()
        controller.rowInRecordArray = sender.tag
        present(controller, animated: true, completion: nil)
    }
    
    func uudoRecord(sender: UIButton) {
        print("Hit Button\(sender.tag)")
        switch sender.tag {
        case 0:
            if recordArray0.count > 0 {
                players[0].recordArray.remove(at: recordArray0.count - 1)
                recordArray0.remove(at: recordArray0.count - 1)
            }
        case 1:
            if recordArray1.count > 0 {
                players[1].recordArray.remove(at: recordArray1.count - 1)
                recordArray1.remove(at: recordArray1.count - 1)
            }
        case 2:
            if recordArray2.count > 0 {
                players[2].recordArray.remove(at: recordArray2.count - 1)
                recordArray2.remove(at: recordArray2.count - 1)
            }
        case 3:
            if recordArray3.count > 0 {
                players[3].recordArray.remove(at: recordArray3.count - 1)
                recordArray3.remove(at: recordArray3.count - 1)
            }
        case 4:
            if recordArray4.count > 0 {
                players[4].recordArray.remove(at: recordArray4.count - 1)
                recordArray4.remove(at: recordArray4.count - 1)
            }
        case 5:
            if recordArray5.count > 0 {
                players[5].recordArray.remove(at: recordArray5.count - 1)
                recordArray5.remove(at: recordArray5.count - 1)
            }
        case 6:
            if recordArray6.count > 0 {
                players[6].recordArray.remove(at: recordArray6.count - 1)
                recordArray6.remove(at: recordArray6.count - 1)
            }
        case 7:
            if recordArray7.count > 0 {
                players[7].recordArray.remove(at: recordArray7.count - 1)
                recordArray7.remove(at: recordArray7.count - 1)
            }
        case 8:
            if recordArray8.count > 0 {
                players[8].recordArray.remove(at: recordArray8.count - 1)
                recordArray8.remove(at: recordArray8.count - 1)
            }
        default:
            return
        }
        DispatchQueue.main.async {
            self.collectionView?.reloadData()
        }

    }
```

執行一下看結果

{% asset_img part3.gif %}
