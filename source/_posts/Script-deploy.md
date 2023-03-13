title: Package Script 新增自動佈署指令
author: Alan Liu
tags:
  - Package
  - npm
  - script
  - scp
  - deploy
categories:
  - Tool
date: 2023-03-13 15:38:00
---
# Package Script 新增自動佈署指令

## 準備

1.設定 SSH 金鑰 ( Public Key Authentication)

*準備完畢後在進行下一步*

---

## 開始

### 第一步、製作 Shell Script 

專案內與 package.json 同層新增一個 deploy.sh 檔案

![新增 deploy.sh](1678691653853.jpg)

deploy.sh 內容如下

```ShellScript=
# deploy.sh

set -e

scp -r dist/ *UserName@*ServerName:/home/*UserName/*ProjectName
```

* scp : 可以在不同的主機之間複製檔案

* -e : 代表後面會執行跳脫字元像是 \n，-n 則會略過。

* -r : 指定資料夾

* *UserName : 為伺服器使用者名稱

* *ServerName : 為伺服器Domain或IP

* *ProjectName : 為專案名稱

這個指令是為了將 dist 整個資料夾上傳至指定 server 的位置上


### 第二步、Package 新增指令

在 package.json 裡面的 scripts 新增 deploy 指令

```package.json=
# package.json

{
  "name": "ProjectName",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "dev": "vite --host",
    "build": "vite build",
    "deploy": "npm run build && sh deploy.sh prod"
  },...(以下省略)
```

![新增 scripts](1678692308368.jpg)

### 第三步、執行指令

``` Linux=
 npm run deploy
```

這個指令會執行 Build 產生 dist 資料夾後
幫我們執行 sh 指令將 dist 資料夾丟至指定 server

這樣下次更新專案就方便多囉 !

今天的分享就到此結束

### Thank you! :smile: