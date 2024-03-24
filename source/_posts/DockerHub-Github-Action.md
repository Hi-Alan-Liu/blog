title: 使用 Github Action 將 Docker 化的專案進行打包、上傳並佈署
author: Alan Liu
tags:
  - DockerHub
  - Github Action
  - Build
  - CI/CD
  - Images
categories:
  - Docker
date: 2024-03-24 23:23:00
---
# 使用 Github Action 將 Docker 化的專案進行打包、上傳並佈署

## 前言

因後端專案逐漸 Docker 化，前端專案也來跟風處理。

---

## 準備

註冊 Docker hub
網址：https://hub.docker.com/

註冊 GitHub
網址：https://github.com/

一個 Docker 化的專案
本篇範例：https://github.com/Hi-Alan-Liu/docker-vue

*準備完畢後在進行下一步*

---

## 開始

### 一、建立 Github Action

從 Github 專案頁面點選 Actions 

並選擇 Docker images

![Action 範例](1711294253296.jpg)

撰寫待會需要執行的 yml 檔案

```yml=
name: Docker Image Build and Deploy

on:
  push:
    branches: ["main"]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Login to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_HUB_USERNAME }}
          password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v2
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKER_HUB_USERNAME }}/${{ secrets.DOCKER_HUB_PROJECT }}:latest

      - name: Deploy to Docker
        run: |
          echo "${{ secrets.DEPLOY_SSH_KEY }}" > private_key && chmod 600 private_key
          ssh -o StrictHostKeyChecking=no -i private_key ${{ secrets.DEPLOY_SSH_USER }}@${{ secrets.DEPLOY_SSH_IP }} '
            sudo docker pull ${{ secrets.DOCKER_HUB_USERNAME }}/${{ secrets.DOCKER_HUB_PROJECT }}:latest
            sudo docker stop ${{ secrets.DOCKER_HUB_PROJECT }}
            sudo docker run -d  --rm --name ${{ secrets.DOCKER_HUB_PROJECT }} -p 2083:80 ${{ secrets.DOCKER_HUB_USERNAME }}/${{ secrets.DOCKER_HUB_PROJECT }}:latest
          '
```

此篇範例是使用 docker/login-action@v1 進行登入

接著執行 docker/build-push-action@v2

將專案打包並更新至 DockerHub

最後使用 ssh 連上雲端主機

進行 Docker pull 及 run 等等指令

![Yml 範例](1711295086499.jpg)

儲存 Commit 後， Action 會開始自動執行

目前一定會顯示執行失敗

![失敗範例](messageImage_1711295596311.jpg)

原因是因為我們沒有將 secrets 相關的 Key Value 設定上去


### 二、設定 Github secrets

從 Github 專案頁面點選 Settings

接著點 Secrets and variables > Actions

![Settings 範例](1711294516622.jpg)

接下來點選 New repository secret

將 Action 會使用到的參數全部填入

![參數範例](1711294929554.jpg)

本篇範例會使用到

`DOCKER_HUB_USERNAME`: DockerHub Username

`DOCKER_HUB_ACCESS_TOKEN`: DockerHub Token

`DOCKER_HUB_PROJECT`: DockerHub 專案名稱

`DEPLOY_SSH_IP`: 雲端主機的公開IP位置

`DEPLOY_SSH_KEY`: 雲端主機的 Private Key

`DEPLOY_SSH_USER`: 雲端主機的使用者名稱

![DOCKER_HUB_ACCESS_TOKEN](1711296512499.jpg)

DOCKER_HUB_ACCESS_TOKEN 可以從 DockerHub Account > Security 裡面建立 Access Tokens


### 三、重新執行 Github Action

從 Github Actino 點選 Re-run jobs 就可以重新執行剛剛的失敗的 Action

![Github Action](1711296738243.jpg)

之後等他執行完成基本上就成功囉 ~

今天的教學就到此

### Thank you! :smile:


