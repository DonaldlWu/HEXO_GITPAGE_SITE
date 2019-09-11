---
title: 圖片cache機制
date: 2017-08-14 01:01:03
tags: [ Swift ]
---

今天來幫之前寫的ImageView extension加上cache機制

不然每次滑動畫面圖片就不斷閃動

看起來超不舒服而且一直從Firebase下載圖片超浪費效能

<!--more-->

先看code

```swift
import UIKit

let imageCache = NSCache<AnyObject, AnyObject>()

extension UIImageView {
    
    func loadImageUsingCashWithUrlString(urlString: String) {
        
        // Check cache for image
        if let cachedImage = imageCache.object(forKey: urlString as AnyObject) as? UIImage {
            self.image = cachedImage
            return
        }
        
        let url = URL(string: urlString)
        
        URLSession.shared.dataTask(with: url!) { (data, response, error) in
            if error != nil {
                print(error!)
                return
            }
            
            DispatchQueue.main.async {
                
                if let downloadedImage = UIImage(data: data!) {
                    imageCache.setObject(downloadedImage, forKey: urlString as AnyObject)
                    self.image = UIImage(data: data!)
                }
                
            }
            
        }.resume()
    }
}

```

呼叫這function時會先去看cache裡面有沒有存過

不然就用拿到的urlString去下載

下載完就用urlString當作cache object的key把圖片存起來