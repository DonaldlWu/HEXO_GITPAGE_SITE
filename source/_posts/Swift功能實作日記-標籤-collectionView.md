---
title: Swift功能實作日記-標籤-collectionView
date: 2017-10-31 22:25:23
tags: [collectionView]
---

由於Swift內UICollectionViewFlowLayout所產生的佈局與我要的需求不同

因此在網路上找到了有大大寫出了解決辦法

就趕緊來記錄一下最後的的做法

<!--more-->

首先來確認一下需求

原本希望利用collectionView將tag排列好

tag的大小取決於內容字串長度

當tag排列到超過collectionView的contentView寬度的話就換行

但實際上做出來會長這樣

UICollectionViewFlowLayout排列的優先順序不如預期

無法有效的靠左對齊

{% asset_img Two.png %}

在繼承UICollectionViewFlowLayout重新對layout方式進行改寫之後

{% asset_img One.png %}

[Github](https://github.com/DonaldlWu/LeftTagDemo/tree/master)
