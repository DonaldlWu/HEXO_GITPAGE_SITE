---
title: backup
date: 2017-06-22 13:45:35
tags:
---

{% asset_img darrenwu.png %}


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

為了確實知道按鈕大小的範圍我們先設定灰色當按鈕的背景色

然後設定按鈕的標題

最後加入點擊的動作，為了讓系統知道我們點擊之後要做什麼

在`#selector()`內我們必須指定兩個Function

分別為`btnNextPressed`與`btnPrevPressed`

寫到這裡Xcode還不知道這兩個是啥東西

所以我們就先來定義一下這兩個Function

```swift
func btnNextPressed() {

	print("Press Next")

}
    
func btnPrevPressed() {

	print("Press Previous")

}
```

2017/7/15

Swift隨手紀錄Day25-Fetch Data Form Firebase Database

防止偷懶Day25

在完成註冊手續之後

接下來就是讓使用者可以新增隊伍然後在HomeController會去Fetch使用者新增過的隊伍資料

然後用tableView顯示出來

<!--more-->

第一步先來看看目前想到在Database儲存資料的形式

{% asset_img Day25Databasestroage.png %}

User的地方是昨天處理好的部分，當使用者註冊時會將資料儲存在一個uid底下

包含了使用者的email跟name這兩項資訊

而今天要讓使用者在新增球隊資訊之後

會在Team底下創造一個tid，裡面會儲存球隊名稱的字串TeamName

以及這個team屬於哪個使用者的uid



開工第一步先來把該有的工具準備好

在Model裡面新增一個定義User這個class的new file

命名成User.swift

然後修改一下Team.swift這個class的內容

內容如下

Team.swift

```swift
import UIKit

class Team: NSObject {
    var uid: String?
    var teamName: String?
}
```

User.swift


```swift
import UIKit

class User: NSObject {
    var name: String?
    var email: String?
    var uid: String?
}
```

然後來到要處理資料的HomeController

一樣先看看程式碼再來一步一步處理

```swift
import UIKit
import Firebase

class HomeController: UITableViewController {
    
    let cellId = "cellId"
    var teams = [Team]()
    var user = User()
    
    override var prefersStatusBarHidden: Bool {
        return false
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        let newTeamButton = UIBarButtonItem(title: "New Team", style: .plain, target: self, action: #selector(toSetNewTeamController))
        navigationItem.rightBarButtonItem = newTeamButton
        
        let logoutButton = UIBarButtonItem(title: "Logout", style: .plain, target: self, action: #selector(handleLogout))
        navigationItem.leftBarButtonItem = logoutButton
        checkUserIsLogin()
    }
    
    func fetchTeam() {
        FIRDatabase.database().reference().child("Team").observe(.childAdded, with: { (snapshot) in
            if let dictionary = snapshot.value as? [String: AnyObject] {
                let team = Team()
                team.setValuesForKeys(dictionary)
                if team.uid == self.user.uid {
                    self.teams.append(team)
                }

                DispatchQueue.main.async {
                    self.tableView.reloadData()
                }
                print(team.teamName as Any, team.uid as Any)
            }
        }, withCancel: nil)
    }
    
    func checkUserIsLogin() {
        if FIRAuth.auth()?.currentUser?.uid == nil {
            perform(#selector(handleLogout), with: nil, afterDelay: 0)
        } else {
            fetchUserSetNavBarTitle()
        }
    }
    
    func fetchUserSetNavBarTitle() {
        guard let uid = FIRAuth.auth()?.currentUser?.uid else {
            return
        }
        FIRDatabase.database().reference().child("User").child(uid).observeSingleEvent(of: .value, with: { (snapshot) in
            print(snapshot)
            if let dictionary = snapshot.value as? [String: Any] {
                self.user.uid = snapshot.key
                self.navigationItem.title = dictionary["name"] as? String
                self.fetchTeam()
            }
        }, withCancel: nil)
    }
    
    func handleLogout() {
        
        do {
            try FIRAuth.auth()?.signOut()
        } catch let logoutError {
            print(logoutError)
        }
        self.teams.removeAll()
        let loginController = LoginController()
        loginController.homeController = self
        present(loginController, animated: true, completion: nil)
    }
    
    func toSetNewTeamController() {
        let alertController = UIAlertController(title: "Add New Team", message: "", preferredStyle: .alert)
        
        let saveAction = UIAlertAction(title: "OK", style: .default, handler: {
            alert -> Void in
            
            let firstTextField = alertController.textFields![0] as UITextField
            let controller = SetNewTeamController()
            
            if firstTextField.text! != "" {
                controller.teamTitle = firstTextField.text!
                let uid = self.user.uid
                let ref = FIRDatabase.database().reference().child("Team")
                let teamRef = ref.childByAutoId()
                let value: [AnyHashable: Any] = ["TeamName": firstTextField.text!, "uid": uid as Any]
                teamRef.updateChildValues(value)
                self.present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
            } else {
                return
            }
            
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
        return teams.count
    }
    
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = UITableViewCell(style: .subtitle, reuseIdentifier: cellId)
        
        cell.textLabel?.text = teams[indexPath.row].teamName
        
        return cell
    }
    
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let controller = SetNewTeamController()
        controller.teamTitle = teams[indexPath.row].teamName
        present(UINavigationController(rootViewController: controller), animated: true, completion: nil)
    }
    
}
```

第一步當然是實作讓使用者新增資料到Database上

之前在`toSetNewTeamController`這個function裡面實作了當使用者點擊右上方的Add Member時

會跳出一個AlertController讓使用者輸入新增球隊的名稱

在第87~91行我們讓使用者把現有的uid跟新增的teamName儲存到Database上的Team節點下

並自動產生一個id當子樹，不過這裡的uid怎麼來的？

當然我們就需要剛剛準備好的工具拉

第7, 8行實作一個teams來儲存Fetch到的Team內容

以及一個user來儲存Fetch到的User內容

接下來在`fetchUserSetNavBarTitle`這個function裡面第56行儲存使用者的uid

這樣就有自己的uid可以使用拉



然後就來實作Fetch隊伍資料的`fetchTeam`這個function

首先，`fetchTeam`會在`fetchUserSetNavBarTitle`拿到使用者的資訊之後執行

以確保自己儲存的uid不會是空值

當`fetchTeam`執行時會去Database觀察Team這個子樹下的資料

如果裡面的uid跟現在使用者的uid是一樣的

就代表這隻Team是這個使用者所創建的

那就把這筆資料儲存到前面創好的teams裡面

最後重新整理現在的tableView內容

p.s.這裡要記得UI必須在主線程裡更新



但我們還沒處理tableView的內容

所以就修改一下`numberOfRowsInSection`回傳儲存的隊伍數量teams.count

以及`cellForRowAt`讓cell的文字顯示隊伍名稱teamName

還有`didSelectRowAt`所傳送的teamTitle

最後要記得處理當使用者登出時要把teams的內容移除

不然登入其他帳號時teams的內容可能還會儲存在裡面

{% asset_img Day25Demo.gif %}


