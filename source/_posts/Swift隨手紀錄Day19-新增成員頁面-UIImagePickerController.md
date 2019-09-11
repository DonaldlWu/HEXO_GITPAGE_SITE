---
title: Swift隨手紀錄Day19-新增成員頁面-UIImagePickerController-Part1
date: 2017-07-10 21:26:48
tags: [Swift]
---

防止偷懶Day19

繼續來製作新增成員頁面的新功能

今天就來讓圖像可以點擊然後更換成手機內的圖片

就用UIImagePicker來處理這功能

<!--more-->

進入到AddMemberController.swift

找到負責生成ImageView的profileImage

稍微做一點修改

```swift
    lazy var profileImage: UIImageView = {
        let image = UIImageView()
        image.translatesAutoresizingMaskIntoConstraints = false
        image.image = #imageLiteral(resourceName: "pied piper")
        image.contentMode = .scaleAspectFill
        image.layer.cornerRadius = 5
        image.layer.masksToBounds = true
        image.addGestureRecognizer(UITapGestureRecognizer(target: self, action: #selector(handleChangeImage)))
        image.isUserInteractionEnabled = true
        return image
    }()
    
    func handleChangeImage() {
        print("123")
    }
```

首先是新增了新的手勢偵測，第一步我們先看是不是能正確運作

所以在偵測到手勢之後執行handleChangeImage這個function

這個function則先印出123這個字串

然後把isUserInteractionEnabled設定成true

這邊需要將原本實作時使用的let改成lazy var，不然是不會有動作的

先來看看成果

{% asset_img Day19Gesture.gif %}

確定手勢可以作用之後就要開始定義function的內容拉

但是在開始定義之前我們可以看到整個AddMemberController.swift其實已經寫了不少內容了

為了之後再回來看程式碼的時候不會有太多想翻桌的衝動

我們就把這個要新增的handleChangeImage寫到別的檔案裡面吧

{% asset_img Day19Addmemberhandler.png %}

如圖所示，我們在Controller裡面新增一個group叫做Extension

裡面新增一個AddMemberHandler.swift的檔案

接下來處理選照片動作的程式碼就都集中在這邊

避免同一檔案裡面太多內容不好閱讀

```swift
import UIKit

extension AddMemberController: UIImagePickerControllerDelegate, UINavigationControllerDelegate {
    
    func handleChangeImage() {
        let imagePicker = UIImagePickerController()
        imagePicker.delegate = self
        present(imagePicker, animated: true, completion: nil)
    }
    
}
```

OK，執行下去

保證crash...

看一下console吧

應該會給出這樣子的內容

```terminal
This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSPhotoLibraryUsageDescription key with a string value explaining to the user how the app uses this data.
```

因為iOS 10以後對個人資料的存取有很多限制所以要去info.plist修改一下

{% asset_img Day19Photoprivacy.gif %}

新增一個欄位選擇Privacy - Photo Library Usage Description

然後在後面選擇跳出要求權限視窗的文字

一般來說到這裡就應該可以看到需要的UIImagePickerController了

但是執行下去應該會像下面這樣

{% asset_img Day19Orientationcrash.gif %}

首先這個可以看到UIImagePickerController預設是在垂直畫面上表現的

不是我們需要的橫躺畫面，接下來點擊OK之後就直接crash了

一樣看向console

```terminal
'Supported orientations has no common orientation with the application, and [PUUIAlbumListViewController shouldAutorotate] is returning YES'
```

看來是因為shouldAutorotate預設的回傳是ture，所以讓我們這只能橫躺的APP沒辦法處理拉

比較簡單的處理就是把這個UIImagePickerController做點修改

以跳出框來表現UIImagePickerController

```swift
    func handleChangeImage() {
        let imagePicker = UIImagePickerController()
        imagePicker.delegate = self
        imagePicker.modalPresentationStyle = .popover
        present(imagePicker, animated: true, completion: nil)
    }
```

{% asset_img Day19UIImagepicker.gif %}

這邊雖然在iPhone7 Plus模擬器上成功了

但是換成其他模擬器跟實機來跑的時候依然會發生crash

目前我還沒找到其他比較好的解法

看官方文件似乎必須要是垂直的畫面才行

因此目前先把不能旋轉的限制解除

看之後有想到方法處理再決定是否需要鎖定畫面方向

回到Deployment Info修改設定

{% asset_img Day19RotationSetting.png %}

但這樣子會有一點小問題發生在RecordController

畫面從垂直旋轉到橫躺時必須要讓collectionView重新調整一次

不然畫面會維持垂直時的大小

所以回到RecordController.swift在viewDidLoad內加上一個觀察者來確認螢幕是不是旋轉過

```swift
NotificationCenter.default.addObserver(self, selector: #selector(didRotation), name: NSNotification.Name.UIDeviceOrientationDidChange, object: nil)
```

當然也要定義旋轉後的動作，我們讓collectionView重新載入

最後記得在離開時移除掉觀差者

```swift
    func didRotation() {
        collectionView?.reloadData()
    }
	
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        NotificationCenter.default.removeObserver(self)
    }
```

下篇待續～


