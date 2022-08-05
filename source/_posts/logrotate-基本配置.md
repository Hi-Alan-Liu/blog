title: logrotate 基本配置
author: Alan Liu
tags:
  - logrotate
  - Linux
  - log
categories:
  - Linux
date: 2022-05-19 16:19:00
---
# logrotate 基本配置

### 前言

使用 Linux 內建的 logrotate 工具，定期清空和壓縮 Log 檔案。
Log 沒有做 rotate ，Log 檔案將會不斷成長到撐爆空間。

### 第一步、 移動到 Logrotate.d 資料夾

```Linux=
    cd /etc/logrotate.d
```

### 第二步、 建立檔案

```Linux=
    sudo vi **project_name**
```

* **project_name** 請填入專案名稱，方便辨識


### 第三步、 填入設定

```logrotate=
/home/**user_name**/**project_name**/*.log {
    daily 
    dateext
    missingok
    rotate 30
    notifempty
    copytruncate
}
```

* **user_name** 請填入使用者名稱
* **project_name** 請填入專案名稱


* daily 表示每天整理，也可以改成 weekly 或 monthly
* dateext 表示檔案補上 rotate 的日期  |  EX: xxx.log-%Y%m%d
* missingok 表示如果找不到 log 檔也沒關系
* rotate 30 表示保留最近 30 份
* compress 表示壓縮起來，預設用 gzip  |  EX: xxx.log-%Y%m%d.tar.gz
* delaycompress 表示延後壓縮直到下一次 rotate
* notifempty 表示如果 log 檔是空的，就不 rotate
* copytruncate 先複製 log 檔的內容後，在清空


### 第四步、 立即執行設定

```Linux=
sudo /usr/sbin/logrotate -v /etc/logrotate.conf
```


### Thank you! :smile: 

---

參考

- [How to Setup and Manage Log Rotation Using Logrotate in Linux](https://www.tecmint.com/install-logrotate-to-manage-log-rotation-in-linux/)