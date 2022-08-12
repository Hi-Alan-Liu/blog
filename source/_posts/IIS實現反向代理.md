title: IIS 實現反向代理
tags:
  - Reverse Proxy
  - IIS
  - URL Rewrite
categories:
  - DevOps
  - ''
date: 2022-05-04 20:02:00
---
# IIS 實現反向代理


## 前言

反向代理（Reverse Proxy）方式是指以代理伺服器來接受internet上的連接請求，然後將請求轉發給內部網絡上的伺服器，並將從伺服器上得到的結果返回給internet上請求連接的客戶端，此時代理伺服器對外就表現為一個反向代理伺服器。

---

## 準備

IIS

Application Request Routing
下載網址：https://www.iis.net/downloads/microsoft/application-request-routing

URL Rewrite
下載網址：https://www.iis.net/downloads/microsoft/url-rewrite

當下載並安裝完成後 IIS 會多出兩個功能

![準備完成樣子](1651667908894_new.jpg)

---

## 開始

第一步、啟動代理
![點擊設置](1651667908894_1.jpg)

![點擊反向代理設定](1651668036918_2.jpg)

![將反向代理開啟](1651668062343_3.jpg)


第二步、建立網站
![建立網站](1660295445854.jpg)

`實體路徑` 設定至專案資料夾

第三步、新增反向代理，設置 URL 規則

![點擊站台設定URL Rewrite](1651668152705_1.jpg)

![新增規則 / 空白規則](1651668152705_2.jpg)

![設定並套用](1651668152705_3.jpg)

`模式`  ^(.*)

`重寫 URL`  http://localhost:3000/{R:1}

設置完畢即完成