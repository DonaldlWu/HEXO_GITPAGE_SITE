---
title: Swift隨手紀錄Day23-利用Breakpoint進行Autolayout除錯
date: 2017-07-14 09:11:54
tags: [Swift]
---

防止偷懶Day23

昨天在處理畫面元件移動的anchor的時候

沒有處理乾淨，所以執行時會出現一些問題

所以這篇就來處理一下，順便記錄一下該如何利用Breakpoint來進行Autolayout的除錯

<!--more-->

首先就來看看發生了什麼事

{% asset_img Day23Break.png %}

右下的console可以看到，應該是設定畫面的anchor時出現了一些問題

看敘述應該是重復的anchor造成其中一個沒作用

所以Xcode在console跳出了警告

雖然到這邊大該需要的資訊應該都有了

不過還是記錄一下要怎麼設定Breakpoint來看更多的資訊



首先就來設定一下Breakpoint，讓Xcode在發現UI設定上錯誤時除了在console顯示之外

還可以幫我們把需要的資訊印出來

{% asset_img Day23Breakpointsetting.png %}

從左邊的Navigator選擇Breakpoint那一欄

然後點擊左下的+號

選擇Symbolic Breakpoint...

設定Symbol為`UIViewAlertForUnsatisfiableConstraints`

然後點擊Add Action輸入`po [[UIWindow keyWindow] _autolayoutTrace]`

然後執行APP，在UI出現問題的地方就會停下來給出這個畫面整個UIWindow的資訊

{% asset_img Day23RedView.gif %}

這個資訊在元件的後方都有一串編碼

這編碼就代表了這個元件

在除錯時還可以把指定的元件變色來確定元件位置

如同圖片裡所顯示，在斷點時進入console輸入

```terminal
expr ((UIView *)0x7f84fa419500).backgroundColor = [UIColor redColor]
```
就可以將指定的畫面在繼續執行APP時變成指定的顏色



最後既然知道是anchor有重複，那就來修改一下anchor吧

順便把繼承來的nameTtext加回去

就來看下修改後的程式碼吧

```swift
import UIKit
import Firebase

class LoginController: AddMemberController {
    
    var profileImageTopAnchor: NSLayoutConstraint?
    var emailTopAnchor: NSLayoutConstraint?
    
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
            nameText.isHidden = true
            profileImageTopAnchor?.constant = 64
            emailTopAnchor?.constant = 8
        } else {
            loginButton.setTitle("Register", for: .normal)
            nameText.isHidden = false
            profileImageTopAnchor?.constant = 32
            emailTopAnchor?.constant = 48
            view.layoutIfNeeded()
        }
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()

        profileImage.removeFromSuperview()
        profileImage.removeConstraints(profileImage.constraints)
        positionSegmentedControl.removeFromSuperview()
        nameText.removeFromSuperview()
        registerButton.removeFromSuperview()
        
        view.addSubview(profileImage)
        view.addSubview(loginSegmentedControl)
        view.addSubview(nameText)
        view.addSubview(emailText)
        view.addSubview(passwordText)
        view.addSubview(loginButton)
        nameText.isHidden = true
        
        profileImageTopAnchor = profileImage.topAnchor.constraint(equalTo: view.topAnchor, constant: 64)
        profileImageTopAnchor?.isActive = true
        profileImage.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        profileImage.heightAnchor.constraint(equalToConstant: 110).isActive = true
        profileImage.widthAnchor.constraint(equalToConstant: 110).isActive = true
        
        loginSegmentedControl.topAnchor.constraint(equalTo: profileImage.bottomAnchor, constant: 8).isActive = true
        loginSegmentedControl.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 22).isActive = true
        loginSegmentedControl.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -22).isActive = true
        loginSegmentedControl.heightAnchor.constraint(equalToConstant: 24).isActive = true
        
        nameText.topAnchor.constraint(equalTo: loginSegmentedControl.bottomAnchor, constant: 8).isActive = true
        nameText.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 22).isActive = true
        nameText.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -22).isActive = true

        
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
```

昨天只靠移除constraint似乎是沒有辦法解決重複的問題

而且嘗試了幾組設定，問題應該都出在繼承過來的元件

所以試著把這些元件先移除再加回來

目前就沒有再發生constraint重複的問題了

所以再回來看看修改完的UI吧

{% asset_img Day23UI.gif %}
