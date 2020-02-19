---
title: 利用 Bash 簡化 iOS ipa OTA 發佈流程 (使用 Dropbox)
date: 2020-02-18 09:10:11
tags: [OTA]
---

去年 Fabric 確定合併回 Firebase 後就決定要另外找地方放出測試版給 QA

在內網架了一個簡單的網站

讓 QA 可以用手機打開網頁直接下載測試版 app

但因為 OTA 機制需要 Https 

存放 manifest.plist 跟 ipa 的地方就選擇了 Dropbox 

選擇的原因讓我後面再詳述

<br/>

然後因為手動上傳檔案跟 manifest 的修改過程太繁雜了，實在不是給人用的

簡單敘述就是

<br/>

1. 包好 ipa

2. 上傳 ipa 到 Dropbox

3. 分享 ipa 的鏈結

4. 將分享鏈結填入 manifest.plist

5. 將 manifest.plist 上傳至 Dropbox

6. 分享 manifest.plist 的鏈結

7. 將 manifest.plist 的鏈結的內容從 dropbox.com 

修改成下載用鏈結 dl.dropboxusercontent.com

8. 依照 itms-service 協議的格式將修改好的 manifest.plist 的下載鏈結放入網站

<br/>

光寫完這一串就累了 @@

所以就決定寫點 bash 幫忙處理上面那一大串雜事吧

因為太多了這篇就只處理從 ipa 上傳到 dropbox 到可以下載的部分

以後有空再補其他像是包版的坑拉，CICD 任務的觸發等等的東西

<!--more-->

首先是選擇 Dropbox 的理由

首先是 OTA 需要下載位置支援 https

自己架在內網內的 Server 不太想弄

<img src="https://dl.dropboxusercontent.com/s/rucfbg0xbxo1w7b/%E7%9B%B8%E7%89%87%202020-2-13%20%E4%B8%8B%E5%8D%883%2001%2035.jpg">

<br/>

所以目標就轉移至簡單使用的雲端儲存服務

看了一下網路上也很多人分享使用 Dropbox 的文章

實作起來並不困難

最重要的是我在 Dropbox 的文件上找到了我需要的功能

就是能利用 POST 進行*檔案的上傳*以及前面提到的*分享鏈結的產生*

<br/>

這樣目標就明確了

在 ipa 打包完成之後，執行 script 去執行上面那一串工作

首先打開 [Dropbox 文件](https://www.dropbox.com/developers/documentation/http/documentation)

來看看這遊戲怎麼玩

### 上傳 API

找到 `upload` 這隻 API 的說明

```
curl -X POST https://content.dropboxapi.com/2/files/upload \
    --header "Authorization: Bearer <get access token>" \
    --header "Dropbox-API-Arg: {\"path\": \"/Homework/math/Matrices.txt\",\"mode\": \"add\",\"autorename\": true,\"mute\": false,\"strict_conflict\": false}" \
    --header "Content-Type: application/octet-stream" \
    --data-binary @local_file.txt
```

其中需要修改的位置有三個

首先是在 `Bearer` 後面要換成自己 Dropbox 帳號的 token

Dropbox 也很貼心的把 <get access token> 弄成一個按鈕

登入之後點下去就會顯示自己的 token 了

第二個是設定 Dropbox 上要儲存的路徑

也就是範例中 `/Homework/math/Matrices.txt` 的部分

最後是要上傳的本地檔案位置

也就是最後一行的 `local_file.txt` 

<br/>
### 取得分享鏈結位置

一樣找到文件裡面 `create_shared_link_with_settings` 的說明

```
curl -X POST https://api.dropboxapi.com/2/sharing/create_shared_link_with_settings \
    --header "Authorization: Bearer " \
    --header "Content-Type: application/json" \
    --data "{\"path\": \"/Homework/math/Matrices.txt\"}"
```

這隻 API 也是設定好 token

然後指定 Dropbox 上要分享的文件位置

對這次要達成的目標而言，重點在這隻 API 的 response

```
{
    ".tag": "file",
    "url": "https://www.dropbox.com/s/2sn712vy1ovegw8/Prime_Numbers.txt?dl=0",
    "name": "Prime_Numbers.txt",
    "link_permissions": {
        "can_revoke": false,
        "resolved_visibility": {
            ".tag": "public"
        },
        "revoke_failure_reason": {
            ".tag": "owner_only"
        }
    },
    "client_modified": "2015-05-12T15:50:38Z",
    "server_modified": "2015-05-12T15:50:38Z",
    "rev": "a1c10ce0dd78",
    "size": 7212,
    "id": "id:a4ayc_80_OEAAAAAAAAAXw",
    "path_lower": "/homework/math/prime_numbers.txt",
    "team_member_info": {
        "team_info": {
            "id": "dbtid:AAFdgehTzw7WlXhZJsbGCLePe8RvQGYDr-I",
            "name": "Acme, Inc."
        },
        "display_name": "Roger Rabbit",
        "member_id": "dbmid:abcd1234"
    }
}
```

會回傳一個 json 格式的資料

其中 `url` 這個欄位所帶回來的值就是我們這次的目標了

現在有了這兩隻 API ，就可以達成上傳檔案並拿到分享鏈結的目標了

<br/>

### manifest.plist

接下來要處理的就是讓 app 能正確被手機下載所需要的 manifest.plist 了

最簡單的取得方式就是直接使用 Xcode 打包一次 development 的版本發佈

裡面就會有產生 manifest.plist 的選項了

這裡給出來的是使用 Development 打包發佈 ipa 的版本(因為是給自家 QA 測試嗎)

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>items</key>
	<array>
		<dict>
			<key>assets</key>
			<array>
				<dict>
					<key>kind</key>
					<string>software-package</string>
					<key>url</key>
					<string>https://dl.dropboxusercontent.com/s/dropbox_shared_link/demo.ipa</string>
				</dict>
			</array>
			<key>metadata</key>
			<dict>
				<key>bundle-identifier</key>
				<string>{your_bundle_identifier}</string>
				<key>bundle-version</key>
				<string>now_build_number</string>
				<key>kind</key>
				<string>software</string>
				<key>platform-identifier</key>
				<string>com.apple.platform.iphoneos</string>
				<key>title</key>
				<string>demo</string>
			</dict>
		</dict>
	</array>
</dict>
</plist>
```

這裡面需要修改兩個地方

一個是你 app 的 `bundle identifier`

直接取代掉 `{your_bundle_identifier}` 

另外是將 `url`, `title` 兩個 key 底下的 demo 換成你的 ipa名稱 與 app 名稱即可

<br/>

manifest.plist 中還有兩個地方是等等我們在寫 BASH

的時候希望能自動幫我們取代的東西

一個是 `dropbox_shared_link` 另一個就是 `bundle-version`

這樣以後執行時帶入取得的下載位置連結跟版本號就輕鬆多了

這裡為何只需要修改 `dropbox_shared_link` 我們文章最後一起討論

<br/>

### 目標細節 & 檔案配置

作為一個減少人工作業的配置，當然需要多將一些繁雜的工作考慮進去

<br/>

##### ipa path

首先是 ipa 檔案打包好之後存放的位置跟上傳之後在 Dropbox 上的位置

希望可以簡單表達出這是哪一個版本，哪一個環境

因此在建置機器的本地端會將檔案輸出到 

`/{project_name}/{build_version}({build_number}){env}`

的目錄底下

並且上傳到 Dropbox 上相同的資料夾位置

因此假設我的專案名稱叫做 demo

這版本是 staging 環境的 1.0.1(345) 版本

打包好我就把它 export 到 `/demo/1.0.1(345)staging` 這個資料夾底下

這樣經過一段時間回來也能方便找到對的版本

<br/>

##### manifest.plist 重複利用

作為大多內容不需要改動的 manifest.plist 檔案

上面的範例檔案的參數就是希望能不需要每次都手動重新配制 manifest.plist

因此確認寫好的 manifest.plist 正確放置在建置機器下的路徑位置，並記錄下來

這樣等一下寫 BASH 檔案的時候我們就知道該去哪裡找到它了

如此一來，只要執行完再利用改動將檔案回復為使用前的狀態

就可以重複利用這個 manifest.plist 的範例檔案了

以後就可以不理他囉

<br/>

### BASH 

準備工作都完成了，開始來寫關鍵的執行腳本吧

重新回顧一下前面的步驟，並搭配我們的工具

可以重新整理出這個 BASH 檔案實際執行需要的配置與動作如下

1. 接收三個外部參數 {version_number}, {build_number}, {env}

2. 上傳 ipa 檔案到 Dropbox

3. 取得 ipa Dropbox 分享鏈結

4. 處理回傳資訊並拿出需要的鏈結位置資訊

5. 將位置資訊以及 {version_number} 寫入 manifest.plist

6. 上傳 manifest.plist

7. 將本地 manifest.plist 回復為原本的設定

8. 取得 manifest.plist Dropbox 分享鏈結

9. 處理回傳資訊取得可以用來下載 app 的 url

如下範例檔案

upload_dropbox_share_tamplate.sh

```
# Step1 Get parameter
echo $1
echo "version number"
echo $2
echo "build number"
echo $3
echo "env"
# prod/preprod/staging/dev

echo "<your_file_location>/demo$1($2)$3"

# Step2 upload ipa to dropbox
curl -X POST https://content.dropboxapi.com/2/files/upload \
--header "Authorization: Bearer <access_token>" \
--header "Dropbox-API-Arg: {\"path\": \"/demo/$1($2)$3/demo.ipa\",\"mode\": \"add\",\"autorename\": true,\"mute\": false,\"strict_conflict\": false}" \
--header "Content-Type: application/octet-stream" \
--data-binary "@<your_file_location>/demo$1($2)$3/demo.ipa"

#---------

# Step3 Create ipa file shared_link
response=$(curl -X POST https://api.dropboxapi.com/2/sharing/create_shared_link_with_settings \
--header "Authorization: Bearer <access_token>" \
--header "Content-Type: application/json" \
--data "{\"path\": \"/demo/$1($2)$3/demo.ipa\"}"
)

# Step4 Handle response
# replce response string ex: "https://www.dropbox.com/s/tcw9p8uxrlwzw09/xxx.ipa?dl=0....."
echo $response
# cut dl=0
left=${response%?dl=0*}
# cut /xxx.ipa
ipaName=${left%/*}
# cut https://www.dropbox.com/s/
link=${ipaName#*s/}
echo $link

# Step5 Set manifest file
# dropbox_shared_link write into manifiest.plist
sed -i '' "s/dropbox_shared_link/${link}/g" <your_file_location>/manifest.plist
sed -i '' "s/now_build_number/$1/g" <your_file_location>/manifest.plist

# Step6 Upload manifest.plist
curl -X POST https://content.dropboxapi.com/2/files/upload \
--header "Authorization: Bearer <access_token>" \
--header "Dropbox-API-Arg: {\"path\": \"/demo/$1($2)$3/manifest.plist\",\"mode\": \"add\",\"autorename\": true,\"mute\": false,\"strict_conflict\": false}" \
--header "Content-Type: application/octet-stream" \
--data-binary "@<your_file_location>/manifest.plist"

# Step7 Reset dropbox_shared_link in manifiest.plist
sed -i '' "s/${link}/dropbox_shared_link/g" <your_file_location>/manifest.plist
sed -i '' "s/$1/now_build_number/g" <your_file_location>/manifest.plist

# Step8 share_menifest.plist
response_manifest=$(curl -X POST https://api.dropboxapi.com/2/sharing/create_shared_link_with_settings \
--header "Authorization: Bearer <access_token>" \
--header "Content-Type: application/json" \
--data "{\"path\": \"/demo/$1($2)$3/manifest.plist\"}"
)

# Step9 Handle response
# replce response string ex: "https://www.dropbox.com/s/tcw9p8uxrlwzw09/manifest.plist?dl=0"
echo $response_manifest
# cut dl=0
left_m=${response_manifest%?dl=0*}
# cut /manifest.plist
ipaName_m=${left_m%/*}
# cut https://www.dropbox.com/s/
link_m=${ipaName_m#*s/}
echo $link_m
```

使用上非常簡單，依照上方範例

修改好裡面檔案路徑位置之後

在 terminal 下移動到存放這個 upload_dropbox_share_tamplate.sh 的位置

執行

```bash
sh upload_dropbox_share_tamplate.sh {version_number} {build_number} {env}
```

執行完會回覆一組字串

將這組字串組好 Dropbox 下載鏈結並放入網站中

點擊連結就可以下載 app 拉

<br/>

### 關於 OTA 原理與 Dropbox 下載鏈結

實作完了我們就來看看前面沒有提到的部分吧

簡單來說就我理解到的 OTA(Over-the-Air) 安裝

是瀏覽器利用 itms-service 協議讓使用者的手機取得 manifest.plist 裡的資訊

app 名稱，版本，下載位置等等，並觸發下載流程

[Over-the-Air Documentation](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/iPhoneOTAConfiguration/OTASecurity/OTASecurity.html)

因此在 manifest.plist 中需要明確指定 ipa 的下載位置

而網站裡也需要有 manifest.plist 的下載位置讓瀏覽器去進行下載動作

為此我們必須將上傳至 Dropbox 並且取得可以用來*直接*下載的鏈結

依照 Dropbox 的說明，想要直接下載時

可以使用 `dl.dropboxusrecontent` 的路徑來取得檔案

並且可以發現下載鏈結的格式都可以統一成 `https://dl.dropboxusercontent.com/s/{dropbox_shared_link}/filename`

而取得分享鏈結的 api 回傳的 url 欄位格式會是 `https://www.dropbox.com/s/{dropbox_shared_link}/filename`

所以在處理下載鏈結時，只需要取出中間 {dropbox_shared_link} 部分的字串就可以了就可以了

BASH 檔案裡的 Step 3, Step 8 我就直接針對 response 的字串結果去處理

直接把前後的字通通摳掉，就可以得到我需要的 {dropbox_share_link} 字串了

再將這字串做需要的處理即可，像是 Step 4 塞入 manifest.plist

<br/>

範例檔案最後只有將 manifest.plist 的下載需要的 {dropbox_share_link}

這一串字印出來而已

其實還可以做後續的處理

像我是把它組成正確的下載鏈結，然後傳到 slack 跟更新到網站上

這樣搭配 CI + fastlane 就能完成 push code 完成就自動打包好送到 QA 手上拉

又朝著薪水小倫的目標更近一步了呢 0.0b

<img src="https://dl.dropboxusercontent.com/s/d9jnudaenk9sav1/%E7%9B%B8%E7%89%87%202020-2-19%20%E4%B8%8B%E5%8D%886%2018%2054.jpg">

<br/>

有空再來寫寫打包的故事 emmm ....

<img src="https://dl.dropboxusercontent.com/s/02nokkwa0l06qji/%E7%9B%B8%E7%89%87%202020-2-19%20%E4%B8%8B%E5%8D%884%2028%2024.jpg">

