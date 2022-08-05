title: NAME.COM建立憑證
author: Alan Liu
tags:
  - SSL
  - CSR
  - NAME.COM
categories:
  - DevOps
  - ''
date: 2022-06-13 11:48:00
---
# 更新憑證#1 NAME.COM SSL 憑證更新

### 第一步、申請 SSL CERTIFICATES 

請至 NAME.COM 申請 SSL CERTIFICATES 並立即設置

![SSL CERTIFICATES 申請範例圖](1655101790321.jpg)

填寫網域個人資料

![Contact information 示意圖](1655101880682.jpg)

留 CERTIFICATE SIGNING REQUEST (CSR) 先不填


### 第二步、產生 SSL 憑證簽署要求（CSR）

```Linux=
sudo openssl req -nodes -newkey rsa:2048 -sha256 -keyout myserver.key -out myserver.csr -utf8
```

* -newkey rsa:2048： 產生 CSR 與一個 2048 位元的 RSA 密鑰；

* -sha256：使用 SHA-2，SHA256 雜湊演算法（hash algorithm）

* -keyout myserver.key：將密鑰儲存在當下目錄的「myserver.key」檔案中。

* -out myserver.csr：將 CSR 檔案儲存在當下目錄的「myserver.csr」檔案中。

執行命令之後，系統會提示您輸入資料，這資料是憑證驗證單位在驗證憑證時會查閱的個人資料

![填寫憑證資料範例圖](1655109081353.jpg)

* Country Name 二字元國碼 : TW

* State or Province Name 州或省名稱 : Taiwan

* Locality Name 城市名稱 : Kaohsiung

* Organization Name 公司名稱 : Company Name CO., LTD.

* Organization Unit Name 部門名稱 : (可不填)

* Common Name 網域名 : www.google.com

* Email Address 電子信箱 : (可不填)

* A challenge password 密碼 : (可不填)

* An optional company name 其他的公司名稱 : (可不填)

資料填寫完獲得 「myserver.key」 及 「myserver.csr」兩個檔案

請將這產生出來的檔案保存好，以便未來使用

![獲得憑證資料](1655110345826.jpg)

### 第三步、完成設定並取得憑證 (crt)

將剛剛產生的 csr 複製內文並貼入 CERTIFICATE SIGNING REQUEST (CSR)

![CERTIFICATE SIGNING REQUEST](1655111563482.jpg)

空格右方會顯示資訊是否正確

如果都沒問題就憑證中心將會給予中繼憑證 (CRT or PEM)

![取得 CRT](1655111920824.jpg)


得到這些憑證接下來就可以放置伺服器中囉 !


### Thank you! :smile:
---

參考

- [私有金鑰、CSR 、CRT 與 中繼憑證](https://haway.30cm.gg/ssl-key-csr-crt-pem/)