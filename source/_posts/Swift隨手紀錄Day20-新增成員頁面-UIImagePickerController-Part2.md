---
title: Swift隨手紀錄Day20-新增成員頁面-UIImagePickerController-Part2
date: 2017-07-11 20:58:35
tags: [Swift]
---

防止偷懶Day20

今天會把換圖像的功能做完

順便處理一些昨天把螢幕旋轉限制解除之後的幾個衍生問題處理一下

<!--more-->

第一步先來看看UIImagePicker提供了些什麼資訊給我們

進到AddMemberHandler.swift

```swift
    func handleChangeImage() {
        let imagePicker = UIImagePickerController()
        imagePicker.delegate = self
        imagePicker.allowsEditing = true
        imagePicker.modalPresentationStyle = .popover
        present(imagePicker, animated: true, completion: nil)
    }
 
    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
        
        print(info)
        
    }
```

為了可以編輯圖片的大小，先把imagePicker的allowEditing打開

然後新增didFinishPickingMediaWithInfo的function

接下來執行一下

{% asset_img Day20Info.gif %}

這裡其實我們可以看到這個函數裡的info可以提供我們五組數據

這邊我們需要圖片原本的大小，或者是有經過調整之後的大小

所以我們要用的的數據分別是`UIImagePickerControllerOriginalImage`以及`UIImagePickerControllerEditedImage`

這兩組key底下的數據

知道需求就來改寫didFinishPickingMediaWithInfo

```swift
    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {

        var selectedImageFromPicker: UIImage?
        
        if let editedImage = info["UIImagePickerControllerEditedImage"] as? UIImage {
            selectedImageFromPicker = editedImage
        } else if let originalImage = info["UIImagePickerControllerOriginalImage"] as? UIImage {
            selectedImageFromPicker = originalImage
        }

        if let selectedImage = selectedImageFromPicker {
            profileImage.image = selectedImage
        }
        dismiss(animated: true, completion: nil)
        
    }
```

function內先定義一個變數來儲存圖片

如果是經過修改的圖片就把`UIImagePickerControllerEditedImage`內的UIImage存進`selectedImageFromPicker`

如果是原圖就在`selectedImageFromPicker`內儲存`UIImagePickerControllerEditedImage`內的資料

接下來，判斷是否`selectedImageFromPicker`內有值，如果有，就把`profileImage`內的圖片替換掉

最後把畫面退出，Done

{% asset_img Day20ChangePic.gif %}

然後修正一下在AddMemberController.swift內處理keyboard升降的function

在可以旋轉螢幕的情況下，直立時keyboard就不會影響到現有畫面

所以我們先判斷使用者現在螢幕的狀態是橫躺還是直立的

如果是橫躺就做跟之前一樣的動作(抬高或降低畫面)

如果是直立就任何事都不做

```swift
    func keyboardWillShow(notification: NSNotification) {
        if UIDevice.current.orientation != .portrait {
            if let keyboardSize = (notification.userInfo?[UIKeyboardFrameBeginUserInfoKey] as? NSValue)?.cgRectValue {
                if self.view.frame.origin.y == 0{
                    self.view.frame.origin.y -= keyboardSize.height
                }
            }
        }
    }
    
    func keyboardWillHide(notification: NSNotification) {
        if UIDevice.current.orientation != .portrait {
            if let keyboardSize = (notification.userInfo?[UIKeyboardFrameBeginUserInfoKey] as? NSValue)?.cgRectValue {
                if self.view.frame.origin.y != 0{
                    self.view.frame.origin.y += keyboardSize.height
                }
            }
        }
    }
```

最後的最後稍微調整一下這一頁的畫面的anchor，因為發現在小螢幕似乎會切到

```swift
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
        profileImage.heightAnchor.constraint(equalToConstant: 110).isActive = true
        profileImage.widthAnchor.constraint(equalToConstant: 110).isActive = true
        
        positionSegmentedControl.topAnchor.constraint(equalTo: profileImage.bottomAnchor, constant: 28).isActive = true
        positionSegmentedControl.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        positionSegmentedControl.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -24).isActive = true
        positionSegmentedControl.heightAnchor.constraint(equalToConstant: 24).isActive = true
        
        
        nameText.topAnchor.constraint(equalTo: positionSegmentedControl.bottomAnchor, constant: 12).isActive = true
        nameText.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        nameText.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -24).isActive = true
        nameText.heightAnchor.constraint(equalToConstant: 30).isActive = true
        
        registerButton.topAnchor.constraint(equalTo: nameText.bottomAnchor, constant: 8).isActive = true
        registerButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        registerButton.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -24).isActive = true
        registerButton.heightAnchor.constraint(equalToConstant: 48).isActive = true
        
    }
```

下篇待續～