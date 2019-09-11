---
title: Swift隨手紀錄Day24-Firebase-Authentication-Login & Logout
date: 2017-07-15 09:09:25
tags: [Swift]
---

防止偷懶Day24

今天來完成整個註冊、登入、登出程序

首先先來釐清需求

使用者打開APP時應該會進入HomeController

這時我們應該先判斷這使用者是否有帳號登入過

如果有就在navigationItem上更新這名使用者的名稱

如果沒有就把畫面送到LoginController

<!--more-->

首先來修改HomeController.swift裡面的程式碼

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        let newTeamButton = UIBarButtonItem(title: "New Team", style: .plain, target: self, action: #selector(toSetNewTeamController))
        navigationItem.rightBarButtonItem = newTeamButton
        
        let logoutButton = UIBarButtonItem(title: "Logout", style: .plain, target: self, action: #selector(handleLogout))
        navigationItem.leftBarButtonItem = logoutButton
        checkUserIsLogin()
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
                self.navigationItem.title = dictionary["name"] as? String
            }
        }, withCancel: nil)
    }
    
    func handleLogout() {
        
        do {
            try FIRAuth.auth()?.signOut()
        } catch let logoutError {
            print(logoutError)
        }
        
        let loginController = LoginController()
        loginController.homeController = self
        present(loginController, animated: true, completion: nil)
    }

```

我新增了兩個新的function跟修改了一個之前寫的function

這裡新增的兩個新的function分別是`checkUserIsLogin`以及`fetchUserSetNavBarTitle`

在viewDidLoad時會執行`checkUserIsLogin`這個function

裡面負責跟Firebase確認這支手機是否有登入過

如果沒有就去執行`handleLogout`讓畫面移動到LoginController

如果有就執行另一個新增的`fetchUserSetNavBarTitle`來更新從Firebase拿到的使用者名稱

然後是修改後的`handleLogout`多做了兩件事情

第一件事情是跟Firebase做登出的動作

第二件事是在實作LoginController時把hemeController的reference加進去

這樣做的目的是等等我們再處理完LoginController的登入或註冊之後可以直接來更新navigationItem的名稱

p.s.記得import Firebase


再來就進入LoginController.swift來處理登入跟註冊的程序

一樣先上程式碼

```swift
import UIKit
import Firebase

class LoginController: AddMemberController {
    
    var homeController: HomeController?
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
        button.addTarget(self, action: #selector(handleLoginRegister), for: .touchUpInside)
        return button
    }()
    
    func handleLoginRegister() {
        if loginSegmentedControl.selectedSegmentIndex == 0 {
            handleLogin()
        } else {
            handleRegister()
        }
    }
	
    func handleLogin() {
        guard let email = emailText.text, let password = passwordText.text else {
            return
        }
        FIRAuth.auth()?.signIn(withEmail: email, password: password, completion: { (user, error) in
            if error != nil {
                return
            }
            self.homeController?.fetchUserSetNavBarTitle()
            self.dismiss(animated: true, completion: nil)
        })
    }
    
    func handleRegister() {
        guard let email = emailText.text, let password = passwordText.text, let name = nameText.text else {
            return
        }
        
        FIRAuth.auth()?.createUser(withEmail: email, password: password, completion: { (user: FIRUser?, error) in
            
            if error != nil {
                print(error!)
                return
            }
            
            guard let uid = user?.uid else {
                return
            }
            
            let ref = FIRDatabase.database().reference(fromURL: "https://hbcrecord-5a3d4.firebaseio.com/")
            let userReference = ref.child("User").child(uid)
            let accountValue = ["email": email, "name": name]
            userReference.updateChildValues(accountValue, withCompletionBlock: { (err, ref) in
                
                if err != nil {
                    print(err!)
                    return
                }
                
                self.homeController?.navigationItem.title = accountValue["name"]
                self.dismiss(animated: true, completion: nil)
                
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

這裡一樣新增兩個新function跟修改一個舊function

不過為了可以更新HomeController的navigationItem.title

先新增一個homeController的物件

然後把按下註冊/登入按鈕的動作從舊的`handleRegister`換成新增的`handleLoginRegister`

在`handleLoginRegister`裡面用loginSegmentedControl所選擇的是login還是register來決定要呼叫哪個function

如果是登入就呼叫新增的第二個function`handleLogin`來跟Firebase做登入的動作以及剛剛提到的更新navigationItem.title

如果是註冊就呼叫之前寫的`handleRegister`

但這邊也對`handleRegister`做了點修改，首先是多傳了name這個值

再來是註冊成功也會去修改homeController的navigationItem.title

不過跟`handleLogin`裡面直接呼叫`fetchUserSetNavBarTitle`不同的是

因為註冊時自己就有name這個數值，就不需要多跟Firebase要一次資料了

所以直接把要傳出去註冊的name拿來用就好

來看看完成品吧

{% asset_img Day24Registerandlogin.gif %}


