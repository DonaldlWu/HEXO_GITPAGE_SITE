---
title: Swift隨手紀錄Day22-LoginController UI修改
date: 2017-07-13 23:16:53
tags:
---

防止偷懶Day22

今天時間不太夠，就來修正一下UI好了

決定在LoginController實作一下用SegmentedControl的選擇來動態調整UI的功能

用來區分使用者是登入還是註冊

如果是註冊就多一個UITextField來讓使用者輸入名稱

之後讓主畫面可以顯示目前登入的使用者名稱

如果是登入就只需要email

先把要移動的部分處理一下

<!--more-->

首先來看一下效果

{% asset_img Day22UI.gif %}

接下來就進入LoginController.swift修改一下

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
            
            let ref = FIRDatabase.database().reference(fromURL: "https://hbcrecord-5a3d4.firebaseio.com/")
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
            profileImageTopAnchor?.constant = 64
            emailTopAnchor?.constant = 8
        } else {
            loginButton.setTitle("Register", for: .normal)
            profileImageTopAnchor?.constant = 32
            emailTopAnchor?.constant = 48
        }
    }
    
    var profileImageTopAnchor: NSLayoutConstraint?
    var emailTopAnchor: NSLayoutConstraint?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        profileImage.removeAllConstraints()
        positionSegmentedControl.removeFromSuperview()
        nameText.removeFromSuperview()
        registerButton.removeFromSuperview()
        
        view.addSubview(loginSegmentedControl)
        view.addSubview(emailText)
        view.addSubview(passwordText)
        view.addSubview(loginButton)
        
        profileImageTopAnchor = profileImage.topAnchor.constraint(equalTo: view.topAnchor, constant: 64)
        profileImageTopAnchor?.isActive = true
        profileImage.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        profileImage.heightAnchor.constraint(equalToConstant: 110).isActive = true
        profileImage.widthAnchor.constraint(equalToConstant: 110).isActive = true
        
        loginSegmentedControl.topAnchor.constraint(equalTo: profileImage.bottomAnchor, constant: 8).isActive = true
        loginSegmentedControl.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 22).isActive = true
        loginSegmentedControl.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -22).isActive = true
        loginSegmentedControl.heightAnchor.constraint(equalToConstant: 24).isActive = true
        
        emailTopAnchor = emailText.topAnchor.constraint(equalTo: loginSegmentedControl.bottomAnchor, constant: 8)
        emailTopAnchor?.isActive = true
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

extension UIView {
    func removeAllConstraints() {
        self.removeConstraints(self.constraints)
        for view in self.subviews {
            view.removeAllConstraints()
        }
    }
}
```

先新增一個function來移除原本的constrains

然後定義兩個變數`profileImageTopAnchor`、`emailTopAnchor`之後用來調整位置

接下來在viewDidLoad裡面利用這兩個變數來調整anchor的定義

最後在handleChangeButtonTitle這個function內調整這兩個anchor的數值以達到移動UI的效果

下篇繼續把調整完成～
