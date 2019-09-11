---
title: Swift隨手紀錄Day18-新增成員頁面-UIView-SegmentedControl
date: 2017-07-09 23:07:14
tags: [Swift]
---

防止偷懶Day18

講了好久的新增成員終於要來做了＠＠

預想中希望有一個UIImageView來選擇頭像

當然也要一個TextField來輸入名字，以及註冊用的button

然後有一個選擇守備位置的功能

想了一想，用之前玩過的SegmentedControl來試試效果好了

GO

<!--more-->

照例先看看完成的畫面

{% asset_img Day18UIView.png %}

先看code吧

```swift
import UIKit

class AddMemberController: UIViewController {
    
    let positionArray = ["P", "C", "1B", "2B", "3B", "SS", "LF", "CF", "RF"]
    
    override var prefersStatusBarHidden: Bool {
        return false
    }
    
    let profileImage: UIImageView = {
        let image = UIImageView()
        image.translatesAutoresizingMaskIntoConstraints = false
        image.image = UIImage(named: "picture")
        image.contentMode = .scaleAspectFill
        image.layer.cornerRadius = 5
        image.layer.masksToBounds = true
        return image
    }()
    
    let positionSegmentedControl: UISegmentedControl = {
        let sc = UISegmentedControl(items: ["P", "C", "1B", "2B", "3B", "SS", "LF", "CF", "RF"])
		sc.selectedSegmentIndex = 0
        sc.translatesAutoresizingMaskIntoConstraints = false
        return sc
    }()
    
    let nameText: UITextField = {
        let text = UITextField()
        text.translatesAutoresizingMaskIntoConstraints = false
        text.placeholder = "Input Name"
        text.layer.borderColor = UIColor.cyan.cgColor
        text.layer.borderWidth = 1
        return text
    }()
    
    let registerButton: UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.backgroundColor = .cyan
        button.setTitle("Register", for: .normal)
        button.addTarget(self, action: #selector(registerNewPlayer), for: .touchUpInside)
        return button
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        NotificationCenter.default.addObserver(self, selector: #selector(AddMemberController.keyboardWillShow), name: NSNotification.Name.UIKeyboardWillShow, object: nil)
        NotificationCenter.default.addObserver(self, selector: #selector(AddMemberController.keyboardWillHide), name: NSNotification.Name.UIKeyboardWillHide, object: nil)
        view.backgroundColor = .white
        
        let backButton = UIBarButtonItem(title: "BACK", style: .done, target: self, action: #selector(backHome))
        navigationItem.leftBarButtonItem = backButton
        
        view.addSubview(profileImage)
        view.addSubview(positionSegmentedControl)
        view.addSubview(nameText)
        view.addSubview(registerButton)
        
        profileImage.topAnchor.constraint(equalTo: view.topAnchor, constant: 64).isActive = true
        profileImage.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        profileImage.heightAnchor.constraint(equalToConstant: 130).isActive = true
        profileImage.widthAnchor.constraint(equalToConstant: 130).isActive = true
        
        positionSegmentedControl.topAnchor.constraint(equalTo: profileImage.bottomAnchor, constant: 32).isActive = true
        positionSegmentedControl.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        positionSegmentedControl.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -24).isActive = true
        positionSegmentedControl.heightAnchor.constraint(equalToConstant: 24).isActive = true
        
        
        nameText.topAnchor.constraint(equalTo: positionSegmentedControl.bottomAnchor, constant: 12).isActive = true
        nameText.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        nameText.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -24).isActive = true
        nameText.heightAnchor.constraint(equalToConstant: 50).isActive = true
        
        registerButton.topAnchor.constraint(equalTo: nameText.bottomAnchor, constant: 12).isActive = true
        registerButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        registerButton.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -24).isActive = true
        registerButton.heightAnchor.constraint(equalToConstant: 50).isActive = true
        
    }
    
    func registerNewPlayer() {
        self.dismiss(animated: true, completion: nil)
    }
    
    func backHome() {
        self.dismiss(animated: true, completion: nil)
    }
    
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        view.endEditing(true)
    }
    
    func keyboardWillShow(notification: NSNotification) {
        if let keyboardSize = (notification.userInfo?[UIKeyboardFrameBeginUserInfoKey] as? NSValue)?.cgRectValue {
            if self.view.frame.origin.y == 0{
                self.view.frame.origin.y -= keyboardSize.height
            }
        }
    }
    
    func keyboardWillHide(notification: NSNotification) {
        if let keyboardSize = (notification.userInfo?[UIKeyboardFrameBeginUserInfoKey] as? NSValue)?.cgRectValue {
            if self.view.frame.origin.y != 0{
                self.view.frame.origin.y += keyboardSize.height
            }
        }
    }
	
	override func viewWillDisappear(animated: Bool) {
        super.viewWillDisappear(animated)
        NSNotificationCenter.defaultCenter().removeObserver(self)
    }
    
}
```

11~44先實作需要的元件，UIImageView、UISegmentedControl、UITextField、UIButton

接下來在viewDidLoad將上面四個元件作為subView加入畫面跟設定anchor

在UISegmentedControl內可以設定一個[Any]!的陣列

這裡我們需要九個守備位置，因此放入`["P", "C", "1B", "2B", "3B", "SS", "LF", "CF", "RF"]`

然後為了避免之後使用者忘記這個欄位把selectedSegmentIndex預設為0

再來是83行點擊註冊鈕觸發的registerNewPlayer function，這邊先讓點擊後畫面在這邊退出回到上一頁

之後這邊準備把資料傳到Firebase上做儲存(應該很快.....吧（￣ c￣）y▂ξ)


最後是91行之後的四個function是負責處理點擊UItextField之後畫面出現keyboard的附加動作

第一個function處理點擊keyboard以外的部分會收起keyboard

第二跟第三個function則是分別負責在keyboard出現之後把畫面往上抬高，讓使用者依然看得到UItextField

以及收起來之後畫面還原的工作，而為了處理這兩個動作必須在viewDidLoad裡面設置一個觀察者來觀察keyboard的活動

因此在48、49行加入了NSNotificationCenter

然而加上NSNotificationCenter之後還是得記得在畫面消失之後回收

因此最後在viewWillDisappear裡面remove，完工

完成的畫面動作如下

{% asset_img Day18.gif %}












