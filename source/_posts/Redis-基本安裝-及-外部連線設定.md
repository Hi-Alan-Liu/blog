title: Redis 基本安裝 及 外部連線設定
author: John Doe
tags:
  - redis
  - config
  - bind
categories:
  - redis
date: 2022-08-05 14:05:00
---
# Redis 基本安裝 及 外部連線設定


## 準備


### 安裝 Redis

Linux 版本
安裝方式：https://redis.io/docs/getting-started/installation/install-redis-on-linux/

macOS 版本
安裝方式：https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/

Windows 版本
安裝方式：https://redis.io/docs/getting-started/installation/install-redis-on-windows/

Docker Image 版本
安裝方式：https://hub.docker.com/_/redis

*補充 
[` Windows 安裝及啟動教學`](https://marcus116.blogspot.com/2019/02/how-to-install-redis-in-windows-os.html?m=1 )

*安裝完畢後在進行下一步*

---

## 開始

### 一、確認 config 位置

先使用 redis-cli 連進 redis

```cmd=
$ redis-cli
```

`127.0.0.1:6379 為系統顯示不需複製`

成功連進後輸入 ping

```redis-cli=
127.0.0.1:6379> ping
```

回應 PONG 代表 redis 正常連線

![回應PONG](1660285557863.jpg)

接著輸入 info 查看 config 的位置

```redis-cli=
127.0.0.1:6379> info
```

位置在 config_file 

![info 範例](1660285894330.jpg)

### 二、設定 config

確認 redis bind，請輸入以下指令

```redis-cli=
127.0.0.1:6379>  config get bind
```

![get bind 範例](1660286930448.jpg)

預設 redis config bind 為 127.0.0.1 因此只能 local 連線
因此需要對 config 進行修改

從上一步驟已得知 config 位置
可以對 config 直接進行修改

![config 範例](1660286516507.jpg)

也可以透過指令進行修改

```redis-cli=
127.0.0.1:6379> set bind 0.0.0.0
127.0.0.1:6379> save
```

設定完畢即可重啟 redis

```redis-cli=
$ /etc/init.d/redis-server restart
```

MAC 重啟方式
```redis-cli=
$ redis-cli shutdown
```

重啟之後就設定完成囉 ~

### Thank you! :smile:
---

參考


- [设置远程Redis服务可以被访问](https://www.jianshu.com/p/0ed7e88325dd)
- [如何停止redis-server？](https://ubuntuqa.com/zh-tw/article/8351.html)