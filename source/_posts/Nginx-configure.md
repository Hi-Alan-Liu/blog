title: Nginx 常用配置
author: Alan Liu
tags:
  - nginx
categories:
  - DevOps
date: 2022-05-06 15:21:00
---
# Nginx 常用配置


### 下載站點

```nginx=
    location /download {    
        alias /home/user/elivolution; # 資料夾路徑
        autoindex on;
        charset utf-8;
    }
```

* alias 資料夾路徑
* autoindex 開啟目錄瀏覽
* charset 防止中文亂碼需加入 utf-8 or gbk


### 反向代理並啟用 CORS

```nginx=
server {
    #以上審略

    location ~ ^/(computer/|mobile|home) {
        add_header 'Access-Control-Allow-Origin' *網域 server_name*;
        add_header 'Access-Control-Allow-Credentials' 'true';
        add_header 'Access-Control-Allow-Headers' 'Authorization,Accept,Origin,DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Content-Range,Range';
        add_header 'Access-Control-Allow-Methods' 'GET,POST,OPTIONS,PUT,DELETE,PATCH';
    
        if ($request_method = 'OPTIONS') {
            add_header 'Access-Control-Max-Age' 1728000;
            add_header 'Content-Type' 'text/plain charset=UTF-8';
            add_header 'Content-Length' 0;
            return 204;
        }
    
        proxy_redirect off;
        proxy_set_header host $host;
        proxy_set_header X-real-ip $remote_addr;
        proxy_set_header X-forward-for $proxy_add_x_forwarded_for;
        proxy_pass http://127.0.0.1:3000;
  }
}
```
* location ~ ^/(computer/|mobile|home) 
  當網址為 http://domain/computer/name 或 http://domain/mobile 時進行轉址
* proxy_pass 填入位置

### http 轉 https

```nginx=
server {
    listen 80;
    listen [::]:80;
    server_name server_name;
    return 301 https://$host$request_uri;
}
```

### Vue-router

```nginx=
server {
    #以上審略

    location / {
        root   /home/deploy/project_name/dist;
        try_files $uri $uri/ /index.html;
    }
}
```

### 防止機器人

```javascript=
server {
    #以上審略

    if ($http_user_agent ~* "SemrushBot|PetalBot|BOT/0.1|YandexBot|Linguee Bot" ) {
        return 403;             
    }
}
```

### Thank you! :smile:

---

參考

- [Nginx 配置下載站點](https://iter01.com/515464.html)
- [How to enable CORS in Nginx proxy server?](https://stackoverflow.com/questions/45986631/how-to-enable-cors-in-nginx-proxy-server)
- [Vue Router](https://router.vuejs.org/guide/essentials/history-mode.html#nginx)