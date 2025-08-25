title: Docker 進入容器並進行操作
author: Alan Liu
tags:
  - Docker
  - Container
  - docker exec
  - vim
categories:
  - Docker
date: 2022-10-28 17:34:00
---
# Docker 進入容器並進行操作

## 前言

Docker container(容器) 運行時，某些時候需要進入容器進行操作，因此紀錄操作方法及過程。

## 開始

### 一、使用指令進入容器

在使用指令前 先使用 ps 查看執行中的容器

```PowerShell-Scripts=
docker ps
```

![docker ps 範例](1666947455476.jpg)

使用 exec 進入容器

```PowerShell-Scripts=
docker exec -ti "CONTAINER ID" bash
```

`CONTAINER ID`: 為容器的 ID

`-i` : 保持打開 STDIN 

`-t` : 分配一個終端

![docker exec 範例](1666947596442.jpg)

這樣就成功進入容器囉!

### 二、對容器終端進行操作

進入容器後發現輸入 `vim` 無法進行操作

也無法使用 `apt-get` 進行安裝

![無法正常使用指令](1666949008877.jpg)


`ls`: 列出當前目錄所有檔案

`cd`: 進入資料夾

`vim`: 編輯檔案

---

這是由於 `apt-get` 未更新造成，所以我們應先執行以下指令

```bash=
apt-get update
```

![更新並安裝範例](1666949218307.jpg)

執行並同意安裝 `vim` 之後

就可以開始編輯容器的檔案囉 !


今天的教學就到此

### Thank you! :smile:

---

參考


- [docker exec](https://docs.docker.com/engine/reference/commandline/exec/)
- [Day12：介紹執行 Docker Container 常用到的指令(二)](https://ithelp.ithome.com.tw/articles/10191727)
