title: AWS Certificate Manager 匯入憑證
author: Alan Liu
tags:
  - SSL
  - AWS Certificate Manager
categories:
  - DevOps
  - ''
date: 2022-06-14 15:42:00
---
# 更新憑證#2 AWS Certificate Manager 匯入憑證

### 匯入憑證

進到 AWS Certificate Manager 點選匯入憑證

![AWS Certificate Manager頁面](1655192633804.jpg)

將前一篇 [NAME.COM SSL 憑證](https://pa013971.github.io/blog/2022/06/13/NAME.COM%E5%BB%BA%E7%AB%8B%E6%86%91%E8%AD%89/) 得到的憑證填入表格中

![憑證詳細資訊](1655197062093.jpg)

憑證內文為 NAME.COM 的 SERVER CERTIFICATE

![SERVER CERTIFICATE](1655198650080.jpg)

憑證私有金鑰為 openssl 產出來的 Key

![openssl private key](1655198688389.jpg)

憑證鏈為 NAME.COM 的 CA CERTIFICATES

![CA CERTIFICATES](1655198667714.jpg)

內文都填入後即可看到此憑證的到期時間

![憑證資訊](1655198974266.jpg)

再將憑證綁定於其他 AWS 的功能 

![負載平衡SSL綁定範例](1655200003246.jpg)

這樣就大功告成囉!

### Thank you! :smile: