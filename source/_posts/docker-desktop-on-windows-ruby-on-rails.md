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
# Docker Desktop on Windows (ruby on rails)


## 前言

因工作環境需求必須在 Windows service 2019 佈署 ruby on rails 

但 capistrano 無法支援佈署至 windows 因此選擇使用 docker desktop 解決問題

## 準備

註冊 Docker hub
註冊網址：https://hub.docker.com/

下載　Docker Desktop
下載網址：https://docs.docker.com/desktop/install/windows-install/

將 Docker Desktop Containers 切換為 Linux
![Docker Desktop Containers](1659335415680.jpg)
**出現 Switch to Windows containers 代表當前為 Linux*

*安裝完畢後在進行下一步*

---

## 開始

### 一、建立 Dockerfile

參考 [Docker docs - Compose and Rails](https://docs.docker.com/samples/rails/) 建立 Dockerfile

```cmd=
$ vim Dockerfile
```

我在 **docker desktop v4.10.1** 照步驟走 docker run 無法正常啟動

因此我照著 [Docker 上的 Windows](https://docs.microsoft.com/zh-tw/virtualization/windowscontainers/manage-docker/manage-windows-dockerfile) 教學稍微改版了一下


```Dockerfile=
## Dockerfile 內容
# syntax=docker/dockerfile:1
FROM ruby:2.4.4
RUN apt-get update -qq && apt-get install -y nodejs
WORKDIR /myapp
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock
RUN bundle install

# Add the app code
ADD . /myapp

# Default command
CMD bash -c "rm -f tmp/pids/server.pid && bundle exec rails db:migrate && bundle exec rails s -p 3000"
```
範例 ruby 為 2.4.4

之後根據需安裝的套件新增指令

```Dockerfile=
## Dockerfile 內容
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
ENV BUNDLER_VERSION=2.3.12
RUN gem install bundler:2.3.12
RUN gem install tiny_tds
ENV RAILS_ENV development
RUN bundle install

ADD . /myapp

# 設定 port
EXPOSE 3000

# Default command
CMD bash -c "rm -f tmp/pids/server.pid && bundle exec rails db:migrate && bundle exec rails s -p 3000"
```
這邊新增了安裝 `imagemagick` 及 `sql server` 需要的套件

並加入指令 `bundler` 解決無法 bundle 的問題


以上的 `Dockerfile` 主要有用到的指令說明如下

`FROM`： 使用 ruby 版本為 2.4.4

`RUN`： RUN 指令後面放 Linux 指令，用來執行安裝和設定這個 Image 需要的東西

`WORKDIR`：　指定工作目錄

`COPY`：　複製檔案

`ADD`： `COPY`　進階版，可用於自動解壓縮的場合

`ENV`： 設定環境變數

`CMD`： docker run 執行 bash 指令`


設定好 Dockerfile 接著就是打包產生 Image

### 二、打包 Docker Image

在和 Dockerfile 檔案同層的資料夾底下輸入 `docker build` 指令

```CMD=
$ docker build -t docker_test:1.0.0 . --no-cache
```

`docker_test` 為 Images Name

`1.0.0` 為 Tag

`--no-cache` 為避免 Dockerfile 被 cache 住，造成 build 到未修改過的 Dockerfile


build docker image 成功之後可以從 `Docker desktop` 看到

![Docker Desktop Images](1659433057364.jpg)

或者輸入以下指令

```CMD=
$ docker images
```

![docker images 指令範例](1659433027937.jpg)

### 三、執行 Docker Image

輸出好 Image 接著在 Docker Desktop 按下 RUN

![Docker Desktop](1659435150426.jpg)

或輸入以下指令

```CMD=
$ docker run docker_test:1.0.0
```

基本上運行到這裡就順利完成囉 !

### 四、刪除 Docker Image

如果有不需要的 Image 可輸入以下指令刪除

```CMD=
$ docker rmi -f docker_test:1.0.0
```


### 補充、Docker compose


另外再本機 run 也可以使用 docker compose

建立 docker-compose.yml

```cmd=
$ docker-compose.yml
```


```docker-compose.yml=
## docker-compose.yml 內容
version: "3.9"
services:
  web:
    image: MyProjectName:1.0
    build: .
    command: bash -c "rm -f tmp/pids/server.pid && bundle exec rails s"
    volumes:
      - .:/myapp
    ports:
      - "3000:3000"
```

`MyProjectName` 為 Image Name

`1.0` 為 Tag

因我的 database 是外連所以無補上 database 的資訊

建立好 `docker-compose.yml` 即可輸入以下指令

```cmd=
$ docker-compose up
```

等指令跑完

就完成了 docker image 及 docker run

---

今天的教學就到此 ~

### Thank you! :smile:
