---
title: Swift隨手紀錄Day9-PickerView
date: 2017-06-30 22:15:22
tags: [Swift]
---

防止偷懶日記Day9

接下來想要實做的就是把打席結果弄成選項給使用者做決定

目前想到的是使用UIPickerView, 或者是用ActionSheet

想了想用ActionSheet好像需要用很多層

所以今天先來試試UIPickerView

<!--more-->

今天的成果大概長這樣

{% asset_img PickerView.gif %}

那就從製作UIPicerㄐView下手

先新增一個RecordPickerController.swift的檔案到Controller的Group內

```swift
import UIKit

class RecordPickerController: UIViewController, UIPickerViewDelegate, UIPickerViewDataSource {
    
    let recordtypeArray = ["H", "BB", "E", "K", "SC"]
    
    var recordPickerView: UIPickerView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .green
        
        recordPickerView = UIPickerView()
        recordPickerView.frame = CGRect(x: 0, y: self.view.bounds.height / 2, width: self.view.bounds.width, height: 200)
        recordPickerView.showsSelectionIndicator = true
        recordPickerView.delegate = self
        recordPickerView.dataSource = self
        
        self.view.addSubview(recordPickerView)

    }

    func numberOfComponents(in pickerView: UIPickerView) -> Int {
        return 1
    }
    
    func pickerView(_ pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
        return recordtypeArray.count
    }
    
    func pickerView(_ pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
        return recordtypeArray[row]
    }
    
    func pickerView(_ pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
        print("select \(recordtypeArray[row])")
    }
    
}
```

9到21行在viewDidLoad裡面實作並設定pickerView的大小、delegate、datasource

然後實作繼承UIPickerViewDelegate, UIPickerViewDataSource需要實作的numberOfRowsInComponent跟titleForRow

回傳前面設定的recordtypeArray的count跟內容


接下來回到RecordController修改一下sentButton()

```swift
    func sentRecord(sender: UIButton) {
        let controller = RecordPickerController()
        present(controller, animated: true, completion: nil)
    }

```

其實蠻偷懶的只是將畫面推到RecordPickerController，接下來應該要讓推出來的畫面只是個子畫面

放一格在現在畫面中間就好，不過這就之後再加上去

這兩天想了想UI還是需要再做一些調整，看明天想先作哪邊ＸＤ