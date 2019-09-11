---
title: CoreData、UserDefault
date: 2017-08-20 22:11:07
tags: [Swift]
---

作為一款記錄用的APP，比賽期間使用者不太可能只開著這款APP

更別提意外跳出或是記憶體不足造成閃退的狀況了

這時候就應該要把比賽中所記錄到的資訊儲存起來

在APP打開的情況下去判斷之前是否有未完成的比賽

有的話就拿回該有的資訊讓使用者繼續記錄

至於要用什麼方式來儲存使用者紀錄的資料呢？

<!--more-->

有想過將資料完全存在後台，而現在使用儲存資料的Firebase realtime database確實很好用

但考慮到用量以及資料儲存的方式，似乎沒有需要將大量之後不需要的資訊儲存在realtime database中

接下來的選擇我想到了UserDefault，用來儲存是否有記錄中的狀態相當不錯

但如果要儲存已經是一個個物件的比賽的相關紀錄處理上相對麻煩

因此最後選擇使用了CoreData來儲存物件，UserDefault來儲存狀態的方法

```swift
func saveToCoreData() {
        let appDel = UIApplication.shared.delegate as? AppDelegate
        guard let context = appDel?.persistentContainer.viewContext else { return }
        do {
            let results = try context.fetch(PlayingPlayer.fetchRequest())
            
            // If CoreData already have data
            if results.count >= 9 {
                for (i,item) in results.enumerated() {
                    let editPlayer = item as? PlayingPlayer
                    if editPlayer?.name == self.players[i].name {
                        editPlayer?.mid = self.players[i].mid
                        editPlayer?.name = self.players[i].name
                        editPlayer?.position = self.players[i].position
                        editPlayer?.profileImage = self.players[i].profileImage
                        let array = self.players[i].recordArray
                        let data = NSKeyedArchiver.archivedData(withRootObject: array)
                        editPlayer?.recordArray = data as NSData
                        appDel?.saveContext()
                    }
                }
                
            // No Data
            } else {
                for savedPlayer in self.players {
                    let player = PlayingPlayer(context: context)
                    player.mid = savedPlayer.mid
                    player.name = savedPlayer.name
                    player.position = savedPlayer.position
                    player.order = savedPlayer.order
                    player.profileImage = savedPlayer.profileImage
                    let array = savedPlayer.recordArray
                    let data = NSKeyedArchiver.archivedData(withRootObject: array)
                    player.recordArray = data as NSData
                    appDel?.saveContext()
                }
            }
        } catch {
            
        }
        
    }
```

再進入紀錄頁面之前的TabBarController新增將目前物件存放到CoreData的function

然後設定key為`gaming`的UserDefault為true，作為現在正在記錄中的狀態

```swift
UserDefaults.standard.setValue("true", forKey: "gaming")
```

接下來也得設定在使用者按下比賽結束的按鈕時將狀態設為false

```swift
UserDefaults.standard.setValue("false", forKey: "gaming")
```

然後使用者進入到在HomeController時拿出`gaming`來判斷是否正在記錄中

```swift
func checkIsGaming() {
        guard let gamingStatus = UserDefaults.standard.string(forKey: "gaming") else {
            return
        }
        if gamingStatus == "true" {
            getDataFromCoreData()
        } else if gamingStatus == "false" {
            return
        }
    }
```

最後如果確定正在記錄中就去把coreData中的資料取出來並把畫面帶到GameTabBarController

```swift
    func getDataFromCoreData() {
        let appDel = UIApplication.shared.delegate as? AppDelegate
        guard let context = appDel?.persistentContainer.viewContext else { return }
        do {
            let result = try context.fetch(PlayingPlayer.fetchRequest())
            for item in result {
                let thisPlayer = item as? PlayingPlayer
                // Reconstruct by using map
                
                if thisPlayer?.recordArray != nil {
                    let unarchiveObject = NSKeyedUnarchiver.unarchiveObject(with: (thisPlayer?.recordArray as NSData?)! as Data)
                    let arrayObject = unarchiveObject as AnyObject! as! Array<String>
                    let array = arrayObject
                    self.players.append(Player(mid: thisPlayer?.mid, name: thisPlayer?.name, order: thisPlayer?.order, position: thisPlayer?.position, recordArray: array, profileImage: thisPlayer?.profileImage))
                } else {
                    self.players.append(Player(mid: thisPlayer?.mid, name: thisPlayer?.name, order: thisPlayer?.order, position: thisPlayer?.position, recordArray: [], profileImage: thisPlayer?.profileImage))
                }
            }
        } catch {
            
        }
        if players.count >= 9 {
            goToGameTabBarController()
        } else {
            return
        }
    }
    
    func goToGameTabBarController() {
        let controller = GameTabBarController()
        controller.players = self.players
        present(controller, animated: true, completion: nil)
    }
```

看看執行結果

{% asset_img CoreDataDemo.gif %}

這兩天也順便把程式碼再重構了一部分

把太煩雜的Controller切割成我自己定義的function以及原本controller所繼承的function兩部份

順便把儲存Player紀錄的array部分簡化了一下

回頭看看真是滿臉黑人問號

當初為何要寫得那麼複雜勒ＸＤ

在簡化後現在也能把板凳人員加上去

接下來應該會先處理比賽順序(先攻後攻)比分跟對手資料的部分