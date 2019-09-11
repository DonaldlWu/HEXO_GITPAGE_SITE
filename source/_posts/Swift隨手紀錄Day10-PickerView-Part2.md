---
title: Swift隨手紀錄Day10-PickerView Part2
date: 2017-07-01 20:03:45
tags: [Swift]
---

防止偷懶日記Day10

今天來把昨天做的UIPickerView繼續完成

首先先來確認目標

<!--more-->

在紀錄時需要的資料希望可以像撥洋蔥一樣一層一層的作選擇

第一層先選擇是安打(H)、保送(BB)、失誤(E)、三振(K)、犧牲飛球(SF)

第二層再更進一步選擇是一安、二安、三安還是全壘打等等

然後能夠從UIPickerView把資料回存在回到collectionView時顯示

來看看現在的UIPickerView長這樣

{% asset_img PickerView.png %}

可以看到需要兩行的選項

昨天定義了第一行的資訊，現在就來繼續定義第二行

回到RecordPickerController.swift

新增一個陣列

```swift
let recordDetialArray = [["H", "2B", "3B", "HR"], ["BB", "HBP"], ["E1", "E2", "E3", "E4", "E5", "E6", "E7", "E8", "E9"], ["K", "K"], ["SF", "SH"]]
```

接下來修改一下`numberOfComponents`、`numberOfRowsInComponent`、`titleForRow`、`didSelectRow`這幾個方法

```swift
    func numberOfComponents(in pickerView: UIPickerView) -> Int {
        return 2
    }
    
    func pickerView(_ pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
    
        if component == 0 {
            
            return recordtypeArray.count
        
        }
		
        return recordDetialArray[recordPickerView.selectedRow(inComponent: 0)].count
        
    }
    
    func pickerView(_ pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
        
        if component == 0 {
            
            return recordtypeArray[row]
        
        }
        
        return recordDetialArray[recordPickerView.selectedRow(inComponent: 0)][row]
    
    }
    
    func pickerView(_ pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
        
        if component == 0 {
        
            recordPickerView.reloadComponent(1)
            tempRecord = recordDetialArray[row][0]
        
        } else if component == 1 {
            
            tempRecord = recordDetialArray[recordPickerView.selectedRow(inComponent: 0)][row]
            
        }

    }
```

我們需要兩行，首先修改`numberOfComponents`回傳2

再來修改`numberOfRowsInComponent`，如果是第零行就回傳`recordtypeArray`的陣列數量

如果是第一行就回傳`recordDetialArray`中對應第零行被選到的對應陣列的數量

接下來是內容的修改，再呈現第零行時回傳`recordtypeArray`內與列數相對的字串

第一行則呈現`recordDetialArray`中對應第零行被選到的對應陣列的字串

然後定義當某一行某一列被選到的時候要做的動作

但在做動作之前，先定義一些輔助用的變數

```swift
var tempRecord: String = "H"
```

因為進入PickerView時預設是選擇第零列

因此如果使用者都不做動作回傳的應該是安打(H)

所以我們預設值給H

輔助用的變數設定完，就來設定被選到時的動作

如果使用者選則了第零行的某一列，應該要去重新設定第一行對應的內容

並將要回傳的資料設定成第零行被選擇那一列的字串

而當使用者選擇了第一行，就回傳`recordDetialArray`

中第零列所選擇的列數所對應到的字串陣列中與現在第一行選擇列數相對應的字串


最後來新增兩個按鈕在UIPickerView內

讓使用者可以做新增或取消的動作

並先將要執行動作的function準備好

```swift
let addButton: UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.backgroundColor = .purple
        button.layer.cornerRadius = 5
        button.layer.borderColor = UIColor.blue.cgColor
        button.layer.borderWidth = 2
        button.setTitle("UPDATE", for: .normal)
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 13)
        button.addTarget(self, action: #selector(sendRecordBack), for: .touchUpInside)
        return button
    }()
    
    let cancelButton: UIButton = {
        let button = UIButton()
        button.translatesAutoresizingMaskIntoConstraints = false
        button.backgroundColor = .purple
        button.layer.cornerRadius = 5
        button.layer.borderColor = UIColor.blue.cgColor
        button.layer.borderWidth = 2
        button.setTitle("CANCEL", for: .normal)
        button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 13)
        button.addTarget(self, action: #selector(sendRecordCancel), for: .touchUpInside)
        return button
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .green
        
        recordPickerView = UIPickerView()
        recordPickerView.frame = CGRect(x: 0, y: self.view.bounds.height / 3, width: self.view.bounds.width, height: 200)
        recordPickerView.showsSelectionIndicator = true
        recordPickerView.delegate = self
        recordPickerView.dataSource = self
        
        self.view.addSubview(recordPickerView)
        self.view.addSubview(addButton)
        self.view.addSubview(cancelButton)
        
        addButton.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -12).isActive = true
        addButton.centerXAnchor.constraint(equalTo: view.centerXAnchor, constant: 64).isActive = true
        addButton.widthAnchor.constraint(equalToConstant: 64).isActive = true
        addButton.heightAnchor.constraint(equalToConstant: 36).isActive = true
        
        cancelButton.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -12).isActive = true
        cancelButton.centerXAnchor.constraint(equalTo: view.centerXAnchor, constant: -64).isActive = true
        cancelButton.widthAnchor.constraint(equalToConstant: 64).isActive = true
        cancelButton.heightAnchor.constraint(equalToConstant: 36).isActive = true

    }
    
    func sendRecordBack() {

    }
    
    func sendRecordCancel() {

    }
	
```

以上，接下來的明天處理

今天回母校打系藍OB賽

學弟們真是用心，準備不少球賽以外的活動

今天的運動量應該是超過過去半年的總和

第一次覺得累到快吸不到氣ＱＱ

該做點運動了....