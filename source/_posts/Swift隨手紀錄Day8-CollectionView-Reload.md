---
title: Swift隨手紀錄Day8-CollectionView Reload
date: 2017-06-29 22:38:10
tags: [Swift]
---

防止偷懶日記Day8

繼續處理昨天沒辦法在新增資料之後更新collectionView的問題

在試了一些不同的方法之後

看了console的內容才終於發現了問題所在

發現當下只差沒撞豆腐自殺（￣ c￣）y▂ξ，真的是來亂的ＸＤ

因為其實我一直有在更新collectionView

<!--more-->

原來原因在於我更新了紀錄用的陣列recordArray

但是collectionViewCell的內容是從之前實作的players裡面去拿到資料的

所以雖然更新了recordArray，但players裡實作的內容其實是沒有改變的

知道問題之後就簡單了

稍微修改一下sentRecord()的內容

更新一下players就好

```swift
    func sentRecord(sender: UIButton) {
        
        print("Hit Button\(sender.tag)")
        switch sender.tag {
        case 0:
            players[0].recordArray.append("new")
        case 1:
            players[1].recordArray.append("record")
        case 2:
            players[2].recordArray.append("has")
        case 3:
            players[3].recordArray.append("been")
        case 4:
            players[4].recordArray.append("add")
        case 5:
            players[5].recordArray.append("into")
        case 6:
            players[6].recordArray.append("collectionView")
        case 7:
            players[7].recordArray.append("cell")
        case 8:
            players[8].recordArray.append("new")
        default:
            return
        }
        DispatchQueue.main.async {
            self.collectionView?.reloadData()
        }
        
    }
```

但是Player這個struct在製作時用的是let，所以這邊xcode會提醒要改成var(雖然提醒的地方讓我滿臉問號)

所以就回到Player.swift做一下修正

```swift
import UIKit

struct Player {
    var name: String
    var order: String
    var position: String
    var recordArray: Array<String>
    var profileImage: UIImage
}

```
考慮到之後應該都是會更動的數值，所以統一改成var

接下來執行之後按按看按鈕是不是確實有如我所想的執行

{% asset_img adddata.gif %}

最後順便將螢幕固定成橫躺來更清楚的呈現新增的資料

將TARGETS -> General -> Deployment -> Device Orientation設定為landscape Right

{% asset_img landscape.png %}