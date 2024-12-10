title: Nginx Docker 常用設置方式
author: Alan Liu
tags:
  - Docker
  - Nginx
  - Let’s Encrypt
  - SSL
  - ''
categories:
  - Docker
date: 2024-12-10 16:50:00
---
# Nginx Docker 常用設置方式與Let’s Encrypt 產生 SSL憑證

## 前言

目前佈署環境大多使用 Docker 並且同時運行多個容器，因此獨立起一個 Nginx Docker 進行網域處理

*範例 虛擬經環境版本為 Amazon Linux 2023

## 開始

### 下載 Nginx Docker

```
sudo docker pull nginx
```

如果有指定版本可新增

```
sudo docker pull nginx:1.23
```

### 新增 .conf 文件

在目錄創建一個 nginx 資料夾

```
mkdir -p ~/nginx
```

建立 .conf 文件

```
vi ~/nginx/ServerName.conf
```

設置文件， 以下範例為參考。

```
# HTTPS 伺服器 (wwww.domain.com)
server {
    listen 80;
    server_name wwww.domain.com;

    location / {
        proxy_pass http://172.17.0.1:8090;
    }
}
```

取得文件完整路徑

```
pwd
```

![pwd 範例](1733824806554.jpg)

### 啟動 Nginx

執行 Docker 並掛載設定好的文件。

```
sudo docker run -d --rm \
-p 80:80 \
--name nginx \
-v /home/ec2-user/nginx/ServerName.conf:/etc/nginx/conf.d/ServerName.conf:ro \
nginx
```

指令說明

`-d`: 為背景執行

`--name`: 為指令容器名稱

`--rm`: 表示在容器停止後自動刪除容器。

`-v`: 將主機上的目錄或文件掛載到容器內部。

`:ro`: 為只可讀

成功執行後即可查看執行狀態

```
sudo docker ps
```

![docker ps](1733824621970.jpg)

### 掛載 Let's Encrypt SSL 憑證

查看憑證

```
sudo certbot certificates
```

![docker ps](1733825168715.jpg)

從圖可以看到憑證位置


設置 .conf 文件

```
# HTTP 重定向到 HTTPS
server {
    listen 80;
    server_name wwww.domain.com;
    return 301 https://$host$request_uri;
}

# HTTPS 伺服器 (wwww.domain.com)
server {
    listen 443 ssl;
    server_name wwww.domain.com;

    ssl_certificate     /usr/share/certbot/ssl/live/wwww.domain.com/fullchain.pem;
    ssl_certificate_key /usr/share/certbot/ssl/live/wwww.domain.com/privkey.pem;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;

    location / {
        proxy_pass http://172.17.0.1:8083;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

執行並掛載 SSL 憑證

```
sudo docker run -d --rm \
-p 80:80 -p 443:443 \
--name nginx \
-v /home/ec2-user/nginx/ServerName.conf:/etc/nginx/conf.d/ServerName.conf:ro \
-v /etc/letsencrypt/live/wwww.domain.com/fullchain.pem:/etc/ssl/certs/fullchain.pem:ro \
-v /etc/letsencrypt/live/wwww.domain.com/privkey.pem:/etc/ssl/private/privkey.pem:ro \
nginx
```

### 更新 Let's Encrypt SSL 憑證

```
sudo certbot renew && sudo docker stop nginx && sudo docker run -d --rm \
-p 80:80 -p 443:443 \
--name nginx \
-v /home/ec2-user/nginx/ServerName.conf:/etc/nginx/conf.d/ServerName.conf:ro \
-v /etc/letsencrypt/live/wwww.domain.com/fullchain.pem:/etc/ssl/certs/fullchain.pem:ro \
-v /etc/letsencrypt/live/wwww.domain.com/privkey.pem:/etc/ssl/private/privkey.pem:ro \
nginx
```

今天的教學就到此

### Thank you! :smile: