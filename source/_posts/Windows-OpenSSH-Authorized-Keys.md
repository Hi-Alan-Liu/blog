title: Windows OpenSSH 使用 Authorized Keys 登入的方法
author: Alan Liu
tags:
  - OpenSSh
  - FileZilla
  - Authorized Keys
  - ssh-ketgen
categories:
  - Windows
date: 2024-04-02 21:00:00
---
# Windows OpenSSH 使用 Authorized Keys 登入的方法

## 前言

因環境需求我們需要架設一台 Windows 主機讓我們遠端使用，
每次遠端都需要輸入密碼很麻煩，因此使用 Authorized Keys 免手輸密碼直接登入，

## 準備


1.下載並安裝 FileZilla : https://filezilla-project.org/

![FileZilla](1712080597425.jpg)

2.準備 SSH Key，可直接使用指令進行建立


## 開始

### 建立 administrators_authorized_keys 

首先我們要先建立一個名為 administrators_authorized_keys 的檔案

之後將 ssh-keygen 產生出來的公鑰 `xxx.pub` 貼近此檔案內

### 使用 FileZilla 上傳 administrators_authorized_keys 

接著我們使用 FileZilla 進行連線

協定選擇 `SFTP`  登入型式 `一般`

![FileZilla](1712055388767.jpg)

連上主機後

我們將位置移動到 `C:/ProgramData/ssh`

![上傳的位置](1712081530904.jpg)

並將 administrators_authorized_keys 上傳上去

基本上這樣就可以使用 ssh 連線測試囉

今天的教學就到此

### Thank you! :smile:

---

參考

- [Administrative user](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement?WT.mc_id=DOP-MVP-37580#administrative-user)

- [產生SSH Key並且透過KEY進行免密碼登入](https://xenby.com/b/220-%E6%95%99%E5%AD%B8-%E7%94%A2%E7%94%9Fssh-key%E4%B8%A6%E4%B8%94%E9%80%8F%E9%81%8Ekey%E9%80%B2%E8%A1%8C%E5%85%8D%E5%AF%86%E7%A2%BC%E7%99%BB%E5%85%A5)