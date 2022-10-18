title: Docker 生成 Images 並更新至 Docker Hub
author: Alan Liu
tags:
  - Docker
  - Docker hub
  - Docker Images
  - Repository
categories:
  - Docker
date: 1970-01-01 08:00:00
---
# Docker 生成 Images 並更新至 Docker Hub

## 準備

1. 撰寫 Dockerfile

   如果還沒有準備好 dockerfile 可依照前篇
   
2. 註冊 DockerHub

   官方網站 https://hub.docker.com/

## 開始

### 第一步、Build Docker Images

使用 docker 指令 build images

```CMD=
$ docker build -t docker_project:version . --no-cache
```

`docker_project` 為 REPOSITORY

`version` 為 TAG ； 可使用 TAG 來分專案版本

*如果不填 `version` 系統則會自動填入 latest

`--no-cache` 為 避免 Dockerfile 被 cache 住，造成 build 到未修改過的 Dockerfile


### 第二步、檢查 Docker Images

使用 docker 指令列出現有 Images

```CMD=
$ docker images
```

檢查 Images 是否生成完成 並檢查 REPOSITORY/TAG 是否有誤

![Docker Images](1666087484902.jpg)

檢查沒問題即可下一步

### 第三步、更新 Docker Repository

因要把 Docker Images Push 到 Docker Hub 上，需要把 Docker Images Repository 與 Docker Hub 一致

```CMD=
$ docker tag docker_project:version dockerhub_username/docker_project:version
```
`docker_project` 為原 image 名稱

`dockerhub_username/docker_project:version` 為 DockerHub 使用者名稱 / Image 名稱 : 版本

執行後可以輸入 Docker Images 查看

![Docker Images](1666087484902.jpg)

### 第四步、Push 至 docker hub

```CMD=
$ docker push dockerhub_username/docker_project:version
```

執行後只需要等待他跑完就可以到 Docker Hub 檢查囉 !

![Docker Hub](1666088236900.jpg)

---

今天的教學就到此 ~

### Thank you! :smile:

