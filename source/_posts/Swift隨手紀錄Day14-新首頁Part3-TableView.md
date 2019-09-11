---
title: Swift隨手紀錄Day14-新首頁Part3-TableView
date: 2017-07-05 21:31:09
tags: [Swift]
---

防止偷懶Day14

昨天接上了首頁

接下來我希望首頁能用TableView顯示出已經登陸過的球隊

然後可以點擊球隊來進入之前寫的紀錄頁面

當然這樣也需要一個按鈕來新增球隊

以及一個讓球隊可以新增隊員資料的頁面

<!--more-->

首先就來看看做完的效果應該是怎麼一個樣子的

{% asset_img Day14Demo.gif %}

可以看到首頁先放了五個假資料，右上方放了一個button

點擊下去跳出一個alertController可以輸入新增的隊伍名稱

輸入完隊名(目前不輸也行)按下OK會進入新的球隊人員設定頁面

按下取消則是回到首頁不做任何事情

先到HomeController.swift進行修改

內容如下

```swift
import UIKit

class HomeController: UITableViewController {
    
    let cellId = "cellId"
    
    override var prefersStatusBarHidden: Bool {
        return false
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        let newTeamButton = UIBarButtonItem(title: "New Team",
                                         style: .plain, target: self, action: #selector(toSetNewTeamController))
        navigationItem.rightBarButtonItem = newTeamButton

    }
    
    func toSetNewTeamController() {
        let alertController = UIAlertController(title: "Add New Team", message: "", preferredStyle: .alert)
        
        let saveAction = UIAlertAction(title: "OK", style: .default, handler: {
            alert -> Void in
            
            let firstTextField = alertController.textFields![0] as UITextField

            let controller = SetNewTeamController()
            controller.teamTitle = firstTextField.text!
            self.present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
        })
        
        let cancelAction = UIAlertAction(title: "Cancel", style: .default, handler: {
            (action : UIAlertAction!) -> Void in
        })
        
        alertController.addTextField { (textField : UITextField!) -> Void in
            textField.placeholder = "Enter Team Name"
        }
        
        alertController.addAction(saveAction)
        alertController.addAction(cancelAction)
        
        self.present(alertController, animated: true, completion: nil)
        
    }
    
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 5
    }
    
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = UITableViewCell(style: .subtitle, reuseIdentifier: cellId)
        
        cell.textLabel?.text = "TEAM LIST HERE"
        
        return cell
    }
    
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let controller = SetNewTeamController()
        controller.teamTitle = "Exit Team Name\(indexPath.row)"
        present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
    }
    
}
```

首先是將HomeController從UIViewController的子類別改成UITableView的子類別

然後實作`numberOfRowsInSection`、`cellForRowAt`回傳5個row跟帶有textLabel的cell

再來是在viewDidLoad內實作右上的Add Team button

按鈕的動作則放在`toSetNewTeamController`這個function裡面

在裡面我們新增一個UIAlertController

UIAlertController內包含一個textField跟兩個UIAction

兩個UIAction一個負責取消動作，另一個則負責將新隊伍名稱跟畫面帶到下一個Controller

最後在59行設定當首頁的Row被選擇時，會將畫面送到新增的SetNewTeamController

雖然目前只有轉換畫面，之後再把資料串起來

接下來就來看SetNewTeamController的內容

```swift
import UIKit

class SetNewTeamController: UITableViewController {
    
    let cellId = "cellId"
    var teamTitle: String?
    
    override var prefersStatusBarHidden: Bool {
        return false
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        self.navigationItem.title = teamTitle
        
        let backButton = UIBarButtonItem(title: "BACK",
                                         style: .done, target: self, action: #selector(backHome))
        navigationItem.leftBarButtonItem = backButton
        
        let startButton = UIBarButtonItem(title: "START",
                                         style: .plain, target: self, action: #selector(toRecordController))
        navigationItem.rightBarButtonItem = startButton
        
        
    }
    
    func backHome() {
        self.dismiss(animated: true, completion: nil)
    }
    
    func toRecordController() {
        let controller = RecordController(collectionViewLayout: UICollectionViewFlowLayout())
        present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
    }
    
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 5
    }
    
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = UITableViewCell(style: .default, reuseIdentifier: cellId)
        cell.textLabel?.text = "MEMBER HERE"
        return cell
    }
    
}
```
SetNewTeamController一樣使用tableView

並在navigationBar左右各放一個button

左邊leftBarButtonItem負責回到home

又邊rightBarButtonItem負責將畫面送到RecordController

並且navigationItem.title放上從home page設定的隊伍名稱

Done~

接下來預計做選手新增用的畫面

