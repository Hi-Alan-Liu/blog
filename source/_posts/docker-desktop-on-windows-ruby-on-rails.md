---
title: Docker Desktop on Windows (ruby on rails)
author: Alan Liu
tags:
  - Docker
  - Ruby on Rails
  - Windows
  - Dockerfile
  - docker-compose
  - image
categories:
  - Docker
date: 2022-08-01 14:06:00
---

因工作環境需求，必須在 Windows Server 2019 部署 Ruby on Rails，但 Capistrano 不支援部署至 Windows，因此改用 Docker Desktop 解決。

<!-- more -->

## 準備

1. 註冊 [Docker Hub](https://hub.docker.com/)
2. 下載並安裝 [Docker Desktop](https://docs.docker.com/desktop/install/windows-install/)
3. 將 Docker Desktop 的 Containers 切換為 Linux（選單出現 **Switch to Windows containers** 代表目前已是 Linux）

![Docker Desktop Containers](1659335415680.jpg)

---

## 一、建立 Dockerfile

參考 [Docker docs - Compose and Rails](https://docs.docker.com/samples/rails/) 建立 Dockerfile。我在 **Docker Desktop v4.10.1** 照官方步驟無法正常啟動，因此參考 [Docker 上的 Windows](https://docs.microsoft.com/zh-tw/virtualization/windowscontainers/manage-docker/manage-windows-dockerfile) 調整如下（Ruby 2.4.4，並加上 `imagemagick` 與 SQL Server 需要的 FreeTDS）：

```Dockerfile
# syntax=docker/dockerfile:1
FROM ruby:2.4.4
RUN apt-get update && apt-get install -y nodejs imagemagick freetds-dev wget build-essential
RUN wget http://www.freetds.org/files/stable/freetds-1.1.24.tar.gz
RUN tar -xzf freetds-1.1.24.tar.gz
RUN cd freetds-1.1.24 \
    && ./configure --prefix=/usr/local --with-tdsver=7.3  \
    && make \
    && make install
WORKDIR /myapp
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock
# 指定 bundler 版本，解決無法 bundle 的問題
ENV BUNDLER_VERSION=2.3.12
RUN gem install bundler:2.3.12
RUN gem install tiny_tds
ENV RAILS_ENV development
RUN bundle install

ADD . /myapp

EXPOSE 3000

CMD bash -c "rm -f tmp/pids/server.pid && bundle exec rails db:migrate && bundle exec rails s -p 3000"
```

| 指令 | 說明 |
| --- | --- |
| `FROM` | 基底 Image，這裡使用 Ruby 2.4.4 |
| `RUN` | 執行 Linux 指令，安裝與設定 Image 需要的套件 |
| `WORKDIR` | 指定工作目錄 |
| `COPY` / `ADD` | 複製檔案；`ADD` 為進階版，可自動解壓縮 |
| `ENV` | 設定環境變數 |
| `EXPOSE` | 宣告容器使用的 port |
| `CMD` | `docker run` 時執行的指令 |

## 二、打包 Docker Image

在 Dockerfile 所在的資料夾執行：

```cmd
docker build -t docker_test:1.0.0 . --no-cache
```

`docker_test` 為 Image 名稱，`1.0.0` 為 Tag，`--no-cache` 避免使用快取而 build 到舊的內容。

成功後可以在 Docker Desktop 看到，或輸入 `docker images` 查看：

![Docker Desktop Images](1659433057364.jpg)

![docker images 指令範例](1659433027937.jpg)

## 三、執行 Docker Image

在 Docker Desktop 按下 RUN，或輸入：

```cmd
docker run -p 3000:3000 docker_test:1.0.0
```

`-p 3000:3000` 將容器的 3000 port 對應到本機，即可用 `http://localhost:3000` 開啟網站。

![Docker Desktop](1659435150426.jpg)

不需要的 Image 可以用以下指令刪除：

```cmd
docker rmi -f docker_test:1.0.0
```

## 補充：使用 Docker Compose

本機開發也可以改用 Docker Compose。在專案根目錄建立 `docker-compose.yml`：

```yaml
version: "3.9"
services:
  web:
    image: myprojectname:1.0
    build: .
    command: bash -c "rm -f tmp/pids/server.pid && bundle exec rails s"
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
```

`myprojectname` 為 Image 名稱（Docker 的 Image 名稱只能用小寫），`1.0` 為 Tag。我的資料庫是外部連線，所以沒有設定 database 服務。

建立好後執行，就會自動完成 build 與 run：

```cmd
docker-compose up
```
