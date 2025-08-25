title: Octopus Deploy Server License 過期解決辦法
author: Alan Liu
tags:
  - Octopus Deploy
  - License
  - Master Key
categories:
  - Other
date: 2022-10-12 15:14:00
---
# Octopus Deploy Server License 過期解決辦法

## 前言

由於公司使用的佈署工具過期，造成無法正常佈署專案。
因此需 Renew License 並設定至程式軟體中。

![Octopus Log](1665543125338.png)

而過期的 license key 已與 master key 做綁定
因此需先移除並重新綁定。

## 開始

### 一、重新申請 License

申請網址: https://octopus.com/upgrade

填入當初申請的 email 及 license 或 serial number

![Manage your licenses](1665545746087.jpg)

登入並進入後點擊 Renew license 即可取得新的 license key

![Renew License](1665545918617.jpg)

取得 license key 先存起來後續會使用到

### 二、儲存 Master Key

處理好 License 後，接著回到 Octopus Manager

![Octopus Manager](1665543125341.jpg)

(※重要) 將 master key 顯示並儲存起來

![Save Master Key](1665543185709.jpg)

### 三、刪除 Octopus instance

因原本的 license key 已與 master key 綁定
因此需刪除現在的 instance 重新跑綁定流程

這個過程不會動到 DB 因此不用擔心

![Octopus Manager](1665543218541.jpg)

![Octopus Delete](1665543218543.jpg)

![Octopus Delete](1665543298211.jpg)

刪除後，重新跑設定流程

### 四、Octopus Setup Wizard

重新設定 Octopus

將第一步驟申請的 License 貼上

![Set License key](1665543472322.jpg)

設定基礎目錄

![Set Home](1665543481232.jpg)

設定使用者

![Set Home](1665543490733.jpg)

設定 Database

*建議延用上次的 Database 以免丟失設定檔

![Set Database](1665543504034.jpg)

如果延用上次的 Database 則需要填入第二步驟儲存的 Master Key

![Set Master Key](1665543540226.jpg)

設定 Web Portal

![Set Web Portal](1665543569752.jpg)

設定 Authenticaation

因延用上次的 Database ；所以我選擇不建立 administator

![Set Authenticaation](1665543590243.jpg)

安裝 Octopus 

![Installation Octopus](1665543601011.jpg)

設定到這裡基本上大功告成囉 ~

### 補、設定 Bindings

設定對外連線的網址

![Change Bindings](1665543692362.jpg)

![Add Binding](1665543749573.jpg)

![Web Portal Bindings](1665543861099.jpg)


今天的教學就到此 ~

### Thank you! :smile: