title: Docker 設定容器自動重啟
author: Alan Liu
tags:
  - Docker
  - Container
  - Restart
  - Policy
categories:
  - Docker
date: 2023-10-13 12:00:00
---
# Docker 設定容器自動重啟

## 前言

因 Windows 系統定期會重新啟動，所以需設置 Docker container (容器) 能夠自動啟動。

避免 server 無運作等問題。

## 開始

### 第一步、確認 Container ID

在使用指令前 先使用 Docker PS 查看執行中的容器ID

```PowerShell-Scripts=
docker ps
```

![docker ps 範例](1697168878486.jpg)


### 第二步、設定 Container 重啟策略

使用指令設定 Container (容器) 重啟策略

```PowerShell-Scripts=
docker run --restart={Policy} {Container ID}
```

![docker 重啟策略範例](1697169178517.jpg)

* Policy : 重啟策略

* Container ID : 容器的 ID

#### Policy 列表描述

* no : 默認，不自動重啟容器。

* on-failure[:max-retries] : 如果容器因錯誤退出，則重新啟動容器，這表現為非零退出代碼。或者，使用該:max-retries選項限制 Docker 守護程序嘗試重新啟動容器的次數。

* always : 無論退出狀態如何，始終重新啟動容器。

* unless-stopped : 無論退出狀態如何，始終重啟容器，包括在守護進程啟動時，除非容器在 Docker 守護進程停止之前進入停止狀態。

### 第三步、檢查 Container 設置

使用指令查詢 Container 是否設置成功

```PowerShell-Scripts=
docker inspect -f "{{ .HostConfig.RestartPolicy }}" {Container ID}
```

![檢查 Container 設置範例](1697169640784.jpg)

當有回覆 {always 0} 則代表有設置成功!

如果想要查詢全部的 Container 的重啟策略可以使用以下指令


```PowerShell-Scripts=
docker inspect --format "{{.HostConfig.RestartPolicy.Name}}, {{.Name}}, {{.Id}}" $(docker ps -qf status=running)
```

![查看所有重啟策略範例](1697169841734.jpg)

這樣就可以查詢所有的重啟策略

今天的教學就到此

### Thank you! :smile:

---

參考


- [Docker restart](https://quietbo.com/2022/07/27/docker-restart/)
