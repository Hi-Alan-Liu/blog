title: Vue 專案 Docker 化
author: Alan Liu
tags:
  - Docker
  - Docker hub
  - Docker Images
  - Vue.js
categories:
  - Docker
date: 2024-02-26 17:47:00
---
# Vue 專案 Docker 化

## 前言

因後端專案逐漸 Docker 化，前端專案也來跟風處理。

---

## 準備

註冊 Docker hub
註冊網址：https://hub.docker.com/

下載　Docker Desktop
下載網址：https://docs.docker.com/desktop/install/windows-install/

*安裝完畢後在進行下一步*

---

## 開始

### 一、建立 Dockerfile

```Dockerfile=
FROM node:18 as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

![Dockerfile 範例](1708943495204.jpg)

我們利用 Docker 多階段建構來完成建置

* 處理vue.js專案建置與打包 
* 處理 Nginx 設定並將 Vue.js 打包好的專案進行複製

npm run build 可根據 package Scripts 進行更動

### 二、執行指令並測試


```shell#=
  docker build -t dockerize-vuejs-app .
```

vuejs-cookbook/dockerize-vuejs-app 為打包的成 Docker Images 的名稱

```shell#=
  docker images
```

輸入 Docker Images 可查看打包好的Images

![Docker Images](1708943480518.jpg)


運行 Images

```shell#=
  docker run -d -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

`-d`: 為背景執行

`-p`: 設定 Port

`--name`: 設定 Containers 名稱

之後打開瀏覽器進入 http://localhost:8080/

有出現專案就完成囉 !

今天的教學就到此

### Thank you! :smile:

---

參考

- [Dockerize Vue.js App](https://v2.vuejs.org/v2/cookbook/dockerize-vuejs-app)

- [在 Docker 容器中使用 Nginx 部署你的应用](https://cli.vuejs.org/zh/guide/deployment.html#docker-nginx)