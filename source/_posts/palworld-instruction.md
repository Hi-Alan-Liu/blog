title: 幻獸帕魯伺服器常用指令
author: Alan Liu
tags:
  - Palworld
categories:
  - Other
date: 2024-01-25 01:42:00
---
# 幻獸帕魯伺服器常用指令

## 前言

這是一個為了玩幻獸帕魯架設伺服器的常用指令

如果有其他資訊會再補充

## 開始


#### 移動至主程式位置

```
cd ~/Steam/steamapps/common/PalServer
```

#### 使用 tmux 啟動伺服器


```
tmux
```

```
./PalServer.sh
```

#### 進入 tmux 關閉伺服器

```
tmux attach -t 0
```

按 ctrl + C 關閉伺服器


#### 更新伺服器版本

```
steamcmd +login anonymous +app_update 2394010 validate +quit
```

#### 編寫設定檔

```
vi ~/Steam/steamapps/common/PalServer/Pal/Saved/Config/LinuxServer/PalWorldSettings.ini
```


### Thank you! :smile:


---

參考


- [【心得】簡易google雲端架設小小心得，圖多!](https://forum.gamer.com.tw/C.php?bsn=71458&snA=94)

- [Palworld tech guide - Dedicated server guide](https://tech.palworldgame.com/dedicated-server-guide)