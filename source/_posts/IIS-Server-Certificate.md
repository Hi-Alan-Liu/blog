title: IIS Server Certificate 憑證更新
author: Alan Liu
tags:
  - SSL
  - IIS
  - Certificate
categories:
  - SSL CERTIFICATES
date: 2023-01-30 16:06:00
---
# 更新憑證#3 IIS Server Certificate 憑證更新

## 準備


指令產生憑證，可參考 [NAME.COM SSL 憑證](https://hi-alan-liu.github.io/blog/NAME.COM-certificate/)

![準備憑證檔案](1675069465828.jpg)

*準備完畢後在進行下一步*

---

## 開始

### 第一步、產生 IIS 所需的 PFX 檔

```Linux=
sudo openssl pkcs12 -in myserver.cer -inkey myserver.key -export -out myserver.pfx
```

* myserver.cer : 為憑證公司產生的檔案

* myserver.key : 為 openssl 產生的檔案

* myserver.pfx : 此指令產生出來的檔案，供 IIS 使用

![產生 PFX](1675069171929.jpg)

### 第二步、設定 Server Certificate

點選 Server Certificate 

![設定 Certificate](1675059927328.jpg)

匯入上一步產生出來的 PFX 

![匯入 PFX](1675071077072.jpg)

### 第三步、設定 Bindings

選擇站台並點選 Bindings

![設定 Bindings](1675071194833.jpg)

設定 Port 並選擇設定好的 SSL Certificate

![設定 SSL certificate](1675071249831.jpg)

設定完畢之後重新啟動站台

最後確認憑證是否成功

![檢查憑證](1675071249835.jpg)

這樣就大功告成囉 !

### Thank you! :smile: