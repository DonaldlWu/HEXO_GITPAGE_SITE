---
title: Swift隨手紀錄Day21-Firebase Authentication & Database - Part1
date: 2017-07-12 20:54:58
tags: [Swift]
---

防止偷懶Day21

裝了Firebase套件好幾天，終於要來用了

先來做個登入介面，然後來實作Firebase Authentication

然後把資料傳到Firebase的Database

<!--more-->

登入介面的部分跟之前幾天在做的新增成員頁面其實很像

一樣有一個可以換圖的UIImageView

一個選擇是登入還是註冊的UISegmentedControl

跟一個UIButton執行動作

不一樣的是在要有兩個UITextField可以分別填入要註冊的email跟密碼



既然畫面及功能都很像，那就試試用之前做好的畫面來重複利用吧

首先先到HomeController的viewDidLoad裡面加上logout用的button

```swift
let logoutButton = UIBarButtonItem(title: "Logout",
                                           style: .plain, target: self, action: #selector(handleLogout))
        navigationItem.leftBarButtonItem = logoutButton
```

接下來在Controller裡面新增一個LoginController.swift

就來看看寫了什麼吧

```swift
import UIKit
import Firebase

class LoginController: AddMemberController {
    
    let loginSegmentedControl: UISegmentedControl = {
        let sc = UISegmentedControl(items: ["Login", "Register"])
        sc.selectedSegmentIndex = 0
        sc.translatesAutoresizingMaskIntoConstraints = false
        sc.addTarget(self, action: #selector(handleChangeButtonTitle), for: .valueChanged)
        return sc
    }()
    
    let emailText: UITextField = {
        let text = UITextField()
        text.placeholder = "email"
        text.translatesAutoresizingMaskIntoConstraints = false
        text.layer.borderColor = UIColor.cyan.cgColor
        text.layer.borderWidth = 1
        return text
    }()
    
    let passwordText: UITextField = {
        let text = UITextField()
        text.placeholder = "password"
        text.translatesAutoresizingMaskIntoConstraints = false
        text.isSecureTextEntry = true
        text.layer.borderColor = UIColor.cyan.cgColor
        text.layer.borderWidth = 1
        return text
    }()
    
    lazy var loginButton: UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.setTitle("Login", for: .normal)
        button.backgroundColor = .cyan
        button.addTarget(self, action: #selector(handleRegister), for: .touchUpInside)
        return button
    }()
    
    func handleRegister() {
        guard let email = emailText.text, let password = passwordText.text else {
            print("Input Error")
            return
        }
        
        FIRAuth.auth()?.createUser(withEmail: email, password: password, completion: { (user: FIRUser?, error) in
            
            if error != nil {
                print(error!)
                return
            }
            
            let ref = FIRDatabase.database().reference(fromURL: "https://xxxxxxxx.firebaseio.com/")
            let userReference = ref.child("User")
            let accountValue = ["email": email]
            userReference.updateChildValues(accountValue, withCompletionBlock: { (err, ref) in
                
                if err != nil {
                    print(err!)
                    return
                }
                
                print("New user saved success")
                
            })
            
        })
    }
    
    func handleChangeButtonTitle() {
        if loginSegmentedControl.selectedSegmentIndex == 0 {
            loginButton.setTitle("Login", for: .normal)
        } else {
            loginButton.setTitle("Register", for: .normal)
        }
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        positionSegmentedControl.removeFromSuperview()
        nameText.removeFromSuperview()
        registerButton.removeFromSuperview()
        
        view.addSubview(loginSegmentedControl)
        view.addSubview(emailText)
        view.addSubview(passwordText)
        view.addSubview(loginButton)
        
        loginSegmentedControl.topAnchor.constraint(equalTo: profileImage.bottomAnchor, constant: 12).isActive = true
        loginSegmentedControl.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 22).isActive = true
        loginSegmentedControl.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -22).isActive = true
        loginSegmentedControl.heightAnchor.constraint(equalToConstant: 24).isActive = true
        
        emailText.topAnchor.constraint(equalTo: loginSegmentedControl.bottomAnchor, constant: 8).isActive = true
        emailText.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 22).isActive = true
        emailText.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -22).isActive = true
        emailText.heightAnchor.constraint(equalToConstant: 32).isActive = true
        
        passwordText.topAnchor.constraint(equalTo: emailText.bottomAnchor, constant: 8).isActive = true
        passwordText.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 22).isActive = true
        passwordText.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -22).isActive = true
        passwordText.heightAnchor.constraint(equalToConstant: 32).isActive = true
        
        loginButton.topAnchor.constraint(equalTo: passwordText.bottomAnchor, constant: 8).isActive = true
        loginButton.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 22).isActive = true
        loginButton.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -22).isActive = true
        loginButton.heightAnchor.constraint(equalToConstant: 50).isActive = true
        
    }

}
```

首先當然要使用Firebase的功能當然要先import Firebase

接下來這個LoginController是AddMemberController的子類別

所以前面寫過的功能這裡都可以用(ex..選圖、換圖、觀察keyboard移動螢幕)

不過除了UIImageView之外其他東西還是有點區別，所以先一樣在6~40行先實作我們需要的物件

然後在viewDidLoad裡面把繼承過來但不需要的物件從view裡面刪除掉

再加入剛剛新增的物件，最後設定anchor

這裡比較不一樣的是UISegmentedControl的選擇會影響UIButton的title

以及之後會實作的不同動作，這就定義在handleChangeButtonTitle這個function內



再來就是今天的重點了

按下Button應該要可以觸發註冊或是登入，今天先從註冊開始

button會觸發註冊程序

要註冊Firebase要先去Firebase console把需要的登入方式打開

這邊我使用Email登入

{% asset_img Day21Authset.gif %}

接下來就回到程式碼，按下註冊會觸發handleRegister這個function

首先先確定兩個UITextField的內容有順利儲存

接下來就跟Firebase進行註冊，如果失敗就把錯誤訊息丟回console

成功就準備來儲存資料到Firebase的Database

在第55行要用URL連線到自己的database

這邊的URL可以在下圖反白的地方找到，複製貼上即可

{% asset_img Day21Database.png %}

然後把資料存在`User`這個子樹底下，然後依照成功或失敗在console印出信息，Done

下篇繼續

